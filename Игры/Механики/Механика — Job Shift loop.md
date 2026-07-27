# Механика — Job / Shift loop

> Часть: [[00 — оглавление]] · Core: **C3** · Engine: **E5**

## Суть
Игрок **выполняет рабочие задачи** в смене с escalating difficulty. Каждая смена = mini-session с stakes.

## Core verb
`scan` · `treat` · `reject` · `restock` · `rescue` · `extinguish`

## Что цепляет
- **«Something is wrong»** — anomaly among normal tasks
- **Time pressure** — queue grows, shift timer
- **Competence** — «I learned to spot fakes»
- **Co-op panic** — «DON'T put that on shelf 7!»

| Hook сила | ★★★★★ (2026 trend) |

## Что удерживает
- **Shift # counter** — «I'll reach shift 50»
- **New task/anomaly types** — live ops content
- **Codex** — catalog of seen anomalies
- **Co-op appointment** — friends at 8pm

| D1 | D7 | D28 |
|---|---|---|
| ★★★★★ | ★★★★ | ★★★★ |

## Feedback requirements

| Moment | Feedback |
|---|---|
| Correct action | Green flash + cha-ching + XP |
| Wrong action | Red flash + alarm + sanity/health drop |
| Anomaly reveal | Distortion VFX + sting + screen shake |
| Shift end | Summary screen + shift # + rewards |

## Roblox примеры

| Игра | Job | CCU |
|---|---|---|
| Animal Hospital | Vet check patients | S |
| Scary Grocery | Store night shift | niche |
| Fire Shift (concept) | Firefighter calls | hypothesis |
| Clean The Library | Sort books speedrun | B |

## Solo dev

| | |
|---|---|
| **Complexity** | Medium (task system + anomaly RNG) |
| **Art** | Medium (1 location, reusable NPCs) |
| **MVP** | 1 map, 3 task types, 3 anomalies, shift timer |
| **Netcode** | Co-op adds complexity — start solo + AI helper OR 2P |

## Лучшие комбо

| Partner | Why |
|---|---|
| Anomaly / Horror | Hook engine |
| Co-op roles | Info asymmetry |
| Collection codex | D28 chase |
| Incremental $ | Grow between shifts |
| Events | Weekly anomaly |

## Anti-patterns

- Pure RP job (ER:LC) without stakes loop
- Same 3 tasks forever — no live ops
- Anomaly = jump scare only (no detection gameplay)
- Realistic sim scope (full city) on MVP

## MVP spec (Fire Shift example)

```
DAY:   training calls, gear check, earn $
NIGHT: dispatch → arrive → anomaly OR normal fire
       3 anomaly types, 5–7 min target
META:  shift #, codex 3/50, co-op 2 roles minimum
```

→ [[../Roblox — исследование идей/Fire Shift — пожарные + anomaly (альтернатива)]]

#gamedesign #roblox #mechanics #job #shift
