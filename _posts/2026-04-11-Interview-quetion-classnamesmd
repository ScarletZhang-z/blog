---
layout: default
title: "FrontEnd Intervew Questions — classnames"
description: ""
tags:
  - Front-end
  - JS
  - InterviewQuestion
---

# classnames

## Problem Description

`classnames` is a commonly-used utility in modern front end applications to conditionally join CSS class names together. If you've written React applications, you likely have used a similar library.

Implement the `classnames` function.

---

## Examples

```js
classNames('foo', 'bar'); // 'foo bar'
classNames('foo', { bar: true }); // 'foo bar'
classNames({ 'foo-bar': true }); // 'foo-bar'
classNames({ 'foo-bar': false }); // ''
classNames({ foo: true }, { bar: true }); // 'foo bar'
classNames({ foo: true, bar: true }); // 'foo bar'
classNames({ foo: true, bar: false, qux: true }); // 'foo qux'

// Arrays will be recursively flattened as per the rules above.
classNames('a', 'b', { c: true, d: false }); // 'a b c'

// Values can be mixed.
classNames(
  'foo',
  {
    bar: true,
    duck: false,
  },
  'baz',
  { quux: true },
); // 'foo bar baz quux'

// Falsey values are ignored.
classNames(null, false, 'bar', undefined, { baz: null }, ''); // 'bar'
```

In addition, the returned string should not have any leading or trailing whitespace.

---

## Clarification Questions

The following are good questions to ask the interviewer to demonstrate your thoughtfulness. Depending on their response, you might need to adjust the implementation accordingly.

> **Can there be duplicated classes in the input? Should the output contain duplicated classes?**

Yes, there can be. In this case the output will contain duplicated classes. However, we will not test for this case.

> **What if a class was added and then later turned off? E.g. `classNames('foo', { foo: false })`?**

In the library implementations, the final result will be `'foo'`. However, we will not test for this case.

---

## Solution

### Key Insight

The tricky part of this solution is the **recursive nature** of the function. Hence we can separate out the solution into two parts:

1. Handling of each data type.
2. Recursing for array type.

We will need a data structure `classes` to collect all the classes for the lifetime of the function that the recursive calls have access to. An `Array` works well for this, but you can also use a `Set`.

---

### How to Handle Each Data Type

| Type | Action |
|------|--------|
| Falsy values (`null`, `false`, `undefined`, `""`) | Ignore / skip |
| `string` | Add directly to the `classes` collection |
| `number` | Add directly to the `classes` collection |
| `Array` | Recursively invoke `classNames` and add the result |
| `object` | Loop through key/value pairs; add keys with truthy values |

---

### Approach: Pure Recursive Function

In this approach, the `classNames` function calls itself and its return value is a string that can be composed by parent recursive calls.

```js
/**
 * @param {...(any|Object|Array<any|Object|Array>)} args
 * @return {string}
 */
export default function classNames(...args) {
  const classes = [];

  args.forEach((arg) => {
    // Ignore falsey values.
    if (!arg) {
      return;
    }

    const argType = typeof arg;

    // Handle string and numbers.
    if (argType === 'string' || argType === 'number') {
      classes.push(arg);
      return;
    }

    // Handle arrays.
    if (Array.isArray(arg)) {
      classes.push(classNames(...arg));
      return;
    }

    // Handle objects.
    if (argType === 'object') {
      for (const key in arg) {
        // Only process non-inherited keys.
        if (Object.hasOwn(arg, key) && arg[key]) {
          classes.push(key);
        }
      }
      return;
    }
  });

  return classes.join(' ');
}
```

---

### Alternative Approaches for the Recursive Helper

Three patterns are possible for managing the `classes` collection across recursive calls:

1. **Pure recursive function** — Recursive calls are self-contained; each call returns a string that the parent composes. *(Used in the solution above.)*
2. **Inner recursive helper that modifies an external value** — The collection is defined at the top level; inner functions mutate it directly.
3. **Inner recursive helper that modifies the argument** — The collection is passed as an argument into recursive calls and mutated there.

---

## Common Pitfalls

- **`typeof []` returns `'object'`** — You must check `Array.isArray(arg)` *before* checking `typeof arg === 'object'`, otherwise arrays will be mishandled as plain objects.
- **Stack overflow** — Any recursive solution is vulnerable to stack overflow on deeply nested arrays. Worth mentioning in an interview.
- **Circular references** — Arrays or objects with circular references can cause infinite recursion. Also worth flagging as an edge case.

---

## Follow-up: De-duplicating Classes

The provided solution doesn't handle de-duplicating classes. Without de-duplication, `classNames('foo', 'foo')` produces `'foo foo'`, which is redundant from the browser's perspective.

De-duplication also affects edge cases like `classNames('foo', { foo: false })`. Since `{ foo: false }` appears later, the user likely intended for `'foo'` *not* to appear in the result.

This can be handled by using a `Set` instead of an `Array` to collect classes — adding or removing entries as needed.

> De-duplicating classes is usually out of scope for interviews but is a possible follow-up. See **Classnames II** for practice.

---

## Techniques Covered

- Familiarity with JavaScript value types and how to check for them
- Recursion
- Handling variadic arguments (`...args`)
- Converting between `Array` and `Set` (for the de-duplication follow-up)

---

## Library Reference Implementation

For reference, this is how the [`classnames` npm package](https://github.com/JedWatson/classnames) is implemented:

```js
var hasOwn = {}.hasOwnProperty;

export default function classNames() {
  var classes = [];

  for (var i = 0; i < arguments.length; i++) {
    var arg = arguments[i];
    if (!arg) continue;

    var argType = typeof arg;

    if (argType === 'string' || argType === 'number') {
      classes.push(arg);
    } else if (Array.isArray(arg)) {
      if (arg.length) {
        var inner = classNames.apply(null, arg);
        if (inner) {
          classes.push(inner);
        }
      }
    } else if (argType === 'object') {
      if (arg.toString === Object.prototype.toString) {
        for (var key in arg) {
          if (hasOwn.call(arg, key) && arg[key]) {
            classes.push(key);
          }
        }
      } else {
        classes.push(arg.toString());
      }
    }
  }

  return classes.join(' ');
}
```

---

## Resources

- [`classnames` library on GitHub](https://github.com/JedWatson/classnames)
- [`clsx` library on GitHub](https://github.com/lukeed/clsx) — A newer, faster, and smaller drop-in replacement for `classnames`