# Deep Bunker — Game Design Document

> **Статус:** Phase 0 Desk · концепт  
> **Жанр:** Build / Defend / Steal / Tycoon (Simulation)  
> **Платформа:** Roblox · mobile-first · 6 players / server  
> **Обновлено:** 2026-07-16  
> **MECE (desk, strict):** ~65–72 / 100  
> **Связь:** [[../Фундамент/Алгоритм MECE Score — оценка набора механик|MECE]] · [[../Механики/Как комбинировать механики — рецепты|Рецепт Passive Empire]] · [[../Creation — семейство механик|Creation]] · [[../Механики/Механика — Prestige Rebirth|Rebirth]]

---

## 1. Elevator pitch

**One-liner:**  
Игрок строит подземный бункер, ставит в него сигналы (редкие аномальные объекты), защищает их и крадёт чужие — потом rebirth глубже, чтобы строить снова.

**Pitch (15 сек):**  
Ты инженер подземелья. Роллишь странные сигналы, ставишь их в свой бункер — они качают деньги. Соседи лезут в твой лабиринт. Ты бьёшь батом и ставишь ловушки. Когда бункер maxed — снос, спуск глубже, новые детали, новый chase.

**Core verbs:** `build` · `roll` · `place` · `defend` · `steal` · `rebirth`

**Архетип:** A (Фабрика) + social PvP-lite  
**Primary engine:** E2 (build / prestige)  
**Secondary:** E1 idle · E3 collection · E4 steal  

---

## 2. Positioning

| | SAB / BaBaS | Deep Bunker |
|---|---|---|
| Noun | Brainrot / pets | **Сигналы / аномалии** |
| Creation | База вокруг слотов | **Бункер из комнат** — layout = геймплей |
| Steal | Подбежал → взял | Забежал в **чужой лабиринт** |
| Rebirth | Floors / multiplier | **Новая глубина = новый холст** |
| Вайб | Мемы, хаос | Тёмный tech-glow, напряжение |
| Diff | — | Подземная инженерия + layout stakes |

**Не делать:** клон «Build a Base and Steal с другим скином».  
**Делать:** тот же жанровый stack, другой noun + build-as-gameplay + rebirth-as-canvas.

---

## 3. Fantasy & tone

- **Fantasy:** «Я спроектировал крепость под землёй и охраняю добычу»  
- **Tone:** dark industrial, soft horror (свет, статика, гул) — **не** jump-scare, не gore  
- **Social fantasy:** «смотри мой бункер» + «я украл у тебя Mythic»  
- **Clip bait:** mythic signal reveal · успешный steal · tour базы после rebirth  

---

## 4. Core loop

```
ROLL signal
    ↓
BUILD bunker (стены / комнаты / ловушки)
    ↓
PLACE signal → $/sec
    ↓
DEFEND (lock / traps / bat)
    ↓
STEAL from open bunkers
    ↓
SPEND → снова BUILD
    ↓
[потолок] → REBIRTH → глубже → новый plot + parts
    ↓
EVENT cadence (RETURN между rebirth)
```

### Типы наград за один цикл

| Тип | Пример |
|---|---|
| Числовая | +$/sec |
| Предметная | Rare signal |
| Творческая / пространственная | Новый этаж, видимый глазами |
| Социальная | Steal / flex layout |
| Страх потери | Украли твой Mythic |

**Правило:** ≥3 типа награды за цикл — sticky.

---

## 5. Session flow

### First 15 minutes (D1 hook)

| Мин | Beat |
|---:|---|
| 0–2 | Пустая яма. «Поставь первую стену» |
| 2–5 | Комната + free Common signal → +$1/s |
| 5–8 | Roll ×3, Uncommon → доход ×3 |
| 8–12 | Metal walls, 2-я комната |
| 12–15 | Steal у открытого соседа / первый defend |

**Wow ≤60 сек:** первая стена + первый $/sec на экране.

### Typical session (10–25 мин)

1. Collect offline  
2. Spend на build / roll  
3. 1–2 raid попытки  
4. Defend / re-layout  
5. Event, если активен  
6. Progress toward rebirth  

---

## 6. Systems

### 6.1 World layout

- **6 bunkers** на сервере (maxPlayers = 6)  
- Общая **Roll Machine** в центре  
- Каждый бункер = вертикальный plot (яма вниз)  
- Private servers: **free** (viral co-play с друзьями)

```
[ Roll ]───────────────
   │
[B1] [B2] [YOU] [B3] [B4] [B5]
```

### 6.2 Build (PRIMARY — creation drug)

**Модель:** prefab grid, **не** free voxel build (mobile + scope).

| Элемент | MVP | Later |
|---|---|---|
| Wall tiers | Wood → Metal → Reinforced | Neon / Anomaly |
| Room types | Generator bay · Signal pedestal · Empty | Vault · Trap corridor |
| Traps | 1 type (spike / laser gate) | 3–4 types |
| Floors | 1–2 этажа | +1 этаж per rebirth |

**Правила:**
1. Layout **виден глазами** — не только HUD  
2. Layout влияет на steal (путь до Mythic длиннее)  
3. Один primary creation sub-type: **Construction + Layout**  
4. Не смешивать freebuild + crafting + декор в MVP  

**Spend loop:** cash → walls / rooms / traps → safer / prettier / stronger income capacity.

### 6.3 Signals (collection / chase)

Сигналы = аномальные объекты на пьедесталах. Генерируют `/sec`.

| Rarity | Пример vibe | Relative $/s |
|---|---|---:|
| Common | Тусклый кристалл | 1× |
| Uncommon | Пульсирующая сфера | 3× |
| Rare | Живой провод | 10× |
| Epic | Статика + шум | 40× |
| Mythic | Экран трясётся при place | 200× |

**Traits (v0.3+):**  
«×2 ночью» · «+10% resist steal» · «aura boost соседям» — data rows, не отдельные системы.

**Slots:** ограничены числом pedestal rooms. Rebirth → больше slots / floors.

**Reveal:** короткий VFX (статика → вспышка → объект). Clip moment.

### 6.4 Roll

- Центральная машина  
- Spend cash (or free tickets from events)  
- Rates в таблице `SignalDatabase`  
- Soft pity optional (v0.3): после N fails — Rare+ guaranteed  

### 6.5 Defend

| Tool | Поведение |
|---|---|
| **Lock** | База закрыта на T сек (друзья могут входить — optional) |
| **Traps** | Замедление / knockback / alarm |
| **Bat** | Удар вора → signal дропается → возвращается владельцу |

**Default:** Lock off при AFK? → **Lock on by default when leaving plot** (снижает toxicity). Steal только у **открытых** или **expired lock**.

### 6.6 Steal

1. Цель: открытый бункер  
2. Вход в **чужой layout**  
3. Click signal → несёшь на спине (slow)  
4. Добежать до своего pedestal  
5. Owner / others могут bat / trap  

**Steal rules (balance):**
- Нельзя красть trophy  
- Carry slow + no tools while carrying  
- Owner notification on pickup  
- Max 1 signal carry  

### 6.7 Offline / Idle (E1)

- Income начисляется offline с cap (например 4–8 ч)  
- Claim при входе → spend spike → return  
- Event «Deep Pulse» может ×2 offline временно  

### 6.8 Rebirth (RESET — обязателен)

**Проблема без rebirth:** база maxed → creation-high мёртв.  
**Rebirth = новый холст**, не только multiplier.

| From→To | ~Time | Keep | Gain |
|---|---|---|---|
| R0→R1 | 25–35 мин | 1 trophy signal | +1.5× income, +plot, Metal+, new rooms |
| R1→R2 | 45–70 мин | 1 trophy | +floor, Rare+ pool boost, Laser gate |
| R2→R3 | ~2 ч | 1 trophy | Vault, Mythic pool, Anomaly traps |
| R3+ | exponential | trophy | deeper tiers, exclusive blueprints |

**UI (обязательно):**
- Preview: «После rebirth получишь: … / потеряешь: …»  
- Never feel like loss: unlocks > sunk cost  
- Rebirth badge на входе бункера (social flex)

**Anti-patterns:**  
Rebirth 1 через 6ч · только ×2 без parts · скрытый benefit · 10 валют.

### 6.9 Events (RETURN — live ops)

**Один template, data-driven.** Не 12 режимов.

```
EventRow = {
  id, durationSec, rollBoost?, spawnPool?,
  incomeBoost?, raidModifier?, startsAt / cron
}
```

| Event | Dur | Effect |
|---|---|---|
| **Signal Storm** | 15 мин | ×2 roll luck + storm-exclusive signals |
| **Raid Hour** | 10 мин | Steal spike (короткий lock / bonus steal cash) |
| **Deep Pulse** | 30 мин | ×2 $/s + ×2 offline claim |
| **Weekly Exclusive** | 7 дней | 1 новый signal в таблице |

**Cadence:** ≥1 rotating event / day + 1 weekly content row.  
Codes menu — optional, не MVP.

---

## 7. Meta layers (7 слоёв)

| Слой | Реализация | Strength |
|---|---|---|
| HOOK | Roll reveal + steal drama + «мой бункер» | ★★★★ |
| LOOP | Build → place → defend / raid | ★★★★★ |
| GROW | Walls, rooms, traps, slots | ★★★★★ |
| CHASE | Signal rarities / traits / weekly exclusive | ★★★★★ |
| SOCIAL | Steal + layout flex + free PS | ★★★★★ |
| RESET | Rebirth → deeper canvas | ★★★★★ |
| RETURN | Offline + event timers | ★★★★ |

---

## 8. Economy (high-level)

**Soft currency:** Credits (from signals /sec + steals)  
**Sinks:** roll · walls · rooms · traps · lock refresh  

**F2P path:** до R2–R3 без paywall на systems.  
**Robux:** convenience / cosmetics / time-skip — **не** exclusive power that deletes steal fairness.

| Product type | OK? | Example |
|---|---|---|
| Cosmetic bunker skins | ✅ | Neon walls skin |
| Roll luck temporary | ⚠️ mild | Storm ticket |
| Instant rebirth skip | ⚠️ | 1-use |
| Unstealable mythic | ❌ | P2W toxic |
| Permanent ×10 income | ❌ | Breaks balance |

---

## 9. Monetization principles

1. Cosmetic / convenience first  
2. Steal must remain skill+layout, not wallet  
3. Event FOMO ≠ paywall FOMO  
4. Free private servers — acquisition, not monetization  

---

## 10. UX / mobile

- Prefab place: tap slot → choose part  
- Large hit targets for bat / steal  
- HUD: $/s · lock timer · event banner · rebirth progress  
- No dense text tutorial — arrows + 3 beats  
- Perf: 6 players, simple bunker meshes, no heavy particles spam  

---

## 11. Discover / RFY

| Asset | Promise |
|---|---|
| Title | Deep Bunker (или «Deep Bunker: Build & Raid») |
| Thumbnail | Свой бункер в разрезе + glowing Mythic + вор на лестнице |
| Clip 1 | Mythic reveal |
| Clip 2 | Steal through trap maze |
| Clip 3 | Before/after rebirth depth |

**Honest hook:** thumbnail = реальный геймплей.  
**Bounce risk:** если thumb = meme pets / brainrot — mismatch.

---

## 12. MVP scope (≤8 systems · ~35 дней)

| # | System | Phase |
|---|---|---|
| 1 | Plot + place walls/rooms | PROVE |
| 2 | Signals + $/sec | PROVE |
| 3 | Roll machine + reveal | v0.2 |
| 4 | Lock + steal + bat | v0.2 |
| 5 | Traps (1 type) | v0.2 |
| 6 | Rebirth + preview + trophy | v0.3 |
| 7 | Offline income | v0.3 |
| 8 | Event template (3 rows) | v0.3 |

### Cut list (НЕ в MVP)

- Trading  
- Freebuild voxels  
- Co-op roles / jobs  
- Full combat / guns  
- Lore campaign  
- 50+ signals  
- Codes  
- Multiple maps  
- Friends-only economy  

---

## 13. Graybox / PROVE (3–5 дней)

**Только:** build + place + income + lock.  
**Без:** steal, rebirth, monetization, events.

| Metric | Kill | Pivot | Go |
|---|---|---|---|
| Session median | <1.5 min | 1.5–3 | >3 min |
| «Хочешь ещё строить?» | <50% | 50–70% | >70% |
| Понял без текста | <60% | — | >80% |

**Kill if:** build не даёт creation-high (люди не хотят «ещё комнату»).

---

## 14. Soft launch gates

| Metric | Kill | Target |
|---|---|---|
| D1 | <15% | >20% |
| Same-day 2nd session | <20% | >25% |
| Median session | <5 min | >8 min |
| Like ratio | <70% | >85% |

Если все kill — чинить **LOOP/HOOK**, не добавлять контент.

---

## 15. Live ops plan (первые 2 недели)

| Week | Content |
|---|---|
| Launch | R0–R1, 12 signals, 3 wall tiers, Signal Storm |
| W1 | Raid Hour + 2 signals + 1 trap skin |
| W2 | R2 unlocks live + Weekly Exclusive #1 |

**Правило:** weekly = data row, не новый код.

---

## 16. Data tables (design)

### SignalDatabase (пример)

| id | rarity | incomePerSec | trait | unlockRebirth |
|---|---|---:|---|---:|
| static_shard | Common | 1 | — | 0 |
| pulse_orb | Uncommon | 3 | — | 0 |
| live_wire | Rare | 10 | — | 0 |
| blackbox | Epic | 40 | night_x2 | 1 |
| void_beacon | Mythic | 200 | anti_steal_10 | 2 |
| storm_echo | Event | 80 | — | event |

### RebirthTier

| tier | costSoft | mult | plotBonus | unlocks |
|---:|---:|---:|---|---|
| 1 | … | 1.5 | +30% | metal+, trap_v1 |
| 2 | … | 2.0 | +floor | laser, rare_pool |
| 3 | … | 3.0 | +vault | mythic_pool |

### EventSchedule

| id | duration | mods |
|---|---|---|
| signal_storm | 900 | rollBoost=2, pool=storm |
| raid_hour | 600 | lockDuration*=0.5 |
| deep_pulse | 1800 | income*=2 |

---

## 17. Risks & mitigations

| Risk | Mitigation |
|---|---|
| 🔴 полка SAB-клонов | Noun + layout steal + depth rebirth |
| Build слишком сложный | Prefabs only |
| Steal toxicity | Lock default, short windows, no P2W unstealable |
| Art cost | 4 wall skins × 3 tiers, 12 signals start |
| Creation dies at ceiling | Rebirth canvas day-1 design |
| Mid-tier ceiling ~50k | Accept P1 stretch; E7 later if viral |

---

## 18. Success tiers

| Tier | CCU 30d | Условие |
|---|---:|---|
| P2 target | 5–15k | Honest hook + rebirth + weekly ops |
| P1 stretch | 50k+ | Clip viral (reveal/steal) + polish |
| Top-10 | 1M+ | Нужен cultural spike — не план MVP |

---

## 19. MECE desk (strict)

| Block | /max | Score | Note |
|---|---:|---:|---|
| A Hook | 10 | 7 | Clear verb; clipable; not meme IP |
| B Loop | 10 | 9 | Build+steal+roll; creation reward |
| C Engines | 20 | 14 | E1+E2+E3+E4; shelf yellow/red — twist required |
| D Layers | 14 | 13 | All 7 designed |
| E Market | 16 | 8 | Saturated shelf; timing still eats mid-tier |
| F RFY | 14 | 9 | Co-play, events, honest thumb |
| G Ship | 16 | 10 | Prefab MVP, cut list, kill gates |
| **Total** | **100** | **~70** | Priority graybox if PROVE passes |

**Gate:** ≥55 desk → PROVE. Kill if build loop fails PROVE.

---

## 20. Open questions

1. Финальное название (Deep Bunker vs alternatives)?  
2. Signals: сколько на launch (12 vs 20)?  
3. Lock: friends-pass или hard lock?  
4. Steal: можно ли красть у AFK с expired lock?  
5. Visual direction: more horror vs more tech?

---

## 21. Next actions

1. Freeze name + thumbnail concept (3 hooks)  
2. PROVE graybox 3–5 дней (build+income only)  
3. Если Go → v0.2 steal → v0.3 rebirth+events  
4. Soft launch → weekly data rows  

---

## Appendix A — Reference comps

| Game | Take |
|---|---|
| Steal a Brainrot | Stack proof: idle + collect + steal + rebirth + events |
| Build a Base and Steal | Build + roll + steal + events; **add rebirth as canvas** |
| Build to Defend Loot | Mid-tier ceiling of shelf |

## Appendix B — One-page pitch

> **Deep Bunker** — строй подземный бункер, ролль аномальные сигналы, защищай layout ловушками, кради у соседей, rebirth глубже за новым холстом. Free private servers. Weekly Signal Storms.

#gamedesign #roblox #concept #deep-bunker #build-steal
