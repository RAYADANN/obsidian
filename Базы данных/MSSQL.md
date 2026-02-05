( MSSQL ) — это реляционная система управления базами данных от Microsoft, основанная на SQL. В отличие от MySQL, которую мы обсуждали в предыдущем разделе, MSSQL является закрытым исходным кодом и изначально была написана для работы в операционных системах Windows. Она популярна среди администраторов баз данных и разработчиков при создании приложений, работающих на платформе Microsoft .NET, благодаря своей мощной нативной поддержке .NET. Существуют версии MSSQL, работающие на Linux и MacOS, но чаще всего мы будем сталкиваться с экземплярами MSSQL на целевых системах под управлением Windows.

#### Клиенты MSSQL

[SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) ( `SSMS`SSMS) поставляется в составе установочного пакета MSSQL или может быть загружен и установлен отдельно. Обычно его устанавливают на сервер для первоначальной настройки и долгосрочного управления базами данных администраторами. Следует помнить, что, поскольку SSMS является клиентским приложением, его можно установить и использовать на любой системе, с которой администратор или разработчик планирует управлять базой данных. Он существует не только на сервере, где размещена база данных. Это означает, что мы можем столкнуться с уязвимой системой, в которой SSMS использует сохраненные учетные данные, позволяющие подключаться к базе данных. На изображении ниже показана работа SSMS.

![В SQL Server Management Studio отображается обозреватель объектов с развернутой базой данных «Сотрудники», показывающий таблицы, представления и другие объекты базы данных.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/112/ssms.png)

Для доступа к базе данных, работающей на MSSQL, можно использовать множество других клиентов, включая, помимо прочих:

||||||
|---|---|---|---|---|
|[mssql-cli](https://docs.microsoft.com/en-us/sql/tools/mssql-cli?view=sql-server-ver15)|[SQL Server PowerShell](https://docs.microsoft.com/en-us/sql/powershell/sql-server-powershell?view=sql-server-ver15)|[HeidiSQL](https://www.heidisql.com/)|[SQLPro](https://www.macsqlclient.com/)|[mssqlclient.py от Impacket](https://github.com/SecureAuthCorp/impacket/blob/master/examples/mssqlclient.py)|

Из перечисленных выше клиентов MSSQL, специалистам по тестированию на проникновение может больше всего пригодиться mssqlclient.py от Impacket, поскольку проект Impacket от SecureAuthCorp уже установлен во многих дистрибутивах для тестирования на проникновение. Чтобы узнать, находится ли клиент на нашем хосте и где именно, можно использовать следующую команду:

        shellsession
`MuraMitchi@htb[/htb]$ locate mssqlclient /usr/bin/impacket-mssqlclient /usr/share/doc/python3-impacket/examples/mssqlclient.py`

#### Базы данных MSSQL

В MSSQL есть системные базы данных по умолчанию, которые помогают понять структуру всех баз данных, которые могут размещаться на целевом сервере. Ниже приведены базы данных по умолчанию и краткое описание каждой из них:

|База данных системы по умолчанию|Описание|
|---|---|
|`master`|Отслеживает всю системную информацию для экземпляра SQL-сервера.|
|`model`|Шаблонная база данных, которая служит структурой для каждой новой создаваемой базы данных. Любые изменения настроек в шаблонной базе данных будут отражены в любой новой базе данных, созданной после внесения изменений в шаблонную базу данных.|
|`msdb`|Агент SQL Server использует эту базу данных для планирования заданий и оповещений.|
|`tempdb`|Хранит временные предметы|
|`resource`|База данных только для чтения, содержащая системные объекты, входящие в состав SQL-сервера.|

Источник таблицы: [Системные базы данных, документация Microsoft.](https://docs.microsoft.com/en-us/sql/relational-databases/databases/system-databases?view=sql-server-ver15)

---

## Конфигурация по умолчанию

Когда администратор впервые устанавливает и настраивает MSSQL для доступа по сети, служба SQL, скорее всего, будет работать от имени пользователя `NT SERVICE\MSSQLSERVER`. Подключение со стороны клиента возможно с помощью аутентификации Windows, и по умолчанию шифрование при попытке подключения не применяется.

![В окне подключения к SQL Server отображаются параметры типа сервера, имя сервера «ILF-SQL-01» и аутентификация Windows.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/112/auth.png)

Настройка аутентификации `Windows Authentication`означает, что базовая операционная система Windows обработает запрос на вход в систему и будет использовать либо локальную базу данных SAM, либо контроллер домена (на котором размещен Active Directory), прежде чем разрешить подключение к системе управления базами данных. Использование Active Directory может быть идеальным вариантом для аудита активности и контроля доступа в среде Windows, но если учетная запись скомпрометирована, это может привести к повышению привилегий и перемещению пользователей внутри домена Windows. Как и в случае с любой ОС, службой, ролью сервера или приложением, может быть полезно настроить ее на виртуальной машине от установки до конфигурации, чтобы понять все настройки по умолчанию и потенциальные ошибки, которые может допустить администратор.

---

## Опасные условия

В процессе работы над проектом полезно взглянуть на ситуацию глазами ИТ-администратора. Такой подход помогает не забывать проверять различные настройки, которые могли быть неправильно или опасно сконфигурированы администратором. Рабочий день в ИТ-сфере может быть довольно напряженным: одновременно выполняется множество различных проектов, а необходимость работать быстро и точно является реальностью во многих организациях, поэтому ошибки случаются очень часто. Достаточно одной незначительной ошибки в конфигурации, чтобы поставить под угрозу критически важный сервер или службу в сети. Это относится практически ко всем сетевым службам и серверным ролям, которые можно настроить, включая MSSQL.

Это не исчерпывающий список, поскольку существует бесчисленное множество способов настройки баз данных MSSQL администраторами в зависимости от потребностей их организаций. Нам может быть полезно рассмотреть следующие варианты:

- Клиенты MSSQL не используют шифрование для подключения к серверу MSSQL.
- Использование самоподписанных сертификатов при применении шифрования. Самоподписанные сертификаты можно подделать.
- Использование [труб с указанием их названия](https://docs.microsoft.com/en-us/sql/tools/configuration-manager/named-pipes-properties?view=sql-server-ver15)
- Слабые и стандартные `sa`учетные данные. Администраторы могут забыть отключить эту учетную запись.

---

## Создание базы данных для сервиса

Существует множество способов сбора информации о работе службы MSSQL, и чем точнее будут наши сканирования, тем больше полезной информации мы сможем собрать. NMAP имеет скрипты MSSQL по умолчанию, которые можно использовать для выбора TCP-порта по умолчанию, `1433`на котором работает MSSQL.

Приведенный ниже скрипт сканирования NMAP предоставляет нам полезную информацию. Мы можем увидеть `hostname`, `database instance name`, `software version of MSSQL`и `named pipes are enabled`. Нам будет полезно добавить эти открытия в наши заметки.

#### NMAP MSSQL Script Scan

        shellsession
`MuraMitchi@htb[/htb]$ sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248 Starting Nmap 7.91 ( https://nmap.org ) at 2021-11-08 09:40 EST Nmap scan report for 10.129.201.248 Host is up (0.15s latency). PORT     STATE SERVICE  VERSION 1433/tcp open  ms-sql-s Microsoft SQL Server 2019 15.00.2000.00; RTM | ms-sql-ntlm-info:  |   Target_Name: SQL-01 |   NetBIOS_Domain_Name: SQL-01 |   NetBIOS_Computer_Name: SQL-01 |   DNS_Domain_Name: SQL-01 |   DNS_Computer_Name: SQL-01 |_  Product_Version: 10.0.17763 Host script results: | ms-sql-dac:  |_  Instance: MSSQLSERVER; DAC port: 1434 (connection failed) | ms-sql-info:  |   Windows server name: SQL-01 |   10.129.201.248\MSSQLSERVER:  |     Instance name: MSSQLSERVER |     Version:  |       name: Microsoft SQL Server 2019 RTM |       number: 15.00.2000.00 |       Product: Microsoft SQL Server 2019 |       Service pack level: RTM |       Post-SP patches applied: false |     TCP port: 1433 |     Named pipe: \\10.129.201.248\pipe\sql\query |_    Clustered: false Service detection performed. Please report any incorrect results at https://nmap.org/submit/ . Nmap done: 1 IP address (1 host up) scanned in 8.52 seconds`

Мы также можем использовать Metasploit для запуска вспомогательного сканера `mssql_ping`, который будет сканировать службу MSSQL и предоставлять полезную информацию в процессе сбора информации о её работе.

#### Пинг MSSQL в Metasploit

        shellsession
`msf6 auxiliary(scanner/mssql/mssql_ping) > set rhosts 10.129.201.248 rhosts => 10.129.201.248 msf6 auxiliary(scanner/mssql/mssql_ping) > run [*] 10.129.201.248:       - SQL Server information for 10.129.201.248: [+] 10.129.201.248:       -    ServerName      = SQL-01 [+] 10.129.201.248:       -    InstanceName    = MSSQLSERVER [+] 10.129.201.248:       -    IsClustered     = No [+] 10.129.201.248:       -    Version         = 15.0.2000.5 [+] 10.129.201.248:       -    tcp             = 1433 [+] 10.129.201.248:       -    np              = \\SQL-01\pipe\sql\query [*] 10.129.201.248:       - Scanned 1 of 1 hosts (100% complete) [*] Auxiliary module execution completed`

#### Подключение с помощью Mssqlclient.py

Если нам удастся угадать или получить доступ к учетным данным, это позволит нам удаленно подключиться к серверу MSSQL и начать взаимодействовать с базами данных с помощью T-SQL ( `Transact-SQL`). Аутентификация в MSSQL позволит нам напрямую взаимодействовать с базами данных через механизм баз данных SQL. С помощью Pwnbox или персонального хоста для атак мы можем использовать mssqlclient.py из Impacket для подключения, как показано в приведенном ниже выводе. После подключения к серверу может быть полезно оценить ситуацию и перечислить базы данных, присутствующие в системе.

        shellsession
`MuraMitchi@htb[/htb]$ python3 mssqlclient.py Administrator@10.129.201.248 -windows-auth Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation Password: [*] Encryption required, switching to TLS [*] ENVCHANGE(DATABASE): Old Value: master, New Value: master [*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english [*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192 [*] INFO(SQL-01): Line 1: Changed database context to 'master'. [*] INFO(SQL-01): Line 1: Changed language setting to us_english. [*] ACK: Result: 1 - Microsoft SQL Server (150 7208)  [!] Press help for extra shell commands SQL> select name from sys.databases name                                                                                                                                -------------------------------------------------------------------------------------- master                                                                                                                              tempdb                                                                                                                              model                                                                                                                               msdb                                                                                                                                Transactions`