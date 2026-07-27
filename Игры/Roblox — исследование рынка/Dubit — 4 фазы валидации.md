# Dubit — 4 фазы валидации

> Часть: [[00 — оглавление]]  
> Самый **документированный** pre-build pipeline на Roblox.  
> Источник: [How We Build Hit Roblox Games](https://dubit.io/blog/how-we-build-hit-roblox-games)

---

## Обзор

| Фаза | Вопрос | Метод | Kill if |
|---:|---|---|---|
| **1** | Do people want it? | Fake game pages + Roblox ads + **opt-in rate** | Weak CPC / low opt-in vs genre |
| **2** | Is core fun? | Graybox prototype, **session time only** | Seconds, not minutes |
| **3** | Will they return? | +progression, public unbranded launch, **D1/D7** | Below genre benchmark |
| **4** | Is it awesome? | Brand, polish, social hooks, organic growth | — (scale) |

> *«Concept below threshold for its genre is killed or reworked. Concept that outperforms is fast-tracked.»*

---

## Phase 1 — Do people want it?

**До написания кода.**

- Несколько **Coming Soon** страниц (artwork, description)
- Traffic через Roblox ads
- **Opt-in rate** на launch notifications = signal
- Cheap click = attention; high opt-in = **holds** interest

**Инструменты Dubit:** Game Spark (concepts), Storefront (pages)

---

## Phase 2 — Is core play fun?

- Playable prototype: **placeholder art**, no progression, no monetization
- Метрика: **session time** (minutes, not seconds)
- Только core action — понятно за секунды?
- Player acquisition pennies → real players in days

**Kill:** игроки уходят через секунды

---

## Phase 3 — Will players return?

- Retention layer: progression, unlocks, goals
- **Public unbranded launch** — no reputational risk
- Metrics: **D1, D7**, shape of retention curve
- Benchmark **per genre** (tycoon ≠ obby ≠ competitive)

**Kill:** below genre threshold → shelve

---

## Phase 4 — Is it awesome?

- Brand identity, polish, social/sharing hooks
- **Minimum Awesome Product** = desirability + fun + retention + brand
- Organic growth, friend invites

---

## Применение solo (упрощённо)

| Dubit phase | Solo equivalent |
|---|---|
| 1 | Icon + title test, малый sponsored test, «would you play?» в Discord |
| 2 | Unlisted prototype, смотреть session length |
| 3 | Public beta, Creator Dashboard D1/D7 |
| 4 | Icon polish, weekly updates, clip-friendly events |

→ [[Чеклист — оценка идеи перед dev]]

#gamedesign #roblox #validation #dubit
