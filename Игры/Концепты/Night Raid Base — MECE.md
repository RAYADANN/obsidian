# Night Raid Base — MECE Scorecard

> **Дата:** 2026-07-16  
> **Фаза:** Phase 0 Desk → Converge (Idea Machine)  
> **Режим:** **strict** (как Deep Digger / BaBaS — без баллов «за фичи на бумаге»)  
> **Связь:** [[Night Raid Base — GDD]] · [[../Фундамент/Алгоритм MECE Score — оценка набора механик]]

---

## One-liner

| Поле | Значение |
|---|---|
| **Verb** | `shine` (night) + `build` / `steal` (day) |
| **One-liner** | Днём строй базу и кради pets; ночью чудище лезет за ними — отпугивай фонариком и светом |
| **Архетип** | A (Фабрика) + session pressure |
| **Primary** | E5 (night session stakes) + E2 (build/lights) |
| **Secondary** | E1 idle · E3 collection · E4 day steal |

---

## Scorecard /100 (strict)

### A — HOOK & CLARITY (0–10)

| # | Критерий | Балл | Почему |
|---|---|---:|---|
| A1 | Verb из названия | **1** | «Night Raid» понятно, финальное имя ещё WIP |
| A2 | Thumbnail = геймплей | **2** | Фонарик + монстр + pet = честный hook |
| A3 | Без text tutorial | **2** | Dusk warning + луч света самоочевидны |
| A4 | Diff vs топ-3 полки | **1** | Рядом с SAB/BaBaS; twist механический, полка та же |
| A5 | Clip 15 сек | **2** | Fear meter → монстр бежит |
| **A** | | **8** | |

### B — LOOP QUALITY (0–10)

| # | Критерий | Балл | Почему |
|---|---|---:|---|
| B1 | Одно core action | **1** | Два режима (day/night) — ясно, но не один verb |
| B2 | Feedback каждое действие | **2** | Fear meter, $/s, light cone |
| B3 | Enjoyable без прогрессии | **1** | Desk only — не playtested (strict) |
| B4 | Wow ≤60 сек | **2** | Стена + income + первый dusk |
| B5 | Session target | **2** | Цикл 5–6 мин = цель «пережить ночь» |
| **B** | | **8** | |

### C — ENGINE STACK (0–20)

| # | Критерий | Балл | Почему |
|---|---|---:|---|
| C1 | Primary engine чётко | **2** | Night stakes (E5) + build/lights (E2) |
| C2 | Secondary | **2** | E3 pets |
| C3 | 2+ engine, не клон одного | **1** | Stack всё ещё SAB-adjacent |
| C4 | RETURN ≤24ч | **2** | Ночь каждые ~5 мин + offline soft |
| C5 | CHASE ∞ | **2** | Pet rarities |
| C6 | RESET | **1** | Rebirth в GDD, не в PROVE |
| C7 | SOCIAL | **2** | Day steal |
| C8 | D30 ≠ story | **2** | Collection + night + rebirth |
| C9 | Полка не 🔴 | **0** | Steal/tycoon clones = 🔴 ([[../Starter Pack/03 — топ-50 паттерны]]) |
| C10 | +20% diff vs #1 | **1** | Night+light сильный twist; SAB может скопировать |
| **C** | | **15** | |

### D — LAYER COVERAGE (0–14)

| Слой | Балл |
|---|---:|
| HOOK | 2 |
| LOOP | 2 |
| GROW | 2 |
| CHASE | 2 |
| SOCIAL | 2 |
| RESET | 1 |
| RETURN | 2 |
| **D** | **13** |

### E — MARKET & TIMING (0–16)

| # | Критерий | Балл | Почему |
|---|---|---:|---|
| E1 | Genre CCU/game | **1** | Жёлтая: BaBaS ~15k доказал mid, SAB доминирует |
| E2 | Trend Δ | **1** | Steal cooling, ещё ест |
| E3 | #2 с twist | **1** | Можно; first-mover SAB непреодолим на топ |
| E4 | Window открыт | **1** | Light/night в tycoon — не dying, не virgin |
| E5 | Gap | **1** | Flashlight-defend в steal shelf слабо занят |
| E6 | Realistic tier | **2** | P2: 5–15k; P1 stretch 50k |
| E7 | MVP ≤8 / ≤40d | **2** | PROVE 4 systems |
| E8 | Live ops weekly | **1** | План есть; cadence не proven |
| **E** | | **10** | |

### F — DISCOVER & RFY (0–14)

| # | Критерий | Балл | Почему |
|---|---|---:|---|
| F1 | D8–28 hook | **1** | Night+pets да; rebirth depth = v0.3 |
| F2 | Co-play | **2** | Free PS + day steal |
| F3 | Honest thumb | **2** | |
| F4 | Mobile-first | **1** | Flashlight на touch — риск |
| F5 | Reveal/clip | **2** | |
| F6 | Monetization OK | **2** | Cosmetic/convenience |
| F7 | Distro до dev | **1** | Hooks planned, clips не сняты |
| **F** | | **11** | |

### G — SHIP READINESS (0–16)

| # | Критерий | Балл | Почему |
|---|---|---:|---|
| G1 | Graybox ≤5d | **2** | |
| G2 | Data-driven | **2** | |
| G3 | Cut list | **2** | |
| G4 | Kill criteria | **2** | |
| G5 | New identity | **2** | Greenfield place (не mining reskin) |
| G6 | Anti-patterns | **1** | Steal → v0.2; всё ещё 🔴-полка risk |
| G7 | 3 thumb hooks | **1** | Planned |
| G8 | 2 weekly updates | **1** | Written in GDD |
| **G** | | **13** | |

---

## Итог

```
A8 + B8 + C15 + D13 + E10 + F11 + G13 = 78 / 100
```

| | |
|---|---|
| **MECE TOTAL** | **78 / 100** |
| **Вердикт** | 🔥 **Сильная** — приоритет CONVERGE → PROVE |
| Gate → MAP | ✅ ≥55 |
| Gate → GRAYBOX | ✅ C≥12 (15) · D≥8 (13) |
| Kill | ❌ нет |

### Сравнение (strict)

| Идея                 |   MECE | Вердикт        |
| -------------------- | -----: | -------------- |
| Steal a Brainrot     |   ~85+ | Reference      |
| **Night Raid Base**  | **78** | Priority PROVE |
| Fire Shift (concept) |    ~82 | Priority       |
| Deep Bunker          |    ~70 | Alt path       |
| BaBaS as-is          | ~49–54 | Mid clone      |
| Deep Digger          | ~28–35 | Pivot          |

### Главный риск (не в score)

Полка **🔴** (C9=0) — весь upside держится на том, что **night + light** ощущаются как новая игра, а не «BaBaS с монстром». PROVE должен убить или подтвердить именно это.

### Predictions (до PROVE)

| Metric | Predicted |
|---|---|
| Tier | P2 (5–15k), stretch P1 |
| PROVE session median | > 4 min |
| «Want another night?» | > 70% |
| Kill mode if fail | Verb/feel (flashlight chore) OR hook (night = wait) |

#gamedesign #mece #night-raid
