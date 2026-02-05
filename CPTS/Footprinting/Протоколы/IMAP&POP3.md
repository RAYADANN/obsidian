### 1. 🚀 Quick Commands

#### OS Terminal
```bash
# Сканирование стандартных портов POP3 и IMAP с определением версий и запуском скриптов
sudo nmap <TARGET_IP> -sV -p110,143,993,995 -sC

# Взаимодействие с IMAP через cURL (использование учетных данных)
curl -k 'imaps://<TARGET_IP>' --user <USER>:<PASS>

# Взаимодействие с IMAP через cURL с подробным выводом (Banner Grabbing/SSL Info)
curl -k 'imaps://<TARGET_IP>' --user <USER>:<PASS> -v

# Подключение к POP3 через защищенное соединение TLS/SSL
openssl s_client -connect <TARGET_IP>:995

# Подключение к IMAP через защищенное соединение TLS/SSL
openssl s_client -connect <TARGET_IP>:993
```

#### Service Interaction (IMAP)
```bash
# Аутентификация в IMAP
1 LOGIN <USER> <PASS>

# Просмотр списка всех папок/каталогов
1 LIST "" *

# Выбор конкретного почтового ящика (например, INBOX)
1 SELECT INBOX

# Извлечение данных сообщения по его ID
1 FETCH <ID> all

# Завершение сессии
1 LOGOUT
```

#### Service Interaction (POP3)
```bash
# Идентификация пользователя
USER <USER>

# Ввод пароля
PASS <PASS>

# Просмотр количества и размера писем
STAT

# Вывод списка сообщений
LIST

# Получение содержимого письма по ID
RETR <ID>

# Завершение сессии
QUIT
```

---

### 2. 🛠 Tools & Utilities

- `[[Nmap]]` — сетевой сканер для обнаружения открытых портов и определения версий служб.
- `[[cURL]]` — инструмент командной строки для передачи данных, поддерживающий протоколы IMAP/POP3.
- `[[OpenSSL]]` — утилита для работы с TLS/SSL, используемая для подключения к зашифрованным сервисам.
- `[[Netcat]]` (ncat) — "швейцарский армейский нож" для сетевых соединений.
- `[[Dovecot]]` — популярный open-source сервер IMAP и POP3 для Linux.

---

### 3. 📝 Technical Analysis & Media

#### Порты и Службы
| Порт | Протокол | Служба | Роль |
| :--- | :--- | :--- | :--- |
| **110** | TCP | POP3 | Стандартный порт (передача в открытом виде) |
| **143** | TCP | IMAP | Стандартный порт (передача в открытом виде) |
| **993** | TCP | IMAPS | IMAP через SSL/TLS (зашифрованный) |
| **995** | TCP | POP3S | POP3 через SSL/TLS (зашифрованный) |

> [!info] IMAP vs POP3
> **IMAP (Internet Message Access Protocol)** — позволяет управлять почтой непосредственно на сервере. Поддерживает синхронизацию между несколькими клиентами, структуру папок и онлайн-режим. Письма остаются на сервере, пока не будут удалены.
> **POP3 (Post Office Protocol)** — предназначен для загрузки писем на локальный клиент. Обладает ограниченным функционалом (просмотр, извлечение, удаление) и не поддерживает сложную иерархию папок на стороне сервера.

#### Конфигурация Dovecot
Для экспериментов с конфигурацией рекомендуется установка пакетов `dovecot-imapd` и `dovecot-pop3d`. Основные настройки хранятся в файлах конфигурации Dovecot.

#### Логи и Вывод терминала

> [!example] Результаты сканирования Nmap
> ```shellsession
> MuraMitchi@htb[/htb]$ sudo nmap 10.129.14.128 -sV -p110,143,993,995 -sC Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 22:09 CEST Nmap scan report for 10.129.14.128 Host is up (0.00026s latency). PORT    STATE SERVICE  VERSION 110/tcp open  pop3     Dovecot pop3d |_pop3-capabilities: AUTH-RESP-CODE SASL STLS TOP UIDL RESP-CODES CAPA PIPELINING | ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US | Not valid before: 2021-09-19T19:44:58 |_Not valid after:  2295-07-04T19:44:58 143/tcp open  imap     Dovecot imapd |_imap-capabilities: more have post-login STARTTLS Pre-login capabilities LITERAL+ LOGIN-REFERRALS OK LOGINDISABLEDA0001 SASL-IR ENABLE listed IDLE ID IMAP4rev1 | ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US | Not valid before: 2021-09-19T19:44:58 |_Not valid after:  2295-07-04T19:44:58 993/tcp open  ssl/imap Dovecot imapd |_imap-capabilities: more have post-login OK capabilities LITERAL+ LOGIN-REFERRALS Pre-login AUTH=PLAINA0001 SASL-IR ENABLE listed IDLE ID IMAP4rev1 | ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US | Not valid before: 2021-09-19T19:44:58 |_Not valid after:  2295-07-04T19:44:58 995/tcp open  ssl/pop3 Dovecot pop3d |_pop3-capabilities: AUTH-RESP-CODE USER SASL(PLAIN) TOP UIDL RESP-CODES CAPA PIPELINING | ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US | Not valid before: 2021-09-19T19:44:58 |_Not valid after:  2295-07-04T19:44:58 MAC Address: 00:00:00:00:00:00 (VMware) Service detection performed. Please report any incorrect results at https://nmap.org/submit/ . Nmap done: 1 IP address (1 host up) scanned in 12.74 seconds
> ```

> [!example] Взаимодействие через OpenSSL (POP3S)
> ```shellsession
> MuraMitchi@htb[/htb]$ openssl s_client -connect 10.129.14.128:pop3s CONNECTED(00000003) Can't use SSL_get_servername depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb verify error:num=18:self signed certificate verify return:1 depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb verify return:1 --- Certificate chain  0 s:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb ...SNIP... --- read R BLOCK --- Post-Handshake New Session Ticket arrived: SSL-Session:     Protocol  : TLSv1.3    Cipher    : TLS_AES_256_GCM_SHA384    Session-ID: 3CC39A7F2928B252EF2FFA5462140B1A0A74B29D4708AA8DE1515BB4033D92C2    Session-ID-ctx:    Resumption PSK: 68419D933B5FEBD878FF1BA399A926813BEA3652555E05F0EC75D65819A263AA25FA672F8974C37F6446446BB7EA83F9    PSK identity: None    PSK identity hint: None    SRP username: None    TLS session ticket lifetime hint: 7200 (seconds)    TLS session ticket:    0000 - d7 86 ac 7e f3 f4 95 35-88 40 a5 b5 d6 a6 41 e4   ...~...5.@....A.    0010 - 96 6c e6 12 4f 50 ce 72-36 25 df e1 72 d9 23 94   .l..OP.r6%..r.#.    0020 - cc 29 90 08 58 1b 57 ab-db a8 6b f7 8f 31 5b ad   .)..X.W...k..1[.    0030 - 47 94 f4 67 58 1f 96 d9-ca ca 56 f9 7a 12 f6 6d   G..gX.....V.z..m    0040 - 43 b9 b6 68 de db b2 47-4f 9f 48 14 40 45 8f 89   C..h...GO.H.@E..    0050 - fa 19 35 9c 6d 3c a1 46-5ca2 65 ab 87 a4 fd 5e   ..5.m<.F\.e....^    0060 - a2 95 25 d4 43 b8 71 70-40 6c fe 6f 0e d1 a0 38   ..%.C.qp@l.o...8    0070 - 6e bd 73 91 ed 05 89 83-f5 3e d9 2a e0 2e 96 f8   n.s......>.*....    0080 - 99 f0 50 15 e0 1b 66 db-7c 9f 10 80 4a a1 8b 24   ..P...f.|...J..$    0090 - bb 00 03 d4 93 2b d9 95-64 44 5b c2 6b 2e 01 b5   .....+..dD[.k...    00a0 - e8 1b f4 a4 98 a7 7a 7d-0a 80 cc 0a ad fe 6e b3   ......z}......n.    00b0 - 0a d6 50 5d fd 9a b4 5c-28 a4 c9 36 e4 7d 2a 1e   ..P]...\(..6.}*.     Start Time: 1632081313    Timeout   : 7200 (sec)    Verify return code: 18 (self signed certificate)    Extended master secret: no    Max Early Data: 0 --- read R BLOCK +OK HTB-Academy POP3 Server
> ```

---

### 4. 🔍 Command Breakdown (Deep Dive)

#### IMAP Commands
| Команда | Описание | Зачем это для CPTS |
| :--- | :--- | :--- |
| `1 LOGIN user pass` | Вход пользователя. | Получение доступа к почтовому ящику при наличии кредов. |
| `1 LIST "" *` | Список всех каталогов. | Перечисление структуры папок для поиска интересных данных (например, "Drafts", "Sent"). |
| `1 CREATE "INBOX"` | Создает почтовый ящик. | Возможность манипуляции структурой ящика. |
| `1 DELETE "INBOX"` | Удаляет почтовый ящик. | Деструктивное воздействие на данные. |
| `1 SELECT INBOX` | Выбор почтового ящика. | Необходимый этап перед чтением сообщений. |
| `1 FETCH <ID> all` | Извлечение данных сообщения. | Чтение содержимого писем для сбора конфиденциальной информации. |

#### POP3 Commands
| Команда | Описание | Зачем это для CPTS |
| :--- | :--- | :--- |
| `USER username` | Идентификация пользователя. | Первый этап аутентификации. |
| `PASS password` | Аутентификация. | Получение доступа к письмам. |
| `STAT` | Кол-во писем. | Оценка объема доступной информации. |
| `LIST` | Номера и размеры писем. | Перечисление доступных объектов для загрузки. |
| `RETR id` | Доставка письма по ID. | Эксфильтрация содержимого конкретных сообщений. |
| `CAPA` | Возможности сервера. | Banner grabbing и определение поддерживаемых методов аутентификации. |

---

### 5. ⚠️ Vulnerable Configurations & Flaws (Critical)

> [!warning] Опасные настройки и недостатки
> 1. **Clear-text Protocols:** По умолчанию IMAP (143) и POP3 (110) передают данные (включая пароли) в открытом виде. Это позволяет злоумышленнику в той же сети выполнить **MITM атаку** и перехватить учетные данные.
> 2. **Dangerous Parameters (Dovecot):**
>    - `auth_debug`: Включает отладку аутентификации, что может привести к утечке деталей процесса в логи.
>    - `auth_debug_passwords`: **Критическая уязвимость конфигурации**. Пароли записываются в логи сервера.
>    - `auth_anonymous_username`: Позволяет настроить анонимный вход через механизм SASL, аналогично анонимному FTP.
> 3. **Information Disclosure:** SSL-сертификаты на портах 993/995 часто содержат внутренние имена хостов (FQDN), названия организаций и email-адреса администраторов, что полезно для фазы Reconnaissance.

---

### 6. 💡 Pentest Insights

> [!tip] Хакерские хитрости
> - **OSINT через SSL:** Всегда проверяйте поле `Subject` в SSL-сертификате. Там часто указаны поддомены (например, `mail1.inlanefreight.htb`), которые могут отсутствовать в публичном DNS.
> - **Banner Grabbing:** Используйте `curl -v` или `openssl s_client`, чтобы увидеть баннеры. Они часто раскрывают точную версию ПО (например, `Dovecot v2.3.x`), что позволяет искать публичные эксплойты.
> - **Recursive Search:** Если получен доступ к IMAP, проверяйте папку `Sent` (Отправленные) и `Drafts` (Черновики). В них часто забывают пароли, ключи API и другую чувствительную информацию.

---

### 7. 🔗 Connections

- [[Footprinting]]
- [[Information Disclosure]]
- [[Clear-Text Protocols]]
- [[Network Protocols]]
- [[Post-Exploitation Basics]]
- [[Enumeration]]
- [[SSL-TLS Analysis]]