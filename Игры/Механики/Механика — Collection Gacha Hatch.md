# Механика — Collection / Gacha / Hatch

> Часть: [[00 — оглавление]] · Meta: M1/M2 · Engine: **E3**

## Суть
Игрок **охотится за редкими объектами** из пула с tier-распределением. Ключевой момент — **reveal** (яйцо, roll, catch).

## Core verb
`hatch` · `catch` · `roll` · `pull` · `open`

## Что цепляет (hook)
- **Variable ratio** — не знаешь что выпадет
- **Reveal VFX** — screen flash + sound sting + rarity banner
- **Near-miss** — «almost legendary» анимация
- **Flex moment** — показать друзьям

| Hook сила | ★★★★★ |

## Что удерживает
- **Chase ∞** — mythic 0.01%, album 847/∞
- **Completionism** — codex %, set bonuses
- **Trade value** — rare = social currency

| D1 | D7 | D28 |
|---|---|---|
| ★★★★ | ★★★★★ | ★★★★★ |

## Feedback requirements

| Element | Must have |
|---|---|
| Pre-reveal | Shake, glow, anticipation 1–3 sec |
| Reveal | Flash + sting SFX + rarity color |
| Banner | COMMON → MYTHIC text |
| Post | Add to inventory + stat bump visible |

**Без reveal moment** — слабый TikTok, слабый hook.

## Roblox примеры

| Игра | Collection type | CCU |
|---|---|---|
| Pet Simulator 99 | Pets hatch | A |
| Adopt Me | Pets trade | A |
| GaG | Mutations (variant) | S |
| Fisch / Fish It | Fish biomes | A–B |
| Sol's RNG | Aura roll | B |

## Solo dev

| | |
|---|---|
| **Code complexity** | Medium (data tables) |
| **Art cost** | Medium–High (models per tier) |
| **MVP minimum** | 3 tiers, 1 hatch type, 1 reveal VFX polished |
| **Scale trick** | New pet = **1 row** in database |

## Лучшие комбо

| Partner | Why |
|---|---|
| Prestige / Rebirth | New pool access per rebirth |
| Idle / Passive | Hatch while away (optional) |
| Events / FOMO | Limited egg 24h |
| Trade | Social economy |
| Co-op | «Watch me hatch live» |

## Anti-patterns

- Legendary every 5 min → no chase
- 20 currencies → confusion
- Reveal without SFX → flat
- Same model recolored only → weak flex

## MVP spec

```
- 1 hatch point (egg machine / fishing spot)
- 5 items: 60% common, 25% rare, 10% epic, 4% legendary, 1% mythic
- 1 polished reveal sequence (only this needs AAA polish)
- Simple inventory + equip
- Tease: «X mythic exists» in loading tips
```

#gamedesign #roblox #mechanics #collection #gacha
