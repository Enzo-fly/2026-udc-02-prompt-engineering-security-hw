# Тести модуля `money.test.ts`

## Overview

**Файл:** `app/src/money.test.ts`

**Фреймворк:** vitest (`describe`, `it`, `expect`).

**Імпорт тестованих символів:**

```typescript
import { formatCents, parseAmount, splitEvenly, applyDiscount } from "./money.js";
```

**Структура:** 4 блоки `describe`, **18** тест-кейсів `it`.

| Метрика | Значення |
|---------|----------|
| `describe` блоків | 4 |
| `it` кейсів | 18 |
| Імпортованих функцій | 4 |

## Suite summary

| `describe` блок | Функція під тестом | Кількість `it` | Тема |
|-----------------|-------------------|----------------|------|
| `formatCents` | `formatCents` | 3 | Форматування центів у рядок |
| `parseAmount` | `parseAmount` | 5 | Парсинг рядка в центи, помилки |
| `splitEvenly` | `splitEvenly` | 4 | Рівний поділ суми, остача |
| `applyDiscount` | `applyDiscount` | 6 | Знижка, округлення, валідація `percent` |

---

### formatCents

Блок тестує `formatCents` — перетворення цілого числа центів у рядок.

#### `formats whole and fractional`

| Поле | Значення |
|------|----------|
| **Target** | `formatCents` |
| **Setup** | none |

**Assertion 1**

| Поле | Значення |
|------|----------|
| **Inputs** | `cents: number` = `42800` |
| **Assertion** | `expect(...).toBe(...)` |
| **Expected** | `string` = `"428.00"` |

**Assertion 2**

| Поле | Значення |
|------|----------|
| **Inputs** | `cents: number` = `5` |
| **Assertion** | `expect(...).toBe(...)` |
| **Expected** | `string` = `"0.05"` |

#### `formats zero`

| Поле | Значення |
|------|----------|
| **Target** | `formatCents` |
| **Setup** | none |
| **Inputs** | `cents: number` = `0` |
| **Assertion** | `expect(...).toBe(...)` |
| **Expected** | `string` = `"0.00"` |

#### `formats negative amounts`

| Поле | Значення |
|------|----------|
| **Target** | `formatCents` |
| **Setup** | none |
| **Inputs** | `cents: number` = `-42800` |
| **Assertion** | `expect(...).toBe(...)` |
| **Expected** | `string` = `"-428.00"` |

---

### parseAmount

Блок тестує `parseAmount` — парсинг рядкової суми в цілі центи.

#### `parses a plain decimal`

| Поле | Значення |
|------|----------|
| **Target** | `parseAmount` |
| **Setup** | none |

**Assertion 1**

| Поле | Значення |
|------|----------|
| **Inputs** | `input: string` = `"428.00"` |
| **Assertion** | `expect(...).toBe(...)` |
| **Expected** | `number` = `42800` |

**Assertion 2**

| Поле | Значення |
|------|----------|
| **Inputs** | `input: string` = `"12"` |
| **Assertion** | `expect(...).toBe(...)` |
| **Expected** | `number` = `1200` |

#### `parses a single fractional digit`

| Поле | Значення |
|------|----------|
| **Target** | `parseAmount` |
| **Setup** | none |
| **Inputs** | `input: string` = `"12.5"` |
| **Assertion** | `expect(...).toBe(...)` |
| **Expected** | `number` = `1250` |

#### `parses negative amounts`

| Поле | Значення |
|------|----------|
| **Target** | `parseAmount` |
| **Setup** | none |
| **Inputs** | `input: string` = `"-12.50"` |
| **Assertion** | `expect(...).toBe(...)` |
| **Expected** | `number` = `-1250` |

#### `trims surrounding whitespace`

| Поле | Значення |
|------|----------|
| **Target** | `parseAmount` |
| **Setup** | none |
| **Inputs** | `input: string` = `"  428.00  "` |
| **Assertion** | `expect(...).toBe(...)` |
| **Expected** | `number` = `42800` |

#### `throws on invalid input`

| Поле | Значення |
|------|----------|
| **Target** | `parseAmount` |
| **Setup** | none |
| **Inputs** | `input: string` = `"abc"` (виклик у стрілковій функції) |
| **Assertion** | `expect(() => ...).toThrow(...)` |
| **Expected** | regex `/Not a valid amount/` |

---

### splitEvenly

Блок тестує `splitEvenly` — поділ загальної суми в центах на `n` часток.

#### `splits a cleanly divisible total`

| Поле | Значення |
|------|----------|
| **Target** | `splitEvenly` |
| **Setup** | none |
| **Inputs** | `totalCents: number` = `9000`, `n: number` = `3` |
| **Assertion** | `expect(...).toEqual(...)` |
| **Expected** | `number[]` = `[3000, 3000, 3000]` |

#### `distributes remainder cents to first shares`

| Поле | Значення |
|------|----------|
| **Target** | `splitEvenly` |
| **Setup** | `const shares = splitEvenly(100, 3)` |

**Assertion 1**

| Поле | Значення |
|------|----------|
| **Inputs** | (через `shares`) `totalCents` = `100`, `n` = `3` |
| **Assertion** | `expect(shares).toEqual(...)` |
| **Expected** | `number[]` = `[34, 33, 33]` |

**Assertion 2**

| Поле | Значення |
|------|----------|
| **Inputs** | `shares.reduce((a, b) => a + b, 0)` |
| **Assertion** | `expect(...).toBe(...)` |
| **Expected** | `number` = `100` |

#### `returns a single share when n is 1`

| Поле | Значення |
|------|----------|
| **Target** | `splitEvenly` |
| **Setup** | none |
| **Inputs** | `totalCents: number` = `500`, `n: number` = `1` |
| **Assertion** | `expect(...).toEqual(...)` |
| **Expected** | `number[]` = `[500]` |

#### `handles zero total`

| Поле | Значення |
|------|----------|
| **Target** | `splitEvenly` |
| **Setup** | none |
| **Inputs** | `totalCents: number` = `0`, `n: number` = `3` |
| **Assertion** | `expect(...).toEqual(...)` |
| **Expected** | `number[]` = `[0, 0, 0]` |

---

### applyDiscount

Блок тестує `applyDiscount` — застосування відсоткової знижки до суми в центах.

#### `applies a simple discount`

| Поле | Значення |
|------|----------|
| **Target** | `applyDiscount` |
| **Setup** | none |
| **Inputs** | `cents: number` = `10000`, `percent: number` = `10` |
| **Assertion** | `expect(...).toBe(...)` |
| **Expected** | `number` = `9000` |

#### `applies 0% discount`

| Поле | Значення |
|------|----------|
| **Target** | `applyDiscount` |
| **Setup** | none |
| **Inputs** | `cents: number` = `10000`, `percent: number` = `0` |
| **Assertion** | `expect(...).toBe(...)` |
| **Expected** | `number` = `10000` |

#### `applies 100% discount`

| Поле | Значення |
|------|----------|
| **Target** | `applyDiscount` |
| **Setup** | none |
| **Inputs** | `cents: number` = `10000`, `percent: number` = `100` |
| **Assertion** | `expect(...).toBe(...)` |
| **Expected** | `number` = `0` |

#### `rounds to the nearest cent`

| Поле | Значення |
|------|----------|
| **Target** | `applyDiscount` |
| **Setup** | none |
| **Inputs** | `cents: number` = `99`, `percent: number` = `10` |
| **Assertion** | `expect(...).toBe(...)` |
| **Expected** | `number` = `89` |

#### `rejects negative percent`

| Поле | Значення |
|------|----------|
| **Target** | `applyDiscount` |
| **Setup** | none |
| **Inputs** | `cents: number` = `10000`, `percent: number` = `-1` (у стрілковій функції) |
| **Assertion** | `expect(() => ...).toThrow(...)` |
| **Expected** | regex `/percent/i` |

#### `rejects percent above 100`

| Поле | Значення |
|------|----------|
| **Target** | `applyDiscount` |
| **Setup** | none |
| **Inputs** | `cents: number` = `10000`, `percent: number` = `101` (у стрілковій функції) |
| **Assertion** | `expect(() => ...).toThrow(...)` |
| **Expected** | regex `/percent/i` |

---

## Assertion reference

Матчери vitest, що зустрічаються у `money.test.ts`:

| Матчер | Кількість викликів | Призначення у цьому файлі |
|--------|-------------------|---------------------------|
| `toBe` | 14 | Строга рівність для примітивів (`number`, `string`) |
| `toEqual` | 5 | Глибока рівність для масивів (`number[]`) |
| `toThrow` | 3 | Виклик функції кидає помилку; 2 з regex-матчером повідомлення |

## Comments in source

З файлу `money.test.ts` (рядки 4–6):

> Minimal smoke tests — they pass. The cookbook "add tests" prompt should ADD  
> the edge cases these intentionally skip (remainder cents, negatives, bad input,  
> out-of-range discount).

*(Примітка: у поточному файлі edge cases для remainder cents, negatives, bad input та out-of-range discount уже присутні в окремих `it` — коментар відображає початковий задум репозиторію.)*

## Generated

Документ згенеровано промптом `prompts/app-test-functionality-description.md`.

**Єдине джерело:** `app/src/money.test.ts`.
