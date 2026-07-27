# Shelf Shift — supermarket anomaly job

> Часть: [[00 — оглавление]] · Статус: **concept** · Конкурент: [[Конкуренты — Animal Hospital и Scary Grocery#Scary Grocery The Night Shift HORROR|Scary Grocery]]

---

## One-liner

Работаешь кассиром/кладовщиком в супермаркете — **днём** обслуживаешь NPC, **ночью** в магазине происходят anomalies.

---

## Почему рынок (signal)

| Signal | Данные |
|---|---|
| Animal Hospital | ~1.27M CCU — job + anomaly + co-op **работает** |
| Scary Grocery | ~13k CCU — **прямой конкурент** в grocery |
| Supermarket Simulator | ~23k peak — store fantasy есть |
| Off-platform | Night of the Consumers ~38M views; HELLMART (Steam) |

**Gap score: 5/10** — demand высокий, но niche занята Scary Grocery.

---

## Дифференциатор (+20%)

Не клон Scary Grocery:

1. **Environmental anomalies** — полки, камеры, товар (не shooter vs doppelganger)
2. **Co-op roles = core** — кассир vs подсобка, один не выигрывает solo
3. **Day = tutorial + earn**, Night = exam + horror

**Clip hooks:**
- «Ночь 5, полка смотрит на тебя»
- Co-op panic: «я на камере вижу, у тебя за спиной»

---

## Core loop

```
DAY:  scan → stock shelves → serve NPC → earn $
NIGHT: anomalies spawn → detect/contain → survive shift → bonus + catalog entry
META: 7/14/30 nights prestige · weekly new anomaly · cosmetic flex
```

---

## Co-op roles (2–4)

| Роль | Зона | Информация |
|---|---|---|
| Cashier | Front | Клиенты, сканер, «не те» покупатели |
| Stocker | Back / floor | Полки, камеры, prop anomalies |
| (Later) Security | CCTV desk | Mark suspicious zones |

---

## Replay drivers

- Random anomalies per shift
- Prestige nights 7/14/30 (not story)
- Anomaly catalog (collector meta)
- Weekly new anomaly type (live ops)

---

## MVP 2 недели (scope)

**In:**
- 1 store layout (register + backroom + 3–4 cameras)
- Day/night on same map
- 3 anomaly types
- Co-op 2–4, 2 roles
- Simple NPC queue

**Out:**
- Tycoon economy
- Multiple stores
- 10+ anomalies
- Complex fire/spread sim

### 3 anomaly types (MVP)

1. **Moving shelf** — layout shifts, block path
2. **Wrong product** — SKU mismatch on scan
3. **Lying camera** — backroom looks empty on CCTV but isn't

---

## Оценка

| Критерий | /10 |
|---|---:|
| Demand | 9 |
| Gap | 5 |
| Differentiation | 7 (if environmental + co-op) |
| Viral/clips | 8 |
| Solo MVP | 6.5 |

---

## Вердict

**GO**, но риск клонирования Scary Grocery высокий.  
→ Предпочтительная альтернатива: [[Fire Shift — пожарные + anomaly (альтернатива)]]

Monetization: [[Shelf Shift — монетизация]]
