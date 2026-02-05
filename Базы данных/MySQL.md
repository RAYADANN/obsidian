`MySQL`MySQL — это система управления реляционными базами данных с открытым исходным кодом на языке SQL, разработанная и поддерживаемая компанией Oracle. База данных — это просто структурированная коллекция данных, организованная для удобного использования и извлечения. Система баз данных может быстро обрабатывать большие объемы данных с высокой производительностью. Внутри базы данных хранение данных осуществляется таким образом, чтобы занимать как можно меньше места. Управление базой данных осуществляется с помощью [языка баз данных SQL](https://www.w3schools.com/sql/sql_intro.asp) . MySQL работает в соответствии с этим языком `client-server principle`и состоит из сервера MySQL и одного или нескольких клиентов MySQL. Сервер MySQL — это собственно система управления базами данных. Он отвечает за хранение и распределение данных. Данные хранятся в таблицах с различными столбцами, строками и типами данных. Эти базы данных часто хранятся в одном файле с расширением .sql `.sql`, например, .sql `wordpress.sql`.

#### Клиенты MySQL

Клиенты MySQL могут получать и редактировать данные, используя структурированные запросы к ядру базы данных. Вставка, удаление, изменение и извлечение данных осуществляется с помощью языка баз данных SQL. Таким образом, MySQL подходит для управления множеством различных баз данных, к которым клиенты могут отправлять множество запросов одновременно. В зависимости от назначения базы данных доступ возможен через внутреннюю сеть или общедоступный Интернет.

Одним из лучших примеров использования баз данных является CMS WordPress. WordPress хранит все созданные записи, имена пользователей и пароли в собственной базе данных, доступ к которой возможен только с локального сервера. Однако, как более подробно объясняется в модуле « [Введение в веб-приложения»](https://academy.hackthebox.com/course/preview/introduction-to-web-applications) , существуют также структуры баз данных, распределенные по нескольким серверам.

#### Базы данных MySQL

MySQL идеально подходит для таких приложений, как `dynamic websites`PHP, где важны эффективный синтаксис и высокая скорость отклика. Часто его комбинируют с операционной системой Linux, PHP и веб-сервером Apache, и в этой комбинации он также известен как [LAMP](https://en.wikipedia.org/wiki/LAMP_\(software_bundle\)) (Linux, Apache, MySQL, PHP), или, при использовании Nginx, как [LEMP](https://lemp.io/) . В веб-хостинге с базой данных MySQL он служит центральным экземпляром, в котором хранится контент, необходимый для PHP-скриптов. К ним относятся:

|||||
|---|---|---|---|
|Заголовки|Тексты|Метатеги|Формы|
|Клиенты|Имена пользователей|Администраторы|Модераторы|
|Адреса электронной почты|Информация о пользователе|Разрешения|Пароли|
|Внешние/внутренние ссылки|Ссылки на файлы|Конкретное содержание|Ценности|

В MySQL конфиденциальные данные, такие как пароли, могут храниться в открытом текстовом виде; однако, как правило, они предварительно шифруются PHP-скриптами с использованием безопасных методов, таких как [одностороннее шифрование (One-Way-Encryption](https://en.citizendium.org/wiki/One-way_encryption) ).

#### Команды MySQL

База данных MySQL внутренне преобразует команды в исполняемый код и выполняет запрошенные действия. Веб-приложение информирует пользователя, если во время обработки возникает ошибка, которую `SQL injections`могут вызвать различные факторы. Часто эти описания ошибок содержат важную информацию и подтверждают, среди прочего, что веб-приложение взаимодействует с базой данных иначе, чем предполагали разработчики.

Веб-приложение отправляет сгенерированную информацию обратно клиенту, если данные обработаны корректно. Эта информация может представлять собой извлеченные данные из таблицы или записи, необходимые для дальнейшей обработки, включая авторизацию, функции поиска и т. д. Команды SQL позволяют отображать, изменять, добавлять или удалять строки в таблицах. Кроме того, SQL может изменять структуру таблиц, создавать или удалять связи и индексы, а также управлять пользователями.

`MariaDB`MariaDB, которую часто связывают с MySQL, является форком оригинального кода MySQL. Это связано с тем, что главный разработчик MySQL покинул компанию `MySQL AB`после её приобретения `Oracle`и разработал другую систему управления базами данных SQL с открытым исходным кодом, основанную на исходном коде MySQL, и назвал её MariaDB.

---

## Конфигурация по умолчанию

Управление базами данных SQL и их конфигурациями — обширная тема. Она настолько велика, что целые профессии, например `database administrator`, в сфере управления базами данных, практически полностью посвящены работе с базами данных. Эти структуры быстро разрастаются, и их планирование может стать очень сложным. Управление базами данных является ключевой компетенцией не только для специалистов в этой области, `software developers`но и для специалистов по управлению базами данных `information security analysts`. Полное освещение этой области выходит за рамки данного модуля. Поэтому мы рекомендуем настроить экземпляр MySQL/MariaDB для экспериментов с различными конфигурациями, чтобы лучше понять доступные функции и параметры конфигурации. Давайте рассмотрим конфигурацию MySQL по умолчанию.

#### Конфигурация по умолчанию

        shellsession
`MuraMitchi@htb[/htb]$ sudo apt install mysql-server -y MuraMitchi@htb[/htb]$ cat /etc/mysql/mysql.conf.d/mysqld.cnf | grep -v "#" | sed -r '/^\s*$/d' [client] port        = 3306 socket      = /var/run/mysqld/mysqld.sock [mysqld_safe] pid-file    = /var/run/mysqld/mysqld.pid socket      = /var/run/mysqld/mysqld.sock nice        = 0 [mysqld] skip-host-cache skip-name-resolve user        = mysql pid-file    = /var/run/mysqld/mysqld.pid socket      = /var/run/mysqld/mysqld.sock port        = 3306 basedir     = /usr datadir     = /var/lib/mysql tmpdir      = /tmp lc-messages-dir = /usr/share/mysql explicit_defaults_for_timestamp symbolic-links=0 !includedir /etc/mysql/conf.d/`

---

## Опасные условия

В MySQL можно неправильно настроить множество параметров. Для более подробного изучения справочника [MySQL](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html) можно определить, какие параметры можно изменить в конфигурации сервера. Основные параметры, имеющие отношение к безопасности, следующие:

|**Настройки**|**Описание**|
|---|---|
|`user`|Указывает, от имени какого пользователя будет запускаться служба MySQL.|
|`password`|Устанавливает пароль для пользователя MySQL.|
|`admin_address`|IP-адрес, на котором следует ожидать TCP/IP-соединений на административном сетевом интерфейсе.|
|`debug`|Эта переменная указывает текущие настройки отладки.|
|`sql_warnings`|Эта переменная определяет, будут ли операторы INSERT для одной строки выводить информационную строку при возникновении предупреждений.|
|`secure_file_priv`|Эта переменная используется для ограничения влияния операций импорта и экспорта данных.|

Настройки `user`, `password`, и `admin_address`имеют важное значение для безопасности, поскольку записи вводятся в открытом текстовом виде. Часто права доступа к конфигурационному файлу сервера MySQL назначаются некорректно. Если у нас есть другой способ чтения файлов или даже командная оболочка, мы можем увидеть файл, а также имя пользователя и пароль для сервера MySQL. Предположим, что нет других мер безопасности для предотвращения несанкционированного доступа. В этом случае можно просмотреть и даже отредактировать всю базу данных и всю информацию о существующих клиентах, адреса электронной почты, пароли и личные данные.

Настройки обеспечивают подробный вывод информации в случае ошибок, которая важна для администратора, но не должна быть видна другим. Эта информация часто содержит конфиденциальные данные, которые можно обнаружить методом проб и ошибок для выявления дальнейших возможностей атаки. Эти сообщения об ошибках часто отображаются непосредственно в веб-приложениях. Соответственно, SQL-инъекции могут быть использованы даже для того, чтобы заставить сервер MySQL выполнять системные команды. Это обсуждается и демонстрируется в модулях « `debug`Основы [SQL-инъекций»](https://academy.hackthebox.com/course/preview/sql-injection-fundamentals) и [«Основы SQLMap»](https://academy.hackthebox.com/course/preview/sqlmap-essentials) .`sql_warnings`[](https://academy.hackthebox.com/course/preview/sql-injection-fundamentals)[](https://academy.hackthebox.com/course/preview/sqlmap-essentials)

---

## Создание базы данных для сервиса

Существует множество причин, по которым к серверу MySQL можно получить доступ из внешней сети. Тем не менее, это далеко не лучшая практика, и всегда можно найти базы данных, к которым можно получить доступ. Часто эти настройки предназначались лишь для временного использования, но администраторы забывали о них. Такая конфигурация сервера также может использоваться в качестве обходного пути из-за технической проблемы. Обычно сервер MySQL работает на порту `TCP port 3306`, и мы можем просканировать этот порт с помощью команды `Nmap`для получения более подробной информации.

#### Сканирование сервера MySQL

        shellsession
`MuraMitchi@htb[/htb]$ sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql* Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-21 00:53 CEST Nmap scan report for 10.129.14.128 Host is up (0.00021s latency). PORT     STATE SERVICE     VERSION 3306/tcp open  nagios-nsca Nagios NSCA | mysql-brute:  |   Accounts:  |     root:<empty> - Valid credentials |_  Statistics: Performed 45010 guesses in 5 seconds, average tps: 9002.0 |_mysql-databases: ERROR: Script execution failed (use -d to debug) |_mysql-dump-hashes: ERROR: Script execution failed (use -d to debug) | mysql-empty-password:  |_  root account has empty password | mysql-enum:  |   Valid usernames:  |     root:<empty> - Valid credentials |     netadmin:<empty> - Valid credentials |     guest:<empty> - Valid credentials |     user:<empty> - Valid credentials |     web:<empty> - Valid credentials |     sysadmin:<empty> - Valid credentials |     administrator:<empty> - Valid credentials |     webadmin:<empty> - Valid credentials |     admin:<empty> - Valid credentials |     test:<empty> - Valid credentials |_  Statistics: Performed 10 guesses in 1 seconds, average tps: 10.0 | mysql-info:  |   Protocol: 10 |   Version: 8.0.26-0ubuntu0.20.04.1 |   Thread ID: 13 |   Capabilities flags: 65535 |   Some Capabilities: SupportsLoadDataLocal, SupportsTransactions, Speaks41ProtocolOld, LongPassword, DontAllowDatabaseTableColumn, Support41Auth, IgnoreSigpipes, SwitchToSSLAfterHandshake, FoundRows, InteractiveClient, Speaks41ProtocolNew, ConnectWithDatabase, IgnoreSpaceBeforeParenthesis, LongColumnFlag, SupportsCompression, ODBCClient, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults |   Status: Autocommit |   Salt: YTSgMfqvx\x0F\x7F\x16\&\x1EAeK>0 |_  Auth Plugin Name: caching_sha2_password |_mysql-users: ERROR: Script execution failed (use -d to debug) |_mysql-variables: ERROR: Script execution failed (use -d to debug) |_mysql-vuln-cve2012-2122: ERROR: Script execution failed (use -d to debug) MAC Address: 00:00:00:00:00:00 (VMware) Service detection performed. Please report any incorrect results at https://nmap.org/submit/ . Nmap done: 1 IP address (1 host up) scanned in 11.21 seconds`

Как и при всех наших сканированиях, мы должны быть осторожны с результатами и вручную подтверждать полученную информацию, поскольку некоторые данные могут оказаться ложноположительными. Приведенное выше сканирование — отличный тому пример, поскольку мы точно знаем, что целевой сервер MySQL использует для пользователя не пустой пароль `root`, а фиксированный. Мы можем проверить это с помощью следующей команды:

#### Взаимодействие с сервером MySQL

        shellsession
`MuraMitchi@htb[/htb]$ mysql -u root -h 10.129.14.132 ERROR 1045 (28000): Access denied for user 'root'@'10.129.14.1' (using password: NO)`

Например, если мы используем пароль, который угадали или нашли в ходе своих поисков, мы сможем войти на сервер MySQL и выполнить некоторые команды.

        shellsession
`MuraMitchi@htb[/htb]$ mysql -u root -pP4SSw0rd -h 10.129.14.128 Welcome to the MariaDB monitor.  Commands end with ; or \g. Your MySQL connection id is 150165 Server version: 8.0.27-0ubuntu0.20.04.1 (Ubuntu)                                                          Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.                                      Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.                                   MySQL [(none)]> show databases;                                                                           +--------------------+ | Database           | +--------------------+ | information_schema | | mysql              | | performance_schema | | sys                | +--------------------+ 4 rows in set (0.006 sec) MySQL [(none)]> select version(); +-------------------------+ | version()               | +-------------------------+ | 8.0.27-0ubuntu0.20.04.1 | +-------------------------+ 1 row in set (0.001 sec) MySQL [(none)]> use mysql; MySQL [mysql]> show tables; +------------------------------------------------------+ | Tables_in_mysql                                      | +------------------------------------------------------+ | columns_priv                                         | | component                                            | | db                                                   | | default_roles                                        | | engine_cost                                          | | func                                                 | | general_log                                          | | global_grants                                        | | gtid_executed                                        | | help_category                                        | | help_keyword                                         | | help_relation                                        | | help_topic                                           | | innodb_index_stats                                   | | innodb_table_stats                                   | | password_history                                     | ...SNIP... | user                                                 | +------------------------------------------------------+ 37 rows in set (0.002 sec)`

Если мы посмотрим на существующие базы данных, то увидим, что несколько из них уже есть. Наиболее важными базами данных для сервера MySQL являются `system schema`( `sys`) и `information schema`( `information_schema`). Схема системы содержит таблицы, информацию и метаданные, необходимые для управления. Более подробную информацию об этой базе данных можно найти в [справочном руководстве](https://dev.mysql.com/doc/refman/8.0/en/system-schema.html#:~:text=The%20mysql%20schema%20is%20the,used%20for%20other%20operational%20purposes) MySQL.

        shellsession
`mysql> use sys; mysql> show tables;   +-----------------------------------------------+ | Tables_in_sys                                 | +-----------------------------------------------+ | host_summary                                  | | host_summary_by_file_io                       | | host_summary_by_file_io_type                  | | host_summary_by_stages                        | | host_summary_by_statement_latency             | | host_summary_by_statement_type                | | innodb_buffer_stats_by_schema                 | | innodb_buffer_stats_by_table                  | | innodb_lock_waits                             | | io_by_thread_by_latency                       | ...SNIP... | x$waits_global_by_latency                     | +-----------------------------------------------+ mysql> select host, unique_users from host_summary; +-------------+--------------+                    | host        | unique_users |                    +-------------+--------------+                    | 10.129.14.1 |            1 |                    | localhost   |            2 |                    +-------------+--------------+                    2 rows in set (0,01 sec)`

Существует `information schema`также база данных, содержащая метаданные. Однако эти метаданные в основном извлекаются из `system schema`базы данных. Причина существования этих двух баз данных — установленный стандарт ANSI/ISO. `System schema`— это системный каталог Microsoft для серверов SQL и содержит гораздо больше информации, чем `information schema`.

Ниже в таблице описаны некоторые команды, которые следует запомнить и записать для работы с базами данных MySQL.

|**Командование**|**Описание**|
|---|---|
|`mysql -u <user> -p<password> -h <IP address>`|Подключитесь к серверу MySQL. Между флагом '-p' и паролем **не должно быть пробела.**|
|`show databases;`|Показать все базы данных.|
|`use <database>;`|Выберите одну из существующих баз данных.|
|`show tables;`|Отобразить все доступные таблицы в выбранной базе данных.|
|`show columns from <table>;`|Отобразить все столбцы в выбранной таблице.|
|`select * from <table>;`|Отобразите все данные в нужной таблице.|
|`select * from <table> where <column> = "<string>";`|Найдите нужный параметр `string`в требуемой таблице.|

Нам необходимо знать, как взаимодействовать с различными базами данных. Поэтому мы рекомендуем установить и настроить сервер MySQL на одной из наших виртуальных машин для экспериментов. В справочном руководстве также есть раздел, подробно описывающий [вопросы безопасности](https://dev.mysql.com/doc/refman/8.0/en/general-security-issues.html) , в котором рассматриваются лучшие практики защиты серверов MySQL. Его следует использовать при настройке нашего сервера MySQL, чтобы лучше понимать, почему что-то может работать некорректно.