# SMTP (Simple Mail Transfer Protocol)

`Simple Mail Transfer Protocol` (`SMTP`) — это протокол для отправки электронных писем в IP-сети. Он используется между почтовым клиентом и сервером исходящей почты или между двумя SMTP-серверами. SMTP часто комбинируется с протоколами `IMAP` или `POP3`, которые позволяют получать почту. Это протокол типа «клиент-сервер», где сервер при пересылке письма другому серверу сам выступает в роли клиента.

---

### 🚀 Quick Commands

#### OS Terminal
```bash
# Сканирование SMTP с определением версии и запуском стандартных скриптов
sudo nmap <TARGET_IP> -sC -sV -p25

# Проверка SMTP-сервера на наличие открытого ретранслятора (Open Relay)
sudo nmap <TARGET_IP> -p25 --script smtp-open-relay -v

# Подключение к SMTP-серверу через telnet
telnet <TARGET_IP> 25
```

#### Service Interaction
```bash
# Инициализация сессии (HELO или EHLO для расширенного SMTP)
HELO <DOMAIN>
EHLO <DOMAIN>

# Проверка существования пользователя в системе
VRFY <USER>

# Указание отправителя
MAIL FROM: <cry0l1t3@inlanefreight.htb>

# Указание получателя
RCPT TO: <mrb3n@inlanefreight.htb>

# Переход в режим ввода данных письма
DATA

# Завершение сессии
QUIT
```

---

### 🛠 Tools & Utilities

* `[[Telnet]]` — используется для ручного взаимодействия с SMTP и отладки команд.
* `[[Nmap]]` — основной инструмент для перечисления команд (`smtp-commands`) и поиска уязвимостей (`smtp-open-relay`).
* `[[Postfix]]` — популярный агент передачи почты (MTA), конфигурацию которого часто приходится анализировать.
* `[[Netcat]]` — альтернатива telnet для взаимодействия с портом.

---

### 📝 Technical Analysis & Media

#### Порты и Службы
| Порт | Протокол | Назначение |
| :--- | :--- | :--- |
| **25** | TCP | Стандартный порт для передачи почты между серверами (Control). |
| **587** | TCP | Прием почты от аутентифицированных пользователей (Submission) с использованием STARTTLS. |
| **465** | TCP | SMTP через SSL/TLS (SMTPS) для защищенной передачи. |

> [!info] Теория и Принципы работы
> Протокол SMTP работает по цепочке передачи сообщения от отправителя к получателю:
> 1. **MUA (Mail User Agent):** Почтовый клиент (Thunderbird, Outlook), который формирует письмо.
> 2. **MSA (Mail Submission Agent):** Принимает письмо от MUA, проверяет его валидность. Часто называется **Relay**.
> 3. **MTA (Mail Transfer Agent):** Программная основа (например, Postfix). Проверяет размер, спам и ищет в DNS IP-адрес сервера получателя.
> 4. **MDA (Mail Delivery Agent):** Доставляет письмо в конкретный почтовый ящик на целевом сервере.
> 
> | Клиент (`MUA`) | `➞` | Агент подачи (`MSA`) | `➞` | Реле/Передача (`MTA`) | `➞` | Агент доставки (`MDA`) | `➞` | Ящик (`POP3/IMAP`) |
> | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |

#### Конфигурация Postfix
Ниже представлен типичный файл конфигурации `/etc/postfix/main.cf` без комментариев:

> [!example] /etc/postfix/main.cf
> ```shellsession
> smtpd_banner = ESMTP Server  
> biff = no 
> append_dot_mydomain = no 
> readme_directory = no 
> compatibility_level = 2 
> smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache 
> myhostname = mail1.inlanefreight.htb 
> alias_maps = hash:/etc/aliases 
> alias_database = hash:/etc/aliases 
> smtp_generic_maps = hash:/etc/postfix/generic 
> mydestination = $myhostname, localhost  
> masquerade_domains = $myhostname 
> mynetworks = 127.0.0.0/8 10.129.0.0/16 
> mailbox_size_limit = 0 
> recipient_delimiter = + 
> smtp_bind_address = 0.0.0.0 
> inet_protocols = ipv4 
> smtpd_helo_restrictions = reject_invalid_hostname 
> home_mailbox = /home/postfix
> ```

#### Взаимодействие через Telnet
Пример перечисления команд и пользователей:

> [!example] Лог сессии - EHLO и VRFY
> ```shellsession
> MuraMitchi@htb[/htb]$ telnet 10.129.14.128 25
> Trying 10.129.14.128...
> Connected to 10.129.14.128.
> Escape character is '^]'.
> 220 ESMTP Server
> 
> EHLO mail1
> 250-mail1.inlanefreight.htb
> 250-PIPELINING
> 250-SIZE 10240000
> 250-ETRN
> 250-ENHANCEDSTATUSCODES
> 250-8BITMIME
> 250-DSN
> 250-SMTPUTF8
> 250 CHUNKING
> 
> VRFY root
> 252 2.0.0 root
> VRFY cry0l1t3
> 252 2.0.0 cry0l1t3
> VRFY testuser
> 252 2.0.0 testuser
> VRFY aaaaaaaaaaaaaaaaaaaaaaaaaaaa
> 252 2.0.0 aaaaaaaaaaaaaaaaaaaaaaaaaaaa
> ```

Пример ручной отправки письма:

> [!example] Лог сессии - Отправка письма
> ```shellsession
> MuraMitchi@htb[/htb]$ telnet 10.129.14.128 25
> Trying 10.129.14.128...
> Connected to 10.129.14.128.
> Escape character is '^]'.
> 220 ESMTP Server
> EHLO inlanefreight.htb
> 250-mail1.inlanefreight.htb
> 250-PIPELINING
> ...
> MAIL FROM: <cry0l1t3@inlanefreight.htb>
> 250 2.1.0 Ok
> RCPT TO: <mrb3n@inlanefreight.htb> NOTIFY=success,failure
> 250 2.1.5 Ok
> DATA
> 354 End data with <CR><LF>.<CR><LF>
> From: <cry0l1t3@inlanefreight.htb>
> To: <mrb3n@inlanefreight.htb>
> Subject: DB
> Date: Tue, 28 Sept 2021 16:32:51 +0200
> 
> Hey man, I am trying to access our XY-DB but the creds don't work.
> Did you make any changes there?
> .
> 250 2.0.0 Ok: queued as 6E1CF1681AB
> QUIT
> 221 2.0.0 Bye
> Connection closed by foreign host.
> ```

---

### 🔍 Command Breakdown (Deep Dive)

| Команда | Описание | Зачем это для CPTS |
| :--- | :--- | :--- |
| `AUTH PLAIN` | Расширение для аутентификации клиента. | Поиск векторов брутфорса или перехвата учетных данных. |
| `HELO` / `EHLO` | Начало сессии, идентификация клиента. | Сбор баннеров и поддерживаемых функций (STARTTLS, SIZE). |
| `MAIL FROM` | Указание отправителя. | Проверка возможности подмены отправителя (Spoofing). |
| `RCPT TO` | Указание получателя. | Определение валидных email-адресов внутри организации. |
| `DATA` | Начало передачи тела письма. | Эксфильтрация данных или отправка фишинговых сообщений. |
| `VRFY` | Проверка существования почтового ящика. | **Перечисление пользователей (User Enumeration).** |
| `EXPN` | Раскрытие списков рассылки. | Расширение списка целей для атаки. |
| `RSET` | Сброс текущей транзакции. | Очистка состояния без разрыва соединения. |

---

### ⚠️ Vulnerable Configurations & Flaws (Critical)

> [!warning] Критические недостатки и уязвимости
> 1. **Clear-text Protocol:** По умолчанию SMTP передает данные (включая учетные данные через `AUTH PLAIN`) в открытом виде. Это позволяет проводить атаки **MITM** и сниффинг трафика.
> 2. **Open Relay (Открытый ретранслятор):** Если параметр конфигурации `mynetworks = 0.0.0.0/0` настроен неверно, любой желающий может использовать сервер для рассылки спама или фишинга от имени организации.
> 3. **Отсутствие аутентификации отправителя:** SMTP позволяет указывать произвольный адрес в `MAIL FROM`, что ведет к атакам **Mail Spoofing**, если не настроены SPF/DKIM.
> 4. **User Enumeration:** Команды `VRFY` и `EXPN` позволяют атакующему подтвердить наличие пользователей в системе, что является критическим этапом сбора информации.

---

### 💡 Pentest Insights

> [!tip] Хакерские хитрости
> * **Баннер-граббинг:** Всегда проверяйте приветствие сервера. Оно может раскрыть версию ПО (например, `Postfix`) и ОС.
> * **Обход VRFY:** Если `VRFY` выдает код `252` для любых имен (даже случайных), сервер настроен на ложные ответы. В этом случае попробуйте использовать `RCPT TO` для проверки валидности адреса.
> * **Open Relay Test:** Используйте скрипт Nmap `smtp-open-relay`. Он проводит 16 различных тестов, пытаясь переслать почту через целевой сервер на внешние домены (например, `nmap.scanme.org`).
> * **Web Proxy Tunneling:** Если прямой доступ к 25 порту закрыт, но есть доступ к веб-прокси, попробуйте команду `CONNECT <TARGET_IP>:25 HTTP/1.0`.

---

### 🔗 Connections
* [[Footprinting]]
* [[Network Protocols]]
* [[Information Disclosure]]
* [[Clear-Text Protocols]]
* [[Mail Spoofing]]
* [[User Enumeration]]