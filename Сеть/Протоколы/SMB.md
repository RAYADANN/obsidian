Server Message BlockSMB SMB— это клиент-серверный протокол, регулирующий доступ к файлам, целым каталогам и другим сетевым ресурсам, таким как принтеры, маршрутизаторы или интерфейсы, выделенные для сети. Обмен информацией между различными системными процессами также может осуществляться на основе протокола SMB. SMB впервые стал доступен широкой публике, например, в составе сетевой операционной системы OS/2 LAN Manager и LAN Server. С тех пор основной областью применения протокола стали, в частности, операционные системы Windows, сетевые службы которых поддерживают SMB с обратной совместимостью — это означает, что устройства с более новыми версиями могут легко взаимодействовать с устройствами, на которых установлена ​​более старая операционная система Microsoft. В рамках проекта свободного программного обеспечения Samba также существует решение, позволяющее использовать SMB в дистрибутивах Linux и Unix и, таким образом, осуществлять кроссплатформенную связь через SMB.

Протокол SMB позволяет клиенту взаимодействовать с другими участниками той же сети для доступа к файлам или сервисам, совместно используемым в сети. Другая система также должна реализовать этот сетевой протокол и получить и обработать запрос клиента с помощью серверного приложения SMB. Однако перед этим обе стороны должны установить соединение, поэтому сначала они обмениваются соответствующими сообщениями.

В IP-сетях SMB использует для этой цели протокол TCP, который предусматривает трехстороннее рукопожатие между клиентом и сервером перед окончательным установлением соединения. Спецификации протокола TCP также регулируют последующую передачу данных. Примеры можно посмотреть здесь .

Сервер SMB может предоставлять произвольные части своей локальной файловой системы в качестве общих ресурсов. Поэтому иерархия, видимая клиенту, частично независима от структуры на сервере. Права доступа определяются с помощью Access Control Lists( ACL). Их можно контролировать детально на основе таких атрибутов, как execute, read, и full accessдля отдельных пользователей или групп пользователей. Списки контроля доступа (ACL) определяются на основе общих ресурсов и, следовательно, не соответствуют правам, назначенным локально на сервере.

Самба
Как уже упоминалось ранее, существует альтернативная реализация SMB-сервера под названием Samba, разработанная для операционных систем на базе Unix. Samba реализует CIFSсетевой протокол Common Internet File System (CIFS). CIFS — это диалект SMB, то есть специфическая реализация протокола SMB, первоначально созданного Microsoft. Это позволяет Samba эффективно взаимодействовать с более новыми системами Windows. Поэтому её часто называют SMB/CIFS.

Однако CIFSсчитается, что это специфическая версия протокола SMB, в первую очередь соответствующая SMB version 1. При передаче команд SMB через Samba в более старую службу NetBIOS соединения обычно устанавливаются через TCP-порты 137, 138, и 139. В отличие от этого, CIFS работает 445исключительно через TCP-порт. Существует несколько версий SMB, включая более новые версии, такие как SMB 2и SMB 3, которые предлагают улучшения и предпочтительны в современных инфраструктурах, в то время как более старые версии, такие как SMB 1( CIFS), считаются устаревшими, но все еще могут использоваться в определенных средах.

Версия SMB	Поддерживается	Функции
CIFS	Windows NT 4.0	Связь осуществляется через интерфейс NetBIOS.
SMB 1.0	Windows 2000	Прямое соединение через TCP
SMB 2.0	Windows Vista, Windows Server 2008	Улучшена производительность, усовершенствована функция подписи сообщений, добавлено кэширование.
SMB 2.1	Windows 7, Windows Server 2008 R2	Запирающие механизмы
SMB 3.0	Windows 8, Windows Server 2012	Многоканальные соединения, сквозное шифрование, удаленный доступ к хранилищу.
SMB 3.0.2	Windows 8.1, Windows Server 2012 R2	
SMB 3.1.1	Windows 10, Windows Server 2016	Проверка целостности, шифрование AES-128
Начиная с версии 3, сервер Samba получил возможность быть полноправным членом домена Active Directory. В версии 4 Samba даже предоставляет контроллер домена Active Directory. Для этой цели он содержит несколько так называемых демонов — фоновых программ Unix. Демон сервера SMB ( smbd) Samba обеспечивает первые две функции, а демон блоков сообщений NetBIOS ( nmbd) реализует последние две функции. Служба SMB управляет этими двумя фоновыми программами.

Мы знаем, что Samba подходит как для систем Linux, так и для Windows. В сети каждый хост участвует в одной и той же workgroupрабочей группе. Рабочая группа — это имя группы, которое идентифицирует произвольную совокупность компьютеров и их ресурсов в сети SMB. В сети может одновременно существовать несколько рабочих групп. IBM разработала application programming interface( API) для организации работы компьютеров в сети, называемую Network Basic Input/Output System( NetBIOS). API NetBIOS предоставил шаблон для приложения, позволяющего подключаться к другим компьютерам и обмениваться с ними данными. В среде NetBIOS, когда машина подключается к сети, ей требуется имя, которое задается с помощью так называемой name registrationпроцедуры. Либо каждый хост резервирует свое имя хоста в сети, либо для этой цели используется сервер имен NetBIOS ( ). Он также был улучшен до службы имен Windows Internet Name Service ( ).NBNSWINS

Конфигурация по умолчанию
Как вы можете себе представить, Samba предлагает широкий спектр настроек , которые мы можем сконфигурировать. Опять же, мы определяем настройки с помощью текстового файла, где можно получить обзор некоторых из них. В отфильтрованном виде эти настройки выглядят следующим образом:

Конфигурация по умолчанию
        shellsession
MuraMitchi@htb[/htb]$ cat /etc/samba/smb.conf | grep -v "#\|\;" 

[global]
   workgroup = DEV.INFREIGHT.HTB
   server string = DEVSMB
   log file = /var/log/samba/log.%m
   max log size = 1000
   logging = file
   panic action = /usr/share/samba/panic-action %d

   server role = standalone server
   obey pam restrictions = yes
   unix password sync = yes

   passwd program = /usr/bin/passwd %u
   passwd chat = *Enter\snew\s*\spassword:* %n\n *Retype\snew\s*\spassword:* %n\n *password\supdated\ssuccessfully* .

   pam password change = yes
   map to guest = bad user
   usershare allow guests = yes

[printers]
   comment = All Printers
   browseable = no
   path = /var/spool/samba
   printable = yes
   guest ok = no
   read only = yes
   create mask = 0700

[print$]
   comment = Printer Drivers
   path = /var/lib/samba/printers
   browseable = yes
   read only = yes
   guest ok = no

Мы видим глобальные настройки и две общие папки, предназначенные для принтеров. Глобальные настройки — это конфигурация доступного SMB-сервера, используемого для всех общих папок. Однако в отдельных общих папках глобальные настройки могут быть перезаписаны, что с высокой вероятностью может привести к неправильной настройке. Давайте рассмотрим некоторые настройки, чтобы понять, как настраиваются общие папки в Samba.

Параметр	Описание
[sharename]	Название сетевой папки.
workgroup = WORKGROUP/DOMAIN	Рабочая группа, которая будет отображаться при запросах клиентов.
path = /path/here/	Каталог, к которому пользователю будет предоставлен доступ.
server string = STRING	Строка, которая отобразится при установлении соединения.
unix password sync = yes	Синхронизировать пароль UNIX с паролем SMB?
usershare allow guests = yes	Разрешить неавторизованным пользователям доступ к определенной общей папке?
map to guest = bad user	Что делать, если запрос на вход пользователя не соответствует действующему пользователю UNIX?
browseable = yes	Следует ли отображать эту акцию в списке доступных акций?
guest ok = yes	Разрешить подключение к сервису без использования пароля?
read only = yes	Разрешить пользователям только чтение файлов?
create mask = 0700	Какие права доступа необходимо установить для вновь созданных файлов?
Опасные условия
Некоторые из вышеперечисленных настроек уже содержат конфиденциальные параметры. Однако, предположим, мы подвергнем сомнению перечисленные ниже настройки и зададимся вопросом, какую выгоду от них могут получить как сотрудники, так и злоумышленники. В этом случае мы увидим, какие преимущества и недостатки несут эти настройки. Возьмем browseable = yesв качестве примера следующую настройку. Если мы, как администраторы, примем эту настройку, сотрудники компании смогут с удобством просматривать содержимое отдельных папок. Множество папок в конечном итоге используется для лучшей организации и структуры. Если сотрудник может просматривать общие ресурсы, злоумышленник также сможет сделать это после успешного доступа.

Параметр	Описание
browseable = yes	Разрешить отображение списка доступных акций в текущей акции?
read only = no	Запретить создание и изменение файлов?
writable = yes	Разрешить пользователям создавать и изменять файлы?
guest ok = yes	Разрешить подключение к сервису без использования пароля?
enable privileges = yes	Привилегии чести, предоставленные конкретному SID?
create mask = 0777	Какие права доступа необходимо назначить вновь созданным файлам?
directory mask = 0777	Какие права доступа необходимо назначить вновь созданным каталогам?
logon script = script.sh	Какой скрипт необходимо выполнить при входе пользователя в систему?
magic script = script.sh	Какой скрипт должен быть выполнен после закрытия скрипта?
magic output = script.out	Куда следует сохранять результаты работы магического скрипта?
Давайте создадим общий ресурс с именем [notes]и еще несколько других и посмотрим, как их настройки повлияют на процесс перечисления. Мы будем использовать все указанные выше настройки и применим их к этому общему ресурсу. Например, эта настройка часто применяется, хотя бы в целях тестирования. Если же это внутренняя подсеть небольшой команды в большом отделе, то эта настройка часто сохраняется или забывается. Это приводит к тому, что мы можем просмотреть все общие ресурсы и, с высокой вероятностью, даже загрузить и проверить их.

Пример акции
        shellsession
...SNIP...

[notes]
    comment = CheckIT
    path = /mnt/notes/

    browseable = yes
    read only = no
    writable = yes
    guest ok = yes

    enable privileges = yes
    create mask = 0777
    directory mask = 0777

Настоятельно рекомендуется ознакомиться с руководствами по Samba, настроить её самостоятельно и поэкспериментировать с параметрами. Это позволит нам обнаружить потенциальные аспекты, которые будут интересны нам как специалистам по тестированию на проникновение. Кроме того, чем лучше мы освоим сервер Samba и протокол SMB, тем легче будет ориентироваться в среде и использовать её в своих целях. После того, как мы адаптируемся /etc/samba/smb.confк нашим потребностям, необходимо перезапустить службу на сервере.

Перезапустить Samba
        shellsession
root@samba:~# sudo systemctl restart smbd

Теперь мы можем отобразить список ( -L) общих ресурсов сервера с помощью smbclientкоманды с нашего хоста. Мы используем так называемый null session( -N), который обеспечивает anonymousдоступ без ввода существующих пользователей или действительных паролей.

SMBclient — Подключение к общему ресурсу
        shellsession
MuraMitchi@htb[/htb]$ smbclient -N -L //10.129.14.128

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        home            Disk      INFREIGHT Samba
        dev             Disk      DEVenv
        notes           Disk      CheckIT
        IPC$            IPC       IPC Service (DEVSM)
SMB1 disabled -- no workgroup available

Как видно из результата, на сервере Samba теперь пять различных общих ресурсов. При этом print$, IPC$как мы уже видели, они уже включены по умолчанию в базовые настройки. Поскольку мы работаем с [notes]общим ресурсом, давайте войдем в систему и проверим его с помощью той же клиентской программы. Если мы не знакомы с клиентской программой, мы можем использовать команду helpпри успешном входе в систему, которая выведет список всех возможных команд, которые мы можем выполнить.

        shellsession
MuraMitchi@htb[/htb]$ smbclient //10.129.14.128/notes

Enter WORKGROUP\<username>'s password: 
Anonymous login successful
Try "help" to get a list of possible commands.


smb: \> help

?              allinfo        altname        archive        backup         
blocksize      cancel         case_sensitive cd             chmod          
chown          close          del            deltree        dir            
du             echo           exit           get            getfacl        
geteas         hardlink       help           history        iosize         
lcd            link           lock           lowercase      ls             
l              mask           md             mget           mkdir          
more           mput           newer          notify         open           
posix          posix_encrypt  posix_open     posix_mkdir    posix_rmdir    
posix_unlink   posix_whoami   print          prompt         put            
pwd            q              queue          quit           readlink       
rd             recurse        reget          rename         reput          
rm             rmdir          showacls       setea          setmode        
scopy          stat           symlink        tar            tarmode        
timeout        translate      unlock         volume         vuid           
wdel           logon          listconnect    showconnect    tcon           
tdis           tid            utimes         logoff         ..             
!            


smb: \> ls

  .                                   D        0  Wed Sep 22 18:17:51 2021
  ..                                  D        0  Wed Sep 22 12:03:59 2021
  prep-prod.txt                       N       71  Sun Sep 19 15:45:21 2021

                30313412 blocks of size 1024. 16480084 blocks available

Обнаружив интересные файлы или папки, мы можем загрузить их с помощью getкоманды. Smbclient также позволяет выполнять локальные системные команды, используя восклицательный знак в начале ( !<cmd>), без прерывания соединения.

Скачать файлы с SMB
        shellsession
smb: \> get prep-prod.txt 

getting file \prep-prod.txt of size 71 as prep-prod.txt (8,7 KiloBytes/sec) 
(average 8,7 KiloBytes/sec)


smb: \> !ls

prep-prod.txt


smb: \> !cat prep-prod.txt

[] check your code with the templates
[] run code-assessment.py
[] …

С административной точки зрения, мы можем проверить эти соединения с помощью команды smbstatus`samba install`. Помимо версии Samba, мы также можем увидеть, кто, с какого хоста и к какой общей папке подключен клиент. Это особенно важно, если мы попали в подсеть (возможно, даже изолированную), к которой другие пользователи по-прежнему имеют доступ.

Например, при обеспечении безопасности на уровне домена сервер Samba выступает в качестве члена домена Windows. В каждом домене есть как минимум один контроллер домена, обычно это сервер Windows NT, обеспечивающий аутентификацию по паролю. Этот контроллер домена предоставляет рабочей группе основной сервер паролей. Контроллеры домена хранят информацию о пользователях и паролях в своих собственных базах NTDS.ditданных и аутентифицируют каждого пользователя при первом входе в систему и желании получить доступ к общей папке другого компьютера.Security Authentication ModuleSAM

Статус самбы
        shellsession
root@samba:~# smbstatus

Samba version 4.11.6-Ubuntu
PID     Username     Group        Machine                                   Protocol Version  Encryption           Signing              
----------------------------------------------------------------------------------------------------------------------------------------
75691   sambauser    samba        10.10.14.4 (ipv4:10.10.14.4:45564)      SMB3_11           -                    -                    

Service      pid     Machine       Connected at                     Encryption   Signing     
---------------------------------------------------------------------------------------------
notes        75691   10.10.14.4   Do Sep 23 00:12:06 2021 CEST     -            -           

No locked files

Создание базы данных для сервиса
Вернемся к одному из наших инструментов для перечисления. Nmap также имеет множество опций и скриптов NSE, которые могут помочь нам более детально изучить службу SMB целевого сервера и получить больше информации. Однако недостаток таких сканирований заключается в том, что они могут занять много времени. Поэтому рекомендуется также проверять службу вручную, главным образом потому, что мы можем найти гораздо больше деталей, чем может показать Nmap. Но сначала давайте посмотрим, что Nmap может найти на нашем целевом сервере Samba, где мы создали общий ресурс [notes]для целей тестирования.

Nmap
        shellsession
MuraMitchi@htb[/htb]$ sudo nmap 10.129.14.128 -sV -sC -p139,445

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 15:15 CEST
Nmap scan report for sharing.inlanefreight.htb (10.129.14.128)
Host is up (0.00024s latency).

PORT    STATE SERVICE     VERSION
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
MAC Address: 00:00:00:00:00:00 (VMware)

Host script results:
|_nbstat: NetBIOS name: HTB, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-09-19T13:16:04
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.35 seconds

Как видно из результатов, Nmap предоставил нам не так уж много информации. Поэтому нам следует обратиться к другим инструментам, позволяющим вручную взаимодействовать с SMB и отправлять конкретные запросы на получение информации. Одним из удобных инструментов для этого является rpcclient. Это инструмент для выполнения функций MS-RPC.

Удаленный вызов процедур ( RPC RPC) — это концепция и, следовательно, центральный инструмент для реализации операционных структур и структур распределения работы в сетях и клиент-серверных архитектурах. Процесс связи через RPC включает передачу параметров и возврат значения функции.

RPCclient
        shellsession
MuraMitchi@htb[/htb]$ rpcclient -U "" 10.129.14.128

Enter WORKGROUP\'s password:
rpcclient $>

Он rpcclientпредоставляет нам множество различных запросов, с помощью которых мы можем выполнять определенные функции на SMB-сервере для получения информации. Полный список всех этих функций можно найти в справочной странице rpcclient.

Запрос	Описание
srvinfo	Информация о сервере.
enumdomains	Перечислите все домены, развернутые в сети.
querydominfo	Предоставляет информацию о домене, сервере и пользователе развернутых доменов.
netshareenumall	Перечисляет все доступные акции.
netsharegetinfo <share>	Предоставляет информацию о конкретной акции.
enumdomusers	Перечисляет всех пользователей домена.
queryuser <RID>	Предоставляет информацию о конкретном пользователе.
RPCclient - перечисление
        shellsession
rpcclient $> srvinfo

        DEVSMB         Wk Sv PrQ Unx NT SNT DEVSM
        platform_id     :       500
        os version      :       6.1
        server type     :       0x809a03
        
        
rpcclient $> enumdomains

name:[DEVSMB] idx:[0x0]
name:[Builtin] idx:[0x1]


rpcclient $> querydominfo

Domain:         DEVOPS
Server:         DEVSMB
Comment:        DEVSM
Total Users:    2
Total Groups:   0
Total Aliases:  0
Sequence No:    1632361158
Force Logoff:   -1
Domain Server State:    0x1
Server Role:    ROLE_DOMAIN_PDC
Unknown 3:      0x1


rpcclient $> netshareenumall

netname: print$
        remark: Printer Drivers
        path:   C:\var\lib\samba\printers
        password:
netname: home
        remark: INFREIGHT Samba
        path:   C:\home\
        password:
netname: dev
        remark: DEVenv
        path:   C:\home\sambauser\dev\
        password:
netname: notes
        remark: CheckIT
        path:   C:\mnt\notes\
        password:
netname: IPC$
        remark: IPC Service (DEVSM)
        path:   C:\tmp
        password:
        
        
rpcclient $> netsharegetinfo notes

netname: notes
        remark: CheckIT
        path:   C:\mnt\notes\
        password:
        type:   0x0
        perms:  0
        max_uses:       -1
        num_uses:       1
revision: 1
type: 0x8004: SEC_DESC_DACL_PRESENT SEC_DESC_SELF_RELATIVE 
DACL
        ACL     Num ACEs:       1       revision:       2
        ---
        ACE
                type: ACCESS ALLOWED (0) flags: 0x00 
                Specific bits: 0x1ff
                Permissions: 0x101f01ff: Generic all access SYNCHRONIZE_ACCESS WRITE_OWNER_ACCESS WRITE_DAC_ACCESS READ_CONTROL_ACCESS DELETE_ACCESS 
                SID: S-1-1-0

Эти примеры показывают, какая информация может попасть к анонимным пользователям. Получив anonymousдоступ к сетевому сервису, достаточно одной ошибки, чтобы предоставить ему слишком много прав или слишком большую прозрачность, что поставит под серьезную угрозу всю сеть.

Самое важное, что анонимный доступ к таким сервисам может также привести к обнаружению других пользователей, которых в самом агрессивном случае можно атаковать методом перебора паролей. Люди более склонны к ошибкам, чем правильно настроенные компьютерные процессы, а недостаток осведомленности о безопасности и лень часто приводят к слабым паролям, которые легко взломать. Давайте посмотрим, как мы можем перечислить пользователей, используя rpcclient.

Rpcclient - перечисление пользователей
        shellsession
rpcclient $> enumdomusers

user:[mrb3n] rid:[0x3e8]
user:[cry0l1t3] rid:[0x3e9]


rpcclient $> queryuser 0x3e9

        User Name   :   cry0l1t3
        Full Name   :   cry0l1t3
        Home Drive  :   \\devsmb\cry0l1t3
        Dir Drive   :
        Profile Path:   \\devsmb\cry0l1t3\profile
        Logon Script:
        Description :
        Workstations:
        Comment     :
        Remote Dial :
        Logon Time               :      Do, 01 Jan 1970 01:00:00 CET
        Logoff Time              :      Mi, 06 Feb 2036 16:06:39 CET
        Kickoff Time             :      Mi, 06 Feb 2036 16:06:39 CET
        Password last set Time   :      Mi, 22 Sep 2021 17:50:56 CEST
        Password can change Time :      Mi, 22 Sep 2021 17:50:56 CEST
        Password must change Time:      Do, 14 Sep 30828 04:48:05 CEST
        unknown_2[0..31]...
        user_rid :      0x3e9
        group_rid:      0x201
        acb_info :      0x00000014
        fields_present: 0x00ffffff
        logon_divs:     168
        bad_password_count:     0x00000000
        logon_count:    0x00000000
        padding1[0..7]...
        logon_hrs[0..21]...


rpcclient $> queryuser 0x3e8

        User Name   :   mrb3n
        Full Name   :
        Home Drive  :   \\devsmb\mrb3n
        Dir Drive   :
        Profile Path:   \\devsmb\mrb3n\profile
        Logon Script:
        Description :
        Workstations:
        Comment     :
        Remote Dial :
        Logon Time               :      Do, 01 Jan 1970 01:00:00 CET
        Logoff Time              :      Mi, 06 Feb 2036 16:06:39 CET
        Kickoff Time             :      Mi, 06 Feb 2036 16:06:39 CET
        Password last set Time   :      Mi, 22 Sep 2021 17:47:59 CEST
        Password can change Time :      Mi, 22 Sep 2021 17:47:59 CEST
        Password must change Time:      Do, 14 Sep 30828 04:48:05 CEST
        unknown_2[0..31]...
        user_rid :      0x3e8
        group_rid:      0x201
        acb_info :      0x00000010
        fields_present: 0x00ffffff
        logon_divs:     168
        bad_password_count:     0x00000000
        logon_count:    0x00000000
        padding1[0..7]...
        logon_hrs[0..21]...

Затем мы можем использовать полученные результаты для определения RID группы, который, в свою очередь, позволит нам получить информацию обо всей группе.

Rpcclient - Информация о группе
        shellsession
rpcclient $> querygroup 0x201

        Group Name:     None
        Description:    Ordinary Users
        Group Attribute:7
        Num Members:2

Однако может случиться так, что не все команды будут нам доступны, и у нас будут определенные ограничения в зависимости от пользователя. Тем не менее, запрос queryuser <RID>в основном разрешен на основе RID. Поэтому мы можем использовать rpcclient для перебора RID, чтобы получить информацию. Поскольку мы можем не знать, кому какой RID был назначен, мы знаем, что получим информацию об этом, как только запросим назначенный RID. Для этого существует несколько способов и инструментов. Чтобы оставаться в рамках инструментария, мы можем создать объект, For-loopв Bashкотором мы отправляем команду в службу с помощью rpcclient и фильтруем результаты.

Подбор идентификаторов пользователей методом перебора
        shellsession
MuraMitchi@htb[/htb]$ for i in $(seq 500 1100);do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done

        User Name   :   sambauser
        user_rid :      0x1f5
        group_rid:      0x201
        
        User Name   :   mrb3n
        user_rid :      0x3e8
        group_rid:      0x201
        
        User Name   :   cry0l1t3
        user_rid :      0x3e9
        group_rid:      0x201

В качестве альтернативы можно использовать скрипт на Python от Impacket под названием samrdump.py .

Impacket - Samrdump.py
        shellsession
MuraMitchi@htb[/htb]$ samrdump.py 10.129.14.128

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Retrieving endpoint list from 10.129.14.128
Found domain(s):
 . DEVSMB
 . Builtin
[*] Looking up users in domain DEVSMB
Found user: mrb3n, uid = 1000
Found user: cry0l1t3, uid = 1001
mrb3n (1000)/FullName: 
mrb3n (1000)/UserComment: 
mrb3n (1000)/PrimaryGroupId: 513
mrb3n (1000)/BadPasswordCount: 0
mrb3n (1000)/LogonCount: 0
mrb3n (1000)/PasswordLastSet: 2021-09-22 17:47:59
mrb3n (1000)/PasswordDoesNotExpire: False
mrb3n (1000)/AccountIsDisabled: False
mrb3n (1000)/ScriptPath: 
cry0l1t3 (1001)/FullName: cry0l1t3
cry0l1t3 (1001)/UserComment: 
cry0l1t3 (1001)/PrimaryGroupId: 513
cry0l1t3 (1001)/BadPasswordCount: 0
cry0l1t3 (1001)/LogonCount: 0
cry0l1t3 (1001)/PasswordLastSet: 2021-09-22 17:50:56
cry0l1t3 (1001)/PasswordDoesNotExpire: False
cry0l1t3 (1001)/AccountIsDisabled: False
cry0l1t3 (1001)/ScriptPath: 
[*] Received 2 entries.

Полученную нами информацию rpcclientможно получить и с помощью других инструментов. Например, инструменты SMBMap и CrackMapExec также широко используются и полезны для перечисления служб SMB.

SMBmap
        shellsession
MuraMitchi@htb[/htb]$ smbmap -H 10.129.14.128

[+] Finding open SMB ports....
[+] User SMB session established on 10.129.14.128...
[+] IP: 10.129.14.128:445       Name: 10.129.14.128                                     
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  NO ACCESS       Printer Drivers
        home                                                    NO ACCESS       INFREIGHT Samba
        dev                                                     NO ACCESS       DEVenv
        notes                                                   NO ACCESS       CheckIT
        IPC$                                                    NO ACCESS       IPC Service (DEVSM)

CrackMapExec
        shellsession
MuraMitchi@htb[/htb]$ crackmapexec smb 10.129.14.128 --shares -u '' -p ''

SMB         10.129.14.128   445    DEVSMB           [*] Windows 6.1 Build 0 (name:DEVSMB) (domain:) (signing:False) (SMBv1:False)
SMB         10.129.14.128   445    DEVSMB           [+] \: 
SMB         10.129.14.128   445    DEVSMB           [+] Enumerated shares
SMB         10.129.14.128   445    DEVSMB           Share           Permissions     Remark
SMB         10.129.14.128   445    DEVSMB           -----           -----------     ------
SMB         10.129.14.128   445    DEVSMB           print$                          Printer Drivers
SMB         10.129.14.128   445    DEVSMB           home                            INFREIGHT Samba
SMB         10.129.14.128   445    DEVSMB           dev                             DEVenv
SMB         10.129.14.128   445    DEVSMB           notes           READ,WRITE      CheckIT
SMB         10.129.14.128   445    DEVSMB           IPC$                            IPC Service (DEVSM)

Ещё один инструмент, заслуживающий упоминания, — это так называемый enum4linux-ng , основанный на более старом инструменте enum4linux. Этот инструмент автоматизирует многие запросы, но не все, и может возвращать большой объём информации.

Установка Enum4Linux-ng
        shellsession
MuraMitchi@htb[/htb]$ git clone https://github.com/cddmp/enum4linux-ng.git
MuraMitchi@htb[/htb]$ cd enum4linux-ng
MuraMitchi@htb[/htb]$ pip3 install -r requirements.txt

Enum4Linux-ng - Перечисление
        shellsession
MuraMitchi@htb[/htb]$ ./enum4linux-ng.py 10.129.14.128 -A

ENUM4LINUX - next generation

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.129.14.128
[*] Username ......... ''
[*] Random Username .. 'juzgtcsu'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)

 =====================================
|    Service Scan on 10.129.14.128    |
 =====================================
[*] Checking LDAP
[-] Could not connect to LDAP on 389/tcp: connection refused
[*] Checking LDAPS
[-] Could not connect to LDAPS on 636/tcp: connection refused
[*] Checking SMB
[+] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS
[+] SMB over NetBIOS is accessible on 139/tcp

 =====================================================
|    NetBIOS Names and Workgroup for 10.129.14.128    |
 =====================================================
[+] Got domain/workgroup name: DEVOPS
[+] Full NetBIOS names information:
- DEVSMB          <00> -         H <ACTIVE>  Workstation Service
- DEVSMB          <03> -         H <ACTIVE>  Messenger Service
- DEVSMB          <20> -         H <ACTIVE>  File Server Service
- ..__MSBROWSE__. <01> - <GROUP> H <ACTIVE>  Master Browser
- DEVOPS          <00> - <GROUP> H <ACTIVE>  Domain/Workgroup Name
- DEVOPS          <1d> -         H <ACTIVE>  Master Browser
- DEVOPS          <1e> - <GROUP> H <ACTIVE>  Browser Service Elections
- MAC Address = 00-00-00-00-00-00

 ==========================================
|    SMB Dialect Check on 10.129.14.128    |
 ==========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
SMB 1.0: false
SMB 2.02: true
SMB 2.1: true
SMB 3.0: true
SMB1 only: false
Preferred dialect: SMB 3.0
SMB signing required: false

 ==========================================
|    RPC Session Check on 10.129.14.128    |
 ==========================================
[*] Check for null session
[+] Server allows session using username '', password ''
[*] Check for random user session
[+] Server allows session using username 'juzgtcsu', password ''
[H] Rerunning enumeration with user 'juzgtcsu' might give more results

 ====================================================
|    Domain Information via RPC for 10.129.14.128    |
 ====================================================
[+] Domain: DEVOPS
[+] SID: NULL SID
[+] Host is part of a workgroup (not a domain)

 ============================================================
|    Domain Information via SMB session for 10.129.14.128    |
 ============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: DEVSMB
NetBIOS domain name: ''
DNS domain: ''
FQDN: htb

 ================================================
|    OS Information via RPC for 10.129.14.128    |
 ================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[+] Found OS information via 'srvinfo'
[+] After merging OS information we have the following result:
OS: Windows 7, Windows Server 2008 R2
OS version: '6.1'
OS release: ''
OS build: '0'
Native OS: not supported
Native LAN manager: not supported
Platform id: '500'
Server type: '0x809a03'
Server type string: Wk Sv PrQ Unx NT SNT DEVSM

 ======================================
|    Users via RPC on 10.129.14.128    |
 ======================================
[*] Enumerating users via 'querydispinfo'
[+] Found 2 users via 'querydispinfo'
[*] Enumerating users via 'enumdomusers'
[+] Found 2 users via 'enumdomusers'
[+] After merging user results we have 2 users total:
'1000':
  username: mrb3n
  name: ''
  acb: '0x00000010'
  description: ''
'1001':
  username: cry0l1t3
  name: cry0l1t3
  acb: '0x00000014'
  description: ''

 =======================================
|    Groups via RPC on 10.129.14.128    |
 =======================================
[*] Enumerating local groups
[+] Found 0 group(s) via 'enumalsgroups domain'
[*] Enumerating builtin groups
[+] Found 0 group(s) via 'enumalsgroups builtin'
[*] Enumerating domain groups
[+] Found 0 group(s) via 'enumdomgroups'

 =======================================
|    Shares via RPC on 10.129.14.128    |
 =======================================
[*] Enumerating shares
[+] Found 5 share(s):
IPC$:
  comment: IPC Service (DEVSM)
  type: IPC
dev:
  comment: DEVenv
  type: Disk
home:
  comment: INFREIGHT Samba
  type: Disk
notes:
  comment: CheckIT
  type: Disk
print$:
  comment: Printer Drivers
  type: Disk
[*] Testing share IPC$
[-] Could not check share: STATUS_OBJECT_NAME_NOT_FOUND
[*] Testing share dev
[-] Share doesn't exist
[*] Testing share home
[+] Mapping: OK, Listing: OK
[*] Testing share notes
[+] Mapping: OK, Listing: OK
[*] Testing share print$
[+] Mapping: DENIED, Listing: N/A

 ==========================================
|    Policies via RPC for 10.129.14.128    |
 ==========================================
[*] Trying port 445/tcp
[+] Found policy:
domain_password_information:
  pw_history_length: None
  min_pw_length: 5
  min_pw_age: none
  max_pw_age: 49710 days 6 hours 21 minutes
  pw_properties:
  - DOMAIN_PASSWORD_COMPLEX: false
  - DOMAIN_PASSWORD_NO_ANON_CHANGE: false
  - DOMAIN_PASSWORD_NO_CLEAR_CHANGE: false
  - DOMAIN_PASSWORD_LOCKOUT_ADMINS: false
  - DOMAIN_PASSWORD_PASSWORD_STORE_CLEARTEXT: false
  - DOMAIN_PASSWORD_REFUSE_PASSWORD_CHANGE: false
domain_lockout_information:
  lockout_observation_window: 30 minutes
  lockout_duration: 30 minutes
  lockout_threshold: None
domain_logoff_information:
  force_logoff_time: 49710 days 6 hours 21 minutes

 ==========================================
|    Printers via RPC for 10.129.14.128    |
 ==========================================
[+] No printers returned (this is not an error)

Completed after 0.61 seconds

Для перечисления нам необходимо использовать более двух инструментов. Потому что из-за особенностей программирования этих инструментов может получиться разная информация, которую придется проверять вручную. Поэтому никогда не следует полагаться только на автоматизированные инструменты, если мы точно не знаем, как они были написаны.