# Grow a Garden — разбор

> Часть: [[Механики удержания — обзор]] · Кейс [[Roblox — столпы удержания]]

Почему cozy farming sim стал одной из крупнейших игр Roblox (до ~9M CCU в пике, 2025).

---

## Core loop

```
Plant → Water → Harvest → Sell → Expand
```

Понятен за **60 секунд**. Каждый шаг = instant feedback (sparkle, sound, coin burst, +XP).

Петля **не просто повторяется — эволюционирует**: новые crops, mutations, gear, expansion.

→ [[Формула игровой петли]]

---

## Разбор по семейству Creation

| Sub-type | Как реализован |
|---|---|
| [[Creation — семейство механик#Cultivation\|Cultivation]] | Посадка, рост, harvest |
| [[Creation — семейство механик#Layout / Planning\|Layout]] | Расстановка грядок, expansion |
| [[Creation — семейство механик#Transformation\|Transformation]] | Мутации меняют вид crop |
| [[Creation — семейство механик#Optimization\|Optimization]] | Stacking mutations, gear, pets |
| [[Creation — семейство механик#Expression\|Expression]] | Визуально уникальный сад |
| [[Creation — семейство механик#Collection-as-Creation\|Collection]] | Rare seeds, pets, mutations |

---

## Столпы помимо creation

| Столп | Реализация |
|---|---|
| **Idle / Offline** | Crops растут офлайн → retention без burnout |
| **Events / FOMO** | Weekly drops, countdowns, limited seeds |
| **Social** | Steal / gift crops → stories, mischief, mentorship |
| **Gambling / RNG** | Mutations, rare crops |
| **Progression** | Exponential economy, gear tiers |
| **Community** | 4.8M+ Discord, активное комьюнити |

---

## Почему сработало (5 причин)

1. **Cozy aesthetic + low friction** — antidote к exhausting competitive games
2. **Meaning, not just mechanics** — steal/gift создаёт *истории*, не только gameplay
3. **Events as culture** — обновления = события, не «патч-ноуты»
4. **Shareability** — визуал + surprise moments = TikTok/YouTube content
5. **Offline growth** — novel для Roblox на момент запуска; return trigger

---

## Урок для дизайна

> Grow a Garden — **cozy wrapper** вокруг **simulator engine**.

Сад — не цель. Сад — **носитель** для:
- экономики (sell → upgrade)
- коллекции (rare mutations)
- событий (FOMO)
- социальности (steal/gift stories)

**Не нужна groundbreaking идея.** Нужны:
- well-balanced loop
- beautiful feedback
- monetization в моменты эмоции
- 1 retention system (daily / offline / event)

---

## Цитата создателя (Jandel Madsen, GamesBeat)

> «Farming and gardening has been innate to humans for thousands of years… Everyone likes to farm, to watch something grow. And the whole idea of the garden growing offline, the persistent world.»

---

## Связанные заметки

- [[Creation — семейство механик]]
- [[Roblox — столпы удержания]]
- [[Mining и Deep Signal — применение]] — контраст: cozy vs horror-lite mining

#gamedesign #roblox #case-study #grow-a-garden
