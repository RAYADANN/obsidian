# Введение в историю Джона Потрошителя

[John the Ripper](https://github.com/openwall/john) (также `JtR`известный как John the Ripper `john`) — это широко известный инструмент для тестирования на проникновение, используемый для взлома паролей с помощью различных атак, включая перебор и словарный метод. Это программное обеспечение с открытым исходным кодом, первоначально разработанное для систем на базе UNIX и впервые выпущенное в 1996 году. Благодаря своим многочисленным возможностям оно стало неотъемлемой частью индустрии безопасности. `"jumbo"`Для наших целей рекомендуется использовать вариант John the Ripper, поскольку он имеет оптимизации производительности, дополнительные функции, такие как многоязычные списки слов, и поддержку 64-битных архитектур. Эта версия способна взламывать пароли с большей точностью и скоростью. В состав JtR входят различные инструменты для преобразования различных типов файлов и хешей в форматы, пригодные для использования JtR. Кроме того, программное обеспечение регулярно обновляется, чтобы соответствовать современным тенденциям и технологиям в области безопасности.

## Режимы взлома

#### Режим одиночной трещины

`Single crack mode`Это основанный на правилах метод взлома, наиболее полезный при атаке на учетные данные Linux. Он генерирует варианты паролей на основе имени пользователя жертвы, имени домашнего каталога и значений [GECOS](https://en.wikipedia.org/wiki/Gecos_field) (полное имя, номер комнаты, номер телефона и т. д.). Эти строки проверяются на соответствие большому набору правил, которые применяют распространенные изменения строк, встречающиеся в паролях (например, пользователь, чье настоящее имя — , `Bob Smith`может использовать `Smith1`в качестве пароля ).

**Примечание:** Процесс аутентификации в Linux, а также правила взлома будут подробно рассмотрены в последующих разделах. Приведенный ниже пример упрощен для демонстрационных целей.

Представьте, что мы, злоумышленники, обнаружили файл `passwd`со следующим содержимым:

```
r0lf:$6$ues25dIanlctrWxg$nZHVz2z4kCy1760Ee28M1xtHdGoy0C2cYzZ8l2sVa1kIa8K9gAcdBP.GI6ng/qA4oaMrgElZ1Cb9OeXO4Fvy3/:0:0:Rolf Sebastian:/home/r0lf:/bin/bash
```

Исходя из содержимого файла, можно сделать вывод, что жертва имеет имя пользователя `r0lf`, настоящее имя `Rolf Sebastian`и домашний каталог `/home/r0lf`. Режим взлома с одним нажатием кнопки будет использовать эту информацию для генерации возможных паролей и проверки их на соответствие хешу. Атаку можно запустить с помощью следующей команды:

  Введение в историю Джона Потрошителя

```shell-session
MuraMitchi@htb[/htb]$ john --single passwd

Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
[...SNIP...]        (r0lf)     
1g 0:00:00:00 DONE 1/3 (2025-04-10 07:47) 12.50g/s 5400p/s 5400c/s 5400C/s NAITSABESFL0R..rSebastiannaitsabeSr
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

В этом случае хэш пароля был успешно взломан.

#### режим списка слов

`Wordlist mode`Эта команда используется для взлома паролей с помощью словарной атаки, то есть она пытается подобрать все пароли из предоставленного списка слов к хешу пароля. Базовый синтаксис команды выглядит следующим образом:

  Введение в историю Джона Потрошителя

```shell-session
MuraMitchi@htb[/htb]$ john --wordlist=<wordlist_file> <hash_file>
```

Файл (или файлы) со списком слов, используемый для взлома хешей паролей, должен быть в формате открытого текста, по одному слову на строку. Можно указать несколько списков слов, разделяя их запятой. Правила, как пользовательские, так и встроенные, можно задать с помощью соответствующего `--rules`аргумента. Эти правила можно применять для генерации паролей-кандидатов с использованием преобразований, таких как добавление цифр, изменение регистра букв и добавление специальных символов.

#### Инкрементальный режим

`Incremental mode`Это мощный метод взлома паролей методом перебора, который генерирует потенциальные пароли на основе статистической модели ( [цепей Маркова](https://en.wikipedia.org/wiki/Markov_chain) ). Он предназначен для проверки всех комбинаций символов, определенных определенным набором символов, отдавая приоритет более вероятным паролям на основе обучающих данных.

Этот режим является наиболее исчерпывающим, но и наиболее трудоемким. Он генерирует варианты паролей динамически и не полагается на заранее определенный список слов, в отличие от режима со списком слов. В отличие от чисто случайных атак методом перебора, инкрементальный режим использует статистическую модель для обоснованных предположений, что приводит к значительно более эффективному подходу, чем наивные атаки методом перебора.

Основной синтаксис выглядит следующим образом:

  Введение в историю Джона Потрошителя

```shell-session
MuraMitchi@htb[/htb]$ john --incremental <hash_file>
```

По умолчанию JtR использует предопределенные режимы инкрементного ввода, указанные в конфигурационном файле ( `john.conf`), которые определяют наборы символов и длину паролей. Вы можете настроить их или определить свои собственные для паролей, содержащих специальные символы или определенные шаблоны.

  Введение в историю Джона Потрошителя

```shell-session
MuraMitchi@htb[/htb]$ grep '# Incremental modes' -A 100 /etc/john/john.conf

# Incremental modes

# This is for one-off uses (make your own custom.chr).
# A charset can now also be named directly from command-line, so no config
# entry needed: --incremental=whatever.chr
[Incremental:Custom]
File = $JOHN/custom.chr
MinLen = 0

# The theoretical CharCount is 211, we've got 196.
[Incremental:UTF8]
File = $JOHN/utf8.chr
MinLen = 0
CharCount = 196

# This is CP1252, a super-set of ISO-8859-1.
# The theoretical CharCount is 219, we've got 203.
[Incremental:Latin1]
File = $JOHN/latin1.chr
MinLen = 0
CharCount = 203

[Incremental:ASCII]
File = $JOHN/ascii.chr
MinLen = 0
MaxLen = 13
CharCount = 95

...SNIP...
```

**Примечание:** Этот режим может быть ресурсоемким и медленным, особенно для длинных или сложных паролей. Настройка набора символов и длины может повысить производительность и сфокусировать атаку.

## Определение форматов хешей

Иногда хеши паролей могут иметь неизвестный формат, и даже Джон-потрошитель (JtR) не сможет определить их с полной уверенностью. Например, рассмотрим следующий хеш:

```
193069ceb0461e1d40d216e32c79c704
```

Один из способов получить представление — обратиться к [документации JtR по примерам хешей](https://openwall.info/wiki/john/sample-hashes) или [к этому списку от PentestMonkey](https://pentestmonkey.net/cheat-sheet/john-the-ripper-hash-formats) . Оба источника содержат множество примеров хешей, а также соответствующие форматы JtR. Другой вариант — использовать такой инструмент, как [hashID](https://github.com/psypanda/hashID) , который проверяет предоставленные хеши по встроенному списку, чтобы предложить потенциальные форматы. Добавив флаг `-j`, hashID, помимо формата хеша, укажет соответствующий формат JtR:

  Введение в историю Джона Потрошителя

```shell-session
MuraMitchi@htb[/htb]$ hashid -j 193069ceb0461e1d40d216e32c79c704

Analyzing '193069ceb0461e1d40d216e32c79c704'
[+] MD2 [JtR Format: md2]
[+] MD5 [JtR Format: raw-md5]
[+] MD4 [JtR Format: raw-md4]
[+] Double MD5 
[+] LM [JtR Format: lm]
[+] RIPEMD-128 [JtR Format: ripemd-128]
[+] Haval-128 [JtR Format: haval-128-4]
[+] Tiger-128 
[+] Skein-256(128) 
[+] Skein-512(128) 
[+] Lotus Notes/Domino 5 [JtR Format: lotus5]
[+] Skype 
[+] Snefru-128 [JtR Format: snefru-128]
[+] NTLM [JtR Format: nt]
[+] Domain Cached Credentials [JtR Format: mscach]
[+] Domain Cached Credentials 2 [JtR Format: mscach2]
[+] DNSSEC(NSEC3) 
[+] RAdmin v2.x [JtR Format: radmin]
```

К сожалению, в нашем примере по-прежнему не совсем ясно, в каком формате находится хеш. Иногда так бывает, и это просто одна из «проблем», с которыми вы столкнетесь как пентестер. Во многих случаях контекста происхождения хеша будет достаточно, чтобы обоснованно предположить его формат. В этом конкретном примере формат хеша — RIPEMD-128.

JtR поддерживает сотни форматов хешей, некоторые из которых перечислены в таблице ниже. Аргумент `--format`может быть указан для определения формата целевых хешей в JtR.

|**Хэш-формат**|**Пример команды**|**Описание**|
|---|---|---|
|афс|`john --format=afs [...] <hash_file>`|Хэши паролей AFS (Andrew File System)|
|бфегг|`john --format=bfegg [...] <hash_file>`|Хэши bfegg, используемые в IRC-ботах Eggdrop.|
|бф|`john --format=bf [...] <hash_file>`|Хэши криптографического типа Blowfish (3)|
|бсди|`john --format=bsdi [...] <hash_file>`|Хэши BSDi crypt(3)|
|крипт(3)|`john --format=crypt [...] <hash_file>`|Традиционные криптографические хеши Unix crypt(3)|
|дес|`john --format=des [...] <hash_file>`|Традиционные криптографические хеши на основе DES (3)|
|dmd5|`john --format=dmd5 [...] <hash_file>`|Хэши паролей DMD5 (Dragonfly BSD MD5)|
|доминосек|`john --format=dominosec [...] <hash_file>`|Хэши паролей IBM Lotus Domino 6/7|
|Хэши SID EPiServer|`john --format=episerver [...] <hash_file>`|Хэши паролей EPiServer SID (идентификатор безопасности).|
|хдаа|`john --format=hdaa [...] <hash_file>`|Хэши паролей hdaa, используемые в Openwall GNU/Linux|
|hmac-md5|`john --format=hmac-md5 [...] <hash_file>`|хэши паролей hmac-md5|
|hmailserver|`john --format=hmailserver [...] <hash_file>`|хэши паролей почтового сервера|
|ipb2|`john --format=ipb2 [...] <hash_file>`|Хэши паролей Invision Power Board 2|
|krb4|`john --format=krb4 [...] <hash_file>`|Хэши паролей Kerberos 4|
|krb5|`john --format=krb5 [...] <hash_file>`|Хэши паролей Kerberos 5|
|ЛМ|`john --format=LM [...] <hash_file>`|Хэши паролей LM (менеджера локальной сети)|
|лотос5|`john --format=lotus5 [...] <hash_file>`|Хэши паролей Lotus Notes/Domino 5|
|mscash|`john --format=mscash [...] <hash_file>`|Хэши паролей кэша MS|
|mscash2|`john --format=mscash2 [...] <hash_file>`|Хэши паролей MS Cache v2|
|mschapv2|`john --format=mschapv2 [...] <hash_file>`|Хэши паролей MS CHAP v2|
|mskrb5|`john --format=mskrb5 [...] <hash_file>`|Хэши паролей MS Kerberos 5|
|mssql05|`john --format=mssql05 [...] <hash_file>`|Хэши паролей MS SQL 2005|
|mssql|`john --format=mssql [...] <hash_file>`|Хэши паролей MS SQL|
|mysql-fast|`john --format=mysql-fast [...] <hash_file>`|Быстрые хэши паролей MySQL|
|mysql|`john --format=mysql [...] <hash_file>`|Хэши паролей MySQL|
|mysql-sha1|`john --format=mysql-sha1 [...] <hash_file>`|Хэши паролей MySQL SHA1|
|NETLM|`john --format=netlm [...] <hash_file>`|Хэши паролей NETLM (NT LAN Manager)|
|NETLMv2|`john --format=netlmv2 [...] <hash_file>`|Хэши паролей NETLMv2 (NT LAN Manager версии 2)|
|NETNTLM|`john --format=netntlm [...] <hash_file>`|Хэши паролей NETNTLM (NT LAN Manager)|
|NETNTLMv2|`john --format=netntlmv2 [...] <hash_file>`|Хэши паролей NETNTLMv2 (NT LAN Manager версии 2)|
|NEThalfLM|`john --format=nethalflm [...] <hash_file>`|Хэши паролей NEThalfLM (NT LAN Manager)|
|мд5нс|`john --format=md5ns [...] <hash_file>`|хэши паролей md5ns (пространство имен MD5)|
|nsldap|`john --format=nsldap [...] <hash_file>`|Хэши паролей nsldap (OpenLDAP SHA)|
|сша|`john --format=ssha [...] <hash_file>`|Хэши паролей ssha (Salted SHA)|
|НТ|`john --format=nt [...] <hash_file>`|Хэши паролей NT (Windows NT)|
|openssha|`john --format=openssha [...] <hash_file>`|Хэши паролей закрытого ключа OPENSSH|
|oracle11|`john --format=oracle11 [...] <hash_file>`|Хэши паролей Oracle 11|
|оракул|`john --format=oracle [...] <hash_file>`|Хэши паролей Oracle|
|pdf|`john --format=pdf [...] <hash_file>`|Хэши паролей PDF (Portable Document Format)|
|phpass-md5|`john --format=phpass-md5 [...] <hash_file>`|PHPass-MD5 (портативный фреймворк для хеширования паролей на PHP) — хеширование паролей.|
|phps|`john --format=phps [...] <hash_file>`|Хэши паролей PHPS|
|pix-md5|`john --format=pix-md5 [...] <hash_file>`|Хэши паролей Cisco PIX MD5|
|по|`john --format=po [...] <hash_file>`|Хэши паролей Po (Sybase SQL Anywhere)|
|рар|`john --format=rar [...] <hash_file>`|Хэши паролей RAR (WinRAR)|
|raw-md4|`john --format=raw-md4 [...] <hash_file>`|Хэши паролей в формате MD4 без обработки|
|raw-md5|`john --format=raw-md5 [...] <hash_file>`|Необработанные хеши паролей MD5|
|raw-md5-unicode|`john --format=raw-md5-unicode [...] <hash_file>`|Необработанные хеши паролей MD5 в формате Unicode|
|raw-sha1|`john --format=raw-sha1 [...] <hash_file>`|Необработанные хеши паролей SHA1|
|raw-sha224|`john --format=raw-sha224 [...] <hash_file>`|Необработанные хеши паролей SHA224|
|raw-sha256|`john --format=raw-sha256 [...] <hash_file>`|Необработанные хеши паролей SHA256|
|raw-sha384|`john --format=raw-sha384 [...] <hash_file>`|Необработанные хеши паролей SHA384|
|raw-sha512|`john --format=raw-sha512 [...] <hash_file>`|Необработанные хеши паролей SHA512|
|соленый ша|`john --format=salted-sha [...] <hash_file>`|Хэши паролей SHA с солью|
|сапб|`john --format=sapb [...] <hash_file>`|Хэши паролей SAP CODVN B (BCODE)|
|сапг|`john --format=sapg [...] <hash_file>`|Хэши паролей SAP CODVN G (PASSCODE)|
|sha1-gen|`john --format=sha1-gen [...] <hash_file>`|Общие хеши паролей SHA1|
|скей|`john --format=skey [...] <hash_file>`|Хэши S-ключа (одноразового пароля)|
|ssh|`john --format=ssh [...] <hash_file>`|Хэши паролей SSH (Secure Shell)|
|сибазаза|`john --format=sybasease [...] <hash_file>`|Хэши паролей Sybase ASE|
|xsha|`john --format=xsha [...] <hash_file>`|Хэши паролей xsha (расширенный SHA)|
|зип|`john --format=zip [...] <hash_file>`|Хэши паролей ZIP-архивов (WinZip)|

## Взлом файлов

Также с помощью JtR можно взламывать файлы, защищенные паролем или зашифрованные. `"2john"`В состав JtR входит множество инструментов, которые можно использовать для обработки файлов и получения хешей, совместимых с JtR. Обобщенный синтаксис для этих инструментов выглядит следующим образом:

  Введение в историю Джона Потрошителя

```shell-session
MuraMitchi@htb[/htb]$ <tool> <file_to_crack> > file.hash
```

В состав JtR входят следующие инструменты:

|**Инструмент**|**Описание**|
|---|---|
|`pdf2john`|Преобразует PDF-документы для Джона|
|`ssh2john`|Преобразует закрытые ключи SSH для Джона.|
|`mscash2john`|Преобразует хеши MS Cash для Джона.|
|`keychain2john`|Преобразует файлы связки ключей OS X для Джона.|
|`rar2john`|Преобразует RAR-архивы для Джона.|
|`pfx2john`|Преобразует файлы PKCS#12 для Джона.|
|`truecrypt_volume2john`|Преобразует тома TrueCrypt для Джона.|
|`keepass2john`|Преобразует базы данных KeePass для Джона.|
|`vncpcap2john`|Преобразует файлы VNC PCAP для Джона.|
|`putty2john`|Преобразует закрытые ключи PuTTY для Джона.|
|`zip2john`|Преобразует ZIP-архивы для Джона.|
|`hccap2john`|Преобразует перехваченные соединения WPA/WPA2 для John.|
|`office2john`|Преобразует документы MS Office для Джона.|
|`wpa2john`|Преобразует протоколы WPA/WPA2 для Джона.|
|...СНИМОК...|...СНИМОК...|

Ещё более обширную коллекцию можно найти по адресу `Pwnbox`:

  Введение в историю Джона Потрошителя

```shell-session
MuraMitchi@htb[/htb]$ locate *2john*

/usr/bin/bitlocker2john
/usr/bin/dmg2john
/usr/bin/gpg2john
/usr/bin/hccap2john
/usr/bin/keepass2john
/usr/bin/putty2john
/usr/bin/racf2john
/usr/bin/rar2john
/usr/bin/uaf2john
/usr/bin/vncpcap2john
/usr/bin/wlanhcx2john
/usr/bin/wpapcap2john
/usr/bin/zip2john
/usr/share/john/1password2john.py
/usr/share/john/7z2john.pl
/usr/share/john/DPAPImk2john.py
/usr/share/john/adxcsouf2john.py
/usr/share/john/aem2john.py
/usr/share/john/aix2john.pl
/usr/share/john/aix2john.py
/usr/share/john/andotp2john.py
/usr/share/john/androidbackup2john.py
...SNIP...
```