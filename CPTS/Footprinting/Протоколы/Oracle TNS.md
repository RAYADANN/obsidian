### 🚀 Quick Commands

#### OS Terminal
```bash
# Сканирование порта TNS и определение версии
sudo nmap -p1521 -sV <TARGET_IP> --open

# Брутфорс SID (System Identifier) через Nmap
sudo nmap -p1521 -sV <TARGET_IP> --open --script oracle-sid-brute

# Полное сканирование всеми модулями ODAT для поиска уязвимостей и учетных данных
./odat.py all -s <TARGET_IP>

# Подключение к базе данных через SQLplus
sqlplus <USER>/<PASS>@<TARGET_IP>/<SID>

# Подключение с правами системного администратора (sysdba)
sqlplus <USER>/<PASS>@<TARGET_IP>/<SID> as sysdba

# Загрузка файла на сервер через модуль utlfile (требуются права или уязвимость)
./odat.py utlfile -s <TARGET_IP> -d <SID> -U <USER> -P <PASS> --sysdba --putFile <REMOTE_PATH> <LOCAL_FILE_NAME> <LOCAL_PATH>
```

#### Service Interaction (SQLplus)
```sql
-- Просмотр всех доступных таблиц
select table_name from all_tables;

-- Просмотр ролей и привилегий текущего пользователя
select * from user_role_privs;

-- Извлечение хэшей паролей пользователей (требуются высокие привилегии)
select name, password from sys.user$;
```

---

### 🛠 Tools & Utilities

- [[Nmap]] — используется для обнаружения открытых портов TNS и брутфорса SID с помощью скриптов NSE.
- [[ODAT]] (Oracle Database Attacking Tool) — мощный фреймворк для перечисления, поиска векторов эксплуатации и взлома баз данных Oracle.
- [[SQLplus]] — стандартная утилита командной строки Oracle для взаимодействия с СУБД.
- [[Instant-Client]] — набор библиотек Oracle, необходимых для работы SQLplus и ODAT.

---

### 📝 Technical Analysis & Media

#### Порты и Службы
| Порт | Протокол | Сервис | Роль |
| :--- | :--- | :--- | :--- |
| 1521 | TCP | Oracle TNS | Основной порт прослушивателя (Listener) для клиентских подключений. |
| 1521 | UDP | Oracle TNS | Может использоваться для передачи данных в некоторых конфигурациях. |

> [!info] **Теория и Принципы: Oracle TNS**
> **Oracle Transparent Network Substrate (TNS)** — это протокол связи, обеспечивающий обмен данными между БД Oracle и приложениями. Он работает поверх `TCP/IP` или `IPX/SPX`. 
> 
> Основные функции:
> 1. **Разрешение имен:** Сопоставление имен сервисов с сетевыми адресами.
> 2. **Управление соединениями:** Установление и разрыв сессий.
> 3. **Балансировка нагрузки:** Распределение запросов между экземплярами.
> 4. **Безопасность:** Встроенные механизмы шифрования (Oracle Net Services) и поддержка SSL/TLS.

#### Конфигурация

**tnsnames.ora** (Клиентская сторона)
Используется для преобразования имен служб в сетевые адреса. Находится в `$ORACLE_HOME/network/admin`.
> [!example] Пример tnsnames.ora
> ```text
> ORCL =
>   (DESCRIPTION =
>     (ADDRESS_LIST =
>       (ADDRESS = (PROTOCOL = TCP)(HOST = 10.129.11.102)(PORT = 1521))
>     )
>     (CONNECT_DATA =
>       (SERVER = DEDICATED)
>       (SERVICE_NAME = orcl)
>     )
>   )
> ```

**listener.ora** (Серверная сторона)
Определяет параметры процесса прослушивания.
> [!example] Пример listener.ora
> ```text
> SID_LIST_LISTENER =
>   (SID_LIST =
>     (SID_DESC =
>       (SID_NAME = PDB1)
>       (ORACLE_HOME = C:\oracle\product\19.0.0\dbhome_1)
>       (GLOBAL_DBNAME = PDB1)
>       (SID_DIRECTORY_LIST =
>         (SID_DIRECTORY =
>           (DIRECTORY_TYPE = TNS_ADMIN)
>           (DIRECTORY = C:\oracle\product\19.0.0\dbhome_1\network\admin)
>         )
>       )
>     )
>   )
>  LISTENER =
>   (DESCRIPTION_LIST =
>     (DESCRIPTION =
>       (ADDRESS = (PROTOCOL = TCP)(HOST = orcl.inlanefreight.htb)(PORT = 1521))
>       (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
>     )
>   )
>  ADR_BASE_LISTENER = C:\oracle
> ```

#### Разбор параметров конфигурации

| Параметр | Описание |
| :--- | :--- |
| `DESCRIPTION` | Дескриптор, указывающий имя базы данных и тип подключения к ней. |
| `ADDRESS` | Сетевой адрес базы данных, включающий имя хоста и номер порта. |
| `PROTOCOL` | Сетевой протокол, используемый для связи с сервером. |
| `PORT` | Номер порта, используемый для связи с сервером. |
| `CONNECT_DATA` | Указывает атрибуты соединения (SID, SERVICE_NAME). |
| `INSTANCE_NAME` | Имя экземпляра базы данных. |
| `SERVICE_NAME` | Название сервиса для подключения. |
| `SERVER` | Тип сервера (DEDICATED или SHARED). |
| `USER` | Имя пользователя для аутентификации. |
| `PASSWORD` | Пароль для аутентификации. |
| `SECURITY` | Тип защиты соединения. |
| `VALIDATE_CERT` | Проверка сертификата SSL/TLS. |
| `SSL_VERSION` | Версия SSL/TLS. |
| `CONNECT_TIMEOUT` | Таймаут установления соединения (в сек). |
| `RECEIVE_TIMEOUT` | Таймаут получения ответа (в сек). |
| `SEND_TIMEOUT` | Таймаут отправки запроса (в сек). |
| `SQLNET.EXPIRE_TIME` | Интервал проверки активности соединения. |
| `TRACE_LEVEL` | Уровень трассировки. |
| `TRACE_DIRECTORY` | Каталог файлов трассировки. |
| `TRACE_FILE_NAME` | Имя файла трассировки. |
| `LOG_FILE` | Файл журнала событий. |

---

### 🔍 Command Breakdown (Deep Dive)

| Команда/Флаг | Описание | Зачем это для CPTS |
| :--- | :--- | :--- |
| `oracle-sid-brute` | Скрипт Nmap для перебора SID. | Без SID невозможно подключиться к базе; это первый этап перечисления. |
| `odat.py all` | Запуск всех модулей (брутфорс, эксплойты). | Позволяет быстро найти слабые пароли или уязвимые процедуры (например, `utl_file`). |
| `as sysdba` | Флаг подключения с максимальными правами. | Позволяет обходить ограничения обычного пользователя и извлекать хэши паролей. |
| `utlfile --putFile` | Загрузка локального файла на сервер. | Используется для доставки веб-шеллов в корень веб-сервера или других полезных нагрузок. |

---

### ⚠️ Vulnerable Configurations & Flaws (Critical)

> [!warning] **Критические недостатки и опасные настройки**
> 1. **Default Passwords:** 
>    - Oracle 9i: пароль по умолчанию `CHANGE_ON_INSTALL`.
>    - Сервис DBSNMP: пароль по умолчанию `dbsnmp`.
>    - Аккаунт `scott`: пароль `tiger`.
> 2. **Отсутствие пароля на Listener:** В старых версиях (8i/9i) TNS-слушателем можно управлять удаленно без пароля.
> 3. **Clear-text Communication:** Если шифрование не настроено принудительно, учетные данные и данные БД передаются в открытом виде (уязвимость к MITM).
> 4. **PL/SQL Exclusion List:** Если список `PlsqlExclusionList` настроен неправильно, атакующий может выполнять запрещенные пакеты PL/SQL через веб-сервер.
> 5. **Finger Service:** Использование службы `finger` совместно с Oracle может раскрыть пути к домашним каталогам, облегчая атаку.

---

### 💡 Pentest Insights

> [!tip] **Хакерские хитрости**
> - **SID Guessing:** Всегда пробуйте стандартные SID: `XE`, `ORCL`, `PROD`, `DATABASE`.
> - **Web Shell Upload:** Если вы получили `sysdba`, проверьте наличие веб-сервера. Попробуйте загрузить файл в `/var/www/html` (Linux) или `C:\inetpub\wwwroot` (Windows).
> - **Password Cracking:** После получения хэшей из `sys.user$`, помните, что формат хэша зависит от версии Oracle (DES, SHA-1 и т.д.).
> - **OSINT:** Иногда версия Oracle в баннере подсказывает операционную систему и её версию (например, Oracle 11g Express Edition часто стоит на Windows).

---

### 🔗 Connections

- [[Footprinting]]
- [[Database Hacking]]
- [[Oracle Database]]
- [[Information Disclosure]]
- [[Clear-Text Protocols]]
- [[Privilege Escalation]]
- [[Network Protocols]]