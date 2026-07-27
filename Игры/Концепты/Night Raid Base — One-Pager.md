# Night Raid Base — One-Pager

> **Статус:** Phase 3 PROVE · superseded by [[Night Raid Base — GDD]]  
> **MECE:** [[Night Raid Base — MECE|78/100 strict]]

**Рабочее имя:** Night Raid Base *(placeholder — название позже)*  
**One-liner:** Днём строишь базу и крадёшь pets у игроков. Ночью чудище лезет за твоими — отпугивай фонариком и светом на базе.

---

## 1. Pitch (15 сек)

Ты роллишь pets, строишь базу, днём можешь грабить соседей. Когда темнеет — на **каждого** игрока вылезает чудище и пытается утащить pets. Свети фонариком, ставь лампы, защищай layout. Утро = награда или потери → снова build.

**Core verbs:** `build` · `roll` · `steal` · `shine` · `defend`

---

## 2. Почему не клон BaBaS

| BaBaS | Этот концепт |
|---|---|
| Defend только от игроков | + **PvE night raid** |
| Build = стены от людей | Build = стены **и свет** |
| Events ad-hoc | **Ночь = встроенный event** каждый цикл |
| Clip: steal cry | Clip: фонарик + монстр у базы |

Stack тот же (E1+E3+E4+build). Diff = **ритм + light combat**.

---

## 3. Session rhythm (критично)

**Не real-time сутки.** Короткий цикл:

| Фаза | Длительность | Что можно |
|---|---|---|
| **DAY** | 3–4 мин | Roll, build, steal у игроков, ставить свет |
| **DUSK** | 15 сек | Warning UI + звук · «готовь фонарик» |
| **NIGHT** | 1.5–2 мин | Монстр на базу · shine / traps · pets at risk |
| **DAWN** | 10 сек | Итог ночи · payout / losses |

**Полный цикл ≈ 5–6 мин** → несколько день/ночь за сессию.

**Kill if:** ночь >3 мин или день >6 мин без действия → AFK / скука.

---

## 4. Day loop (как BaBaS)

```
ROLL pets → BUILD base → PLACE pets → STEAL / DEFEND vs players → SPEND
```

Без изменений жанра. Ночь **не отменяет** PvP steal — только добавляет второй режим.

---

## 5. Night loop (killer)

```
DUSK warning
    ↓
На КАЖДОГО игрока спавнится 1 Monster (у входа / края базы)
    ↓
Monster pathfind → ближайший / самый дорогой pet
    ↓
Игрок: SHINE flashlight (основной verb)
       + свет базы замедляет / отталкивает
       + bat / trap (secondary)
    ↓
DAWN: saved pets → bonus cash
      stolen pets → в «туман» / потеря до reclaim (см. §7)
```

### Правила монстра (MVP = 1 тип)

| Параметр | MVP |
|---|---|
| Кол-во | **1 на игрока** за ночь |
| Цель | Pet с max $/s среди unlocked |
| Скорость | Средняя; в свете — slow 50% |
| HP / «страх» | Нет HP-бара → **light meter**: 3–5 сек непрерывного света = отступает |
| Если дошёл | Хватает pet, уносит к краю карты (таймер 8–10 сек на спасение bat/shine) |

**Не делать в MVP:** боссы, волны, 5 видов, инвентарь оружия.

---

## 6. Light as mechanic (80/20)

Свет — **не VFX**, а геймплей.

### Flashlight (player)

| | |
|---|---|
| Equip | Всегда / hotkey |
| Cone | Короткий конус вперёд |
| Battery | Разряд за ночь; DAY = recharge (или buy cells) |
| Effect | На монстре в конусе → fill Fear meter → retreat |

### Base lights (build sink)

| Part | Effect |
|---|---|
| Lamp / torch | Зона slow + soft Fear tick |
| Floodlight (tier 2) | Шире зона, дороже |
| Dark corner | Монстр ускоряется / игнор soft light |

**Build снова наркотик:** не только стены, а **где стоит свет** — layout vs night path.

---

## 7. Stakes & fairness

| Ситуация | Правило |
|---|---|
| Online, проиграл ночь | Pet унесён → можно **reclaim** днём за cash / mini-chase OR lost until next dawn payout skip |
| Offline | Ночь **не** вайпает базу; soft sim: −N% overnight income max, pets safe |
| Lock vs monster | Lock **не** блокирует PvE (иначе ночь бессмысленна) · Lock только vs players |
| Newbie night 1 | Tutorial: монстр слабее / 1 free battery |

**Рекомендация MVP stakes:** унесённый pet = **hold в «Fog Cage»** у края → выкупи / отбей на рассвете. Полная потеря — слишком toxic для D1.

---

## 8. Layers (кратко)

| Слой | Как |
|---|---|
| HOOK | Dusk warning + flashlight cone |
| LOOP | Day build/steal · Night shine |
| GROW | Walls + **lights** + traps |
| CHASE | Pet rarities |
| SOCIAL | Day PvP steal |
| RETURN | Night каждые ~5 мин + offline soft |
| RESET | Rebirth (v0.3) — новый plot + light tiers |

---

## 9. MVP scope (≤7 systems)

| # | System | Phase |
|---|---|---|
| 1 | Base plot + walls + pet slots | PROVE |
| 2 | Pets + $/s + roll | PROVE |
| 3 | Day/night cycle timer | PROVE |
| 4 | 1 monster + flashlight Fear | PROVE |
| 5 | Base lamps (1–2 tiers) | v0.2 |
| 6 | Player steal + bat (BaBaS) | v0.2 |
| 7 | Rebirth + light unlocks | v0.3 |

### Cut list

- 5+ monster types · guns · co-op roles · trading · real 24h clock · story · Deep Bunker lore  

---

## 10. PROVE (3–5 дней)

**Только:** build + pets income + day/night + 1 monster + flashlight.  
**Без:** PvP steal, rebirth, monetization.

| Metric | Go |
|---|---|
| Session > 3 min | ✅ |
| «Хочешь ещё одну ночь?» > 70% | ✅ |
| Понял свет без текста > 80% | ✅ |

**Kill if:** фонарик ощущается как chore ИЛИ ночь = бессмысленный wait.

---

## 11. Monetization (позже, принципы)

| OK | Нет |
|---|---|
| Cosmetic flashlight skins | Unkillable night / pay-to-skip all nights |
| Battery packs (convenience) | Pay for unstealable pets vs PvE |
| Lamp skins | Permanent ×10 income |

---

## 12. Discover hooks

1. Thumbnail: база ночью + луч фонарика на чудище + pet в лапах  
2. Clip: Fear meter почти полный → монстр орёт и бежит  
3. Clip: забыл лампу в углу → Mythic утащили  

---

## 13. Risks

| Risk | Mitigation |
|---|---|
| Cycle слишком длинный | Жёсткий тайминг §3 |
| Два режима = двойной scope | 1 monster, 1 light verb |
| Похоже на horror survival | Держать tycoon HUD + day steal |
| Mobile flashlight awkward | Auto-aim soft assist optional |
| Offline unfair | Pets safe offline (§7) |

---

## 14. Open questions

1. Название?  
2. Pet lost = Fog Cage redeem или hard lose?  
3. Можно ли днём **красть** pets, которых сосед спас прошлой ночью (immunity timer)?  
4. Монстр один скин навсегда или weekly skin-row?  
5. Rebirth: что сбрасывать — базу+pets или только base, pets stay?

---

## 15. Next

1. Зафиксировать имя  
2. Graybox: цикл + фонарик + 1 монстр  
3. Если Go → дописать полный GDD (systems + economy tables)  
4. PvP steal и rebirth — после proof night loop  

---

## One-line summary

> **BaBaS + короткий день/ночь + один монстр на игрока + свет как защита базы.**  
> 80% ценности — ритм и flashlight; всё остальное — polish.

#gamedesign #roblox #concept #night-raid #build-steal #light
