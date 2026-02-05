С помощью протокола POP3 `Internet Message Access Protocol`( `IMAP`) возможен доступ к электронной почте с почтового сервера. В отличие от протокола POP3 `Post Office Protocol`( `POP3`), IMAP позволяет управлять электронной почтой в режиме онлайн непосредственно на сервере и поддерживает структуру папок. Таким образом, это сетевой протокол для онлайн-управления электронной почтой на удаленном сервере. Протокол основан на архитектуре клиент-сервер и позволяет синхронизировать локальный почтовый клиент с почтовым ящиком на сервере, предоставляя своего рода сетевую файловую систему для электронной почты и обеспечивая беспроблемную синхронизацию между несколькими независимыми клиентами. POP3, с другой стороны, не обладает той же функциональностью, что и IMAP, и предоставляет только функции просмотра, извлечения и удаления писем на почтовом сервере. Поэтому для дополнительных функций, таких как иерархические почтовые ящики непосредственно на почтовом сервере, доступ к нескольким почтовым ящикам в течение одной сессии и предварительный выбор писем, необходимо использовать протоколы, подобные IMAP.

Клиенты получают доступ к этим структурам онлайн и могут создавать локальные копии. Это позволяет создавать единую базу данных даже для нескольких клиентов. Электронные письма остаются на сервере до тех пор, пока не будут удалены. IMAP — это текстовый протокол с расширенными функциями, такими как просмотр писем непосредственно на сервере. Также возможно одновременное подключение нескольких пользователей к почтовому серверу. Без активного подключения к серверу управление электронной почтой невозможно. Однако некоторые клиенты предлагают автономный режим с локальной копией почтового ящика. При восстановлении соединения клиент синхронизирует все изменения, внесенные в автономном режиме.

Клиент устанавливает соединение с сервером через порт `143`. Для связи он использует текстовые команды в `ASCII`формате. Несколько команд могут быть отправлены последовательно, не дожидаясь подтверждения от сервера. Последующие подтверждения от сервера могут быть присвоены отдельным командам с использованием идентификаторов, отправленных вместе с командами. Сразу после установления соединения пользователь проходит аутентификацию на сервере с помощью имени пользователя и пароля. Доступ к нужному почтовому ящику возможен только после успешной аутентификации.

Протокол SMTP обычно используется для отправки электронных писем. Копируя отправленные письма в папку IMAP, все клиенты получают доступ ко всем отправленным письмам, независимо от компьютера, с которого они были отправлены. Еще одно преимущество протокола SMTP — создание личных папок и структур папок в почтовом ящике. Эта функция делает почтовый ящик более понятным и простым в управлении. Однако при этом увеличивается потребность в объеме памяти на почтовом сервере.

Без дополнительных мер протокол IMAP работает в незашифрованном виде и передает команды, электронные письма, а также имена пользователей и пароли в открытом текстовом формате. Многие почтовые серверы требуют установления зашифрованной IMAP-сессии для обеспечения большей безопасности почтового трафика и предотвращения несанкционированного доступа к почтовым ящикам. Для этой цели обычно используется SSL/TLS. В зависимости от используемого метода и реализации, зашифрованное соединение использует стандартный порт `143`или альтернативный порт, например `993`, .

---

## Конфигурация по умолчанию

Протоколы IMAP и POP3 имеют большое количество параметров конфигурации, что затрудняет детальное изучение каждого компонента. Если вы хотите подробнее изучить конфигурации этих протоколов, мы рекомендуем создать локальную виртуальную машину, установить два пакета `dovecot-imapd`, `dovecot-pop3d`использовать `apt`их и поэкспериментировать с настройками.

В документации Dovecot можно найти отдельные [основные настройки](https://doc.dovecot.org/2.4.1/core/summaries/settings.html) и параметры [конфигурации служб](https://doc.dovecot.org/2.4.1/core/config/service.html) , которые можно использовать для наших экспериментов. Однако давайте посмотрим на список команд и выясним, как можно напрямую взаимодействовать и обмениваться данными с IMAP и POP3, используя командную строку.

#### Команды IMAP

|**Командование**|**Описание**|
|---|---|
|`1 LOGIN username password`|Вход пользователя.|
|`1 LIST "" *`|Выводит список всех каталогов.|
|`1 CREATE "INBOX"`|Создает почтовый ящик с указанным именем.|
|`1 DELETE "INBOX"`|Удаляет почтовый ящик.|
|`1 RENAME "ToRead" "Important"`|Переименовывает почтовый ящик.|
|`1 LSUB "" *`|Возвращает подмножество имен из набора имен, которые Пользователь указал `active`как `subscribed`.|
|`1 SELECT INBOX`|Выбирает почтовый ящик, чтобы обеспечить доступ к сообщениям в этом ящике.|
|`1 UNSELECT INBOX`|Выходит из выбранного почтового ящика.|
|`1 FETCH <ID> all`|Извлекает данные, связанные с сообщением в почтовом ящике.|
|`1 CLOSE`|Удаляет все сообщения с `Deleted`установленным флагом.|
|`1 LOGOUT`|Закрывает соединение с IMAP-сервером.|

#### Команды POP3

|**Командование**|**Описание**|
|---|---|
|`USER username`|Идентифицирует пользователя.|
|`PASS password`|Аутентификация пользователя с использованием его пароля.|
|`STAT`|Запрашивает у сервера количество сохраненных электронных писем.|
|`LIST`|Сервер запрашивает у него количество и размер всех электронных писем.|
|`RETR id`|Запрашивает у сервера доставку запрошенного электронного письма по идентификатору.|
|`DELE id`|Запрашивает у сервера удаление запрошенного адреса электронной почты по идентификатору.|
|`CAPA`|Запрашивает у сервера отображение его возможностей.|
|`RSET`|Запрашивает у сервера сброс переданной информации.|
|`QUIT`|Закрывает соединение с POP3-сервером.|

---

## Опасные условия

Тем не менее, неправильно настроенные параметры конфигурации могут позволить нам получить дополнительную информацию, например, отладить выполняемые команды в службе или войти в систему анонимно, как в случае с FTP-сервисом. Большинство компаний используют сторонних поставщиков услуг электронной почты, таких как Google, Microsoft и многие другие. Однако некоторые компании по-прежнему используют собственные почтовые серверы по разным причинам. Одна из таких причин — сохранение конфиденциальности, которую они хотят обеспечить. Администраторы могут допускать множество ошибок в настройке, что в худшем случае позволит нам прочитать все отправленные и полученные электронные письма, которые могут даже содержать конфиденциальную или важную информацию. Некоторые из таких параметров конфигурации включают:

|**Параметр**|**Описание**|
|---|---|
|`auth_debug`|Включает все отладочные записи аутентификации.|
|`auth_debug_passwords`|Этот параметр регулирует уровень детализации логов, количество отправленных паролей и схему, которая будет регистрироваться.|
|`auth_verbose`|В журнале регистрируются неудачные попытки аутентификации и их причины.|
|`auth_verbose_passwords`|Пароли, используемые для аутентификации, записываются в журнал и могут быть усечены.|
|`auth_anonymous_username`|Это определяет имя пользователя, которое будет использоваться при входе в систему с помощью механизма ANONYMOUS SASL.|

---

## Создание базы данных для сервиса

По умолчанию порты `110`и `995`используются для POP3, а порты `143`и `993`— для IMAP. Порты более высокого уровня ( `993`и `995`) используют TLS/SSL для шифрования связи между клиентом и сервером. С помощью Nmap мы можем просканировать сервер на наличие этих портов. Сканирование вернет соответствующую информацию (как показано ниже), если сервер использует встроенный сертификат.

#### Nmap

        shellsession
`MuraMitchi@htb[/htb]$ sudo nmap 10.129.14.128 -sV -p110,143,993,995 -sC Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 22:09 CEST Nmap scan report for 10.129.14.128 Host is up (0.00026s latency). PORT    STATE SERVICE  VERSION 110/tcp open  pop3     Dovecot pop3d |_pop3-capabilities: AUTH-RESP-CODE SASL STLS TOP UIDL RESP-CODES CAPA PIPELINING | ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US | Not valid before: 2021-09-19T19:44:58 |_Not valid after:  2295-07-04T19:44:58 143/tcp open  imap     Dovecot imapd |_imap-capabilities: more have post-login STARTTLS Pre-login capabilities LITERAL+ LOGIN-REFERRALS OK LOGINDISABLEDA0001 SASL-IR ENABLE listed IDLE ID IMAP4rev1 | ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US | Not valid before: 2021-09-19T19:44:58 |_Not valid after:  2295-07-04T19:44:58 993/tcp open  ssl/imap Dovecot imapd |_imap-capabilities: more have post-login OK capabilities LITERAL+ LOGIN-REFERRALS Pre-login AUTH=PLAINA0001 SASL-IR ENABLE listed IDLE ID IMAP4rev1 | ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US | Not valid before: 2021-09-19T19:44:58 |_Not valid after:  2295-07-04T19:44:58 995/tcp open  ssl/pop3 Dovecot pop3d |_pop3-capabilities: AUTH-RESP-CODE USER SASL(PLAIN) TOP UIDL RESP-CODES CAPA PIPELINING | ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US | Not valid before: 2021-09-19T19:44:58 |_Not valid after:  2295-07-04T19:44:58 MAC Address: 00:00:00:00:00:00 (VMware) Service detection performed. Please report any incorrect results at https://nmap.org/submit/ . Nmap done: 1 IP address (1 host up) scanned in 12.74 seconds`

Например, из выходных данных видно, что общее имя — `mail1.inlanefreight.htb`, а почтовый сервер принадлежит организации `Inlanefreight`, расположенной в Калифорнии. Отображаемые возможности показывают команды, доступные на сервере и для службы на соответствующем порту.

Если нам удастся выяснить учетные данные доступа одного из сотрудников, злоумышленник сможет войти на почтовый сервер и прочитать или даже отправить отдельные сообщения.

#### cURL

        shellsession
`MuraMitchi@htb[/htb]$ curl -k 'imaps://10.129.14.128' --user user:p4ssw0rd * LIST (\HasNoChildren) "." Important * LIST (\HasNoChildren) "." INBOX`

Если мы также воспользуемся опцией `verbose`( `-v`), мы увидим, как устанавливается соединение. Из этого мы можем увидеть версию TLS, используемую для шифрования, дополнительные сведения о SSL-сертификате и даже баннер, который часто будет содержать версию почтового сервера.

        shellsession
`MuraMitchi@htb[/htb]$ curl -k 'imaps://10.129.14.128' --user cry0l1t3:1234 -v *   Trying 10.129.14.128:993... * TCP_NODELAY set * Connected to 10.129.14.128 (10.129.14.128) port 993 (#0) * successfully set certificate verify locations: *   CAfile: /etc/ssl/certs/ca-certificates.crt   CApath: /etc/ssl/certs * TLSv1.3 (OUT), TLS handshake, Client hello (1): * TLSv1.3 (IN), TLS handshake, Server hello (2): * TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8): * TLSv1.3 (IN), TLS handshake, Certificate (11): * TLSv1.3 (IN), TLS handshake, CERT verify (15): * TLSv1.3 (IN), TLS handshake, Finished (20): * TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1): * TLSv1.3 (OUT), TLS handshake, Finished (20): * SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384 * Server certificate: *  subject: C=US; ST=California; L=Sacramento; O=Inlanefreight; OU=Customer Support; CN=mail1.inlanefreight.htb; emailAddress=cry0l1t3@inlanefreight.htb *  start date: Sep 19 19:44:58 2021 GMT *  expire date: Jul  4 19:44:58 2295 GMT *  issuer: C=US; ST=California; L=Sacramento; O=Inlanefreight; OU=Customer Support; CN=mail1.inlanefreight.htb; emailAddress=cry0l1t3@inlanefreight.htb *  SSL certificate verify result: self signed certificate (18), continuing anyway. * TLSv1.3 (IN), TLS handshake, Newsession Ticket (4): * TLSv1.3 (IN), TLS handshake, Newsession Ticket (4): * old SSL session ID is stale, removing < * OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN] HTB-Academy IMAP4 v.0.21.4 > A001 CAPABILITY < * CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN < A001 OK Pre-login capabilities listed, post-login capabilities have more. > A002 AUTHENTICATE PLAIN AGNyeTBsMXQzADEyMzQ= < * CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND URL-PARTIAL CATENATE UNSELECT CHILDREN NAMESPACE UIDPLUS LIST-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS BINARY MOVE SNIPPET=FUZZY PREVIEW=FUZZY LITERAL+ NOTIFY SPECIAL-USE < A002 OK Logged in > A003 LIST "" * < * LIST (\HasNoChildren) "." Important * LIST (\HasNoChildren) "." Important < * LIST (\HasNoChildren) "." INBOX * LIST (\HasNoChildren) "." INBOX < A003 OK List completed (0.001 + 0.000 secs). * Connection #0 to host 10.129.14.128 left intact`

Для взаимодействия с IMAP или POP3-сервером по SSL можно использовать `openssl`, а также `ncat`. Команды для этого будут выглядеть следующим образом:

#### OpenSSL - TLS-шифрованное взаимодействие POP3

        shellsession
`MuraMitchi@htb[/htb]$ openssl s_client -connect 10.129.14.128:pop3s CONNECTED(00000003) Can't use SSL_get_servername depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb verify error:num=18:self signed certificate verify return:1 depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb verify return:1 --- Certificate chain  0 s:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb ...SNIP... --- read R BLOCK --- Post-Handshake New Session Ticket arrived: SSL-Session:     Protocol  : TLSv1.3    Cipher    : TLS_AES_256_GCM_SHA384    Session-ID: 3CC39A7F2928B252EF2FFA5462140B1A0A74B29D4708AA8DE1515BB4033D92C2    Session-ID-ctx:    Resumption PSK: 68419D933B5FEBD878FF1BA399A926813BEA3652555E05F0EC75D65819A263AA25FA672F8974C37F6446446BB7EA83F9    PSK identity: None    PSK identity hint: None    SRP username: None    TLS session ticket lifetime hint: 7200 (seconds)    TLS session ticket:    0000 - d7 86 ac 7e f3 f4 95 35-88 40 a5 b5 d6 a6 41 e4   ...~...5.@....A.    0010 - 96 6c e6 12 4f 50 ce 72-36 25 df e1 72 d9 23 94   .l..OP.r6%..r.#.    0020 - cc 29 90 08 58 1b 57 ab-db a8 6b f7 8f 31 5b ad   .)..X.W...k..1[.    0030 - 47 94 f4 67 58 1f 96 d9-ca ca 56 f9 7a 12 f6 6d   G..gX.....V.z..m    0040 - 43 b9 b6 68 de db b2 47-4f 9f 48 14 40 45 8f 89   C..h...GO.H.@E..    0050 - fa 19 35 9c 6d 3c a1 46-5c a2 65 ab 87 a4 fd 5e   ..5.m<.F\.e....^    0060 - a2 95 25 d4 43 b8 71 70-40 6c fe 6f 0e d1 a0 38   ..%.C.qp@l.o...8    0070 - 6e bd 73 91 ed 05 89 83-f5 3e d9 2a e0 2e 96 f8   n.s......>.*....    0080 - 99 f0 50 15 e0 1b 66 db-7c 9f 10 80 4a a1 8b 24   ..P...f.|...J..$    0090 - bb 00 03 d4 93 2b d9 95-64 44 5b c2 6b 2e 01 b5   .....+..dD[.k...    00a0 - e8 1b f4 a4 98 a7 7a 7d-0a 80 cc 0a ad fe 6e b3   ......z}......n.    00b0 - 0a d6 50 5d fd 9a b4 5c-28 a4 c9 36 e4 7d 2a 1e   ..P]...\(..6.}*.     Start Time: 1632081313    Timeout   : 7200 (sec)    Verify return code: 18 (self signed certificate)    Extended master secret: no    Max Early Data: 0 --- read R BLOCK +OK HTB-Academy POP3 Server`

#### OpenSSL — зашифрованное взаимодействие по протоколу TLS IMAP

        shellsession
``MuraMitchi@htb[/htb]$ openssl s_client -connect 10.129.14.128:imaps CONNECTED(00000003) Can't use SSL_get_servername depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb verify error:num=18:self signed certificate verify return:1 depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb verify return:1 --- Certificate chain  0 s:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb ...SNIP... --- read R BLOCK --- Post-Handshake New Session Ticket arrived: SSL-Session:     Protocol  : TLSv1.3    Cipher    : TLS_AES_256_GCM_SHA384    Session-ID: 2B7148CD1B7B92BA123E06E22831FCD3B365A5EA06B2CDEF1A5F397177130699    Session-ID-ctx:    Resumption PSK: 4D9F082C6660646C39135F9996DDA2C199C4F7E75D65FA5303F4A0B274D78CC5BD3416C8AF50B31A34EC022B619CC633    PSK identity: None    PSK identity hint: None    SRP username: None    TLS session ticket lifetime hint: 7200 (seconds)    TLS session ticket:    0000 - 68 3b b6 68 ff 85 95 7c-8a 8a 16 b2 97 1c 72 24   h;.h...|......r$    0010 - 62 a7 84 ff c3 24 ab 99-de 45 60 26 e7 04 4a 7d   b....$...E`&..J}    0020 - bc 6e 06 a0 ff f7 d7 41-b5 1b 49 9c 9f 36 40 8d   .n.....A..I..6@.    0030 - 93 35 ed d9 eb 1f 14 d7-a5 f6 3f c8 52 fb 9f 29   .5........?.R..)    0040 - 89 8d de e6 46 95 b3 32-48 80 19 bc 46 36 cb eb   ....F..2H...F6..    0050 - 35 79 54 4c 57 f8 ee 55-06 e3 59 7f 5e 64 85 b0   5yTLW..U..Y.^d..    0060 - f3 a4 8c a6 b6 47 e4 59-ee c9 ab 54 a4 ab 8c 01   .....G.Y...T....    0070 - 56 bb b9 bb 3b f6 96 74-16 c9 66 e2 6c 28 c6 12   V...;..t..f.l(..    0080 - 34 c7 63 6b ff 71 16 7f-91 69 dc 38 7a 47 46 ec   4.ck.q...i.8zGF.    0090 - 67 b7 a2 90 8b 31 58 a0-4f 57 30 6a b6 2e 3a 21   g....1X.OW0j..:!    00a0 - 54 c7 ba f0 a9 74 13 11-d5 d1 ec cc ea f9 54 7d   T....t........T}    00b0 - 46 a6 33 ed 5d 24 ed b0-20 63 43 d8 8f 14 4d 62   F.3.]$.. cC...Mb     Start Time: 1632081604    Timeout   : 7200 (sec)    Verify return code: 18 (self signed certificate)    Extended master secret: no    Max Early Data: 0 --- read R BLOCK * OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN] HTB-Academy IMAP4 v.0.21.4``

После успешного установления соединения и входа на целевой почтовый сервер, мы можем использовать приведенные выше команды для работы с сервером и навигации по нему. Мы хотим отметить, что настройка нашего собственного почтового сервера, исследования в этой области и эксперименты, которые мы можем проводить совместно с другими членами сообщества, дадут нам необходимые знания для понимания происходящего обмена данными и того, какие параметры конфигурации за это отвечают.

В разделе SMTP мы обнаружили пользователя `robin`. Другой член нашей команды смог выяснить, что пользователь также использует свое имя пользователя в качестве пароля ( `robin`: `robin`). Мы можем использовать эти учетные данные и попытаться взаимодействовать с сервисами IMAP/POP3.