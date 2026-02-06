Протокол удаленного управления Windows (WinRM) — это протокол, основанный на командной строке и предназначенный для удаленного управления и администрирования систем Windows. WinRM использует протокол Simple Object Access Protocol (SOAP) для установления соединений с удаленными хостами и их приложениями. Для работы WinRM его необходимо явно включить и настроить, что стало обязательным начиная с Windows 10.

WinRM использует следующие порты для связи:

| Порт | Протокол | Тип   | Назначение                     |
| :--- | :------- | :---- | :----------------------------- |
| 5985 | TCP      | HTTP  | Нешифрованное соединение WinRM |
| 5986 | TCP      | HTTPS | Зашифрованное соединение WinRM |

Изначально для этой цели могли использоваться порты 80 и 443, но ввиду соображений безопасности и для предотвращения конфликтов с веб-сервисами, стандартными стали порты 5985 и 5986.

Еще одним компонентом, тесно связанным с WinRM и подходящим для удаленного выполнения команд, является Windows Remote Shell (WinRS). Эта утилита позволяет выполнять произвольные команды на удаленной системе и входит в состав Windows 7 по умолчанию. Таким образом, WinRM является основой для удаленного выполнения кода (RCE) через PowerShell.

WinRM требуется для работы таких служб, как удаленные сеансы PowerShell и централизованный сбор журналов событий. Он включен по умолчанию начиная с определенной версии Windows Server 2012, но для более старых версий серверов и клиентов его необходимо предварительно настроить, а также создать необходимые исключения в брандмауэре.

## Создание базы данных для сервиса

Как нам уже известно, WinRM по умолчанию использует TCP-порты 5985 (HTTP) и 5986 (HTTPS), которые мы можем просканировать с помощью Nmap. Часто мы будем видеть, что TCP 5985 используется только для HTTP (нешифрованного трафика), а 5986 — для HTTPS.

#### Nmap WinRM

> [!example]
> shellsession
> `MuraMitchi@htb[/htb]$ nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-06 16:31 CET Nmap scan report for 10.129.201.248 Host is up (0.030s latency). PORT     STATE SERVICE VERSION 5985/tcp open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP) |_http-title: Not Found |_http-server-header: Microsoft-HTTPAPI/2.0 Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows Service detection performed. Please report any incorrect results at https://nmap.org/submit/ . Nmap done: 1 IP address (1 host up) scanned in 7.34 seconds`

Если мы хотим выяснить, доступен ли один или несколько удалённых серверов через WinRM, мы можем легко сделать это с помощью PowerShell. За это отвечает командлет [Test-WsMan](https://docs.microsoft.com/en-us/powershell/module/microsoft.wsman.management/test-wsman?view=powershell-7.2), которому передаётся имя искомого хоста. В средах на базе Linux мы можем использовать инструмент [evil-winrm](https://github.com/Hackplayers/evil-winrm), ещё один инструмент для тестирования на проникновение, предназначенный для взаимодействия с WinRM.

> [!example]
> shellsession
> `MuraMitchi@htb[/htb]$ evil-winrm -i 10.129.201.248 -u Cry0l1t3 -p P455w0rD! Evil-WinRM shell v3.3 Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine Data: For more information, check Evil-WinRM Github: https://github.com/Hackplayers/evil-winrm#Remote-path-completion Info: Establishing connection to remote endpoint *Evil-WinRM* PS C:\Users\Cry0l1t3\Documents>`

> [!warning]
> **Недостатки технологии:** WinRM, работающий по порту 5985 (HTTP), передает учетные данные и команды в открытом виде (clear-text), что делает его уязвимым для атак типа Man-in-the-Middle (MITM) при перехвате трафика. Использование 5986 (HTTPS) является предпочтительным для защиты конфиденциальности.

> [!tip]
> При использовании `evil-winrm` для получения интерактивной сессии, после успешного подключения, мы попадаем в командную строку PowerShell, где можно использовать стандартные команды PowerShell для перечисления системы, например, `whoami`, `ipconfig`, `systeminfo`.

> [!tip]
> Если WinRM настроен на использование Windows Basic Authentication (что часто бывает при настройке через `winrm quickconfig`), это может позволить нам использовать простые учетные данные, полученные в ходе перебора или утечек, для получения сессии.

[[Footprinting]]
[[Network Protocols]]
[[Clear-Text Protocols]]
[[Remote Code Execution]]
[[PowerShell]]
[[SOAP]]