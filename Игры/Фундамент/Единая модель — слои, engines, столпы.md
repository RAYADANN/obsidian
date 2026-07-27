# Единая модель — слои, engines, столпы

> Часть: [[00 — фундамент]]  
> Синтез: Starter Pack (7 слоёв + E1–E7) + Obsidian (столпы + creation family)

Три фреймворка из разных исследований описывают **одно и то же** с разных углов. Эта заметка — карта перевода между ними.

---

## Три уровня абстракции

| Уровень | Фреймворк | Вопрос |
|---|---|---|
| **Архитектура** | 7 слоёв (Starter Pack) | Из чего *склеен* хит? |
| **Двигатели** | E1–E7 engines | *Что* возвращает игрока завтра? |
| **Столпы** | Roblox pillars (Obsidian) | *Какие* механики дают CCU? |

---

## 7 слоёв — скелет любой топ-игры

```
HOOK      → понятно ЧТО делать за 10 сек (название + thumbnail)
LOOP      → одно действие + мгновенный feedback каждые 3–30 сек
GROW      → награда усиливает loop (не декор ради декора)
CHASE     → цель, которую нельзя закончить (∞)
SOCIAL    → другие игроки = часть прогресса
RESET     → prestige/rebirth → новый chase
RETURN    → причина зайти завтра (≤ 24ч)
```

**Правило:** минимум **4 из 7** слоёв для 5k–15k CCU. Для 50k+ — **6–7**.

→ [[Starter Pack/01 — мета-паттерн (7 слоёв)]]

---

## 7 engines — что реально крутит retention

| Engine | Суть | RETURN-механизм | Топ-50 |
|---|---|---|---|
| **E1** IDLE | Offline progress | «Накопилось, зайду» | ~14 |
| **E2** NUMBER | Stat растёт + rebirth | «1.5M → хочу 2M» | ~12 |
| **E3** COLLECTION | Rarity, gacha, % | «Нет mythic» | ~22 |
| **E4** SOCIAL | Trade, RP, steal, co-op | «Друзья онлайн» | ~18 |
| **E5** SESSION | Раунды, stakes в сессии | «Ещё одну смену» | ~16 |
| **E6** SKILL | PvP, mastery, rank | «Стану лучше» | ~14 |
| **E7** CULTURE | Мем, тренд, viral | «Все играют» | ~5 (overlay) |

**Правило:** 1 engine = узкий потолок. Хиты комбинируют **2–4 engine**.

→ [[Starter Pack/02 — семь engines (E1–E7)]]

---

## Столпы Roblox — сила механик

| Столп | Сила | Заметка |
|---|---|---|
| Progression | ★★★★★ | Числа растут, открывается новое |
| Collection | ★★★★★ | Редкость, полный сет |
| Events / FOMO | ★★★★★ | «Сейчас или никогда» |
| Gambling / RNG | ★★★★★ | Яйца, мутации, rare drop |
| Creation family | ★★★★☆ | Дорого в арте, но сильно |
| Idle / Offline | ★★★★☆ | Return без burnout |
| Social | ★★★★☆ | Trade, steal, flex |
| Prestige / Rebirth | ★★★★☆ | Сброс → навсегда сильнее |
| Exploration | ★★★★☆ | «Что там дальше?» |
| Tension / Horror | ★★★★☆ | Страх, adrenalin |
| Narrative / Mystery | ★★★☆☆–★★★★★ | Сильный D1, слабый D30 solo |
| Skill / Mastery | ★★★☆☆ | Насыщенные полки |

→ [[Roblox — столпы удержания]]

---

## Маппинг: слой ↔ engine ↔ столп

| Слой | Primary engines | Типичные столпы |
|---|---|---|
| **HOOK** | E7 (culture) | Thumbnail, verb clarity |
| **LOOP** | E2, E5, E6 | Progression, feedback |
| **GROW** | E1, E2 | Progression |
| **CHASE** | E3, E2 | Collection, RNG |
| **SOCIAL** | E4 | Social, trade, co-op |
| **RESET** | E2 | Prestige / Rebirth |
| **RETURN** | E1, E5, events | Idle, FOMO, session stakes |

---

## Creation family — подтип «создания»

Creation — **не отдельный engine**, а семейство из 8 подтипов, усиливающих ownership:

| Sub-type | Пример | Engine overlap |
|---|---|---|
| Construction | Bloxburg | E4 + expression |
| Cultivation | Grow a Garden | E1 |
| **Transformation** | Mining, Deep Signal | E2 + exploration |
| Layout | Tycoon optimization | E2 |
| Customization | Adopt Me | E4 |
| Collection-as-creation | Echo journal | E3 |
| Optimization | Pet loadouts | E2 + E3 |
| Expression | Garden flex | E4 + E7 |

→ [[Creation — семейство механик]]

---

## Sticky stack (Obsidian) = минимальный набор слоёв

```
1 primary loop     (LOOP)
+ 1 ownership      (CHASE / creation sub-type)
+ 1 long-term goal (E3 collection OR E2 prestige)
+ 1 return trigger (E1 idle OR events OR E5 session)
+ strong feedback  (LOOP quality)
```

Это **подмножество** 7 слоёв — минимум для MVP.

→ [[Формула игровой петли]]

---

## Три архетипа топ-50

| Архетип | Engines | Доля топ-50 | Тренд 2026 |
|---|---|---:|---|
| **A — Фабрика** | E1 + E2 + E3 | ~28 | Стабилен, конкуренция высокая |
| **B — Арена** | E5 + E6 (+ E4 co-op) | ~14 | **Быстрейший рост** |
| **C — Площадь** | E4 | ~8 | Evergreen, нужна масса |

**Для solo dev 2026:** архетип **B** (E5 + E3 + E4 co-op) — лучший risk/reward.

Примеры: Animal Hospital, 99 Nights, Fire Shift concept.

---

## Матрица: горизонт × что работает

| Горизонт | Что притягивает (первый контакт) | Что удерживает |
|---|---|---|
| **0–10 сек** | HOOK, thumbnail, verb | — |
| **0–5 мин** | LOOP pleasure, first wow | Session stakes |
| **D1** | Hook + loop + first reward | RETURN timer setup |
| **D3–D7** | — | Collection milestone, first prestige |
| **D8–D28** | — | CHASE ∞ + social + live ops |
| **D30+** | — | Social economy + events cadence |

→ [[Притяжение vs удержание]]

---

## Сильные комбо для solo (40 дней)

| Комбо | Пример | Почему |
|---|---|---|
| **E5 + E3 + E4** | Animal Hospital, Fire Shift | Co-op + collection + clip |
| **E5 + E4** | 99 Nights | Session + friends |
| **E2 + E7** | Keyboard Escape | Viral numbers + theme |
| **E1 + E3 + E4** | Brainrot | Пассив + steal + trade |

## Слабые комбо

| Комбо | Проблема |
|---|---|
| E1 alone | «Ещё один сад» |
| Story only | D30 ≈ 0 |
| E6 alone без E3 | Мало long-tail |
| Narrative + mining без events | Deep Digger trap |

#gamedesign #roblox #model #layers #engines
