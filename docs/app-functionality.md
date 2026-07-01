# Функціональність модуля `money.ts`

## Overview

Модуль `app/src/money.ts` — набір утиліт для роботи з грошовими сумами. Усі суми в API представлені як **цілі центи** (`number`), щоб уникнути похибок чисел з плаваючою комою.

**Експортовані функції:** `formatCents`, `parseAmount`, `splitEvenly`, `applyDiscount`.

**Імпорт (ESM):**

```typescript
import { formatCents, parseAmount, splitEvenly, applyDiscount } from "./money.js";
```

## Module API summary

| Функція | Параметри | Повертає | Кидає помилку |
|---------|-----------|----------|---------------|
| `formatCents` | `cents: number` | `string` | Ні |
| `parseAmount` | `input: string` | `number` | Так — невалідний рядок |
| `splitEvenly` | `totalCents: number`, `n: number` | `number[]` | Ні |
| `applyDiscount` | `cents: number`, `percent: number` | `number` | Так — `percent` поза 0–100 |

---

### formatCents

**Signature**

```typescript
export function formatCents(cents: number): string
```

**Parameters**

| Параметр | Тип | Опис |
|----------|-----|------|
| `cents` | `number` | Сума в цілих центах (може бути від'ємною). |

**Returns**

| Тип | Опис |
|-----|------|
| `string` | Людиночитний рядок виду `"428.00"` (ціла частина, крапка, дві цифри дробової частини). |

**Throws**

Не кидає помилок.

**Behavior**

- Для від'ємних значень на початок рядка додається `"-"`.
- Береться модуль `Math.abs(cents)`.
- Ціла частина: `Math.floor(abs / 100)`.
- Дробова частина (у центах): `abs % 100`, форматується двома цифрами (`padStart(2, "0")`).
- Результат: `` `${sign}${whole}.${frac}` ``.

**Examples**

- `42800` → `"428.00"` (з JSDoc)
- `5` → `"0.05"` (implementation detail: 5 центів)
- `-1250` → `"-12.50"` (implementation detail: від'ємний знак)

---

### parseAmount

**Signature**

```typescript
export function parseAmount(input: string): number
```

**Parameters**

| Параметр | Тип | Опис |
|----------|-----|------|
| `input` | `string` | Рядок суми, напр. `"428.00"` або `"428"`. Пробіли на початку/в кінці обрізаються (`trim`). |

**Returns**

| Тип | Опис |
|-----|------|
| `number` | Сума в цілих центах; від'ємна, якщо в рядку був знак `-`. |

**Throws**

- Якщо рядок не відповідає очікуваному формату: `Error` з повідомленням `` `Not a valid amount: ${input}` `` (оригінальний `input`, не `trimmed`).

**Behavior**

- Після `trim` застосовується regex: `/^(-?)(\d+)(?:\.(\d{1,2}))?$/`.
- Групи: опційний знак `-`, ціла частина (цифри), опційна дробова частина (1–2 цифри після крапки).
- Дробова частина доповнюється нулем справа до двох символів (`padEnd(2, "0")`) перед обчисленням.
- Центи: `Number(whole) * 100 + Number(frac)`; якщо був знак `-`, результат множиться на -1 (через умову `sign === "-" ? -cents : cents`).

**Examples**

- `"428.00"` → `42800` (з JSDoc)
- `"428"` → `42800` (implementation detail: без дробової частини, `frac = "0"`)
- `"12.5"` → `1250` (implementation detail: одна цифра після крапки → `"50"` центів)

---

### splitEvenly

**Signature**

```typescript
export function splitEvenly(totalCents: number, n: number): number[]
```

**Parameters**

| Параметр | Тип | Опис |
|----------|-----|------|
| `totalCents` | `number` | Загальна сума в центах для розподілу. |
| `n` | `number` | Кількість часток (людей); довжина результуючого масиву. |

**Returns**

| Тип | Опис |
|-----|------|
| `number[]` | Масив із `n` елементів — цілі центи на кожну частку. |

**Throws**

Не кидає помилок (валідація `n` у коді not specified).

**Behavior**

- `base = Math.floor(totalCents / n)`.
- `remainder = totalCents % n`.
- Повертається масив довжини `n`: для індексів `i < remainder` значення `base + 1`, інакше `base`.
- У JSDoc зазначено відому прогалину щодо remainder cents; реалізація розподіляє залишок на перші `remainder` часток (implementation detail).

**Examples**

- `9000`, `3` → `[3000, 3000, 3000]` (implementation detail: ділиться без остачі)
- `100`, `3` → `[34, 33, 33]` (implementation detail: `remainder = 1`)
- `500`, `1` → `[500]` (implementation detail: одна частка)

---

### applyDiscount

**Signature**

```typescript
export function applyDiscount(cents: number, percent: number): number
```

**Parameters**

| Параметр | Тип | Опис |
|----------|-----|------|
| `cents` | `number` | Початкова сума в центах. |
| `percent` | `number` | Відсоток знижки; за JSDoc — діапазон **0–100** включно. |

**Returns**

| Тип | Опис |
|-----|------|
| `number` | Сума в центах після знижки, округлена до найближчого цента (`Math.round`). |

**Throws**

- Якщо `percent < 0` або `percent > 100`: `Error` з повідомленням `` `percent must be between 0 and 100, got ${percent}` ``.

**Behavior**

- Спочатку перевірка діапазону `percent`.
- Обчислення: `Math.round(cents * (1 - percent / 100))`.
- `0%` повертає початкову суму (округлену); `100%` дає `0` для додатних `cents` (implementation detail).

**Examples**

- `10000`, `10` → `9000` (implementation detail: 10% знижка)
- `10000`, `0` → `10000` (implementation detail)
- `10000`, `100` → `0` (implementation detail)

---

## Error messages

| Повідомлення | Функція | Умова |
|--------------|---------|--------|
| `` Not a valid amount: ${input} `` | `parseAmount` | Рядок не збігається з `/^(-?)(\d+)(?:\.(\d{1,2}))?$/` після `trim` |
| `` percent must be between 0 and 100, got ${percent} `` | `applyDiscount` | `percent < 0` або `percent > 100` |

## Types & conventions

- **`number` як центи** — усі грошові величини в API (`formatCents`, `splitEvenly`, `applyDiscount`) та результат `parseAmount` — цілі центи.
- **Рядок суми** (`parseAmount`) — опційний `-`, цілі одиниці, опційна крапка і 1–2 десяткові цифри (долари/гривні в «людському» вигляді, не центи).
- **Рядок відображення** (`formatCents`) — `"<ціла>.<дві цифри>"`, для від'ємних — префікс `-`.
- **`splitEvenly`** — масив завжди має довжину `n`; сума елементів дорівнює `totalCents` при `n > 0` (implementation detail з коду розподілу остачі).
- **Знижка** — `percent` у відсотках 0–100; результат округлюється до цілого цента.

## Generated

Документ згенеровано промптом `prompts/app-functionality-description.md`.

**Єдине джерело:** `app/src/money.ts` (сигнатури, JSDoc, реалізація).
