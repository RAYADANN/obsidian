# Secure Shell (SSH)

### 🚀 Quick Commands

**OS Terminal**
```bash
# Клонирование репозитория ssh-audit для анализа конфигурации сервера
git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit

# Запуск аудита безопасности SSH-сервера для выявления слабых алгоритмов и версий
./ssh-audit.py <TARGET_IP>

# Подключение к SSH с флагом подробного вывода для идентификации методов аутентификации и баннеров
ssh -v <USER>@<TARGET_IP>

# Принудительное использование аутентификации по паролю (полезно для обхода попыток входа по ключу)
ssh -v <USER>@<TARGET_IP> -o PreferredAuthentications=password
```

---

### 🛠 Tools & Utilities

* `[[SSH]]` — Протокол для безопасного удаленного доступа и управления системой.
* `[[OpenSSH]]` — Открытая реализация SSH, стандарт для Linux-систем.
* `[[ssh-audit]]` — Инструмент для сканирования конфигурации SSH на предмет уязвимостей и слабых криптографических алгоритмов.

---

### 📝 Technical Analysis & Media

#### Порты и Службы
| Порт | Протокол | Служба | Описание                                                |
| :--- | :------- | :----- | :------------------------------------------------------ |
| 22   | TCP      | SSH    | Стандартный порт для защищенного удаленного управления. |

> [!info] Теория: SSH-1 vs SSH-2
> Существуют два протокола: `SSH-1` и `SSH-2`.
> * **SSH-2** является более продвинутым с точки зрения шифрования, скорости и стабильности.
> * **SSH-1** критически уязвим для атак типа **MITM** (Man-in-the-Middle), в то время как SSH-2 защищен от них.

#### Методы аутентификации OpenSSH
1. Аутентификация по паролю (Password authentication)
2. Аутентификация с помощью открытого ключа (Public key authentication)
3. Аутентификация на основе хоста (Host-based authentication)
4. Аутентификация с клавиатуры (Keyboard-interactive authentication)
5. Аутентификация по принципу «запрос-ответ» (Challenge-response authentication)
6. Аутентификация GSSAPI

> [!info] Глубокий разбор: Аутентификация по открытому ключу
> 1. **Аутентификация сервера:** Сервер отправляет свой `public host key` клиенту. Клиент проверяет его по доверенному источнику (например, `known_hosts`), чтобы исключить подмену.
> 2. **Аутентификация клиента:** Клиент доказывает владение закрытым ключом. Сервер создает криптографическую задачу с использованием открытого ключа клиента, которую тот должен расшифровать своим закрытым ключом.
> 3. **Безопасность:** Закрытый ключ никогда не покидает машину пользователя и защищается парольной фразой (passphrase).

#### Конфигурация по умолчанию
Файл `/etc/ssh/sshd_config` определяет параметры работы сервера.

> [!example] Вывод конфигурации (без комментариев и пустых строк)
> ```shellsession
> MuraMitchi@htb[/htb]$ cat /etc/ssh/sshd_config  | grep -v "#" | sed -r '/^\s*$/d'
> Include /etc/ssh/sshd_config.d/*.conf
> ChallengeResponseAuthentication no
> UsePAM yes
> X11Forwarding yes
> PrintMotd no
> AcceptEnv LANG LC_*
> Subsystem       sftp    /usr/lib/openssh/sftp-server
> ```

#### Анализ безопасности (ssh-audit)
> [!example] Результат работы ssh-audit
> ```shellsession
> MuraMitchi@htb[/htb]$ git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit
> MuraMitchi@htb[/htb]$ ./ssh-audit.py 10.129.14.132
> # general (gen) banner: SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.3
> (gen) software: OpenSSH 8.2p1
> (gen) compatibility: OpenSSH 7.4+, Dropbear SSH 2018.76+
> (gen) compression: enabled (zlib@openssh.com)
> 
> # key exchange algorithms (kex)
> curve25519-sha256                     -- [info] available since OpenSSH 7.4, Dropbear SSH 2018.76
> (kex) curve25519-sha256@libssh.org          -- [info] available since OpenSSH 6.5, Dropbear SSH 2013.62
> (kex) ecdh-sha2-nistp256                    -- [fail] using weak elliptic curves
> `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
> (kex) ecdh-sha2-nistp384                    -- [fail] using weak elliptic curves
> `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
> (kex) ecdh-sha2-nistp521                    -- [fail] using weak elliptic curves
> `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
> (kex) diffie-hellman-group-exchange-sha256 (2048-bit) -- [info] available since OpenSSH 4.4
> (kex) diffie-hellman-group16-sha512         -- [info] available since OpenSSH 7.3, Dropbear SSH 2016.73
> (kex) diffie-hellman-group18-sha512         -- [info] available since OpenSSH 7.3
> (kex) diffie-hellman-group14-sha256         -- [info] available since OpenSSH 7.3, Dropbear SSH 2016.73
> 
> # host-key algorithms (key)
> rsa-sha2-512 (3072-bit)               -- [info] available since OpenSSH 7.2
> (key) rsa-sha2-256 (3072-bit)               -- [info] available since OpenSSH 7.2
> (key) ssh-rsa (3072-bit)                    -- [fail] using weak hashing algorithm
> `- [info] available since OpenSSH 2.5.0, Dropbear SSH 0.28
> `- [info] a future deprecation notice has been issued in OpenSSH 8.2: https://www.openssh.com/txt/release-8.2
> (key) ecdsa-sha2-nistp256                   -- [fail] using weak elliptic curves
> `- [warn] using weak random number generator could reveal the key
> `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
> (key) ssh-ed25519                           -- [info] available since OpenSSH 6.5
> ...SNIP...
> ```

#### Идентификация методов аутентификации
> [!example] Лог отладки SSH-соединения
> ```shellsession
> MuraMitchi@htb[/htb]$ ssh -v cry0l1t3@10.129.14.132
> OpenSSH_8.2p1 Ubuntu-4ubuntu0.3, OpenSSL 1.1.1f  31 Mar 2020
> debug1: Reading configuration data /etc/ssh/ssh_config
> ...SNIP...
> debug1: Authentications that can continue: publickey,password,keyboard-interactive
> ```

---

### 🔍 Command Breakdown (Deep Dive)

| Команда/Флаг                           | Описание                                | Зачем это для CPTS                                                                                      |
| :------------------------------------- | :-------------------------------------- | :------------------------------------------------------------------------------------------------------ |
| `ssh -v`                               | Режим Verbose (подробный вывод).        | Получение баннера версии сервера и списка разрешенных методов аутентификации.                           |
| `-o PreferredAuthentications=password` | Указывает предпочтительный метод входа. | Позволяет форсировать ввод пароля, если сервер по умолчанию требует ключ, к которому у нас нет доступа. |
| `ssh-audit.py`                         | Скрипт для сканирования SSH.            | Выявление слабых алгоритмов шифрования (KEX, Host-key) для векторов MITM или эксплуатации CVE.          |

---

### ⚠️ Vulnerable Configurations & Flaws (Critical)

> [!warning] Опасные настройки в sshd_config
> 
> | Параметр | Описание | Вектор эксплуатации |
> | :--- | :--- | :--- |
> | `PasswordAuthentication yes` | Включает вход по паролю. | Позволяет проводить атаки методом перебора (Brute-force). |
> | `PermitEmptyPasswords yes` | Разрешает пустые пароли. | Позволяет войти в систему без пароля, если он не задан у пользователя. |
> | `PermitRootLogin yes` | Разрешает вход под root. | Дает злоумышленнику максимальные привилегии сразу после успешного брутфорса. |
> | `Protocol 1` | Использование старой версии. | Уязвим для атак перехвата трафика и MITM. |
> | `X11Forwarding yes` | Переадресация графики. | Потенциальные уязвимости внедрения команд (например, CVE-2016 в OpenSSH 7.2p1). |
> | `AllowTcpForwarding yes` | Перенаправление портов. | Позволяет использовать скомпрометированный хост как прокси для атаки внутренней сети. |
> | `PermitTunnel` | Позволяет туннелирование. | Облегчает создание скрытых каналов связи (exfiltration/pivoting). |
> | `DebianBanner yes` | Показ версии ОС в баннере. | Раскрывает точную версию дистрибутива, упрощая поиск подходящих эксплойтов. |

---

### 💡 Pentest Insights

> [!tip] Хакерские хитрости
> * **Banner Grabbing:** Баннеры типа `SSH-1.99` указывают на поддержку обеих версий протокола (1 и 2), что позволяет попытаться откатиться на менее безопасный SSH-1.
> * **Анализ отладки:** Всегда используйте флаг `-v`. Строка `Authentications that can continue` четко показывает, стоит ли тратить время на брутфорс паролей или сервер принимает только ключи.
> * **Шаблоны паролей:** При брутфорсе учитывайте человеческий фактор — пользователи часто добавляют цифры или спецсимволы в конец простых слов.

---

### 🔗 Connections
* [[Footprinting]]
* [[Network Protocols]]
* [[Information Disclosure]]
* [[Clear-Text Protocols]]
* [[Brute Forcing]]
* [[Cryptography]]