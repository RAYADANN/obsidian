# Dual Analysis — MECE × Tizzy

> Часть: [[00 — оглавление]]  
> **Канон процесса:** каждую идею оцениваем **обоими** методами до кода.  
> Шаблон заполнения: [[Шаблон — анализ идеи]]

---

## Зачем два метода

| | **Tizzy /40** | **MECE /100** |
|---|---|---|
| Фокус | Рынок, спрос, дыра, читаемость, UX | Engines, слои, loop quality, retention stack |
| Вопрос | *Будут ли игроки вообще кликать и понимать?* | *Удержит ли стек D1→D28?* |
| Слепая зона | Может пропустить слабый retention stack | Может простить red ocean при «красивых» engines |
| Сильнее на | Desk market research | Desk design architecture |

```
Tizzy без MECE  → «окно рынка», но пустой loop / нет return
MECE без Tizzy  → «сильный стек» в мёртвой / 🔴 полке
Dual pass       → идея достойна PROVE
```

---

## Протокол (45–60 мин)

```
0. One-liner + Verb + Fantasy          (5 мин)
1. Tizzy T1–T2: demand + gap           (15 мин)
2. MECE блоки A–E (desk)               (20 мин)
3. Tizzy T3–T4: readable + UX          (10 мин)
4. Dual verdict matrix                 (5 мин)
5. Kill / Pivot / PROVE                (решение)
```

Порядок важен: **сначала рынок (Tizzy)**, потом глубина стека (MECE), потом UX/readable — иначе тратишь час на engines мёртвой фантазии.

Альтернатива, если идея уже из research-папки: можно начать с MECE, но **T1–T2 обязательны до PROVE**.

---

## Dual verdict matrix

| | Tizzy ≤15 | Tizzy 16–23 | Tizzy 24–29 | Tizzy ≥30 |
|---|---|---|---|---|
| **MECE ≤39** | ❌ Kill | ❌ Kill | ❌ Kill | ⚠️ Rebuild stack |
| **MECE 40–54** | ❌ Kill | ⚠️ Pivot | ⚠️ Pivot + graybox? | ⚠️ Strengthen engines |
| **MECE 55–69** | ⚠️ New market | ⚠️ Mutate | ✅ PROVE candidate | ✅ PROVE |
| **MECE 70–84** | ⚠️ Wrong shelf? | ⚠️ Mutate hard | 🔥 Prioritize | 🔥 Prioritize |
| **MECE ≥85** | ⚠️ Market check again | ✅ After mutate | ⭐ Ship fast | ⭐ Ship fast |

### Текстовые правила

1. **Kill**, если любой score в ❌ зоне матрицы  
2. **PROVE** только если оба ≥ порога прототипа (MECE ≥55 **и** Tizzy ≥24)  
3. **Prioritize**, если MECE ≥70 **и** Tizzy ≥30  
4. Если MECE высокий, а Tizzy низкий → проблема **рынка/дыры**, не «добавь rebirth»  
5. Если Tizzy высокий, а MECE низкий → проблема **стека**, не «новый thumb»  
6. C9 🔴 полка (MECE) + T2 red ocean → нужен **mutate**, иначе kill  

---

## Что писать в карточке идеи

Минимум в конце dual:

```markdown
## Dual verdict
- MECE: __/100 — [вердикт]
- Tizzy: __/40 — [вердикт]
- Matrix: [Kill / Pivot / PROVE / Prioritize]
- Главный риск: [market | stack | UX | scope]
- Next gate: [mutate plan | graybox | park]
```

Полный blank → [[Шаблон — анализ идеи]]

---

## Куда класть заполненные анализы

Рекомендация:
- Концепты с GDD → `Игры/Концепты/<Имя> — Dual.md` (рядом с MECE/GDD)
- Сырые скрининги → `Игры/Метод Tizzy/Анализы/<Имя>.md` (папка по мере роста)

---

## Связь с Idea Machine

| Idea Machine phase | Dual |
|---|---|
| DIVERGE | Быстрый Tizzy T1–T2 на 3 one-pagers |
| CONVERGE | Полный Dual на финалиста |
| PROVE | Пересчёт T4 + MECE B после graybox |
| LEARN | Calibration обоих scorecards |

→ [[../Starter Pack/Idea Machine — pipeline]]

#gamedesign #roblox #tizzy #mece
