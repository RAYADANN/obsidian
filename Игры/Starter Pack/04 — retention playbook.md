# 04 — retention playbook

> Часть: [[00 — оглавление]] · Канон: `docs/starter-pack/04-retention-playbook.md`

---

## Матрица: механика × день

| Механика | D1 | D3 | D7 | D30 |
|---|---|---|---|---|
| Offline / passive (E1) | ✅✅✅ | ✅✅ | ✅✅ | ✅ |
| Scheduled event | ✅✅ | ✅✅✅ | ✅✅✅ | ✅✅ |
| Collection (E3) | ✅✅ | ✅✅✅ | ✅✅✅ | ✅✅✅ |
| Social / trade (E4) | ✅ | ✅✅ | ✅✅✅ | ✅✅✅ |
| Session stakes (E5) | ✅✅✅ | ✅✅ | ✅✅ | ✅✅ |
| Prestige (E2) | ✅ | ✅✅ | ✅✅✅ | ✅✅✅ |
| Skill (E6) | ✅✅ | ✅✅ | ✅✅ | ✅✅ |
| **Story / lore** | ✅ | ❌ | ❌ | ❌ |

**D1** можно купить hook. **D30** — только collection + social + prestige + events.

---

## RETURN-таймеры

| Тип | Пример | Сила |
|---|---|---|
| Предсказуемый | GaG restock 5 мин | Привычка |
| Случайный FOMO | Weather mutation | Дополняет |
| Страх потери | Brainrot steal | D1 strong, churn risk |
| Социальный | «Друг в игре» | Лучший D30 |

---

## Минимальный retention-набор MVP

**3 из 5** для выживания D3–D7:

- [ ] Visible progress каждые 30–120 сек
- [ ] Chase target (% / число)
- [ ] Return timer ≤ 24ч
- [ ] Prestige или collection tier
- [ ] Social hook

---

## Метрики soft launch

| Метрика | Sim/tycoon orientir |
|---|---|
| D1 | >25% |
| D7 | >8–12% |
| Median session D1 | 12–20 min |
| 2nd session same day | >35% |
| First play bounce | <40% |

---

## Формула

```
D1  = HOOK + LOOP pleasure
D7  = RETURN timer + first prestige/collection milestone
D30 = CHASE (∞) + SOCIAL + live ops cadence
```

→ [[../Фундамент/Притяжение vs удержание]] · [[05 — алгоритм RFY 2026]]

#gamedesign #roblox #retention
