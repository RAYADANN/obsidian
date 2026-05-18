Система [доменных имен](https://www.cloudflare.com/learning/dns/what-is-dns/) (DNS `DNS`) преобразует доменные имена (например, hackthebox.com) в числовые IP-адреса (например, 104.17.42.72). DNS в основном использует протоколы UDP `UDP/53`, но со временем DNS будет все больше полагаться на протокол `TCP/53`TCP. DNS изначально разрабатывался для использования как UDP, так и TCP-порта 53, при этом UDP является протоколом по умолчанию, и переключается на TCP, когда не удается установить связь по UDP, обычно когда размер пакета слишком велик для передачи в одном UDP-пакете. Поскольку почти все сетевые приложения используют DNS, атаки на DNS-серверы представляют собой одну из наиболее распространенных и значительных угроз сегодня.

---

## Перечисление

DNS содержит важную информацию для организации. Как обсуждалось в разделе «Информация о домене» модуля « [Сбор](https://academy.hackthebox.com/course/preview/footprinting) информации о доменах », мы можем понять, как работает компания и какие услуги она предоставляет, а также информацию о сторонних поставщиках услуг, таких как электронная почта.

Параметры Nmap `-sC`(скрипты по умолчанию) и `-sV`(сканирование версий) можно использовать для выполнения первоначального перечисления целевых DNS-серверов:

        shellsession
`MuraMitchi@htb[/htb]# nmap -p53 -Pn -sV -sC 10.10.110.213 Starting Nmap 7.80 ( https://nmap.org ) at 2020-10-29 03:47 EDT Nmap scan report for 10.10.110.213 Host is up (0.017s latency). PORT    STATE  SERVICE     VERSION 53/tcp  open   domain      ISC BIND 9.11.3-1ubuntu1.2 (Ubuntu Linux)`

---

## Перенос зоны DNS

Зона DNS — это часть пространства имен DNS, которой управляет конкретная организация или администратор. Поскольку DNS включает в себя несколько зон DNS, DNS-серверы используют передачу зон DNS для копирования части своей базы данных на другой DNS-сервер. Если DNS-сервер не настроен должным образом (ограничение IP-адресов, которые могут выполнять передачу зоны DNS), любой может запросить у DNS-сервера копию информации о его зоне, поскольку передача зон DNS не требует аутентификации. Кроме того, служба DNS обычно работает на порту UDP; однако при передаче зоны DNS она использует порт TCP для надежной передачи данных.

Злоумышленник может использовать эту уязвимость передачи DNS-зоны, чтобы получить больше информации о пространстве имен DNS целевой организации, увеличивая поверхность атаки. Для эксплуатации можно использовать утилиту `dig`с опцией типа запроса DNS `AXFR`, чтобы получить дамп всех пространств имен DNS с уязвимого DNS-сервера:

#### DIG - AXFR Zone Transfer

        shellsession
`MuraMitchi@htb[/htb]# dig AXFR @ns1.inlanefreight.htb inlanefreight.htb ; <<>> DiG 9.11.5-P1-1-Debian <<>> axfr inlanefrieght.htb @10.129.110.213 ;; global options: +cmd inlanefrieght.htb.         604800  IN      SOA     localhost. root.localhost. 2 604800 86400 2419200 604800 inlanefrieght.htb.         604800  IN      AAAA    ::1 inlanefrieght.htb.         604800  IN      NS      localhost. inlanefrieght.htb.         604800  IN      A       10.129.110.22 admin.inlanefrieght.htb.   604800  IN      A       10.129.110.21 hr.inlanefrieght.htb.      604800  IN      A       10.129.110.25 support.inlanefrieght.htb. 604800  IN      A       10.129.110.28 inlanefrieght.htb.         604800  IN      SOA     localhost. root.localhost. 2 604800 86400 2419200 604800 ;; Query time: 28 msec ;; SERVER: 10.129.110.213#53(10.129.110.213) ;; WHEN: Mon Oct 11 17:20:13 EDT 2020 ;; XFR size: 8 records (messages 1, bytes 289)`

Такие инструменты, как [Fierce,](https://github.com/mschwager/fierce) также можно использовать для перечисления всех DNS-серверов корневого домена и сканирования на предмет передачи DNS-зоны:

        shellsession
`MuraMitchi@htb[/htb]# fierce --domain zonetransfer.me NS: nsztm2.digi.ninja. nsztm1.digi.ninja. SOA: nsztm1.digi.ninja. (81.4.108.41) Zone: success {<DNS name @>: '@ 7200 IN SOA nsztm1.digi.ninja. robin.digi.ninja. 2019100801 '                '172800 900 1209600 3600\n'               '@ 300 IN HINFO "Casio fx-700G" "Windows XP"\n'               '@ 301 IN TXT '               '"google-site-verification=tyP28J7JAUHA9fw2sHXMgcCC0I6XBmmoVi04VlMewxA"\n'               '@ 7200 IN MX 0 ASPMX.L.GOOGLE.COM.\n'               '@ 7200 IN MX 10 ALT1.ASPMX.L.GOOGLE.COM.\n'               '@ 7200 IN MX 10 ALT2.ASPMX.L.GOOGLE.COM.\n'               '@ 7200 IN MX 20 ASPMX2.GOOGLEMAIL.COM.\n'               '@ 7200 IN MX 20 ASPMX3.GOOGLEMAIL.COM.\n'               '@ 7200 IN MX 20 ASPMX4.GOOGLEMAIL.COM.\n'               '@ 7200 IN MX 20 ASPMX5.GOOGLEMAIL.COM.\n'               '@ 7200 IN A 5.196.105.14\n'               '@ 7200 IN NS nsztm1.digi.ninja.\n'               '@ 7200 IN NS nsztm2.digi.ninja.', <DNS name _acme-challenge>: '_acme-challenge 301 IN TXT '                             '"6Oa05hbUJ9xSsvYy7pApQvwCUSSGgxvrbdizjePEsZI"', <DNS name _sip._tcp>: '_sip._tcp 14000 IN SRV 0 0 5060 www', <DNS name 14.105.196.5.IN-ADDR.ARPA>: '14.105.196.5.IN-ADDR.ARPA 7200 IN PTR '                                       'www', <DNS name asfdbauthdns>: 'asfdbauthdns 7900 IN AFSDB 1 asfdbbox', <DNS name asfdbbox>: 'asfdbbox 7200 IN A 127.0.0.1', <DNS name asfdbvolume>: 'asfdbvolume 7800 IN AFSDB 1 asfdbbox', <DNS name canberra-office>: 'canberra-office 7200 IN A 202.14.81.230', <DNS name cmdexec>: 'cmdexec 300 IN TXT "; ls"', <DNS name contact>: 'contact 2592000 IN TXT "Remember to call or email Pippa '                     'on +44 123 4567890 or pippa@zonetransfer.me when making '                     'DNS changes"', <DNS name dc-office>: 'dc-office 7200 IN A 143.228.181.132', <DNS name deadbeef>: 'deadbeef 7201 IN AAAA dead:beaf::', <DNS name dr>: 'dr 300 IN LOC 53 20 56.558 N 1 38 33.526 W 0.00m', <DNS name DZC>: 'DZC 7200 IN TXT "AbCdEfG"', <DNS name email>: 'email 2222 IN NAPTR 1 1 "P" "E2U+email" "" '                   'email.zonetransfer.me\n'                   'email 7200 IN A 74.125.206.26', <DNS name Hello>: 'Hello 7200 IN TXT "Hi to Josh and all his class"', <DNS name home>: 'home 7200 IN A 127.0.0.1', <DNS name Info>: 'Info 7200 IN TXT "ZoneTransfer.me service provided by Robin '                  'Wood - robin@digi.ninja. See '                  'http://digi.ninja/projects/zonetransferme.php for more '                  'information."', <DNS name internal>: 'internal 300 IN NS intns1\ninternal 300 IN NS intns2', <DNS name intns1>: 'intns1 300 IN A 81.4.108.41', <DNS name intns2>: 'intns2 300 IN A 167.88.42.94', <DNS name office>: 'office 7200 IN A 4.23.39.254', <DNS name ipv6actnow.org>: 'ipv6actnow.org 7200 IN AAAA '                            '2001:67c:2e8:11::c100:1332', ...SNIP...`

---

## Захват доменов и перечисление поддоменов

`Domain takeover`Это регистрация несуществующего доменного имени с целью получения контроля над другим доменом. Если злоумышленники обнаружат просроченный домен, они могут заявить на него права для проведения дальнейших атак, таких как размещение вредоносного контента на веб-сайте или рассылка фишинговых писем с использованием захваченного домена.

Захват домена также возможен с помощью поддоменов `subdomain takeover`. Запись канонического имени DNS (CDN `CNAME`) используется для сопоставления различных доменов с родительским доменом. Многие организации используют сторонние сервисы, такие как AWS, GitHub, Akamai, Fastly и другие сети доставки контента (CDN), для размещения своего контента. В этом случае они обычно создают поддомен и настраивают его на использование этих сервисов. Например,

        shellsession
`sub.target.com.   60   IN   CNAME   anotherdomain.com`

Доменное имя (например, `sub.target.com`) использует запись CNAME для связи с другим доменом (например, `anotherdomain.com`). Предположим, что срок `anotherdomain.com`действия записи CNAME истекает, и домен становится доступен для всех желающих, поскольку `target.com`DNS-сервер домена имеет эту `CNAME`запись. В этом случае любой, кто зарегистрирует домен , `anotherdomain.com`будет иметь полный контроль над доменом `sub.target.com`до тех пор, пока запись DNS не будет обновлена.

#### Перечисление поддоменов

Перед захватом поддомена следует перечислить поддомены целевого домена с помощью таких инструментов, как [Subfinder](https://github.com/projectdiscovery/subfinder) . Этот инструмент может собирать данные о поддоменах из открытых источников, таких как [DNSdumpster](https://dnsdumpster.com/) . Для перебора поддоменов можно также использовать другие инструменты, например [Sublist3r , предоставив предварительно сгенерированный список слов:](https://github.com/aboul3la/Sublist3r)

        shellsession
`MuraMitchi@htb[/htb]# ./subfinder -d inlanefreight.com -v                                                                                        _     __ _         _                                            ____  _| |__ / _(_)_ _  __| |___ _ _           (_-< || | '_ \  _| | ' \/ _  / -_) '_|                  /__/\_,_|_.__/_| |_|_||_\__,_\___|_| v2.4.5                                                                                                                                                                                                                                                                  projectdiscovery.io                                                                        [WRN] Use with caution. You are responsible for your actions [WRN] Developers assume no liability and are not responsible for any misuse or damage. [WRN] By using subfinder, you also agree to the terms of the APIs used.                                      [INF] Enumerating subdomains for inlanefreight.com [alienvault] www.inlanefreight.com [dnsdumpster] ns1.inlanefreight.com [dnsdumpster] ns2.inlanefreight.com ...snip... [bufferover] Source took 2.193235338s for enumeration ns2.inlanefreight.com www.inlanefreight.com ns1.inlanefreight.com support.inlanefreight.com [INF] Found 4 subdomains for inlanefreight.com in 20 seconds 11 milliseconds`

Отличной альтернативой является инструмент под названием [Subbrute](https://github.com/TheRook/subbrute) . Этот инструмент позволяет использовать самостоятельно определяемые DNS-серверы и проводить атаки методом перебора DNS во время внутренних тестов на проникновение на хостах, не имеющих доступа в Интернет.

#### Саббрут

        shellsession
`MuraMitchi@htb[/htb]$ git clone https://github.com/TheRook/subbrute.git >> /dev/null 2>&1 MuraMitchi@htb[/htb]$ cd subbrute MuraMitchi@htb[/htb]$ echo "ns1.inlanefreight.com" > ./resolvers.txt MuraMitchi@htb[/htb]$ ./subbrute.py inlanefreight.com -s ./names.txt -r ./resolvers.txt Warning: Fewer than 16 resolvers per process, consider adding more nameservers to resolvers.txt. inlanefreight.com ns2.inlanefreight.com www.inlanefreight.com ms1.inlanefreight.com support.inlanefreight.com <SNIP>`

Иногда внутренние физические конфигурации плохо защищены, что может быть использовано для загрузки наших инструментов с USB-накопителя. Другой сценарий — это когда мы получили доступ к внутреннему хосту через механизм переадресации и хотим работать оттуда. Конечно, есть и другие альтернативы, но не помешает знать альтернативные способы и возможности.

Инструмент обнаружил четыре поддомена, связанных с `inlanefreight.com`. Используя команду `nslookup`или `host`, мы можем перечислить `CNAME`записи для этих поддоменов.

        shellsession
`MuraMitchi@htb[/htb]# host support.inlanefreight.com support.inlanefreight.com is an alias for inlanefreight.s3.amazonaws.com`

Поддомен `support`имеет запись псевдонима, указывающую на корзину AWS S3. Однако URL-адрес `https://support.inlanefreight.com`выдает `NoSuchBucket`ошибку, указывающую на потенциальную уязвимость поддомена к захвату. Теперь мы можем захватить поддомен, создав корзину AWS S3 с тем же именем поддомена.

![Ошибка XML: NoSuchBucket. Сообщение: Указанный сегмент 'inlanefreight' не существует. Включает RequestId и HostId.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/s3.png)

Репозиторий [can-i-take-over-xyz](https://github.com/EdOverflow/can-i-take-over-xyz) также является отличным источником информации об уязвимости, связанной с захватом поддоменов. Он показывает, уязвимы ли целевые сервисы к захвату поддоменов, и предоставляет рекомендации по оценке такой уязвимости.

---

## Подмена DNS

Подмена DNS-записей также называется отравлением DNS-кэша. Эта атака заключается в изменении легитимных DNS-записей с помощью ложной информации, чтобы их можно было использовать для перенаправления онлайн-трафика на мошеннический веб-сайт. Примеры путей атаки с отравлением DNS-кэша следующие:

- Злоумышленник может перехватить обмен данными между пользователем и DNS-сервером, чтобы перенаправить пользователя на мошеннический ресурс вместо легитимного, выполнив `MITM`атаку типа «человек посередине» (Man-in-the-Middle, ).
- Использование уязвимости, обнаруженной в DNS-сервере, может привести к тому, что злоумышленник получит контроль над сервером и сможет изменять DNS-записи.

#### Отравление локального DNS-кэша

С точки зрения локальной сети, злоумышленник также может выполнить отравление DNS-кэша, используя инструменты MITM, такие как [Ettercap](https://www.ettercap-project.org/) или [Bettercap](https://www.bettercap.org/) .

Для использования уязвимости отравления DNS-кэша с помощью `Ettercap`, сначала следует отредактировать `/etc/ettercap/etter.dns`файл, чтобы сопоставить целевое доменное имя (например, `inlanefreight.com`), которое они хотят подделать, и IP-адрес злоумышленника (например, `192.168.225.110`), на который они хотят перенаправить пользователя:

        shellsession
`MuraMitchi@htb[/htb]# cat /etc/ettercap/etter.dns inlanefreight.com      A   192.168.225.110 *.inlanefreight.com    A   192.168.225.110`

Далее запустите `Ettercap`инструмент и выполните сканирование сети на наличие активных хостов, перейдя по адресу `Hosts > Scan for Hosts`. После завершения добавьте целевой IP-адрес (например, `192.168.152.129`) в Target1 и добавьте IP-адрес шлюза по умолчанию (например, `192.168.152.2`) в Target2.

![Интерфейс Ettercap отображает список хостов с IP- и MAC-адресами. Выделенная запись: IP 192.168.152.129, MAC 00:0C:29:A7:9D:13. Параметры для удаления или добавления хоста к целевым объектам.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/target.png)

Активируйте `dns_spoof`атаку, перейдя по адресу `Plugins > Manage Plugins`. Это отправит на целевой компьютер поддельные DNS-ответы, которые будут разрешаться `inlanefreight.com`в IP-адрес `192.168.225.110`:

![В списке плагинов Ettercap выделена версия dns_spoof 1.3. Информация: Отправляет поддельные DNS-ответы. Хост 192.168.152.129 добавлен в TARGET1.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/etter_plug.png)

После успешной атаки с подменой DNS, если пользователь-жертва с целевого компьютера `192.168.152.129`посетит `inlanefreight.com`домен в веб-браузере, он будет перенаправлен на сайт `Fake page`, размещенный по IP-адресу `192.168.225.110`:

![В окне браузера отображается URL-адрес 'http://inlanefreight.com/' с текстом 'Поддельная страница' на пустой веб-странице.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/116/etter_site.png)

Кроме того, пинг, отправленный с целевого IP-адреса `192.168.152.129`на указанный адрес, также `inlanefreight.com`должен быть разрешен в сторону :`192.168.225.110`

        cmd-session
`C:\>ping inlanefreight.com Pinging inlanefreight.com [192.168.225.110] with 32 bytes of data: Reply from 192.168.225.110: bytes=32 time<1ms TTL=64 Reply from 192.168.225.110: bytes=32 time<1ms TTL=64 Reply from 192.168.225.110: bytes=32 time<1ms TTL=64 Reply from 192.168.225.110: bytes=32 time<1ms TTL=64 Ping statistics for 192.168.225.110:     Packets: Sent = 4, Received = 4, Lost = 0 (0% loss), Approximate round trip times in milli-seconds:     Minimum = 0ms, Maximum = 0ms, Average = 0ms`

Это лишь несколько примеров распространенных DNS-атак. Существуют и другие, более сложные атаки, которые будут рассмотрены в последующих модулях.