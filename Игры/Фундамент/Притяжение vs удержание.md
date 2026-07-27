# Притяжение vs удержание

> Часть: [[00 — фундамент]]  
> Ключевая ошибка solo dev: путать «интересно зайти» и «интересно вернуться».

---

## Определения

| | Притяжение (Acquisition) | Удержание (Retention) |
|---|---|---|
| **Вопрос** | Почему кликнули? | Почему вернулись? |
| **Горизонт** | 0–60 сек, D1 | D2–D28, D30+ |
| **Драйверы** | Thumbnail, title, hook, trend | Engines, return timer, chase, social |
| **Метрики RFY** | PTR, first play bounce | D1, D2–7, **D8–28**, play days |
| **Можно купить?** | Ads, TikTok (seed) | **Нет** — только дизайн |

---

## Что притягивает (подтверждено)

### 1. Verb clarity за 3–10 секунд
Игрок понимает **одно действие** из названия + thumbnail без текста.

| Сильно | Слабо |
|---|---|
| Steal a Brainrot | Epic Mining Adventure RPG |
| Animal Hospital (Anomaly) | Deep Underground Simulator |
| +1 Speed Keyboard Escape | The Last Expedition |

**Вердикт:** ✅ подтверждено (top-50 naming patterns + RFY bounce rate 2026).

### 2. Число / редкость / опасность на thumbnail
- `+1 SPEED EVERY STEP`
- `MYTHIC PULL`
- `SHIFT 47 — CAN YOU BEAT IT?`
- `SOMETHING IS WRONG HERE`

**Вердикт:** ✅ подтверждено — но thumbnail **должен = правда** (иначе bounce → RFY штраф).

### 3. Cultural moment (E7)
Italian brainrot peak → Steal a Brainrot. Cozy TikTok → GaG peak.

**Вердикт:** ✅ подтверждено как **ускоритель**, не как standalone engine.

### 4. Trend adjacency
Не клон лидера, а **соседняя полка** с twist (Animal Hospital ≠ brainrot, но breakout 2026).

**Вердикт:** ✅ подтверждено.

### 5. Reveal moment (первые 60 сек)
Hatch pop, rarity banner, first scare, first +1000 coins.

**Вердикт:** ✅ подтверждено — снижает bounce, даёт clip.

---

## Что удерживает (подтверждено)

### D1 — hook + loop pleasure
| Механика | Сила D1 |
|---|---|
| Session stakes (E5) | ✅✅✅ |
| Offline/passive (E1) | ✅✅✅ |
| Collection chase (E3) | ✅✅ |
| Skill mastery (E6) | ✅✅ |
| Story / lore | ✅ (только D1!) |

### D7 — return timer + first milestone
| Механика | Сила D7 |
|---|---|
| Scheduled events | ✅✅✅ |
| Collection chase | ✅✅✅ |
| Social / trade | ✅✅ |
| Prestige stack | ✅✅ |
| Story / lore | ❌ |

### D30 — бесконечный chase + social + live ops
| Механика | Сила D30 |
|---|---|
| Collection chase | ✅✅✅ |
| Social economy | ✅✅✅ |
| Prestige stack | ✅✅✅ |
| Scheduled events | ✅✅ |
| Story / lore | ❌ |

**Вердикт:** ✅ подтверждено — D30 покупается **только** collection + social + prestige + events.

→ [[Starter Pack/04 — retention playbook]]

---

## Что НЕ удерживает (осporено / переоценено в GDD)

| Миф | Реальность | Вердикт |
|---|---|---|
| «Глубокий сюжет = retention» | One-and-done, D7 пусто | ❌ **Оспорено** как core engine |
| «Красивый арт = хит» | Retention + distribution важнее | ⚠️ Частично — арт = hook, не retention |
| «50 journal entries = depth» | Lore = D1 масло, не D30 двигатель | ❌ **Оспорено** solo |
| «Уникальная механика = hit» | Twist на работающем жанре | ✅ Подтверждено (differentiation, not invention) |
| «Paid YouTuber = hit» | Seed only, не fix retention | ⚠️ **Переоценено** «бесполезна» → **полезна как seed**, бесполезна без organic |

---

## Два типа «ownership» — притяжение vs удержание

| Тип ownership | Притягивает? | Удерживает D30? |
|---|---|---|
| «Мой mythic pet» (E3) | ✅ clip | ✅✅✅ chase |
| «Мой rebirth count» (E2) | ⚠️ flex | ✅✅✅ prestige |
| «Моя база / сад» (creation) | ✅ visual | ✅✅ если + events |
| «Моя история / Echo» (narrative) | ✅ curiosity D1 | ❌ solo без parallel goals |
| «Мой shift #47» (E5) | ✅ number hook | ✅✅ session stakes |
| «Мой depth record» (exploration) | ✅ flex | ⚠️ без social/events слабо |

**Правило:** narrative ownership **должен быть обёрнут** в collection (Echo codex) + events (Signal Storm) + social (flex depth).

→ [[Mining и Deep Signal — применение]]

---

## Минимальный набор для удержания (MVP)

Solo dev: **минимум 3 из 5** для выживания D3–D7:

- [ ] **Visible progress** каждые 30–120 сек (LOOP + GROW)
- [ ] **Chase target** с % или числом (CHASE / E3)
- [ ] **Return timer** ≤ 24ч (offline ИЛИ event ИЛИ fear)
- [ ] **Prestige или collection tier** (RESET / E3)
- [ ] **Social hook** (trade, co-op, leaderboard, steal-lite)

Без 3+ — игра умрёт на D3–D7, даже с отличным hook.

---

## Формула (кратко)

```
ПРИТЯЖЕНИЕ = HOOK × Thumbnail truth × Trend window × Clip potential
УДЕРЖАНИЕ  = LOOP × STACK(2–4 engines) × RETURN timer × Live ops cadence

D1  = притяжение + loop pleasure
D7  = return timer + first prestige/collection milestone  
D28 = chase (∞) + social + weekly updates (RFY 2026 priority)
D30 = chase + social economy + events (не сюжет)
```

---

## Практический тест

Заполни для своей идеи:

```markdown
### Притяжение (0–60 сек)
- Verb одним словом: 
- Thumbnail обещание: 
- Первый wow/tension: 
- Clip moment (3 сек TikTok): 

### Удержание (D1–D28)
- RETURN (зачем завтра): 
- CHASE (что ∞): 
- Engines (2–4): 
- Live ops (weekly plan): 
- Social hook: 

### Red flag?
- [ ] Retention завязан на story?
- [ ] Нет return timer?
- [ ] Только 1 engine?
```

#gamedesign #retention #acquisition #hook
