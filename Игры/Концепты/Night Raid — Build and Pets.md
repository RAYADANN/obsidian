# Night Raid — Build & Pets (механика)

> **Канон продукта:** `docs/GDD.md` (§3, §4A–4G).  
> **Сцена BaBaS:** `docs/REFERENCE_BABAS_SCENE.md`.  
> Обновлено: 2026-07-17

---

## Жёсткие правила (не ломать)

1. **Блоки имеют rarity** как pets (`1 in X`, weights, buyCost).  
2. Блоки **роллятся и покупаются** (Roll → Buy | Re-roll).  
3. **Buy блока / trap = всегда +100** в inventory.  
4. Стройка только из inventory (ghost на yard grid), не «слот за $».  
5. Есть **ловушки** (foundation: Spikes); ставятся как блоки.  
6. MPS → **buffer** → **Collect All**.  
7. Plot = **Hub wood + Yard grass**.

---

## Dual gacha

| | Blocks / Traps | Pets |
|---|---|---|
| Station | Block Roll pad | Pet Roll pad |
| Buy | `+= 100` | 1 pet Home |
| Rarity | ✅ | ✅ |

## Build hotbar

1 Build · 2 Take · 3 Edit · 4 Bat · R/T/G

## Placeable kinds

`wall` | `trap` | `lamp` — одна `BlockDatabase`.

PROVE: dirt, wood_planks, stone, **spikes**, lamp_t1.

## Night twist (не BaBaS)

Flashlight Fear · 1 monster · Fog Cage · lamps.

#gamedesign #night-raid #babas
