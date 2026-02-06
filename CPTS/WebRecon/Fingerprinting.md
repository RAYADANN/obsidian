Идентификация по цифровым сигнатурам (fingerprinting) фокусируется на извлечении технических деталей о технологиях, лежащих в основе веб-сайта или веб-приложения. Подобно тому, как отпечаток пальца однозначно идентифицирует человека, цифровые сигнатуры веб-серверов, операционных систем и программных компонентов могут раскрыть важную информацию об инфраструктуре цели и потенциальных уязвимостях в системе безопасности. Эти знания позволяют злоумышленникам адаптировать атаки и использовать уязвимости, специфичные для идентифицированных технологий.

Идентификация по отпечаткам пальцев является краеугольным камнем веб-разведки по нескольким причинам:

- `Targeted Attacks`Зная конкретные используемые технологии, злоумышленники могут сосредоточить свои усилия на эксплойтах и ​​уязвимостях, которые, как известно, затрагивают эти системы. Это значительно повышает шансы на успешное взлом.
- `Identifying Misconfigurations`Метод «снятия отпечатков пальцев» позволяет выявить неправильно настроенное или устаревшее программное обеспечение, настройки по умолчанию или другие уязвимости, которые могут быть незаметны при использовании других методов разведки.
- `Prioritising Targets`При наличии нескольких потенциальных целей идентификация по отпечаткам пальцев помогает расставить приоритеты, выявляя системы, которые с большей вероятностью могут быть уязвимы или содержать ценную информацию.
- `Building a Comprehensive Profile`Сочетание данных отпечатков пальцев с другими результатами разведки позволяет получить целостное представление об инфраструктуре цели, что помогает понять ее общую безопасность и потенциальные векторы атак.

## Методы дактилоскопии

Для идентификации веб-серверов и используемых технологий применяется несколько методов:

- `Banner Grabbing`Анализ баннеров включает в себя анализ баннеров, отображаемых веб-серверами и другими сервисами. Эти баннеры часто раскрывают информацию о программном обеспечении сервера, номерах версий и других деталях.
- `Analysing HTTP Headers`HTTP-заголовки, передаваемые с каждым запросом и ответом веб-страницы, содержат массу информации. `Server`Заголовок обычно раскрывает программное обеспечение веб-сервера, а также `X-Powered-By`может раскрывать информацию о дополнительных технологиях, таких как языки сценариев или фреймворки.
- `Probing for Specific Responses`Отправка специально сформированных запросов целевому объекту может вызвать уникальные ответы, раскрывающие особенности конкретных технологий или версий. Например, определенные сообщения об ошибках или особенности поведения характерны для определенных веб-серверов или программных компонентов.
- `Analysing Page Content`Содержимое веб-страницы, включая ее структуру, скрипты и другие элементы, часто может дать подсказки о лежащих в ее основе технологиях. Например, там может быть заголовок с указанием авторских прав, который говорит об использовании конкретного программного обеспечения.

Существует множество инструментов, автоматизирующих процесс снятия отпечатков пальцев, сочетающих различные методы для идентификации веб-серверов, операционных систем, систем управления контентом и других технологий:

|Инструмент|Описание|Функции|
|---|---|---|
|`Wappalyzer`|Расширение для браузера и онлайн-сервис для технологического профилирования веб-сайтов.|Определяет широкий спектр веб-технологий, включая CMS, фреймворки, аналитические инструменты и многое другое.|
|`BuiltWith`|Инструмент для анализа веб-технологий, предоставляющий подробные отчеты о технологическом стеке веб-сайта.|Предлагает как бесплатные, так и платные тарифные планы с различным уровнем детализации.|
|`WhatWeb`|Инструмент командной строки для идентификации веб-сайтов по их внешнему виду.|Использует обширную базу данных сигнатур для идентификации различных веб-технологий.|
|`Nmap`|Универсальный сетевой сканер, который можно использовать для различных задач разведки, включая определение типа сервиса и операционной системы.|Может использоваться со скриптами (NSE) для выполнения более специализированной дактилоскопии.|
|`Netcraft`|Предлагает широкий спектр услуг по обеспечению безопасности веб-сайтов, включая идентификацию веб-сайтов и составление отчетов о безопасности.|Предоставляет подробные отчеты о технологиях веб-сайта, поставщике хостинга и состоянии его безопасности.|
|`wafw00f`|Инструмент командной строки, специально разработанный для идентификации межсетевых экранов веб-приложений (WAF).|Помогает определить, присутствует ли WAF, и если да, то его тип и конфигурацию.|

## Снятие отпечатков пальцев inlanefreight.com

Давайте применим наши знания в области дактилоскопии, чтобы раскрыть цифровую ДНК нашего специально созданного хоста `inlanefreight.com`. Мы будем использовать как ручные, так и автоматизированные методы для сбора информации о его веб-сервере, технологиях и потенциальных уязвимостях.

### Захват баннера

Наш первый шаг — получить информацию непосредственно с самого веб-сервера. Мы можем сделать это с помощью `curl`команды с `-I`флагом (или `--head`), чтобы получить только HTTP-заголовки, а не всё содержимое страницы.

        shellsession
`MuraMitchi@htb[/htb]$ curl -I inlanefreight.com`

В результате будет отображен баннер сервера, показывающий программное обеспечение веб-сервера и номер его версии:

        shellsession
`MuraMitchi@htb[/htb]$ curl -I inlanefreight.com HTTP/1.1 301 Moved Permanently Date: Fri, 31 May 2024 12:07:44 GMT Server: Apache/2.4.41 (Ubuntu) Location: https://inlanefreight.com/ Content-Type: text/html; charset=iso-8859-1`

В данном случае мы видим, что `inlanefreight.com`он работает на `Apache/2.4.41`[указать `Ubuntu`версию]. Эта информация — наша первая подсказка, указывающая на используемый технологический стек. Он также пытается перенаправить на [указать адрес], `https://inlanefreight.com/`поэтому загрузите и эти баннеры.

        shellsession
`MuraMitchi@htb[/htb]$ curl -I https://inlanefreight.com HTTP/1.1 301 Moved Permanently Date: Fri, 31 May 2024 12:12:12 GMT Server: Apache/2.4.41 (Ubuntu) X-Redirect-By: WordPress Location: https://www.inlanefreight.com/ Content-Type: text/html; charset=UTF-8`

Теперь мы получаем действительно интересный заголовок: сервер снова пытается нас перенаправить, но на этот раз мы видим, что `WordPress`перенаправление осуществляет именно он.`https://www.inlanefreight.com/`

        shellsession
`MuraMitchi@htb[/htb]$ curl -I https://www.inlanefreight.com HTTP/1.1 200 OK Date: Fri, 31 May 2024 12:12:26 GMT Server: Apache/2.4.41 (Ubuntu) Link: <https://www.inlanefreight.com/index.php/wp-json/>; rel="https://api.w.org/" Link: <https://www.inlanefreight.com/index.php/wp-json/wp/v2/pages/7>; rel="alternate"; type="application/json" Link: <https://www.inlanefreight.com/>; rel=shortlink Content-Type: text/html; charset=UTF-8`

Ещё несколько интересных заголовков, включая интересный путь, содержащий `wp-json`. Этот `wp-`префикс распространён в WordPress.

### Wafw00f

`Web Application Firewalls`( `WAFs`) — это решения в области безопасности, предназначенные для защиты веб-приложений от различных атак. Прежде чем приступать к дальнейшей идентификации, крайне важно определить, `inlanefreight.com`использует ли он WAF, поскольку это может помешать нашим проверкам или потенциально заблокировать наши запросы.

Для обнаружения наличия WAF мы воспользуемся `wafw00f`инструментом. Для его установки `wafw00f`можно использовать pip3:

        shellsession
`MuraMitchi@htb[/htb]$ pip3 install git+https://github.com/EnableSecurity/wafw00f`

После установки передайте инструменту в качестве аргумента домен, который вы хотите проверить:

        shellsession
```MuraMitchi@htb[/htb]$ wafw00f inlanefreight.com                 ______               /      \              (  W00f! )               \  ____/               ,,    __            404 Hack Not Found           |`-.__   / /                      __     __           /"  _/  /_/                       \ \   / /          *===*    /                          \ \_/ /  405 Not Allowed         /     )__//                           \   /    /|  /     /---`                        403 Forbidden    \\/`   \ |                                 / _ \    `\    /_\\_              502 Bad Gateway  / / \ \  500 Internal Error      `_____``-`                             /_/   \_\                         ~ WAFW00F : v2.2.0 ~        The Web Application Firewall Fingerprinting Toolkit     [*] Checking https://inlanefreight.com [+] The site https://inlanefreight.com is behind Wordfence (Defiant) WAF. [~] Number of requests: 2```

Сканирование `wafw00f`показывает `inlanefreight.com`, что веб-сайт защищен `Wordfence Web Application Firewall`( `WAF`), разработанным компанией Defiant.

Это означает, что на сайте есть дополнительный уровень безопасности, который может блокировать или фильтровать наши попытки разведки. В реальных условиях крайне важно помнить об этом при проведении дальнейшего расследования, поскольку вам может потребоваться адаптировать методы для обхода или избежания механизмов обнаружения WAF.

### Никто

`Nikto`Это мощный сканер веб-серверов с открытым исходным кодом. Помимо своей основной функции в качестве инструмента оценки уязвимостей, `Nikto's`возможности создания «цифровых отпечатков» позволяют получить представление о технологическом стеке веб-сайта.

`Nikto`pwnbox предустановлен, но если вам нужно его установить, вы можете выполнить следующие команды:

        shellsession
`MuraMitchi@htb[/htb]$ sudo apt update && sudo apt install -y perl MuraMitchi@htb[/htb]$ git clone https://github.com/sullo/nikto MuraMitchi@htb[/htb]$ cd nikto/program MuraMitchi@htb[/htb]$ chmod +x ./nikto.pl`

Для сканирования `inlanefreight.com`с использованием `Nikto`только модулей идентификации по отпечаткам пальцев выполните следующую команду:

        shellsession
`MuraMitchi@htb[/htb]$ nikto -h inlanefreight.com -Tuning b`

Этот `-h`флаг указывает на целевой хост. `-Tuning b`Флаг указывает `Nikto`на необходимость запуска только модулей идентификации программного обеспечения.

`Nikto`Затем будет запущена серия тестов, направленных на выявление устаревшего программного обеспечения, небезопасных файлов или конфигураций, а также других потенциальных угроз безопасности.

        shellsession
`MuraMitchi@htb[/htb]$ nikto -h inlanefreight.com -Tuning b - Nikto v2.5.0 --------------------------------------------------------------------------- + Multiple IPs found: 134.209.24.248, 2a03:b0c0:1:e0::32c:b001 + Target IP:          134.209.24.248 + Target Hostname:    www.inlanefreight.com + Target Port:        443 --------------------------------------------------------------------------- + SSL Info:        Subject:  /CN=inlanefreight.com                    Altnames: inlanefreight.com, www.inlanefreight.com                   Ciphers:  TLS_AES_256_GCM_SHA384                   Issuer:   /C=US/O=Let's Encrypt/CN=R3 + Start Time:         2024-05-31 13:35:54 (GMT0) --------------------------------------------------------------------------- + Server: Apache/2.4.41 (Ubuntu) + /: Link header found with value: ARRAY(0x558e78790248). See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Link + /: The site uses TLS and the Strict-Transport-Security HTTP header is not defined. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security + /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/ + /index.php?: Uncommon header 'x-redirect-by' found, with contents: WordPress. + No CGI Directories found (use '-C all' to force check all possible dirs) + /: The Content-Encoding header is set to "deflate" which may mean that the server is vulnerable to the BREACH attack. See: http://breachattack.com/ + Apache/2.4.41 appears to be outdated (current is at least 2.4.59). Apache 2.2.34 is the EOL for the 2.x branch. + /: Web Server returns a valid response with junk HTTP methods which may cause false positives. + /license.txt: License file found may identify site software. + /: A Wordpress installation was found. + /wp-login.php?action=register: Cookie wordpress_test_cookie created without the httponly flag. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies + /wp-login.php:X-Frame-Options header is deprecated and has been replaced with the Content-Security-Policy HTTP header with the frame-ancestors directive instead. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options + /wp-login.php: Wordpress login found. + 1316 requests: 0 error(s) and 12 item(s) reported on remote host + End Time:           2024-05-31 13:47:27 (GMT0) (693 seconds) --------------------------------------------------------------------------- + 1 host(s) tested`

В ходе разведывательного сканирования `inlanefreight.com`были получены несколько ключевых результатов:

- `IPs`Веб-сайт разрешается как по IPv4 ( `134.209.24.248`), так и `2a03:b0c0:1:e0::32c:b001`по IPv6 ( ).
- `Server Technology`Сайт работает на`Apache/2.4.41 (Ubuntu)`
- `WordPress Presence`В результате сканирования была обнаружена установка WordPress, включая страницу входа ( `/wp-login.php`). Это позволяет предположить, что сайт может стать потенциальной целью для распространенных уязвимостей, связанных с WordPress.
- `Information Disclosure`Наличие файла `license.txt`может раскрыть дополнительные сведения о программных компонентах веб-сайта.
- `Headers`Было обнаружено несколько нестандартных или небезопасных заголовков, включая отсутствующий `Strict-Transport-Security`заголовок и потенциально небезопасный `x-redirect-by`заголовок.