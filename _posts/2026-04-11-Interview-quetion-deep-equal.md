---
layout: default
title: "FrontEnd Intervew Questions — deep-equal"
description: ""
tags:
  - Front-end
  - JS
  - InterviewQuestion
---
# Deep Equal

## Problem

Implement `deepEqual(a, b)` to deeply compare two values. Return `true` if their contents are identical, `false` otherwise.

- Inputs are JSON-serializable only: numbers, strings, booleans, `null`, objects, arrays.
- No circular references.

```js
deepEqual('foo', 'foo')             // true
deepEqual({ id: 1 }, { id: 1 })     // true
deepEqual(1, 2)                     // false
deepEqual({ id: '1' }, { id: '2' }) // false
```

---

## Core Approach

1. Use `Object.is(a, b)` for primitive comparison — more accurate than `===` (handles `NaN` and `-0`)
2. Use `Object.prototype.toString.call(v)` to precisely identify type — distinguishes `null`, arrays, and plain objects
3. Only recurse into `[object Object]` and `[object Array]` types; everything else falls through to `Object.is`
4. Use `Object.entries()` to get key-value pairs, compare count first, then recurse on each value

---

## Key Concepts

### Why `Object.is` instead of `===`

`===` has two edge cases that produce wrong results:

```js
NaN === NaN   // false  ← should be true
+0  === -0    // true   ← should be false

Object.is(NaN, NaN)  // true  ✓
Object.is(+0, -0)    // false ✓
```

> React's reconciliation phase also uses `Object.is` to detect prop changes.

---

### Why `Object.prototype.toString` instead of `typeof`

`typeof` can't distinguish null, arrays, plain objects, or Date:

```js
typeof null        // "object"  ← wrong
typeof []          // "object"  ← can't tell from {}
typeof new Date()  // "object"  ← can't tell from {}

Object.prototype.toString.call(null)       // "[object Null]"   ✓
Object.prototype.toString.call([])         // "[object Array]"  ✓
Object.prototype.toString.call({})         // "[object Object]" ✓
Object.prototype.toString.call(new Date()) // "[object Date]"   ✓
```

---

### Why `Object.entries` instead of `for...in`

`for...in` walks up the prototype chain and includes inherited properties. `Object.entries` returns only own enumerable properties, and lets you short-circuit early by comparing lengths first.

---

## Official Solution

```js
function getType(value) {
  return Object.prototype.toString.call(value);
}

function shouldDeepCompare(type) {
  return type === '[object Object]' || type === '[object Array]';
}

function deepEqual(value1, value2) {
  const type1 = getType(value1);
  const type2 = getType(value2);

  if (type1 === type2 && shouldDeepCompare(type1)) {
    const entries1 = Object.entries(value1);
    const entries2 = Object.entries(value2);

    if (entries1.length !== entries2.length) return false;

    return entries1.every(([key, val]) =>
      deepEqual(val, value2[key])
    );
  }

  return Object.is(value1, value2);
}
```

---

## Simple vs Official Solution

| Topic | Simple solution | Official solution |
|---|---|---|
| Value comparison | `===` | `Object.is` (handles NaN / -0) |
| Type detection | `typeof` | `Object.prototype.toString` |
| Key iteration | `Object.keys` + loop | `Object.entries` + `every` |
| null handling | Manual null check needed | Falls through to `Object.is` automatically |
| Date / Regex | Not handled | Falls through to `Object.is` (reference equality) |

---

## Edge Cases — Mention These in Interviews

- **`NaN` and `-0`** — handled correctly by `Object.is`
- **`null` vs `{}`** — `toString` distinguishes them precisely, no false positives
- **Circular references** — not covered here; real solution needs a `WeakMap` to track visited nodes
- **Property descriptors** (writable / enumerable) — not compared
- **Symbol-keyed and non-enumerable properties** — `Object.entries` skips these, so they are not compared