# Механика — Prestige / Rebirth

> Часть: [[00 — оглавление]] · Meta: **M3** · Engine: **E2**

## Суть
Достиг потолка → **сброс прогресса** → permanent bonus → новый chase с multiplier.

## Core verb
`rebirth` · `prestige` · `ascend` · `reset`

## Что цепляет
- **Power fantasy** — «I'm 10x stronger now»
- **Before/after** — visible multiplier jump
- **Unlock tease** — «Rebirth 5 unlocks Void zone»

| Hook сила | ★★★ (weak alone — needs partner hook) |

## Что удерживает
- **Infinite ceiling** — rebirth 1 → 100 → ∞
- **Stack layers** — Rebirth → Evolution → Ascension
- **D28 engine** — primary long-term for sims

| D1 | D7 | D28 |
|---|---|---|
| ★★★ | ★★★★★ | ★★★★★ |

## Design rules

| Rule | Example |
|---|---|
| **Show benefit BEFORE reset** | «+2x forever, unlock Zone 2» |
| **First rebirth fast** | 15–30 min to rebirth 1 |
| **Later rebirths slower** | Exponential time = D30 |
| **Something new each tier** | Zone, pet pool, mechanic |
| **Never feel like loss** | Bonus > what you lose |

## Roblox примеры

All major simulators: Pet Sim, Brainrot (18 floors), GaG, mining sims.

## Solo dev

| | |
|---|---|
| **Complexity** | **Low** — multiplier + reset function |
| **MVP** | 1 rebirth tier, 2x multiplier, 1 unlock |
| **Data-driven** | `RebirthTier` table |

## Лучшие comбо

| Partner | Why |
|---|---|
| Collection | New pool per rebirth |
| Idle | Faster accumulation post-reset |
| Incremental | Number grows faster |
| Exploration | New zone access |
| Events | Rebirth weekend bonus |

## Anti-patterns

- Rebirth 1 after 6 hours → churn before first reset
- Reset with unclear benefit → feels like punishment
- Only multiplier, no new content → boring cycles
- 10 rebirth currencies → confusion

## MVP spec

```
Rebirth 1: ~20 min, +2x coins, unlock Layer 2
Rebirth 2: ~45 min, +3x total, unlock pet tier
Show preview UI: «After rebirth you get: ...»
```

#gamedesign #roblox #mechanics #prestige #rebirth
