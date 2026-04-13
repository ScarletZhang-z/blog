---
layout: default
title: "FrontEnd Intervew Questions — list-format"
description: ""
tags:
  - Front-end
  - JS
  - InterviewQuestion
---


**List Format**

**Languages:** JS / TS | **Difficulty:** Medium | **Time:** ~20 mins

---

### The Task

Write a function called `listFormat` that takes a list of names (strings) and joins them into a single, readable sentence.

**Basic joining rules:**
- No items → return an empty string `''`
- One item → return it as-is
- Two items → join with `" and "`
- Three or more → separate with commas, and use `" and "` before the last one

```js
listFormat([])                          // ''
listFormat(['Bob'])                     // 'Bob'
listFormat(['Bob', 'Alice'])            // 'Bob and Alice'
listFormat(['Bob', 'Ben', 'Tim', 'Jane', 'John'])
                                        // 'Bob, Ben, Tim, Jane and John'
```

---

### Options (second parameter)

The function accepts an optional settings object as the **last argument**:

**`sorted`** — Sort the names alphabetically before formatting.

**`length`** — Only show the first N names. Summarize the rest as `"and X other(s)"`. Ignore this option if the value is 0, negative, or otherwise invalid.

**`unique`** — Remove duplicate names before formatting.

```js
// length: only show first 3, summarize the rest
listFormat(['Bob', 'Ben', 'Tim', 'Jane', 'John'], { length: 3 })
// 'Bob, Ben, Tim and 2 others'

// length: when only 1 is hidden, use "other" (not "others")
listFormat(['Bob', 'Ben', 'Tim', 'Jane', 'John'], { length: 4 })
// 'Bob, Ben, Tim, Jane and 1 other'

// sorted + length: sort first, then trim
listFormat(['Bob', 'Ben', 'Tim', 'Jane', 'John'], { length: 3, sorted: true })
// 'Ben, Bob, Jane and 2 others'

// unique + length: remove duplicates first, then trim
listFormat(['Bob', 'Ben', 'Tim', 'Jane', 'John', 'Bob'], { length: 3, unique: true })
// 'Bob, Ben, Tim and 2 others'

// Empty strings should be ignored
listFormat(['Bob', 'Ben', '', '', 'John'])
// 'Bob, Ben and John'
```

---

### Things to Watch Out For

- **Empty strings** in the list should be silently dropped.
- **`length` values** that are 0, negative, or non-numeric should be ignored (treat the list as if no `length` was given).
- **Order of operations** when multiple options are combined: apply `unique` → `sorted` → `length`, in that order.
- **"other" vs "others"**: use `"1 other"` for exactly one hidden item, `"X others"` for two or more.



### How to Solve It

The solution has two main parts:

1. **Clean up and process the input list** — strip empty strings, sort, deduplicate.
2. **Format the result** — join everything into a readable sentence.

---

### Part 1: Processing the List

Apply the options in this order:

- **Filter** empty/falsy strings out first (always)
- **Sort** alphabetically if `sorted: true`
- **Deduplicate** if `unique: true`

These are all straightforward array operations.

---

### Part 2: Formatting the Sentence

The key insight is that `length` splits the array into two parts:

- **First part** — the names to show, joined by `", "`
- **Second part** — either the last name, or `"X other(s)"` if `length` is active

```
['Bob', 'Ben', 'Tim', 'Jane', 'John']
 └── first part: 'Bob, Ben, Tim' ──┘  └── second part: '2 others'
```

Use `" and "` to connect the two parts.

---

### The Code

```js
const SEPARATOR = ', ';
const OTHERS_SEPARATOR = ' and ';
const OTHERS_LABEL = 'other';

export default function listFormat(itemsParam, options = {}) {
  // Step 1: Remove empty/falsy strings
  let items = itemsParam.filter((item) => !!item);

  // Early exits for trivial cases
  if (items.length === 0) return '';
  if (items.length === 1) return items[0];

  // Step 2: Sort alphabetically if requested
  if (options.sorted) {
    items.sort();
  }

  // Step 3: Remove duplicates if requested
  if (options.unique) {
    items = Array.from(new Set(items));
  }

  // Step 4a: If `length` is valid and cuts the list short...
  if (options.length > 0 && options.length < items.length) {
    const shown    = items.slice(0, options.length).join(SEPARATOR);
    const count    = items.length - options.length;
    const label    = count + ' ' + OTHERS_LABEL + (count > 1 ? 's' : '');
    return shown + OTHERS_SEPARATOR + label;
  }

  // Step 4b: No `length` limit — show everything
  const allButLast = items.slice(0, -1).join(SEPARATOR);
  const last       = items[items.length - 1];
  return allButLast + OTHERS_SEPARATOR + last;
}
```

---

### Key Details to Get Right

| Edge case | How it's handled |
|---|---|
| Empty strings in input | Filtered out with `.filter(Boolean)` |
| `length: 0` or negative | Treated as if `length` wasn't given |
| Exactly 1 item hidden | `"1 other"` (no "s") |
| 2+ items hidden | `"2 others"`, `"3 others"`, etc. |
| Single item in list | Returned directly, no joining needed |

---

### Worth Knowing

This solution hardcodes English separators (`","` and `"and"`). The real `Intl.ListFormat` API handles this properly — it adapts separators based on the user's locale, so `"and"` might become `"y"` in Spanish, for example. For any real production use, you'd want to either use `Intl.ListFormat` directly, or accept the separators as configurable options rather than hardcoding them.