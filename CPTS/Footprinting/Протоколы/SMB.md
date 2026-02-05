# Server Message Block (SMB) & Samba

**SMB (Server Message Block)** — это клиент-серверный протокол, регулирующий доступ к файлам, каталогам и другим сетевым ресурсам (принтеры, маршрутизаторы). Он также обеспечивает обмен информацией между системными процессами. Проект **Samba** позволяет использовать SMB в системах Linux и Unix, обеспечивая кроссплатформенную связь.

---

### 🚀 Quick Commands

#### OS Terminal
```bash
# Перечисление доступных общих ресурсов (shares) анонимно
smbclient -N -L //<TARGET_IP>

# Подключение к конкретной сетевой папке анонимно
smbclient //<TARGET_IP>/<SHARE_NAME>

# Подключение к сетевой папке под пользователем
smbclient -U <USER> //<TARGET_IP>/<SHARE_NAME>

# Перечисление ресурсов через CrackMapExec (анонимно)
crackmapexec smb <TARGET_IP> --shares -u '' -p ''

# Использование rpcclient для взаимодействия с MS-RPC (анонимный вход)
rpcclient -U "" <TARGET_IP>

# Быстрое перечисление прав доступа к папкам через SMBMap
smbmap -H <TARGET_IP>

# Комплексное сканирование через enum4linux-ng
./enum4linux-ng.py <TARGET_IP> -A

# Перебор RID для поиска имен пользователей через rpcclient (Bash-цикл)
for i in $(seq 500 1100); do rpcclient -N -U "" <TARGET_IP> -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo ""; done
```

#### Service Interaction (внутри smbclient)
```bash
# Просмотр содержимого текущей директории
ls

# Скачивание файла на локальную машину
get <FILE_NAME>

# Загрузка файла на сервер (если есть права на запись)
put <LOCAL_FILE>

# Выполнение локальной системной команды без разрыва сессии
!<COMMAND> (например: !ls или !cat file.txt)

# Вывод справки по доступным командам
help
```

---

### 🛠 Tools & Utilities

*   [[Samba]] — реализация SMB для Unix-подобных систем.
*   [[smbclient]] — консольный клиент для доступа к ресурсам SMB.
*   [[rpcclient]] — инструмент для выполнения функций MS-RPC.
*   [[Nmap]] — используется для обнаружения службы и запуска NSE-скриптов (`smb-enum-shares`, `smb-os-discovery`).
*   [[Impacket]] — коллекция скриптов (например, `samrdump.py`) для взаимодействия с сетевыми протоколами.
*   [[SMBMap]] — удобный инструмент для перечисления прав на общие ресурсы.
*   [[CrackMapExec]] — мощный инструмент для аудита сетей и перечисления SMB.
*   [[enum4linux-ng]] — современный скрипт для автоматизации сбора информации об SMB/Samba.

---

### 📝 Technical Analysis & Media

#### Порты и Службы
| Порт | Протокол | Назначение |
| :--- | :--- | :--- |
| **137** | UDP | NetBIOS Name Service (NBNS) |
| **138** | UDP | NetBIOS Datagram Service |
| **139** | TCP | NetBIOS Session Service (SMB поверх NetBIOS) |
| **445** | TCP | Microsoft-DS (SMB поверх TCP/Direct Host) |

> [!info] Теория: SMB vs CIFS
> **CIFS (Common Internet File System)** — это ранняя версия (диалект) SMB 1.0. В современных сетях SMB работает напрямую через TCP порт 445, минуя NetBIOS. Samba может выступать как клиент, сервер или даже контроллер домена Active Directory (начиная с версии 4).

#### Версии протокола SMB
| Версия SMB | Поддерживается (ОС) | Особенности |
| :--- | :--- | :--- |
| **CIFS** | Windows NT 4.0 | Связь через NetBIOS. |
| **SMB 1.0** | Windows 2000 | Прямое соединение через TCP. |
| **SMB 2.0** | Windows Vista / Server 2008 | Улучшена производительность, подпись сообщений, кэширование. |
| **SMB 2.1** | Windows 7 / Server 2008 R2 | Механизмы блокировки (Leasing). |
| **SMB 3.0** | Windows 8 / Server 2012 | Многоканальность, сквозное шифрование, RDMA. |
| **SMB 3.1.1** | Windows 10 / Server 2016 | Проверка целостности (Pre-auth integrity), AES-128-GCM. |

#### Конфигурация Samba (`/etc/samba/smb.conf`)

> [!example] Содержимое конфигурационного файла (Default)
> ```shellsession
> MuraMitchi@htb[/htb]$ cat /etc/samba/smb.conf | grep -v "#\|\;"
> [global]
>    workgroup = DEV.INFREIGHT.HTB
>    server string = DEVSMB
>    log file = /var/log/samba/log.%m
>    max log size = 1000
>    logging = file
>    panic action = /usr/share/samba/panic-action %d
> 
>    server role = standalone server
>    obey pam restrictions = yes
>    unix password sync = yes
> 
>    passwd program = /usr/bin/passwd %u
>    passwd chat = *Enter\snew\s*\spassword:* %n\n *Retype\snew\s*\spassword:* %n\n *password\supdated\ssuccessfully* .
> 
>    pam password change = yes
>    map to guest = bad user
>    usershare allow guests = yes
> 
> [printers]
>    comment = All Printers
>    browseable = no
>    path = /var/spool/samba
>    printable = yes
>    guest ok = no
>    read only = yes
>    create mask = 0700
> 
> [print$]
>    comment = Printer Drivers
>    path = /var/lib/samba/printers
>    browseable = yes
>    read only = yes
>    guest ok = no
> ```

#### Разбор параметров конфигурации
| Параметр | Описание |
| :--- | :--- |
| `[sharename]` | Название сетевой папки. |
| `workgroup` | Рабочая группа или домен, отображаемый клиентам. |
| `path` | Реальный путь к каталогу на сервере. |
| `server string` | Баннер/строка приветствия сервера. |
| `unix password sync` | Синхронизация паролей UNIX и SMB. |
| `usershare allow guests` | Разрешение гостевого доступа к пользовательским папкам. |
| `map to guest = bad user` | Обработка неизвестных пользователей как гостей. |
| `browseable = yes` | Отображение папки в списке доступных ресурсов. |
| `guest ok = yes` | Разрешение входа без пароля. |
| `read only = yes` | Запрет на запись. |
| `create mask = 0700` | Права доступа для новых файлов. |

---

### 🔍 Command Breakdown (Deep Dive)

| Команда/Флаг | Описание | Зачем это для CPTS |
| :--- | :--- | :--- |
| `smbclient -N` | Null Session (без пароля). | Позволяет проверить наличие анонимного доступа. |
| `smbclient -L` | List (список ресурсов). | Обнаружение скрытых или неправильно настроенных папок. |
| `rpcclient -c <cmd>` | Выполнение команды RPC. | Автоматизация сбора данных (пользователи, группы, RID). |
| `enumdomusers` | RPC-команда перечисления пользователей. | Получение списка валидных имен пользователей для брутфорса. |
| `srvinfo` | Информация о сервере через RPC. | Точное определение версии ОС и роли сервера. |
| `queryuser <RID>` | Детальная информация по ID. | Поиск описаний, путей к профилям и скриптов входа. |

---

### ⚠️ Vulnerable Configurations & Flaws (Critical)

> [!warning] Критические недостатки и опасные настройки
> 1.  **Anonymous/Guest Access (`guest ok = yes`, `map to guest = bad user`):** Позволяет любому пользователю в сети просматривать и скачивать конфиденциальные документы без аутентификации.
> 2.  **Weak Permissions (`create mask = 0777`, `read only = no`):** Позволяет злоумышленнику изменять существующие файлы или загружать вредоносные скрипты (например, веб-шеллы, если папка связана с веб-сервером).
> 3.  **Magic Scripts (`magic script = script.sh`):** Крайне опасная функция. Если пользователь открывает файл, Samba выполняет указанный скрипт. Это прямой вектор к **RCE (Remote Code Execution)**.
> 4.  **SMBv1 (CIFS):** Устаревший протокол, подверженный атакам типа Man-in-the-Middle (MITM) и не имеющий встроенных механизмов защиты от эксплойтов (например, EternalBlue). Данные передаются в открытом виде (clear-text).
> 5.  **Information Disclosure через RPC:** Даже без доступа к файлам, анонимный RPC-сеанс позволяет выгрузить список всех пользователей домена, политику паролей и структуру групп.

---

### 💡 Pentest Insights

> [!tip] Хакерские хитрости
> *   **RID Brute-forcing:** Если `enumdomusers` заблокирован, используйте перебор RID (от 500 и выше) через `rpcclient` для поиска скрытых учетных записей.
> *   **Sensitive Files:** Всегда ищите файлы с расширениями `.conf`, `.xml`, `.txt`, `.pdf`, `.xls`. Часто в сетевых папках забывают бэкапы БД, пароли в текстовых файлах или инструкции для сотрудников.
> *   **Banner Grabbing:** Используйте `srvinfo` в `rpcclient` или `smb-os-discovery` в Nmap, чтобы узнать точную версию Windows/Samba. Это поможет найти публичные эксплойты.
> *   **Write Access:** Если у вас есть права на запись, попробуйте загрузить файл и проверить, исполняется ли он (например, через планировщик задач или веб-сервер).

---

### 🔗 Connections

*   [[Footprinting]]
*   [[Information Disclosure]]
*   [[Network Protocols]]
*   [[Clear-Text Protocols]]
*   [[Active Directory Enumeration]]
*   [[Privilege Escalation]]
*   [[Windows Security]]