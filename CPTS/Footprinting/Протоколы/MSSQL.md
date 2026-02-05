### 🚀 Quick Commands

**OS Terminal**
```bash
# Поиск местоположения скрипта mssqlclient на системе
locate mssqlclient

# Полное сканирование MSSQL с использованием специализированных скриптов Nmap
sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 <TARGET_IP>

# Использование вспомогательного модуля Metasploit для пинга и сбора информации о MSSQL
msfconsole -q -x "use auxiliary/scanner/mssql/mssql_ping; set RHOSTS <TARGET_IP>; run; exit"

# Подключение к MSSQL через Impacket с использованием Windows-аутентификации
python3 mssqlclient.py <USER>@<TARGET_IP> -windows-auth
```

**Service Interaction**
```sql
-- Просмотр списка всех баз данных на сервере
select name from sys.databases;
GO
```

---

### 🛠 Tools & Utilities

* `[[Nmap]]` — используется для перечисления (footprinting) сервиса и запуска скриптов категории `ms-sql-*`.
* `[[Metasploit Framework]]` — содержит модуль `mssql_ping` для быстрого обнаружения экземпляров SQL Server.
* `[[Impacket]]` — включает `mssqlclient.py`, мощный инструмент для удаленного подключения и взаимодействия с MSSQL.
* `[[SQL Server Management Studio (SSMS)]]` — графический клиент для управления MSSQL, часто встречается на рабочих станциях администраторов.
* `[[mssql-cli]]` / `[[HeidiSQL]]` / `[[SQLPro]]` — альтернативные клиенты для работы с базами данных.

---

### 📝 Technical Analysis & Media

**Порты и Службы**

| Порт | Протокол | Назначение |
| :--- | :--- | :--- |
| 1433 | TCP | Стандартный порт MSSQL для входящих соединений |
| 1434 | UDP | SQL Server Browser Service (используется для перечисления инстансов) |

> [!info] **Обзор Microsoft SQL (MSSQL)**
> MSSQL — это проприетарная реляционная СУБД от Microsoft. В отличие от MySQL, она имеет закрытый исходный код и изначально разрабатывалась для Windows. Благодаря глубокой интеграции с .NET framework, она крайне популярна в корпоративных средах. Хотя существуют версии для Linux и MacOS, в рамках пентеста чаще всего встречаются инстансы на базе Windows.

#### Клиенты MSSQL
SQL Server Management Studio (`SSMS`) — основной инструмент администрирования.
**Важно для пентестера:** Поскольку SSMS — клиентское приложение, оно может быть установлено не только на сервере, но и на рабочей станции админа. Обнаружение скомпрометированной системы с установленным SSMS и сохраненными учетными данными — прямой путь к базе данных.

![SQL Server Management Studio showing Object Explorer with 'Employees' database expanded, displaying tables, views, and other database objects.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/112/ssms.png)

#### Системные базы данных
MSSQL содержит стандартные БД, помогающие понять структуру сервера:

| Системная БД | Описание |
| :--- | :--- |
| `master` | Хранит всю системную информацию для экземпляра SQL Server. |
| `model` | Шаблонная база данных; настройки в ней копируются во все новые создаваемые БД. |
| `msdb` | Используется SQL Server Agent для планирования заданий и алертов. |
| `tempdb` | Хранит временные объекты. |
| `resource` | База только для чтения, содержащая системные объекты, включенные в SQL Server. |

#### Конфигурация по умолчанию
Обычно служба запускается от имени `NT SERVICE\MSSQLSERVER`. По умолчанию шифрование при подключении не является обязательным, а аутентификация часто настроена как `Windows Authentication`.

![SQL Server connection window showing options for server type, server name 'ILF-SQL-01', and Windows Authentication.](https://cdn.services-k8s.prod.srv.aws.htb.systems/content/modules/112/auth.png)

> [!example] **Логи сканирования Nmap (MSSQL Script Scan)**
> ```text
> MuraMitchi@htb[/htb]$ sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248
> Starting Nmap 7.91 ( https://nmap.org ) at 2021-11-08 09:40 EST
> Nmap scan report for 10.129.201.248
> Host is up (0.15s latency).
> 
> PORT     STATE SERVICE  VERSION
> 1433/tcp open  ms-sql-s Microsoft SQL Server 2019 15.00.2000.00; RTM
> | ms-sql-ntlm-info: 
> |   Target_Name: SQL-01
> |   NetBIOS_Domain_Name: SQL-01
> |   NetBIOS_Computer_Name: SQL-01
> |   DNS_Domain_Name: SQL-01
> |   DNS_Computer_Name: SQL-01
> |_  Product_Version: 10.0.17763
> | ms-sql-dac: 
> |_  Instance: MSSQLSERVER; DAC port: 1434 (connection failed)
> | ms-sql-info: 
> |   Windows server name: SQL-01
> |   10.129.201.248\MSSQLSERVER: 
> |     Instance name: MSSQLSERVER
> |     Version: 
> |       name: Microsoft SQL Server 2019 RTM
> |       number: 15.00.2000.00
> |       Product: Microsoft SQL Server 2019
> |       Service pack level: RTM
> |       Post-SP patches applied: false
> |     TCP port: 1433
> |     Named pipe: \\10.129.201.248\pipe\sql\query
> |_    Clustered: false
> 
> Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
> Nmap done: 1 IP address (1 host up) scanned in 8.52 seconds
> ```

> [!example] **Логи Metasploit (mssql_ping)**
> ```text
> msf6 auxiliary(scanner/mssql/mssql_ping) > set rhosts 10.129.201.248
> rhosts => 10.129.201.248
> msf6 auxiliary(scanner/mssql/mssql_ping) > run
> 
> [*] 10.129.201.248:       - SQL Server information for 10.129.201.248:
> [+] 10.129.201.248:       -    ServerName      = SQL-01
> [+] 10.129.201.248:       -    InstanceName    = MSSQLSERVER
> [+] 10.129.201.248:       -    IsClustered     = No
> [+] 10.129.201.248:       -    Version         = 15.0.2000.5
> [+] 10.129.201.248:       -    tcp             = 1433
> [+] 10.129.201.248:       -    np              = \\SQL-01\pipe\sql\query
> [*] 10.129.201.248:       - Scanned 1 of 1 hosts (100% complete)
> [*] Auxiliary module execution completed
> ```

---

### 🔍 Command Breakdown (Deep Dive)

| Команда/Флаг | Описание | Зачем это для CPTS |
| :--- | :--- | :--- |
| `--script ms-sql-info` | Сбор базовой информации об инстансе. | Определение версии ОС и SQL Server для подбора эксплойтов. |
| `--script ms-sql-ntlm-info` | Извлечение имен домена и компьютера через NTLM. | Помогает в сопоставлении целей внутри Active Directory. |
| `--script ms-sql-empty-password` | Проверка пустых паролей (особенно для `sa`). | Быстрый поиск критических мисконфигураций доступа. |
| `-windows-auth` | Флаг в `mssqlclient.py` для использования Windows-аутентификации. | Позволяет использовать скомпрометированные доменные учетки для входа в СУБД. |
| `mssql_ping` | Сканирование через UDP 1434. | Обнаружение скрытых или динамических портов MSSQL. |

---

### ⚠️ Vulnerable Configurations & Flaws (Critical)

> [!warning] **Критические уязвимости и настройки**
> *   **Отсутствие шифрования:** По умолчанию MSSQL не всегда принуждает к использованию TLS. Это позволяет атакующему перехватывать трафик и учетные данные в открытом виде (MITM).
> *   **Самоподписанные сертификаты:** Если шифрование включено, но используются самоподписанные сертификаты, их можно подделать (Spoofing).
> *   **Named Pipes (Именованные каналы):** Включение `\\<IP>\pipe\sql\query` может предоставить дополнительные векторы для бокового перемещения.
> *   **Учетная запись `sa`:** Администраторы часто оставляют дефолтную учетку `sa` (System Administrator) с простым паролем или забывают её отключить. Это дает полный контроль над СУБД.
> *   **Windows Authentication:** Если доменная учетная запись скомпрометирована, она может иметь права доступа к SQL, что ведет к Privilege Escalation внутри домена.

---

### 💡 Pentest Insights

> [!tip] **Хакерские хитрости**
> *   **OSINT через MSSQL:** Скрипт `ms-sql-ntlm-info` часто выдает реальное имя сервера и внутреннее имя домена, даже если сервер находится за NAT.
> *   **Information Disclosure:** Обязательно проверяйте наличие системных баз данных. База `master` может содержать информацию о других базах данных на сервере, к которым у вас может не быть прямого доступа изначально.
> *   **Credential Hunting:** Всегда ищите установленный `SSMS` на машинах разработчиков. Проверяйте файлы конфигурации приложений (`web.config`, `appsettings.json`) на наличие строк подключения.

---

### 🔗 Connections

* [[Footprinting]]
* [[Information Disclosure]]
* [[Databases]]
* [[Active Directory]]
* [[Windows Authentication]]
* [[Impacket]]
* [[Network Protocols]]