# Domain Name System (DNS)

`DNS` является неотъемлемой частью Интернета. Через доменные имена, такие как `academy.hackthebox.com` или `www.hackthebox.com`, мы получаем доступ к веб-серверам, которым выделены конкретные IP-адреса. DNS — это система преобразования имен компьютеров в IP-адреса, распределенная по тысячам серверов имен.

### 🚀 Quick Commands

**OS Terminal**
```bash
# Получение NS-записей (серверов имен) для домена
dig ns <DOMAIN> @<TARGET_IP>

# Запрос версии DNS-сервера (CHAOS query)
dig CH TXT version.bind <TARGET_IP>

# Запрос всех доступных записей (может быть ограничено сервером)
dig any <DOMAIN> @<TARGET_IP>

# Попытка полной передачи зоны (AXFR)
dig axfr <DOMAIN> @<TARGET_IP>

# Попытка передачи зоны для внутреннего поддомена
dig axfr internal.<DOMAIN> @<TARGET_IP>

# Перебор поддоменов с помощью Bash-цикла и dig
for sub in $(cat <WORDLIST>); do dig $sub.<DOMAIN> @<TARGET_IP> | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt; done

# Комплексное перечисление с помощью dnsenum
dnsenum --dnsserver <TARGET_IP> --enum -p 0 -s 0 -o subdomains.txt -f <WORDLIST> <DOMAIN>
```

---

### 🛠 Tools & Utilities

- `[[dig]]` — мощный инструмент командной строки для выполнения DNS-запросов и диагностики.
- `[[dnsenum]]` — инструмент для автоматизации сбора информации о DNS, включая перебор поддоменов и попытки AXFR.
- `[[SecLists]]` — сборник списков слов (wordlists), используемый для брутфорса поддоменов.

---

### 📝 Technical Analysis & Media

#### Порты и Службы

| Порт | Протокол | Роль |
| :--- | :--- | :--- |
| 53 | UDP | Стандартные DNS-запросы (разрешение имен). |
| 53 | TCP | Передача зон (AXFR) и обработка ответов размером более 512 байт. |

> [!info] Типы DNS-серверов
> - **Корневой DNS-сервер (Root Server):** Отвечает за домены верхнего уровня (TLD). Связующее звено между пользователем и IP. Всего в мире 13 таких серверов (координируются ICANN).
> - **Авторитетный сервер имен (Authoritative Nameserver):** Обладает полномочиями для конкретной зоны. Его ответы являются обязательными.
> - **Неавторитетный сервер имен (Non-authoritative Nameserver):** Собирает информацию о зонах с помощью рекурсивных или итеративных запросов.
> - **Кэширующий сервер (Caching DNS Server):** Хранит информацию от других серверов на определенный период (TTL).
> - **Сервер переадресации (Forwarding Server):** Пересылает запросы на другой DNS-сервер.
> - **Резолвер (Resolver):** Выполняет разрешение имен локально на уровне ОС или маршрутизатора.

#### Шифрование DNS
По умолчанию DNS-трафик не зашифрован. Для защиты конфиденциальности используются:
- **DNS over TLS (DoT)**
- **DNS over HTTPS (DoH)**
- **DNSCrypt**

#### Иерархия доменов
![Диаграмма, показывающая иерархию доменов: Корневой домен, домены верхнего уровня (TLD), такие как net, org, com, dev, io; домен второго уровня inlanefreight.com; поддомены dev.inlanefreight.com, www.inlanefreight.com, mail.inlanefreight.com; хост WS01.dev.inlanefreight.com.](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/27/tooldev-dns.png)

#### Типы DNS-записей

| Запись | Описание |
| :--- | :--- |
| **A** | IPv4-адрес домена. |
| **AAAA** | IPv6-адрес домена. |
| **MX** | Почтовые серверы, ответственные за домен. |
| **NS** | Серверы имен (DNS-серверы) домена. |
| **TXT** | Текстовая информация (SPF, DMARC, верификация SSL). |
| **CNAME** | Псевдоним (alias) для другого доменного имени. |
| **PTR** | Обратный поиск (IP в доменное имя). |
| **SOA** | Информация о зоне: администратор, серийный номер, таймеры обновления. |

> [!example] Пример запроса SOA через dig
> ```shellsession
> MuraMitchi@htb[/htb]$ dig soa www.inlanefreight.com
> 
> ; <<>> DiG 9.16.27-Debian <<>> soa www.inlanefreight.com
> ;; global options: +cmd
> ;; Got answer:
> ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15876
> ;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
> 
> ;; OPT PSEUDOSECTION:
> ; EDNS: version: 0, flags:; udp: 512
> ;; QUESTION SECTION:
> ;www.inlanefreight.com.         IN      SOA
> 
> ;; AUTHORITY SECTION:
> inlanefreight.com.      900     IN      SOA     ns-161.awsdns-20.com. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400
> 
> ;; Query time: 16 msec
> ;; SERVER: 8.8.8.8#53(8.8.8.8)
> ;; WHEN: Thu Jan 05 12:56:10 GMT 2023
> ;; MSG SIZE  rcvd: 128
> ```
> **Примечание:** В SOA почта администратора `awsdns-hostmaster.amazon.com.` читается как `awsdns-hostmaster@amazon.com`.

---

#### Конфигурация Bind9

В Linux часто используется сервер **Bind9**. Основные файлы конфигурации:
1. `named.conf.local` — описание зон доменов.
2. `named.conf.options` — глобальные параметры.
3. `named.conf.log` — логирование.

> [!info] Настройка зон
> Конфигурация разделена на `global options` (влияют на весь сервер) и `zone options` (приоритетны для конкретной зоны).

**Локальная настройка зоны (/etc/bind/named.conf.local):**
```shellsession
root@bind9:~# cat /etc/bind/named.conf.local
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "domain.com" {
    type master;
    file "/etc/bind/db.domain.com";
    allow-update { key rndc-key; };
};
```

**Файл прямой зоны (/etc/bind/db.domain.com):**
```shellsession
root@bind9:~# cat /etc/bind/db.domain.com
;
; BIND reverse data file for local loopback interface
;
$ORIGIN domain.com
$TTL 86400
@     IN     SOA    dns1.domain.com.     hostmaster.domain.com. (
                    2001062501 ; serial
                    21600      ; refresh after 6 hours
                    3600       ; retry after 1 hour
                    604800     ; expire after 1 week
                    86400 )    ; minimum TTL of 1 day

      IN     NS     ns1.domain.com.
      IN     NS     ns2.domain.com.

      IN     MX     10     mx.domain.com.
      IN     MX     20     mx2.domain.com.

             IN     A       10.129.14.5
server1      IN     A       10.129.14.5
server2      IN     A       10.129.14.7
ns1          IN     A       10.129.14.2
ns2          IN     A       10.129.14.3
ftp          IN     CNAME   server1
mx           IN     CNAME   server1
mx2          IN     CNAME   server2
www          IN     CNAME   server2
```

**Файл обратной зоны (/etc/bind/db.10.129.14):**
```shellsession
root@bind9:~# cat /etc/bind/db.10.129.14
;
; BIND reverse data file for local loopback interface
;
$ORIGIN 14.129.10.in-addr.arpa
$TTL 86400
@     IN     SOA    dns1.domain.com.     hostmaster.domain.com. (
                    2001062501 ; serial
                    21600      ; refresh after 6 hours
                    3600       ; retry after 1 hour
                    604800     ; expire after 1 week
                    86400 )    ; minimum TTL of 1 day

      IN     NS     ns1.domain.com.
      IN     NS     ns2.domain.com.

5    IN     PTR    server1.domain.com.
7    IN     MX     mx.domain.com.
...SNIP...
```

---

### 🔍 Command Breakdown (Deep Dive)

| Команда/Флаг | Описание | Зачем это для CPTS |
| :--- | :--- | :--- |
| `dig ns <domain>` | Запрос серверов имен. | Поиск дополнительных целей (других DNS-серверов) для атаки. |
| `dig CH TXT version.bind` | Запрос версии ПО через класс CHAOS. | Определение точной версии BIND для поиска публичных эксплойтов (CVE). |
| `dig any <domain>` | Запрос всех типов записей сразу. | Быстрый сбор информации о почте (MX), поддоменах и TXT-записях. |
| `dig axfr <domain>` | Попытка передачи зоны (Zone Transfer). | **Критично:** Получение полного списка всех хостов и внутренних IP-адресов без брутфорса. |
| `dnsenum --enum` | Полное автоматическое перечисление. | Экономия времени на экзамене: объединяет AXFR, брутфорс и Google scraping. |

---

### ⚠️ Vulnerable Configurations (Critical)

> [!warning] Опасные настройки BIND9
> 
> | Опция | Описание | Риск |
> | :--- | :--- | :--- |
> | `allow-query` | Кто может отправлять запросы. | Если не ограничена, позволяет внешним злоумышленникам опрашивать сервер. |
> | `allow-recursion` | Кто может делать рекурсивные запросы. | Может привести к DNS Amplification атакам (DDoS). |
> | **`allow-transfer`** | Кто может получать файлы зон (AXFR). | **Самая критичная уязвимость.** Если установлено `any`, любой может скачать всю структуру сети (имена хостов, IP). |
> | `zone-statistics` | Сбор статистики. | Утечка информации об интенсивности использования определенных ресурсов. |

---

### 💡 Pentest Insights

> [!tip] Хакерские хитрости DNS
> - **Banner Grabbing:** Всегда проверяйте версию через `version.bind`. Администраторы часто забывают скрыть эту информацию.
> - **Внутренние зоны:** Если удалось выполнить AXFR для основного домена, попробуйте сделать то же самое для найденных поддоменов (например, `internal.target.htb`). Там часто хранятся данные о DC и внутренних сервисах.
> - **Записи TXT:** Внимательно изучайте их. Там могут быть ключи верификации сторонних сервисов или специфические настройки SPF, раскрывающие IP-диапазоны компании.
> - **Обратный поиск (Reverse Lookup):** Если у вас есть диапазон IP, используйте `dig -x <IP>`, чтобы найти соответствующие имена хостов, даже если прямой перебор не дал результатов.

---

### 🔗 Connections
- **Родители:** [[Footprinting]], [[Network Protocols]]
- **Смежные:** [[Nmap Scripting Engine]], [[Information Disclosure]], [[OSINT]], [[DNS Enumeration]]