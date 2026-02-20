Плагины — это уже доступное программное обеспечение, выпущенное сторонними разработчиками, которые дали разрешение создателям Metasploit на интеграцию своего ПО в эту платформу. Это могут быть коммерческие продукты, доступные `Community Edition`для бесплатного использования, но с ограниченной функциональностью, или же отдельные проекты, разработанные отдельными людьми.

Использование плагинов значительно упрощает работу пентестера, интегрируя функциональность известного программного обеспечения в `msfconsole`среду Metasploit Pro. Если раньше приходилось переключаться между различными программами для импорта и экспорта результатов, постоянно настраивая параметры, то теперь, благодаря плагинам, всё автоматически документируется msfconsole в используемую базу данных, и информация о хостах, службах и уязвимостях становится доступна пользователю с первого взгляда. Плагины [работают](https://web.archive.org/web/20240302133153/https://www.rubydoc.info/github/rapid7/metasploit-framework/Msf/Plugin) напрямую с API и могут использоваться для управления всей системой. Они могут быть полезны для автоматизации повторяющихся задач, добавления новых команд `msfconsole`и расширения уже мощной системы.

---

## Использование плагинов

Чтобы начать использовать плагин, нам нужно убедиться, что он установлен в правильную директорию на нашем компьютере. Перейдя в `/usr/share/metasploit-framework/plugins`директорию `/etc/ `msfconsole`plugin ...

  Плагины

```shell-session
MuraMitchi@htb[/htb]$ ls /usr/share/metasploit-framework/plugins

aggregator.rb      beholder.rb        event_tester.rb  komand.rb     msfd.rb    nexpose.rb   request.rb  session_notifier.rb  sounds.rb  token_adduser.rb  wmap.rb
alias.rb           db_credcollect.rb  ffautoregen.rb   lab.rb        msgrpc.rb  openvas.rb   rssfeed.rb  session_tagger.rb    sqlmap.rb  token_hunter.rb
auto_add_route.rb  db_tracker.rb      ips_filter.rb    libnotify.rb  nessus.rb  pcap_log.rb  sample.rb   socket_logger.rb     thread.rb  wiki.rb
```

Если плагин будет найден, мы можем запустить его внутри `msfconsole`, и в ответ получим приветственное сообщение для этого конкретного плагина, сигнализирующее о его успешной загрузке и готовности к использованию:

#### MSF - Загрузка Нессуса

  Плагины

```shell-session
msf6 > load nessus

[*] Nessus Bridge for Metasploit
[*] Type nessus_help for a command listing
[*] Successfully loaded Plugin: Nessus


msf6 > nessus_help

Command                     Help Text
-------                     ---------
Generic Commands            
-----------------           -----------------
nessus_connect              Connect to a Nessus server
nessus_logout               Logout from the Nessus server
nessus_login                Login into the connected Nessus server with a different username and 

<SNIP>

nessus_user_del             Delete a Nessus User
nessus_user_passwd          Change Nessus Users Password
                            
Policy Commands             
-----------------           -----------------
nessus_policy_list          List all polciies
nessus_policy_del           Delete a policy
```

Если плагин установлен неправильно, при попытке его загрузки мы получим следующую ошибку.

  Плагины

```shell-session
msf6 > load Plugin_That_Does_Not_Exist

[-] Failed to load plugin from /usr/share/metasploit-framework/plugins/Plugin_That_Does_Not_Exist.rb: cannot load such file -- /usr/share/metasploit-framework/plugins/Plugin_That_Does_Not_Exist.rb
```

Чтобы начать использовать плагин, выполните команды, доступные в меню справки этого конкретного плагина. Каждая кроссплатформенная интеграция предлагает уникальный набор взаимодействий, которые мы можем использовать во время наших оценок, поэтому полезно ознакомиться с каждым из них, прежде чем использовать, чтобы получить максимальную пользу от их наличия.

---

## Установка новых плагинов

С каждым обновлением дистрибутива Parrot OS устанавливаются новые, более популярные плагины, которые распространяются их разработчиками и собираются в репозитории обновлений Parrot. Чтобы установить новые пользовательские плагины, не включенные в новые обновления дистрибутива, можно взять файл .rb, предоставленный на странице разработчика, и поместить его в папку с `/usr/share/metasploit-framework/plugins`соответствующими правами доступа.

Например, давайте попробуем установить [Metasploit-Plugins от DarkOperator](https://github.com/darkoperator/Metasploit-Plugins.git) . Затем, перейдя по указанной выше ссылке, мы получим пару `.rb`файлов Ruby ( ), которые можно напрямую поместить в упомянутую выше папку.

#### Загрузка плагинов MSF

  Плагины

```shell-session
MuraMitchi@htb[/htb]$ git clone https://github.com/darkoperator/Metasploit-Plugins
MuraMitchi@htb[/htb]$ ls Metasploit-Plugins

aggregator.rb      ips_filter.rb  pcap_log.rb          sqlmap.rb
alias.rb           komand.rb      pentest.rb           thread.rb
auto_add_route.rb  lab.rb         request.rb           token_adduser.rb
beholder.rb        libnotify.rb   rssfeed.rb           token_hunter.rb
db_credcollect.rb  msfd.rb        sample.rb            twitt.rb
db_tracker.rb      msgrpc.rb      session_notifier.rb  wiki.rb
event_tester.rb    nessus.rb      session_tagger.rb    wmap.rb
ffautoregen.rb     nexpose.rb     socket_logger.rb
growl.rb           openvas.rb     sounds.rb
```

`pentest.rb`В качестве примера мы можем взять этот плагин и скопировать его в `/usr/share/metasploit-framework/plugins`.

#### MSF - Копирование плагина в MSF

  Плагины

```shell-session
MuraMitchi@htb[/htb]$ sudo cp ./Metasploit-Plugins/pentest.rb /usr/share/metasploit-framework/plugins/pentest.rb
```

После этого запустите `msfconsole`плагин и проверьте его установку, выполнив `load`команду. После загрузки плагина его `help menu`содержимое `msfconsole`автоматически расширяется дополнительными функциями.

#### MSF - Загрузка плагина

  Плагины

```shell-session
MuraMitchi@htb[/htb]$ msfconsole -q

msf6 > load pentest

       ___         _          _     ___ _           _
      | _ \___ _ _| |_ ___ __| |_  | _ \ |_  _ __ _(_)_ _
      |  _/ -_) ' \  _/ -_|_-<  _| |  _/ | || / _` | | ' \ 
      |_| \___|_||_\__\___/__/\__| |_| |_|\_,_\__, |_|_||_|
                                              |___/
      
Version 1.6
Pentest Plugin loaded.
by Carlos Perez (carlos_perez[at]darkoperator.com)
[*] Successfully loaded plugin: pentest


msf6 > help

Tradecraft Commands
===================

    Command          Description
    -------          -----------
    check_footprint  Checks the possible footprint of a post module on a target system.


auto_exploit Commands
=====================

    Command           Description
    -------           -----------
    show_client_side  Show matched client side exploits from data imported from vuln scanners.
    vuln_exploit      Runs exploits based on data imported from vuln scanners.


Discovery Commands
==================

    Command                 Description
    -------                 -----------
    discover_db             Run discovery modules against current hosts in the database.
    network_discover        Performs a port-scan and enumeration of services found for non pivot networks.
    pivot_network_discover  Performs enumeration of networks available to a specified Meterpreter session.
    show_session_networks   Enumerate the networks one could pivot thru Meterpreter in the active sessions.


Project Commands
================

    Command       Description
    -------       -----------
    project       Command for managing projects.


Postauto Commands
=================

    Command             Description
    -------             -----------
    app_creds           Run application password collection modules against specified sessions.
    get_lhost           List local IP addresses that can be used for LHOST.
    multi_cmd           Run shell command against several sessions
    multi_meter_cmd     Run a Meterpreter Console Command against specified sessions.
    multi_meter_cmd_rc  Run resource file with Meterpreter Console Commands against specified sessions.
    multi_post          Run a post module against specified sessions.
    multi_post_rc       Run resource file with post modules and options against specified sessions.
    sys_creds           Run system password collection modules against specified sessions.

<SNIP>
```

Многие разработчики создают множество различных плагинов для Metasploit. Все они имеют определённое назначение и могут значительно сэкономить время после ознакомления с ними. Ознакомьтесь со списком популярных плагинов ниже:

||||
|---|---|---|
|[nMap (предустановлено)](https://nmap.org/)|[NexPose (предустановлен)](https://sectools.org/tool/nexpose/)|[Nessus (предустановлен)](https://www.tenable.com/products/nessus)|
|[Mimikatz (предустановленная версия 1)](http://blog.gentilkiwi.com/mimikatz)|[Stdapi (предустановлен)](https://www.rubydoc.info/github/rapid7/metasploit-framework/Rex/Post/Meterpreter/Extensions/Stdapi/Stdapi)|[Рельсовая пушка](https://github.com/rapid7/metasploit-framework/wiki/How-to-use-Railgun-for-Windows-post-exploitation)|
|[Приват](https://github.com/rapid7/metasploit-framework/blob/master/lib/rex/post/meterpreter/extensions/priv/priv.rb)|[Режим инкогнито (предустановлен)](https://www.offensive-security.com/metasploit-unleashed/fun-incognito/)|[Темный Оператор](https://github.com/darkoperator/Metasploit-Plugins)|

---

## Миксины

Metasploit Framework написан на Ruby, объектно-ориентированном языке программирования. Это во многом объясняет его `msfconsole`удобство использования. Миксины — одна из тех функций, которые при реализации предоставляют большую гибкость как создателю скрипта, так и пользователю.

Миксины — это классы, которые выступают в качестве методов, используемых другими классами, не обязательно являясь родительским классом этих других классов. Таким образом, называть это наследованием было бы неуместно, лучше использовать термин «включение». Они в основном используются, когда мы:

1. Хочу предоставить классу множество дополнительных функций.
2. Хотите использовать одну конкретную функцию для множества занятий?

Большая часть языка программирования Ruby основана на использовании примесей (mixins) в качестве модулей. Концепция примесей реализуется с помощью слова `mixins` `include`, которому передается имя модуля `parameter`. Подробнее о примесях можно прочитать [здесь](https://en.wikibooks.org/wiki/Metasploit/UsingMixins) .

Если вы только начинаете работать с Metasploit, вам не стоит беспокоиться об использовании миксинов или их влиянии на оценку. Однако они упомянуты здесь как напоминание о том, насколько сложной может быть настройка Metasploit.