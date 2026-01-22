## Security Assessment Report: Information Disclosure & Internal API Leakage

**Target:** `klook.com` / `log.klook.com` **Severity:** **Medium (6.1)** — согласно CVSS v3.1 (AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:L/A:N) **CWE:** [CWE-200: Exposure of Sensitive Information to an Unauthorized Actor](https://www.google.com/search?q=https://cwe.mitre.org/data/definitions/200.html) **CVE Reference:** Данная находка является **Security Misconfiguration** (ошибкой конфигурации), специфичной для Klook, поэтому она не имеет уникального CVE (CVE присваиваются софту, а не конкретным сайтам), но попадает под паттерны уязвимостей типа **Insecure Log Storage / Exposure**.

---

### 1. Executive Summary

В ходе исследования инфраструктуры Klook была обнаружена утечка внутренней конфигурации фронтенда на партнерских поддоменах (`*.partner.klook.com`). Утечка раскрывает внутренние API-эндпоинты для сбора логов и аналитики (`log.klook.com`), а также токены авторизации и структуру микросервисов.

Экспериментальным путем было подтверждено, что эндпоинты логирования принимают произвольные данные от неавторизованных пользователей, что позволяет осуществлять **Log Injection** и потенциально манипулировать данными мониторинга.

---

### 2. Vulnerability Details

#### A. Frontend Environment Leakage

В глобальном объекте `window.__conf_env` на страницах партнерских ресурсов содержатся следующие чувствительные данные:

- **Internal API URLs:** Пути к `v1`, `v2` и `v3` сервисам логирования.
    
- **Service Map:** Список названий внутренних сервисов (например, `airport-transfer-web`, `hotel-web`), что упрощает проведение целевых атак (Reconnaissance).
    
- **Auth Tokens:** Использование `node_...` токенов в открытом виде для взаимодействия с лог-сервером.
    

#### B. Unauthorized API Access (Log Manipulation)

Сервер `https://log.klook.com/v2/frontlogsrv/log/web` некорректно проверяет входящие запросы. Отсутствие строгой валидации на стороне сервера позволяет:

1. Отправлять поддельные логи от имени любого пользователя.
    
2. Засорять систему мониторинга (DoS на уровне хранилища логов).
    
3. Проводить XSS-атаки через поля логов, если администраторы просматривают их в незащищенной панели управления.
    

---

### 3. Steps to Reproduce (PoC)

1. **Обнаружение данных:** Открыть исходный код страницы любого партнерского сайта Klook и найти объект `__conf_env`.
    
2. **Эксплуатация API:** Выполнить следующий запрос через терминал (используя данные из твоей находки):
    
    Bash
    
    ```
    curl -X POST https://log.klook.com/v2/frontlogsrv/log/web \
    -H "Content-Type: application/json" \
    -d '{"service":"hotel-web","level":"error","message":"SYSTEM_BREACH_TEST","payload":{"unauthorized_access":true}}'
    ```
    
3. **Результат:** Сервер возвращает статус `200 OK`, подтверждая прием данных без какой-либо проверки прав доступа.
    

---

### 4. Impact

- **Infrastructure Reconnaissance:** Раскрытие структуры сети и именования сервисов.
    
- **Data Integrity:** Злоумышленник может наполнить логи ложной информацией, скрывая следы реальной атаки или вызывая ложные срабатывания у команды безопасности (SOC).
    
- **Compliance Risk:** Утечка технических деталей конфигурации нарушает стандарты безопасной разработки (OWASP ASVS).
    

---

### 5. Recommendations (Remediation)

1. **Sanitize Frontend Config:** Удалить чувствительные URL и названия сервисов из публичного JS-объекта. Использовать относительные пути.
    
2. **Implement Rate Limiting:** Ограничить количество запросов к эндпоинтам логирования с одного IP.
    
3. **Strict Validation:** Внедрить проверку `Origin` и `Referer` заголовков на стороне `log.klook.com`, чтобы принимать данные только от доверенных доменов.