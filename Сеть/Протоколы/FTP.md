Протокол FTP `File Transfer Protocol`(или `FTP`FTP) — один из старейших протоколов в интернете. FTP работает на прикладном уровне стека протоколов TCP/IP. Таким образом, он находится на том же уровне, что и `HTTP`другие протоколы `POP`. Эти протоколы также работают с поддержкой браузеров или почтовых клиентов для предоставления своих услуг. Существуют также специальные FTP-программы для протокола передачи файлов.

Представим, что мы хотим загрузить локальные файлы на сервер и скачать другие файлы, используя протокол [FTP](https://datatracker.ietf.org/doc/html/rfc959) . В FTP-соединении открываются два канала. Сначала клиент и сервер устанавливают управляющий канал через `TCP port 21`. Клиент отправляет команды серверу, а сервер возвращает коды состояния. Затем оба участника связи могут установить канал передачи данных через `TCP port 20`. Этот канал используется исключительно для передачи данных, и протокол отслеживает ошибки в процессе передачи. Если соединение обрывается во время передачи, передача может быть возобновлена ​​после восстановления контакта.

Различают два варианта : FTP `active`и `passive`TCP. В активном варианте клиент устанавливает соединение, как описано, через TCP-порт 21 и, таким образом, сообщает серверу, через какой клиентский порт сервер может передавать свои ответы. Однако, если брандмауэр защищает клиента, сервер не может ответить, поскольку все внешние соединения блокируются. Для этой цели `passive mode`был разработан протокол FTP. В этом случае сервер объявляет порт, через который клиент может установить канал передачи данных. Поскольку клиент инициирует соединение этим методом, брандмауэр не блокирует передачу.

FTP-сервер поддерживает различные [команды](https://web.archive.org/web/20230326204635/https://www.smartfile.com/blog/the-ultimate-ftp-commands-list/) и коды состояния. Не все из этих команд последовательно выполняются на сервере. Например, клиентская сторона дает серверу команду на загрузку или скачивание файлов, организацию каталогов или удаление файлов. В каждом случае сервер отвечает кодом состояния, указывающим на успешность выполнения команды. Список возможных кодов состояния можно найти [здесь](https://en.wikipedia.org/wiki/List_of_FTP_server_return_codes) .

Обычно для использования FTP на сервере требуются учетные данные. Также необходимо знать, что FTP — это `clear-text`протокол, который иногда может быть перехвачен, если условия в сети благоприятны. Однако существует также возможность, что сервер предоставляет доступ к FTP `anonymous FTP`. В этом случае оператор сервера позволяет любому пользователю загружать или скачивать файлы через FTP без использования пароля. Поскольку с такими общедоступными FTP-серверами связаны риски безопасности, возможности пользователей обычно ограничены.

---

## TFTP

`Trivial File Transfer Protocol`TFTP `TFTP`проще, чем FTP, и осуществляет передачу файлов между клиентским и серверным процессами. Однако он `does not`обеспечивает аутентификацию пользователей и другие ценные функции, поддерживаемые FTP. Кроме того, в то время как FTP использует TCP, TFTP использует UDP `UDP`, что делает его ненадежным протоколом и заставляет его использовать восстановление на прикладном уровне с поддержкой UDP.

Это отражается, например, в том, что TFTP, в отличие от FTP, не требует аутентификации пользователя. Он не поддерживает защищенный вход по паролям и устанавливает ограничения на доступ, основываясь исключительно на правах чтения и записи файла в операционной системе. На практике это приводит к тому, что TFTP работает исключительно в каталогах и с файлами, которые были предоставлены всем пользователям и могут читаться и записываться глобально. Из-за отсутствия безопасности TFTP, в отличие от FTP, может использоваться только в локальных и защищенных сетях.

Рассмотрим несколько команд `TFTP`:

|**Команды**|**Описание**|
|---|---|
|`connect`|Задает удаленный хост и, при необходимости, порт для передачи файлов.|
|`get`|Передает файл или набор файлов с удаленного хоста на локальный хост.|
|`put`|Передает файл или набор файлов с локального хоста на удаленный хост.|
|`quit`|Завершение работы TFTP.|
|`status`|Отображает текущее состояние TFTP, включая текущий режим передачи (ASCII или двоичный), состояние соединения, значение тайм-аута и т. д.|
|`verbose`|Включает или выключает подробный режим, отображающий дополнительную информацию во время передачи файла.|

В отличие от FTP-клиента, `TFTP`не имеет функции отображения списка каталогов.

---

## Конфигурация по умолчанию

Одним из наиболее часто используемых FTP-серверов в дистрибутивах Linux является [vsFTPd](https://security.appspot.com/vsftpd.html) . Конфигурацию vsFTPd по умолчанию можно найти в файле `.config.js` `/etc/vsftpd.conf`, и некоторые параметры уже предопределены по умолчанию. Настоятельно рекомендуется установить сервер vsFTPd на виртуальную машину и внимательно изучить эту конфигурацию.

#### Установите vsFTPd

        shellsession
`MuraMitchi@htb[/htb]$ sudo apt install vsftpd`

Сервер vsFTPd — лишь один из немногих доступных нам FTP-серверов. Существует множество альтернатив, которые, помимо прочего, предлагают гораздо больше функций и параметров конфигурации. Мы будем использовать сервер vsFTPd, поскольку это отличный способ наглядно и понятно продемонстрировать возможности конфигурации FTP-сервера, не вдаваясь в подробности справочных страниц (man pages). Если мы посмотрим на конфигурационный файл vsFTPd, мы увидим множество закомментированных или закомментированных параметров и настроек. Однако конфигурационный файл не содержит всех возможных настроек. Существующие и отсутствующие параметры можно найти в справочной [странице (man page)](http://vsftpd.beasts.org/vsftpd_conf.html) .

#### Файл конфигурации vsFTPd

        shellsession
`MuraMitchi@htb[/htb]$ cat /etc/vsftpd.conf | grep -v "#"`

|**Параметр**|**Описание**|
|---|---|
|`listen=NO`|Запускать из inetd или как отдельный демон?|
|`listen_ipv6=YES`|Слушать через IPv6?|
|`anonymous_enable=NO`|Разрешить анонимный доступ?|
|`local_enable=YES`|Разрешить вход локальным пользователям?|
|`dirmessage_enable=YES`|Отображать сообщения Active Directory при переходе пользователей в определенные каталоги?|
|`use_localtime=YES`|Использовать местное время?|
|`xferlog_enable=YES`|Включить регистрацию загрузок/скачиваний?|
|`connect_from_port_20=YES`|Подключиться через порт 20?|
|`secure_chroot_dir=/var/run/vsftpd/empty`|Название пустой директории|
|`pam_service_name=vsftpd`|Эта строка содержит имя службы PAM, которую будет использовать vsftpd.|
|`rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem`|Последние три параметра указывают местоположение сертификата RSA, используемого для SSL-шифрованных соединений.|
|`rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key`||
|`ssl_enable=NO`||

Кроме того, существует файл с именем `/etc/ftpusers`, на который также следует обратить внимание, поскольку он используется для запрета доступа к FTP-сервису определенным пользователям. В следующем примере пользователям `guest`, `john`, и `kevin`не разрешается входить в FTP-сервис, даже если они существуют в системе Linux.

#### FTPUSERS

        shellsession
`MuraMitchi@htb[/htb]$ cat /etc/ftpusers guest john kevin`

---

## Опасные условия

На каждом FTP-сервере можно установить множество различных настроек безопасности. Они могут иметь разные цели, например, проверку соединений через брандмауэры, проверку маршрутов и механизмов аутентификации. Один из таких механизмов аутентификации — это аутентификация пользователя `anonymous`. Это часто используется для того, чтобы все пользователи внутренней сети могли обмениваться файлами и данными, не имея доступа к компьютерам друг друга. В vsFTPd [дополнительные параметры](http://vsftpd.beasts.org/vsftpd_conf.html) , которые можно добавить в конфигурационный файл для анонимного входа, выглядят следующим образом:

|**Параметр**|**Описание**|
|---|---|
|`anonymous_enable=YES`|Разрешить анонимный вход?|
|`anon_upload_enable=YES`|Разрешить анонимным пользователям загружать файлы?|
|`anon_mkdir_write_enable=YES`|Разрешить анонимным пользователям создавать новые каталоги?|
|`no_anon_password=YES`|Не запрашивать пароль у анонимных пользователей?|
|`anon_root=/home/username/ftp`|Справочник для анонимных пользователей.|
|`write_enable=YES`|Разрешить использование команд FTP: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE и SITE?|

С помощью стандартного FTP-клиента ( `ftp`) мы можем получить доступ к FTP-серверу и войти в систему под анонимным пользователем, если были использованы указанные выше настройки. Использование анонимной учетной записи может происходить во внутренних средах и инфраструктурах, где все участники известны. Доступ к этому типу сервиса может быть установлен временно или с помощью настройки для ускорения обмена файлами.

Как только мы подключаемся к серверу vsFTPd, `response code 220`отображается баннер FTP-сервера. Часто этот баннер содержит описание `service`и даже `version`его содержимое. Он также указывает на тип системы, используемой FTP-сервером. Одна из наиболее распространенных конфигураций FTP-серверов — разрешение `anonymous`доступа, которое не требует действительных учетных данных, но предоставляет доступ к некоторым файлам. Даже если мы не можем их загрузить, иногда достаточно просто перечислить содержимое, чтобы сгенерировать дополнительные идеи и записать информацию, которая поможет нам в другом подходе.

#### Анонимный вход

        shellsession
`MuraMitchi@htb[/htb]$ ftp 10.129.14.136 Connected to 10.129.14.136. 220 "Welcome to the HTB Academy vsFTP service." Name (10.129.14.136:cry0l1t3): anonymous 230 Login successful. Remote system type is UNIX. Using binary mode to transfer files. ftp> ls 200 PORT command successful. Consider using PASV. 150 Here comes the directory listing. -rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Clients drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees -rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt 226 Directory send OK.`

Однако, чтобы получить первое представление о настройках сервера, можно использовать следующую команду:

#### Статус vsFTPd

        shellsession
`ftp> status Connected to 10.129.14.136. No proxy connection. Connecting using address family: any. Mode: stream; Type: binary; Form: non-print; Structure: file Verbose: on; Bell: off; Prompting: on; Globbing: on Store unique: off; Receive unique: off Case: off; CR stripping: on Quote control characters: on Ntrans: off Nmap: off Hash mark printing: off; Use of PORT cmds: on Tick counter printing: off`

Некоторые команды следует использовать лишь изредка, поскольку они позволят серверу отобразить больше информации, которую мы сможем использовать в своих целях. К таким командам относятся `debug`и `trace`.

#### Подробный вывод vsFTPd

        shellsession
`ftp> debug Debugging on (debug=1). ftp> trace Packet tracing on. ftp> ls ---> PORT 10,10,14,4,188,195 200 PORT command successful. Consider using PASV. ---> LIST 150 Here comes the directory listing. -rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx drwxrwxr-x    2 1002     1002         4096 Sep 14 17:03 Clients drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees -rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt 226 Directory send OK.`

|**Параметр**|**Описание**|
|---|---|
|`dirmessage_enable=YES`|Отображать сообщение при первом входе в новую директорию?|
|`chown_uploads=YES`|Изменить владельца анонимно загруженных файлов?|
|`chown_username=username`|Пользователь, которому предоставлено право собственности на анонимно загруженные файлы.|
|`local_enable=YES`|Разрешить вход в систему локальным пользователям?|
|`chroot_local_user=YES`|Добавить локальных пользователей в их домашние каталоги?|
|`chroot_list_enable=YES`|Использовать список локальных пользователей, которые будут помещены в их домашний каталог?|

|**Параметр**|**Описание**|
|---|---|
|`hide_ids=YES`|Вся информация о пользователях и группах в списках каталогов будет отображаться как "ftp".|
|`ls_recurse_enable=YES`|Позволяет использовать рекурсивные списки.|

В следующем примере мы видим, что если эта `hide_ids=YES`настройка присутствует, представление UID и GUID сервиса будет перезаписано, что затруднит определение того, с какими правами были записаны и загружены эти файлы.

#### Скрытие идентификационных данных - ДА

        shellsession
`ftp> ls ---> TYPE A 200 Switching to ASCII mode. ftp: setsockopt (ignored): Permission denied ---> PORT 10,10,14,4,223,101 200 PORT command successful. Consider using PASV. ---> LIST 150 Here comes the directory listing. -rw-rw-r--    1 ftp     ftp      8138592 Sep 14 16:54 Calender.pptx drwxrwxr-x    2 ftp     ftp         4096 Sep 14 17:03 Clients drwxrwxr-x    2 ftp     ftp         4096 Sep 14 16:50 Documents drwxrwxr-x    2 ftp     ftp         4096 Sep 14 16:50 Employees -rw-rw-r--    1 ftp     ftp           41 Sep 14 16:45 Important Notes.txt -rw-------    1 ftp     ftp            0 Sep 15 14:57 testupload.txt 226 Directory send OK.`

Эта настройка является функцией безопасности, предотвращающей раскрытие локальных имен пользователей. Теоретически, используя имена пользователей, мы могли бы атаковать такие сервисы, как FTP, SSH и многие другие, методом перебора паролей. Однако на практике решения [fail2ban](https://en.wikipedia.org/wiki/Fail2ban) теперь являются стандартной реализацией любой инфраструктуры, которая регистрирует IP-адрес и блокирует весь доступ к инфраструктуре после определенного количества неудачных попыток входа.

Ещё одна полезная настройка, которую мы можем использовать в наших целях, — это параметр ` `ls_recurse_enable=YES`.`. Его часто устанавливают на сервере vsFTPd для лучшего обзора структуры каталогов FTP, поскольку он позволяет одновременно видеть всё видимое содержимое.

#### Рекурсивный листинг

        shellsession
`ftp> ls -R ---> PORT 10,10,14,4,222,149 200 PORT command successful. Consider using PASV. ---> LIST -R 150 Here comes the directory listing. .: -rw-rw-r--    1 ftp      ftp      8138592 Sep 14 16:54 Calender.pptx drwxrwxr-x    2 ftp      ftp         4096 Sep 14 17:03 Clients drwxrwxr-x    2 ftp      ftp         4096 Sep 14 16:50 Documents drwxrwxr-x    2 ftp      ftp         4096 Sep 14 16:50 Employees -rw-rw-r--    1 ftp      ftp           41 Sep 14 16:45 Important Notes.txt -rw-------    1 ftp      ftp            0 Sep 15 14:57 testupload.txt ./Clients: drwx------    2 ftp      ftp          4096 Sep 16 18:04 HackTheBox drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:00 Inlanefreight ./Clients/HackTheBox: -rw-r--r--    1 ftp      ftp         34872 Sep 16 18:04 appointments.xlsx -rw-r--r--    1 ftp      ftp        498123 Sep 16 18:04 contract.docx -rw-r--r--    1 ftp      ftp        478237 Sep 16 18:04 contract.pdf -rw-r--r--    1 ftp      ftp           348 Sep 16 18:04 meetings.txt ./Clients/Inlanefreight: -rw-r--r--    1 ftp      ftp         14211 Sep 16 18:00 appointments.xlsx -rw-r--r--    1 ftp      ftp         37882 Sep 16 17:58 contract.docx -rw-r--r--    1 ftp      ftp            89 Sep 16 17:58 meetings.txt -rw-r--r--    1 ftp      ftp        483293 Sep 16 17:59 proposal.pptx ./Documents: -rw-r--r--    1 ftp      ftp         23211 Sep 16 18:05 appointments-template.xlsx -rw-r--r--    1 ftp      ftp         32521 Sep 16 18:05 contract-template.docx -rw-r--r--    1 ftp      ftp        453312 Sep 16 18:05 contract-template.pdf ./Employees: 226 Directory send OK.`

`Downloading`Одной из главных особенностей является работа с файлами, полученными с такого FTP-сервера, а также `uploading`с файлами, созданными нами. Это позволяет нам, например, использовать уязвимости LFI для того, чтобы заставить хост выполнять системные команды. Помимо файлов, мы можем просматривать, скачивать и анализировать их. Атаки также возможны с использованием FTP-журналов, что приводит к `Remote Command Execution`( `RCE`). Это относится к FTP-сервисам и всем тем, которые мы можем обнаружить на этапе перечисления.

#### Скачать файл

        shellsession
`ftp> ls 200 PORT command successful. Consider using PASV. 150 Here comes the directory listing. -rwxrwxrwx    1 ftp      ftp             0 Sep 16 17:24 Calendar.pptx drwxrwxrwx    4 ftp      ftp          4096 Sep 16 17:57 Clients drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:05 Documents drwxrwxrwx    2 ftp      ftp          4096 Sep 16 17:24 Employees -rwxrwxrwx    1 ftp      ftp            41 Sep 18 15:58 Important Notes.txt 226 Directory send OK. ftp> get Important\ Notes.txt local: Important Notes.txt remote: Important Notes.txt 200 PORT command successful. Consider using PASV. 150 Opening BINARY mode data connection for Important Notes.txt (41 bytes). 226 Transfer complete. 41 bytes received in 0.00 secs (606.6525 kB/s) ftp> exit 221 Goodbye.`

        shellsession
`MuraMitchi@htb[/htb]$ ls | grep Notes.txt 'Important Notes.txt'`

Мы также можем загрузить все доступные нам файлы и папки одновременно. Это особенно полезно, если на FTP-сервере находится множество различных файлов в большой структуре папок. Однако это может вызвать опасения, поскольку никто из сотрудников компании обычно не хочет загружать все файлы и контент сразу.

#### Скачать все доступные файлы

        shellsession
`MuraMitchi@htb[/htb]$ wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136 --2021-09-19 14:45:58--  ftp://anonymous:*password*@10.129.14.136/                                                     => ‘10.129.14.136/.listing’                                                                      Connecting to 10.129.14.136:21... connected.                                                                Logging in as anonymous ... Logged in! ==> SYST ... done.    ==> PWD ... done. ==> TYPE I ... done.  ==> CWD not needed. ==> PORT ... done.    ==> LIST ... done.                                                                  12.12.1.136/.listing           [ <=>                                  ]     466  --.-KB/s    in 0s                                                                                                                  2021-09-19 14:45:58 (65,8 MB/s) - ‘10.129.14.136/.listing’ saved [466]                                      --2021-09-19 14:45:58--  ftp://anonymous:*password*@10.129.14.136/Calendar.pptx               => ‘10.129.14.136/Calendar.pptx’                                        ==> CWD not required.                                                            ==> SIZE Calendar.pptx ... done.                                                                                                                             ==> PORT ... done.    ==> RETR Calendar.pptx ... done.        ...SNIP... 2021-09-19 14:45:58 (48,3 MB/s) - ‘10.129.14.136/Employees/.listing’ saved [119] FINISHED --2021-09-19 14:45:58-- Total wall clock time: 0,03s Downloaded: 15 files, 1,7K in 0,001s (3,02 MB/s)`

После загрузки всех файлов мы `wget`создадим каталог с именем, соответствующим IP-адресу целевого объекта. Все загруженные файлы будут храниться там, и мы сможем просмотреть их локально.

        shellsession
`MuraMitchi@htb[/htb]$ tree . . └── 10.129.14.136     ├── Calendar.pptx    ├── Clients    │   └── Inlanefreight    │       ├── appointments.xlsx    │       ├── contract.docx    │       ├── meetings.txt    │       └── proposal.pptx    ├── Documents    │   ├── appointments-template.xlsx    │   ├── contract-template.docx    │   └── contract-template.pdf    ├── Employees    └── Important Notes.txt 5 directories, 9 files`

Далее мы можем проверить, есть ли у нас разрешения на загрузку файлов на FTP-сервер. Особенно на веб-серверах часто происходит синхронизация файлов, и разработчики имеют к ним быстрый доступ. FTP часто используется для этой цели, и чаще всего ошибки конфигурации обнаруживаются на серверах, которые администраторы считают необнаружимыми. Убеждение в том, что внутренние сетевые компоненты недоступны извне, приводит к тому, что защита внутренних систем часто игнорируется, что влечет за собой ошибки конфигурации.

Возможность загружать файлы на FTP-сервер, подключенный к веб-серверу, увеличивает вероятность получения прямого доступа к веб-серверу и даже к обратной оболочке, которая позволяет выполнять внутренние системные команды и, возможно, даже повышать свои привилегии.

#### Загрузить файл

        shellsession
`MuraMitchi@htb[/htb]$ touch testupload.txt`

С помощью этой `PUT`команды мы можем загружать файлы из текущей папки на FTP-сервер.

        shellsession
`ftp> put testupload.txt  local: testupload.txt remote: testupload.txt ---> PORT 10,10,14,4,184,33 200 PORT command successful. Consider using PASV. ---> STOR testupload.txt 150 Ok to send data. 226 Transfer complete. ftp> ls ---> TYPE A 200 Switching to ASCII mode. ---> PORT 10,10,14,4,223,101 200 PORT command successful. Consider using PASV. ---> LIST 150 Here comes the directory listing. -rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx drwxrwxr-x    2 1002     1002         4096 Sep 14 17:03 Clients drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees -rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt -rw-------    1 1002     133             0 Sep 15 14:57 testupload.txt 226 Directory send OK.`

---

## Создание базы данных для сервиса

Сбор информации о сети с помощью различных сетевых сканеров также является удобным и широко распространенным подходом. Эти инструменты упрощают идентификацию различных служб, даже если они недоступны на стандартных портах. Одним из наиболее широко используемых инструментов для этой цели является Nmap. Nmap также включает в себя [Nmap Scripting Engine](https://nmap.org/book/nse.html) ( `NSE`NSE), набор множества различных скриптов, написанных для конкретных служб. Более подробную информацию о возможностях Nmap и NSE можно найти в модуле [«Перечисление сети с помощью Nmap»](https://academy.hackthebox.com/course/preview/network-enumeration-with-nmap) . Мы можем обновить эту базу данных скриптов NSE с помощью показанной команды.

#### Скрипты Nmap для FTP

        shellsession
`MuraMitchi@htb[/htb]$ sudo nmap --script-updatedb Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 13:49 CEST NSE: Updating rule database. NSE: Script Database updated successfully. Nmap done: 0 IP addresses (0 hosts up) scanned in 0.28 seconds`

Все скрипты NSE находятся в Pwnbox `/usr/share/nmap/scripts/`, но на наших системах мы можем найти их с помощью простой команды.

        shellsession
`MuraMitchi@htb[/htb]$ find / -type f -name ftp* 2>/dev/null | grep scripts /usr/share/nmap/scripts/ftp-syst.nse /usr/share/nmap/scripts/ftp-vsftpd-backdoor.nse /usr/share/nmap/scripts/ftp-vuln-cve2010-4221.nse /usr/share/nmap/scripts/ftp-proftpd-backdoor.nse /usr/share/nmap/scripts/ftp-bounce.nse /usr/share/nmap/scripts/ftp-libopie.nse /usr/share/nmap/scripts/ftp-anon.nse /usr/share/nmap/scripts/ftp-brute.nse`

Как нам уже известно, FTP-сервер обычно работает на стандартном TCP-порту 21, который мы можем просканировать с помощью Nmap. Мы также используем сканирование версий ( `-sV`), агрессивное сканирование ( `-A`) и сканирование скриптов по умолчанию ( `-sC`) для нашей цели `10.129.14.136`.

#### Nmap

        shellsession
`MuraMitchi@htb[/htb]$ sudo nmap -sV -p21 -sC -A 10.129.14.136 Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-16 18:12 CEST Nmap scan report for 10.129.14.136 Host is up (0.00013s latency). PORT   STATE SERVICE VERSION 21/tcp open  ftp     vsftpd 2.0.8 or later | ftp-anon: Anonymous FTP login allowed (FTP code 230) | -rwxrwxrwx    1 ftp      ftp       8138592 Sep 16 17:24 Calendar.pptx [NSE: writeable] | drwxrwxrwx    4 ftp      ftp          4096 Sep 16 17:57 Clients [NSE: writeable] | drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:05 Documents [NSE: writeable] | drwxrwxrwx    2 ftp      ftp          4096 Sep 16 17:24 Employees [NSE: writeable] | -rwxrwxrwx    1 ftp      ftp            41 Sep 16 17:24 Important Notes.txt [NSE: writeable] |_-rwxrwxrwx    1 ftp      ftp             0 Sep 15 14:57 testupload.txt [NSE: writeable] | ftp-syst:  |   STAT:  | FTP server status: |      Connected to 10.10.14.4 |      Logged in as ftp |      TYPE: ASCII |      No session bandwidth limit |      Session timeout in seconds is 300 |      Control connection is plain text |      Data connections will be plain text |      At session startup, client count was 2 |      vsFTPd 3.0.3 - secure, fast, stable |_End of status`

Сканирование скриптов по умолчанию основано на отпечатках сервисов, ответах и ​​стандартных портах. После обнаружения сервиса Nmap выполняет отмеченные скрипты один за другим, предоставляя различную информацию. Например, скрипт [ftp-anon](https://nmap.org/nsedoc/scripts/ftp-anon.html) NSE проверяет, разрешает ли FTP-сервер анонимный доступ. Если да, то содержимое корневого каталога FTP отображается для анонимного пользователя.

`ftp-syst`Например, команда выполняет команду `STAT`, которая отображает информацию о состоянии FTP-сервера. Это включает в себя конфигурации, а также версию FTP-сервера. Nmap также предоставляет возможность отслеживать ход выполнения скриптов NSE на сетевом уровне, если мы используем соответствующую опцию `--script-trace`в наших сканированиях. Это позволяет нам видеть, какие команды отправляет Nmap, какие порты используются и какие ответы мы получаем от сканируемого сервера.

#### Nmap Script Trace

        shellsession
`MuraMitchi@htb[/htb]$ sudo nmap -sV -p21 -sC -A 10.129.14.136 --script-trace Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 13:54 CEST                                                                                                                                                    NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 8 [10.129.14.136:21]                                    NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 16 [10.129.14.136:21]              NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 24 [10.129.14.136:21] NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 32 [10.129.14.136:21] NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #1 [10.129.14.136:21] (timeout: 7000ms) EID 42 NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #2 [10.129.14.136:21] (timeout: 9000ms) EID 50 NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #3 [10.129.14.136:21] (timeout: 7000ms) EID 58 NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #4 [10.129.14.136:21] (timeout: 11000ms) EID 66 NSE: TCP 10.10.14.4:54226 > 10.129.14.136:21 | CONNECT NSE: TCP 10.10.14.4:54228 > 10.129.14.136:21 | CONNECT NSE: TCP 10.10.14.4:54230 > 10.129.14.136:21 | CONNECT NSE: TCP 10.10.14.4:54232 > 10.129.14.136:21 | CONNECT NSOCK INFO [11.4660s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 50 [10.129.14.136:21] (41 bytes): 220 Welcome to HTB-Academy FTP service... NSOCK INFO [11.4660s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 58 [10.129.14.136:21] (41 bytes): 220 Welcome to HTB-Academy FTP service... NSE: TCP 10.10.14.4:54228 < 10.129.14.136:21 | 220 Welcome to HTB-Academy FTP service.`

История сканирования показывает, что к сервису выполняются четыре параллельных сканирования с различными таймаутами. Для скриптов NSE мы видим, что наша локальная машина использует другие выходные порты ( `54226`, `54228`, `54230`, `54232`) и сначала инициирует соединение с помощью `CONNECT`команды. Из первого ответа от сервера видно, что мы получаем баннер от сервера для нашего второго скрипта NSE ( `54228`) с целевого FTP-сервера. При необходимости мы, конечно, можем использовать другие приложения, такие как `netcat`или , `telnet`для взаимодействия с FTP-сервером.

#### Взаимодействие сервиса

        shellsession
`MuraMitchi@htb[/htb]$ nc -nv 10.129.14.136 21`

        shellsession
`MuraMitchi@htb[/htb]$ telnet 10.129.14.136 21`

Ситуация немного меняется, если FTP-сервер работает с шифрованием TLS/SSL. В этом случае нам нужен клиент, способный обрабатывать TLS/SSL. Для этого мы можем использовать клиент `openssl`и взаимодействовать с FTP-сервером. Преимущество использования клиента `openssl`заключается в том, что мы можем видеть SSL-сертификат, что также может быть полезно.

        shellsession
`MuraMitchi@htb[/htb]$ openssl s_client -connect 10.129.14.136:21 -starttls ftp CONNECTED(00000003)                                                                                       Can't use SSL_get_servername                         depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb verify error:num=18:self signed certificate verify return:1 depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb verify return:1 ---                                                  Certificate chain  0 s:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb   i:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb ---   Server certificate -----BEGIN CERTIFICATE----- MIIENTCCAx2gAwIBAgIUD+SlFZAWzX5yLs2q3ZcfdsRQqMYwDQYJKoZIhvcNAQEL ...SNIP...`

Это связано с тем, что SSL-сертификат позволяет нам распознавать `hostname`, например, и в большинстве случаев также идентификатор `email address`организации или компании. Кроме того, если у компании несколько филиалов по всему миру, сертификаты могут быть созданы и для конкретных местоположений, которые также могут быть идентифицированы с помощью SSL-сертификата.