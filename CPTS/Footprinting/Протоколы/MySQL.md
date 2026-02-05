### 🚀 Quick Commands

**OS Terminal**
```bash
# Сканирование порта MySQL и запуск скриптов перечисления Nmap
sudo nmap <TARGET_IP> -sV -sC -p3306 --script mysql*

# Удаленное подключение к серверу MySQL (ВАЖНО: нет пробела между -p и паролем)
mysql -u <USER> -p<PASS> -h <TARGET_IP>

# Попытка подключения без пароля (Anonymous Check)
mysql -u root -h <TARGET_IP>
```

**Service Interaction**
```sql
# Просмотр всех доступных баз данных
show databases;

# Проверка текущей версии сервера
select version();

# Переключение на использование конкретной базы данных
use <database>;

# Список всех таблиц в текущей базе данных
show tables;

# Просмотр структуры конкретной таблицы (столбцы и типы данных)
show columns from <table>;

# Извлечение всех данных из таблицы
select * from <table>;

# Поиск конкретной строки по значению в столбце
select * from <table> where <column> = "<string>";
```

---

### 🛠 Tools & Utilities

* `[[Nmap]]` — Использование специализированных NSE-скриптов (`mysql-brute`, `mysql-enum`, `mysql-empty-password`) для первичного аудита.
* `[[MySQL Client]]` — Основной консольный клиент для взаимодействия с базой данных.
* `[[MariaDB]]` — Популярный форк MySQL с открытым исходным кодом, часто встречающийся в современных Linux-дистрибутивах.
* `[[SQLmap]]` — Инструмент для автоматизации поиска и эксплуатации SQL-инъекций (упомянут как связанная тема).

---

### 📝 Technical Analysis & Media

#### Порты и Службы
| Порт | Протокол | Служба | Описание |
| :--- | :--- | :--- | :--- |
| 3306 | TCP | MySQL / MariaDB | Основной порт взаимодействия клиента и сервера БД |

> [!info] Теория и Принципы
> **MySQL** — это реляционная система управления базами данных (RDBMS), работающая по принципу `client-server`. Сервер отвечает за хранение и распределение данных в структурированных таблицах. 
> 
> В веб-среде MySQL является критическим компонентом стеков **LAMP** (Linux, Apache, MySQL, PHP) и **LEMP** (Nginx). В таких связках БД выступает «мозгом» приложения, храня:
> - Учетные данные (логины, хеши паролей).
> - Контент (заголовки, тексты, метатеги).
> - Права доступа и настройки администраторов.

#### Конфигурация
Файлы конфигурации (обычно `/etc/mysql/mysql.conf.d/mysqld.cnf`) содержат критические параметры безопасности.

> [!example] Содержимое конфигурационного файла по умолчанию
> ```shellsession
> [client]
> port        = 3306
> socket      = /var/run/mysqld/mysqld.sock
> 
> [mysqld_safe]
> pid-file    = /var/run/mysqld/mysqld.pid
> socket      = /var/run/mysqld/mysqld.sock
> nice        = 0
> 
> [mysqld]
> skip-host-cache
> skip-name-resolve
> user        = mysql
> pid-file    = /var/run/mysqld/mysqld.pid
> socket      = /var/run/mysqld/mysqld.sock
> port        = 3306
> basedir     = /usr
> datadir     = /var/lib/mysql
> tmpdir      = /tmp
> lc-messages-dir = /usr/share/mysql
> explicit_defaults_for_timestamp
> symbolic-links=0
> !includedir /etc/mysql/conf.d/
> ```

#### Логи и Вывод терминала
> [!example] Результат сканирования Nmap
> ```shellsession
> MuraMitchi@htb[/htb]$ sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*
> Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-21 00:53 CEST
> Nmap scan report for 10.129.14.128
> Host is up (0.00021s latency).
> 
> PORT     STATE SERVICE     VERSION
> 3306/tcp open  nagios-nsca Nagios NSCA
> | mysql-brute: 
> |   Accounts: 
> |     root:<empty> - Valid credentials
> |_  Statistics: Performed 45010 guesses in 5 seconds, average tps: 9002.0
> |_mysql-databases: ERROR: Script execution failed (use -d to debug)
> |_mysql-dump-hashes: ERROR: Script execution failed (use -d to debug)
> | mysql-empty-password: 
> |_  root account has empty password
> | mysql-enum: 
> |   Valid usernames: 
> |     root:<empty> - Valid credentials
> |     netadmin:<empty> - Valid credentials
> |     guest:<empty> - Valid credentials
> |     user:<empty> - Valid credentials
> |     web:<empty> - Valid credentials
> |     sysadmin:<empty> - Valid credentials
> |     administrator:<empty> - Valid credentials
> |     webadmin:<empty> - Valid credentials
> |     admin:<empty> - Valid credentials
> |     test:<empty> - Valid credentials
> |_  Statistics: Performed 10 guesses in 1 seconds, average tps: 10.0
> | mysql-info: 
> |   Protocol: 10
> |   Version: 8.0.26-0ubuntu0.20.04.1
> |   Thread ID: 13
> |   Capabilities flags: 65535
> |   Some Capabilities: SupportsLoadDataLocal, SupportsTransactions, Speaks41ProtocolOld, LongPassword, DontAllowDatabaseTableColumn, Support41Auth, IgnoreSigpipes, SwitchToSSLAfterHandshake, FoundRows, InteractiveClient, Speaks41ProtocolNew, ConnectWithDatabase, IgnoreSpaceBeforeParenthesis, LongColumnFlag, SupportsCompression, ODBCClient, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
> |   Status: Autocommit
> |   Salt: YTSgMfqvx\x0F\x7F\x16&\x1EAeK>0
> |_  Auth Plugin Name: caching_sha2_password
> |_mysql-users: ERROR: Script execution failed (use -d to debug)
> |_mysql-variables: ERROR: Script execution failed (use -d to debug)
> |_mysql-vuln-cve2012-2122: ERROR: Script execution failed (use -d to debug)
> MAC Address: 00:00:00:00:00:00 (VMware)
> ```

---

### 🔍 Command Breakdown (Deep Dive)

| Команда/Флаг | Описание | Зачем это для CPTS |
| :--- | :--- | :--- |
| `-u <user>` | Указывает имя пользователя для подключения. | Перебор стандартных имен (root, admin, guest). |
| `-p` | Запрашивает пароль или принимает его сразу после флага. | Использование найденных или дефолтных кредов. |
| `-h <IP>` | Указывает целевой хост. | Удаленное подключение к БД при открытом порту. |
| `--script mysql*` | Запуск всех NSE-скриптов для MySQL. | Автоматизация поиска пустых паролей и перечисления юзеров. |
| `show databases;` | Список БД на сервере. | Поиск нестандартных баз с чувствительными данными. |
| `select version();` | Вывод версии ПО. | Определение векторов эксплуатации под конкретную версию. |
| `use sys;` | Переход в системную схему. | Доступ к метаданным и информации о хостах/пользователях. |

---

### ⚠️ Vulnerable Configurations & Flaws (Critical)

> [!warning] Критические уязвимости настроек
> 1. **`user`, `password`, `admin_address`:** Эти параметры часто прописываются в конфигурационных файлах в открытом виде. Если у пентестера есть доступ на чтение файлов (через LFI или SSH), он может получить полный контроль над БД.
> 2. **`secure_file_priv`:** Если эта переменная пуста или настроена некорректно, атакующий может использовать SQL-инъекцию для чтения локальных файлов сервера или записи веб-шеллов.
> 3. **`debug` и `sql_warnings`:** Включение отладки приводит к подробному выводу ошибок в веб-приложении. Это облегчает проведение **Error-based SQL Injection**.
> 4. **Пустые пароли (Empty Passwords):** Часто учетные записи `root` или `test` не имеют пароля, что позволяет мгновенно получить доступ к данным через внешнюю сеть.
> 5. **Clear-text Protocol:** Протокол MySQL по умолчанию передает данные без шифрования, что делает его уязвимым к MITM-атакам и перехвату трафика.

---

### 💡 Pentest Insights

> [!tip] Хакерские хитрости
> - **Схема системных данных:** Всегда проверяйте базы `information_schema` и `sys`. В них хранятся метаданные о всей структуре сервера, включая информацию о подключениях (`host_summary`).
> - **Ложные срабатывания Nmap:** Nmap может сообщать о пустом пароле `root`, но при попытке входа сервер выдаст `Access denied`. Всегда проверяйте доступ вручную.
> - **SQL Injection:** Помните, что через SQL-инъекцию можно не только красть данные, но и выполнять системные команды (в зависимости от привилегий пользователя БД и настроек ОС).

---

### 🔗 Connections
- [[Footprinting]]
- [[Information Disclosure]]
- [[Database Security]]
- [[SQL Injection Fundamentals]]
- [[Network Protocols]]
- [[Privilege Escalation]]
- [[Post-Exploitation]]

