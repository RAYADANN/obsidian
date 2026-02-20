[Hashcat](https://hashcat.net/) — это хорошо известный инструмент для взлома паролей в Linux, Windows и macOS. С 2009 по 2015 год это было проприетарное программное обеспечение, но с тех пор оно было выпущено как открытый исходный код. Благодаря превосходной поддержке графических процессоров, его можно использовать для взлома самых разных хешей. Подобно JtR, Hashcat поддерживает несколько режимов атаки (взлома), которые можно использовать для эффективной атаки на хеши паролей.

Общий синтаксис для запуска hashcat выглядит следующим образом:

  Введение в Hashcat

```shell-session
MuraMitchi@htb[/htb]$ hashcat -a 0 -m 0 <hashes> [wordlist, rule, mask, ...]
```

В приведенной выше команде:

- `-a`используется для указания`attack mode`
- `-m`используется для указания`hash type`
- `<hashes>`Это либо строка хеша, либо файл, содержащий один или несколько хешей паролей одного типа.
- `[wordlist, rule, mask, ...]`является заполнителем для дополнительных аргументов, зависящих от режима атаки.

## Хэш-типы

Hashcat поддерживает сотни различных типов хешей, каждому из которых присваивается идентификатор. Список связанных идентификаторов можно сгенерировать, запустив команду `hashcat --help`.

  Введение в Hashcat

```shell-session
MuraMitchi@htb[/htb]$ hashcat --help

...SNIP...

- [ Hash modes ] -

      # | Name                                                       | Category
  ======+============================================================+======================================
    900 | MD4                                                        | Raw Hash
      0 | MD5                                                        | Raw Hash
    100 | SHA1                                                       | Raw Hash
   1300 | SHA2-224                                                   | Raw Hash
   1400 | SHA2-256                                                   | Raw Hash
  10800 | SHA2-384                                                   | Raw Hash
   1700 | SHA2-512                                                   | Raw Hash
  17300 | SHA3-224                                                   | Raw Hash
  17400 | SHA3-256                                                   | Raw Hash
  17500 | SHA3-384                                                   | Raw Hash
  17600 | SHA3-512                                                   | Raw Hash
   6000 | RIPEMD-160                                                 | Raw Hash
    600 | BLAKE2b-512                                                | Raw Hash
  11700 | GOST R 34.11-2012 (Streebog) 256-bit, big-endian           | Raw Hash
  11800 | GOST R 34.11-2012 (Streebog) 512-bit, big-endian           | Raw Hash
   6900 | GOST R 34.11-94                                            | Raw Hash
  17010 | GPG (AES-128/AES-256 (SHA-1($pass)))                       | Raw Hash
   5100 | Half MD5                                                   | Raw Hash
  17700 | Keccak-224                                                 | Raw Hash
  17800 | Keccak-256                                                 | Raw Hash
  17900 | Keccak-384                                                 | Raw Hash
  18000 | Keccak-512                                                 | Raw Hash
   6100 | Whirlpool                                                  | Raw Hash
  10100 | SipHash                                                    | Raw Hash
     70 | md5(utf16le($pass))                                        | Raw Hash
    170 | sha1(utf16le($pass))                                       | Raw Hash
   1470 | sha256(utf16le($pass))                                     | Raw Hash
...SNIP...
```

На сайте Hashcat размещен исчерпывающий список [примеров хешей](https://hashcat.net/wiki/doku.php?id=example_hashes) , которые могут помочь в ручном определении неизвестного типа хеша и установлении соответствующего идентификатора режима хеширования Hashcat.

В качестве альтернативы, для быстрого определения типа хеша hashcat можно использовать [hashID](https://github.com/psypanda/hashID)`-m` , указав соответствующий аргумент.

  Введение в Hashcat

```shell-session
MuraMitchi@htb[/htb]$ hashid -m '$1$FNr44XZC$wQxY6HHLrgrGX0e1195k.1'

Analyzing '$1$FNr44XZC$wQxY6HHLrgrGX0e1195k.1'
[+] MD5 Crypt [Hashcat Mode: 500]
[+] Cisco-IOS(MD5) [Hashcat Mode: 500]
[+] FreeBSD MD5 [Hashcat Mode: 500]
```

## режимы атаки

Hashcat имеет множество различных режимов атаки, включая `dictionary`, `mask`, `combinator`, и `association`. В этом разделе мы рассмотрим первые два, поскольку они, вероятно, наиболее часто используются.

#### атака словаря

[Атака по словарю](https://hashcat.net/wiki/doku.php?id=dictionary_attack) ( Dictionary attack `-a 0`) — это, как следует из названия, атака по словарю. Пользователь предоставляет хеши паролей и список слов в качестве входных данных, а Hashcat проверяет каждое слово в списке как потенциальный пароль, пока не будет найден правильный пароль или список не будет исчерпан.

В качестве примера представим, что мы извлекли из базы данных SQL следующий хеш пароля: `e3e3ec5831ad5e7288241960e5d4fdb8`. Во-первых, мы можем определить его как хеш MD5, имеющий идентификатор хеша `0`. Чтобы попытаться взломать этот хеш, используя `rockyou.txt`список слов, будет использована следующая команда:

  Введение в Hashcat

```shell-session
MuraMitchi@htb[/htb]$ hashcat -a 0 -m 0 e3e3ec5831ad5e7288241960e5d4fdb8 /usr/share/wordlists/rockyou.txt

...SNIP...               

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: e3e3ec5831ad5e7288241960e5d4fdb8
Time.Started.....: Sat Apr 19 08:58:44 2025 (0 secs)
Time.Estimated...: Sat Apr 19 08:58:44 2025 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  1706.6 kH/s (0.14ms) @ Accel:512 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 28672/14344385 (0.20%)
Rejected.........: 0/28672 (0.00%)
Restore.Point....: 27648/14344385 (0.19%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: 010292 -> spongebob9
Hardware.Mon.#1..: Util: 40%

Started: Sat Apr 19 08:58:43 2025
Stopped: Sat Apr 19 08:58:46 2025
```

Одного списка слов часто недостаточно для взлома хеша пароля. Как это было в случае с JtR, `rules`можно использовать для внесения определенных изменений в пароли, чтобы сгенерировать еще больше вариантов. Файлы правил, поставляемые с hashcat, обычно находятся в папке `/usr/share/hashcat/rules`:

  Введение в Hashcat

```shell-session
MuraMitchi@htb[/htb]$ ls -l /usr/share/hashcat/rules

total 2852
-rw-r--r-- 1 root root 309439 Apr 24  2024 Incisive-leetspeak.rule
-rw-r--r-- 1 root root  35802 Apr 24  2024 InsidePro-HashManager.rule
-rw-r--r-- 1 root root  20580 Apr 24  2024 InsidePro-PasswordsPro.rule
-rw-r--r-- 1 root root  64068 Apr 24  2024 T0XlC-insert_00-99_1950-2050_toprules_0_F.rule
-rw-r--r-- 1 root root   2027 Apr 24  2024 T0XlC-insert_space_and_special_0_F.rule
-rw-r--r-- 1 root root  34437 Apr 24  2024 T0XlC-insert_top_100_passwords_1_G.rule
-rw-r--r-- 1 root root  34813 Apr 24  2024 T0XlC.rule
-rw-r--r-- 1 root root   1289 Apr 24  2024 T0XlC_3_rule.rule
-rw-r--r-- 1 root root 168700 Apr 24  2024 T0XlC_insert_HTML_entities_0_Z.rule
-rw-r--r-- 1 root root 197418 Apr 24  2024 T0XlCv2.rule
-rw-r--r-- 1 root root    933 Apr 24  2024 best64.rule
-rw-r--r-- 1 root root    754 Apr 24  2024 combinator.rule
-rw-r--r-- 1 root root 200739 Apr 24  2024 d3ad0ne.rule
-rw-r--r-- 1 root root 788063 Apr 24  2024 dive.rule
-rw-r--r-- 1 root root  78068 Apr 24  2024 generated.rule
-rw-r--r-- 1 root root 483425 Apr 24  2024 generated2.rule
drwxr-xr-x 2 root root   4096 Oct 19 15:30 hybrid
-rw-r--r-- 1 root root    298 Apr 24  2024 leetspeak.rule
-rw-r--r-- 1 root root   1280 Apr 24  2024 oscommerce.rule
-rw-r--r-- 1 root root 301161 Apr 24  2024 rockyou-30000.rule
-rw-r--r-- 1 root root   1563 Apr 24  2024 specific.rule
-rw-r--r-- 1 root root     45 Apr 24  2024 toggles1.rule
-rw-r--r-- 1 root root    570 Apr 24  2024 toggles2.rule
-rw-r--r-- 1 root root   3755 Apr 24  2024 toggles3.rule
-rw-r--r-- 1 root root  16040 Apr 24  2024 toggles4.rule
-rw-r--r-- 1 root root  49073 Apr 24  2024 toggles5.rule
-rw-r--r-- 1 root root  55346 Apr 24  2024 unix-ninja-leetspeak.rule
```

В качестве другого примера представим, что из базы данных SQL произошла утечка дополнительного MD5-хеша: `1b0556a75770563578569ae21392630c`. Нам не удалось взломать его, используя `rockyou.txt`только , поэтому в последующей попытке мы могли бы применить некоторые распространенные преобразования на основе правил. Один из наборов правил, который мы могли бы попробовать, — это `best64.rule`, который содержит 64 стандартных варианта модификации паролей, таких как добавление цифр или замена символов их эквивалентами из «leet». Для выполнения такого рода атаки мы бы добавили опцию `-r <ruleset>`к команде, как показано ниже:

  Введение в Hashcat

```shell-session
MuraMitchi@htb[/htb]$ hashcat -a 0 -m 0 1b0556a75770563578569ae21392630c /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule

...SNIP...

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 1b0556a75770563578569ae21392630c
Time.Started.....: Sat Apr 19 09:16:35 2025 (0 secs)
Time.Estimated...: Sat Apr 19 09:16:35 2025 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Mod........: Rules (/usr/share/hashcat/rules/best64.rule)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........: 13624.4 kH/s (5.40ms) @ Accel:512 Loops:77 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 236544/1104517645 (0.02%)
Rejected.........: 0/236544 (0.00%)
Restore.Point....: 2048/14344385 (0.01%)
Restore.Sub.#1...: Salt:0 Amplifier:0-77 Iteration:0-77
Candidate.Engine.: Device Generator
Candidates.#1....: slimshady -> drousd
Hardware.Mon.#1..: Util: 47%

Started: Sat Apr 19 09:16:35 2025
Stopped: Sat Apr 19 09:16:37 2025
```

#### Нападение в маске

[Атака с использованием маски](https://hashcat.net/wiki/doku.php?id=mask_attack) ( `-a 3`) — это тип атаки методом перебора, при котором пространство ключей явно определяется пользователем. Например, если нам известно, что пароль состоит из восьми символов, вместо того чтобы перебирать все возможные комбинации, мы можем определить маску, которая проверяет комбинации из шести букв, за которыми следуют две цифры.

Маска определяется путем объединения последовательности символов, каждый из которых представляет собой встроенный или пользовательский набор символов. Hashcat включает в себя несколько встроенных наборов символов:

|Символ|Чарсет|
|---|---|
|?л|abcdefghijklmnopqrstuvwxyz|
|?u|ABCDEFGHIJKLMNOPQRSTUVWXYZ|
|?d|0123456789|
|?час|0123456789abcdef|
|?ЧАС|0123456789ABCDEF|
|?s|«пространство»!"#$%&'()*+,-./:;<=>?@[]^_`{|
|?а|?l?u?d?s|
|?б|0x00 - 0xff|

Пользовательские кодировки символов можно определить с помощью аргументов `-1`, `-2`, `-3`, и `-4`, а затем ссылаться на них с помощью `?1`, `?2`, `?3`, и `?4`.

Допустим, мы хотим проверить пароли, которые начинаются с заглавной буквы, продолжаются четырьмя строчными буквами, цифрой и символом. В результате маска хеш-кода будет выглядеть так `?u?l?l?l?l?d?s`: .

  Введение в Hashcat

```shell-session
MuraMitchi@htb[/htb]$ hashcat -a 3 -m 0 1e293d6912d074c0fd15844d803400dd '?u?l?l?l?l?d?s'

...SNIP...

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 1e293d6912d074c0fd15844d803400dd
Time.Started.....: Sat Apr 19 09:43:02 2025 (4 secs)
Time.Estimated...: Sat Apr 19 09:43:06 2025 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Mask.......: ?u?l?l?l?l?d?s [7]
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:   101.6 MH/s (9.29ms) @ Accel:512 Loops:1024 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 456237056/3920854080 (11.64%)
Rejected.........: 0/456237056 (0.00%)
Restore.Point....: 25600/223080 (11.48%)
Restore.Sub.#1...: Salt:0 Amplifier:5120-6144 Iteration:0-1024
Candidate.Engine.: Device Generator
Candidates.#1....: Uayvf7- -> Dikqn5!
Hardware.Mon.#1..: Util: 98%

Started: Sat Apr 19 09:42:46 2025
Stopped: Sat Apr 19 09:43:08 2025
```