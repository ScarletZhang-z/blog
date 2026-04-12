---
layout: default
title: "FrontEnd Intervew Questions — jquery-css"
description: ""
tags:
  - Front-end
  - JS
  - InterviewQuestion
---

The question asks you to implement a jQuery-style `$` function in vanilla JavaScript that supports the `.css()` method — which can either **get** or **set** inline CSS properties on a DOM element, with method chaining support when setting.

## Example Usage

```javascript
const element = document.getElementById('my-element') // Get CSS property
console.log($(element).css('color')) // e.g. "red"

// Set CSS property
$(element).css('color', 'blue') // Sets the color to blue
```
## Key Requirements
1. The `$` function should accept a DOM element and return an object with a `css` method.
2. The `css` method should support two signatures:
   - `css(propertyName)` → returns the value of the specified CSS property.
   - `css(propertyName, value)` → sets the specified CSS property to the given value and returns the object itself to allow method chaining.
3. The implementation should use vanilla JavaScript without relying on any external libraries.

## Clarification questions

Before coding, ask two things upfront:

1. **What if the selector matches nothing?** → Follow jQuery: fail silently, don't throw.
2. **What if we get a property that was never set?** → Return `undefined`, not an empty string.

---

## The core challenge: method chaining

For `$('button').css('color', 'red').css('fontSize', '16px')` to work, every setter call must return the same wrapper object. That means `css()` needs persistent access to the DOM element it found — across every call in the chain.

Two ways to achieve this: **closures** or a **class**.

---

## Solution 1: closure (recommended)

```js
export default function $(selector) {
  const element = document.querySelector(selector); // captured by closure

  return {
    css: function (prop, value) {

      // Getter
      if (value === undefined) {
        if (element == null) return undefined;   // no element found

        const val = element.style[prop];
        return val === '' ? undefined : val;     // unset property → undefined, not ''
      }

      // Setter
      if (element != null) {
        element.style[prop] = value;
      }

      return this;  // return the wrapper itself → enables chaining
    },
  };
}
```

`element` is held by the returned object's `css` method via closure. No class, no `this.element` needed.

---

## Solution 2: class (alternative)

```js
class jQuery {
  constructor(selector) {
    this.element = document.querySelector(selector);
  }

  css(prop, value) {
    if (value === undefined) {
      if (this.element == null) return undefined;

      const val = this.element.style[prop];
      return val === '' ? undefined : val;
    }

    if (this.element != null) {
      this.element.style[prop] = value;
    }

    return this;
  }
}

export default function $(selector) {
  return new jQuery(selector);
}
```

Same logic, just using `this.element` instead of a closure variable. For a problem this small, the class is overkill — the closure approach is cleaner.

---

## Key decisions explained

**Why `value === undefined` and not `!value` to split getter from setter?**

```js
$('button').css('color', '');  // value = '' → should set (clears the style)
$('button').css('color');      // value = undefined → should get
```

A falsy check (`!value`) would misread an empty string as a getter call, breaking the "clear a style" use case. Strict `=== undefined` is the only correct check here.

**Why convert `''` to `undefined` in the getter?**

`el.style[prop]` returns `''` for any property that hasn't been set inline. But jQuery's contract says unset properties return `undefined`. So one extra step is needed:

```js
return val === '' ? undefined : val;
```

**Why can't `css` be an arrow function?**

```js
// BROKEN
css: (prop, value) => {
  ...
  return this;  // `this` is the outer lexical scope, NOT the wrapper object
}
```

Arrow functions don't have their own `this` — they inherit it from where they were defined. At the module's top level that's typically `undefined`. You need a regular `function` so that `this` refers to the object the method is called on (the wrapper).

---

## Edge case summary

| Scenario | Behavior |
|---|---|
| Selector matches nothing, getter called | Return `undefined` |
| Selector matches nothing, setter called | Skip silently, still return `this` |
| Property was never set, getter called | Return `undefined` (not `''`) |
| Multiple setters chained | Each returns the same wrapper — works correctly |