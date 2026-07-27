# Алгоритм MECE Score — оценка набора механик

> Часть: [[00 — фундамент]]  
> **MECE** = Mutually Exclusive, Collectively Exhaustive — каждый аспект идеи проверяется ровно один раз.  
> Синтез: Starter Pack scorecard /40 + Obsidian checklist /35 + 7 слоёв + engines.

**Использовать до Phase 1 (до кода).** Повторять после graybox и soft launch.

> **Dual process:** вместе с [[../Метод Tizzy/05 — Tizzy Scorecard|Tizzy /40]] → протокол [[../Метод Tizzy/06 — Dual Analysis MECE × Tizzy]] · шаблон [[../Метод Tizzy/Шаблон — анализ идеи]]
---

## Вердикты по итоговому score

| Score | /100 | Вердикт | Действие |
|---:|---:|---|---|
| 0–39 | ❌ | Мёртвая идея | Переделать verb или engine combo |
| 40–54 | ⚠️ | Слабая | Pivot hook или добавить engine |
| 55–69 | ✅ | Можно в прототип | Graybox 3–5 дней |
| 70–84 | 🔥 | Сильная | Приоритет, converge + prove |
| 85–100 | ⭐ | Исключительная | Ship fast, не over-polish |

**Для топ-10 амбиции:** target **≥ 70** на desk research + **≥ 65** после graybox signal.

---

## Алгоритм (5 фаз)

```
Phase 0  DESK     (30 мин)   → MECE Score /100
Phase 1  MAP      (15 мин)   → Engine stack + layer coverage
Phase 2  GRAYBOX  (3–5 дней) → Loop signal (без прогрессии!)
Phase 3  SOFT     (2 нед)   → D1/D7 vs benchmarks
Phase 4  SCALE    (ongoing)  → D28 + live ops + RFY expand
```

**Kill gates между фазами — обязательны.** Не переходи без прохождения gate.

---

## Phase 0 — Desk (30 мин)

### Шаг 1: One-liner (обязательно)

```markdown
**Verb:** [одно слово — dig, steal, reject, sort, survive...]
**One-liner:** [Игрок делает X в мире Y, чтобы получить Z]
**Архетип:** A (Фабрика) / B (Арена) / C (Площадь)
**Primary engine:** E_
**Secondary engine:** E_
```

**Kill if:** не можешь заполнить за 5 минут.

---

### Шаг 2: MECE Scorecard /100

Оцени **0 / 1 / 2** за каждый пункт (0 = нет, 1 = слабо, 2 = сильно).

#### Блок A — HOOK & CLARITY (0–10) — *притяжение*

| # | Критерий | 0 | 1 | 2 |
|---|---|---|---|---|
| A1 | Verb понятен из **названия** за 3 сек | | | |
| A2 | Thumbnail обещание = **реальный** геймплей | | | |
| A3 | Играбельно **без text tutorial** | | | |
| A4 | Отличие от **топ-3** в своей полке | | | |
| A5 | Clip test: объяснимо за **15 сек** TikTok | | | |

**Subtotal A:** ___ / 10

#### Блок B — LOOP QUALITY (0–10) — *удовольствие действия*

| # | Критерий | 0 | 1 | 2 |
|---|---|---|---|---|
| B1 | Одно core action, повторяемое | | | |
| B2 | Feedback на **каждое** действие (SFX+VFX+число) | | | |
| B3 | Loop enjoyable **без прогрессии** (Dubit rule) | | | |
| B4 | Первый reward / wow в **≤ 60 сек** | | | |
| B5 | Session target понятен (shift, round, depth run) | | | |

**Subtotal B:** ___ / 10  
*(На desk — оцени по дизайну; на graybox — пересчитай по playtest)*

#### Блок C — ENGINE STACK (0–20) — *удержание-архитектура*

| # | Критерий | 0 | 1 | 2 |
|---|---|---|---|---|
| C1 | Primary engine (E1/E2/E5/E4) **чётко выбран** | | | |
| C2 | Secondary engine (обычно E3) | | | |
| C3 | **2+ engine**, не клон одного хита | | | |
| C4 | **RETURN timer** ≤ 24ч (offline/event/fear/session) | | | |
| C5 | **CHASE** бесконечен (%, число, shift ∞, rarity) | | | |
| C6 | RESET слой (prestige / milestone / new zone) | | | |
| C7 | SOCIAL hook (co-op/trade/leaderboard/steal-lite) | | | |
| C8 | D30 план **не завязан на story** | | | |
| C9 | Полка **не 🔴** (см. [[Starter Pack/03 — топ-50 паттерны]]) | | | |
| C10 | **+20% differentiation** vs #1 в полке | | | |

**Subtotal C:** ___ / 20

#### Блок D — LAYER COVERAGE (0–14) — *7 слоёв мета-паттерна*

Отметь каждый слой: 0 = нет, 1 = слабо, 2 = сильно.

| Слой | Есть? | Балл |
|---|---|---:|
| HOOK | | |
| LOOP | | |
| GROW | | |
| CHASE | | |
| SOCIAL | | |
| RESET | | |
| RETURN | | |

**Subtotal D:** ___ / 14  
**Минимум для ship:** ≥ 8/14 (4+ слоя с баллом ≥ 1)

#### Блок E — MARKET & TIMING (0–16) — *рынок*

| # | Критерий | 0 | 1 | 2 |
|---|---|---|---|---|
| E1 | Genre CCU/**game** (не total) — зелёная/жёлтая зона | | | |
| E2 | Trend direction: CCU Δ **растёт** или stable | | | |
| E3 | First-mover moat: можно быть **#2 с twist** | | | |
| E4 | Cultural/trend window **открыт** (не dying format) | | | |
| E5 | Gap score (Market Friday / Rolimons) | | | |
| E6 | Realistic CCU tier plan (5k / 15k / 50k) | | | |
| E7 | MVP scope ≤ **8 systems**, ≤ **40 дней** solo | | | |
| E8 | Live ops plan (**weekly** minimum) | | | |

**Subtotal E:** ___ / 16

#### Блок F — DISCOVER & RFY (0–14) — *алгоритм 2026*

| # | Критерий | 0 | 1 | 2 |
|---|---|---|---|---|
| F1 | D8–28 hook спроектирован (не только D1 wow) | | | |
| F2 | Intentional **co-play** возможен by design | | | |
| F3 | Thumbnail honest → низкий bounce risk | | | |
| F4 | Mobile-first UI/perf | | | |
| F5 | Reveal moment (clip bait) **запланирован** | | | |
| F6 | Monetization cosmetic/convenience, **не P2W** | | | |
| F7 | Distribution: clip/co-op **до dev**, не после | | | |

**Subtotal F:** ___ / 14

#### Блок G — SHIP READINESS (0–16) — *исполнимость*

| # | Критерий | 0 | 1 | 2 |
|---|---|---|---|---|
| G1 | Core verb проверяем за **≤ 5 дней** graybox | | | |
| G2 | Data-driven content (новый предмет = строка в таблице) | | | |
| G3 | Cut list написан («НЕ в MVP») | | | |
| G4 | Kill criteria записаны (D1, session) | | | |
| G5 | Reuse infra OK, **identity новая** | | | |
| G6 | Anti-patterns проверены ([[Starter Pack/08 — anti-patterns]]) | | | |
| G7 | 3 thumbnail hooks готовы / planned | | | |
| G8 | First 2 weekly updates **написаны** | | | |

**Subtotal G:** ___ / 16

---

### Шаг 3: Итог Phase 0

```
MECE TOTAL = A + B + C + D + E + F + G = ___ / 100
```

| Gate | Условие |
|---|---|
| → Phase 1 MAP | Total ≥ 55 |
| → Phase 2 GRAYBOX | Total ≥ 55 AND C ≥ 12 AND D ≥ 8 |
| ❌ Kill / backlog | Total < 40 OR C < 8 OR A < 4 |

---

## Phase 1 — MAP (15 мин)

Нарисуй **engine stack** и **layer coverage** одной схемой:

```
[HOOK: verb + thumbnail]
        ↓
[LOOP: action → feedback] ←── GROW (spend → stronger)
        ↓
[CHASE: ∞ target] ←── RESET (prestige)
        ↓
[RETURN: timer] ←── SOCIAL (co-op/trade)
        ↓
[LIVE OPS: weekly injection]
```

**Проверка MECE:** каждый engine покрывает ровно один RETURN-механизм?

| Engine | RETURN mechanism | Day target |
|---|---|---|
| E_ | | D1 / D7 / D28 |

**Red flags auto-kill:**
- Story = единственный RETURN
- E1 alone без differentiation
- Нет SOCIAL и нет strong E5 session stakes
- 🔴 полка + score < 60

---

## Phase 2 — GRAYBOX (3–5 дней)

**Цель:** проверить **B3 — Loop enjoyable без прогрессии** (Dubit base layer).

### Делай
- Placeholder art, только verb + feedback
- 5–10 playtesters
- Спроси: «Хочешь ещё раз?» «Понял без текста?»

### Не делай
- Rebirth, pets, monetization, lore

### Метрики graybox

| Метрика | Kill | Pivot | Go |
|---|---|---|---|
| Session median | < 1.5 min | 1.5–3 min | > 3 min |
| «Хочешь ещё?» | < 50% | 50–70% | > 70% |
| Понял без текста | < 60% | — | > 80% |

**Gate → Phase 3:** Go на session + понял без текста.  
**Kill:** Jandel rule — nobody plays prototype → **смени verb**, не добавляй features.

**Пересчитай блок B** по факту playtest.

---

## Phase 3 — SOFT LAUNCH (2 недели)

| Метрика | Kill | Pivot hook | Scale |
|---|---|---|---|
| D1 | < 15% | 15–20% | > 25% |
| 2nd session same day | < 20% | 20–30% | > 35% |
| Median session D1 | < 5 min | 5–8 min | > 12 min |
| First play bounce | > 50% | 40–50% | < 40% |
| Organic clips | 0 | 1–2 | 3+ |

**Kill if ALL three:** D1 < 15% AND 2nd session < 20% AND session < 5 min.  
→ Не добавляй контент. Чини **LOOP или HOOK**.

---

## Phase 4 — SCALE (D28 + RFY)

Оптимизируй под **D8–28** (главный RFY приоритет 2026):

| Приоритет | Система | Практика |
|---|---|---|
| 1 | Collection depth | Новые tiers, codex, rare variants |
| 2 | Live ops cadence | Weekly event, 1–3 data entries |
| 3 | Co-play | Friend bonus, co-op roles, leaderboard |
| 4 | Prestige layer 2 | Evolution, ascension, new zone |
| 5 | Social economy | Trade-lite, flex, steal-lite (v0.2) |

→ [[Roblox — исследование рынка/RFY алгоритм — сигналы 2026]]

---

## Быстрый калькулятор (5 минут)

Если нет времени на полный /100 — **минимальный score /35**:

| Блок | Max | Минимум для prototype |
|---|---:|---:|
| A Hook | 5 | 3 |
| C Engines (половина) | 10 | 6 |
| C RETURN + CHASE | 4 | 3 |
| E Scope + live ops | 4 | 2 |
| F Co-play + honest thumb | 4 | 2 |
| G Graybox-ready | 4 | 2 |
| D Layers (≥4 слоя) | 4 | 2 |
| **TOTAL** | **35** | **≥ 20** |

*(Упрощённая версия Obsidian checklist /35 + Starter Pack /40)*

---

## Примеры scored

### Fire Shift (reference)

| Блок | Score | Notes |
|---|---:|---|
| A | 9/10 | Verb clear, clip strong |
| B | 8/10 | Co-op roles natural |
| C | 17/20 | E5+E3+E4, RETURN=shift # |
| D | 12/14 | 6/7 layers |
| E | 13/16 | Gap 8/10, trend rising |
| F | 12/14 | Co-op core, D28 plan |
| G | 11/16 | MVP 2 weeks spec exists |
| **TOTAL** | **~82/100** | 🔥 Priority |

### Deep Digger (as-is, post-mortem — strict score)

> **Ошибка v1 (55/100):** засчитывали «есть rebirth/pets/daily» и «infra built» как силу идеи.  
> **Правило v2:** оцениваем **позиционирование + stack vs рынок + RFY 2026**, не completeness checklist.

| Блок | Score | Notes |
|---|---:|---|
| A | 3/10 | Verb generic; нет clip; 🔴 не отличим от топ-3 mining |
| B | 5/10 | Loop рабочий, но не standout |
| C | 5/20 | E2+E3 clone; RETURN=daily only; SOCIAL≈0; D30 plan нет |
| D | 6/14 | Нет HOOK, SOCIAL, strong RETURN |
| E | 2/16 | 🔴 mining saturated; нет gap/trend/live ops |
| F | 3/14 | No co-play; no D28 |
| G | 4/16 | Infra ≠ hit-ready; anti-patterns нарушены |
| **TOTAL** | **~28–35/100** | ❌ Pivot identity, не scale |

### «Underwater GaG» (anti-example)

| **TOTAL** | **~25/100** | ❌ Kill — E1 only, 🔴 полка |

---

## Шаблон one-pager (копируй в новую заметку)

```markdown
# [Название] — MECE One-Pager

**Date:** 
**Verb:** 
**Engines:** E_ + E_ + E_
**Архетип:** A / B / C

## 10-sec hook


## RETURN (зачем завтра)


## CHASE (что ∞)


## Clip moment


## MVP systems (max 8)
1.
2.
3.

## НЕ в MVP
-

## MECE Score: /100
A: /10  B: /10  C: /20  D: /14  E: /16  F: /14  G: /16

## Verdict: ❌ / ⚠️ / ✅ / 🔥 / ⭐

## Kill criteria
- Graybox session < ___ min
- D1 < ___%
```

---

## Связанные документы

- [[Единая модель — слои, engines, столпы]]
- [[Притяжение vs удержание]]
- [[Верификация принципов — подтверждено и oспорено]]
- [[Starter Pack/09 — idea checklist scorecard]]
- [[Roblox — исследование рынка/Чеклист — оценка идеи перед dev]]

#gamedesign #roblox #algorithm #mece #scorecard
