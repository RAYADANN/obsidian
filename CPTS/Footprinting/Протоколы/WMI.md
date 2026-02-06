Windows Management Instrumentation (WMI `WMI`) — это реализация Microsoft и расширение Common Information Model (Common Information Model `CIM`), основной функциональности стандартизированной системы Web-Based Enterprise Management (WMI `WBEM`) для платформы Windows. WMI обеспечивает доступ на чтение и запись практически ко всем параметрам в системах Windows. Понятно, что это делает его наиболее важным интерфейсом в среде Windows для администрирования и удаленного обслуживания компьютеров Windows, независимо от того, являются ли они ПК или серверами. Доступ к WMI обычно осуществляется через PowerShell, VBScript или консоль Windows Management Instrumentation (WMI `WMIC`). WMI — это не одна программа, а несколько программ и различных баз данных, также известных как репозитории.

---

## Создание базы данных для сервиса

Инициализация WMI-связи всегда происходит на `TCP`порту `135`, а после успешного установления соединения связь переносится на случайный порт. Например, для этого можно использовать программу [wmiexec.py из инструментария Impacket.](https://github.com/SecureAuthCorp/impacket/blob/master/examples/wmiexec.py)

#### WMIexec.py

        shellsession
`MuraMitchi@htb[/htb]$ /usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname" Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation [*] SMBv3.0 dialect used ILF-SQL-01`