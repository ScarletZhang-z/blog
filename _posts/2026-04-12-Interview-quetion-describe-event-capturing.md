---
layout: default
title: "FrontEnd Intervew Questions — describe-event-capturing"
description: ""
tags:
  - Front-end
  - JS
  - InterviewQuestion
  - quiz
---


## What is Event Capturing?

**Simple version:** Event capturing is the *opposite* of event bubbling. Instead of an event starting at the clicked element and traveling *up* to the top, capturing starts at the *top* of the page and travels *down* to the clicked element.

Think of it like a security checkpoint at the entrance of a building — you intercept visitors *before* they reach their destination, not after.

---

## The Three Phases of a Click Event

Every time you click something, the browser actually goes through three stages:

```
Phase 1 — Capturing:   Page root → div → button  (top down ⬇)
Phase 2 — Target:      The button is reached
Phase 3 — Bubbling:    button → div → Page root  (bottom up ⬆)
```

Capturing happens **first**, before bubbling. But here's the catch — it's **turned off by default**.

---

## How Do I Enable It?

You turn on capturing by passing `true` as the third argument to `addEventListener()`:

```js
parent.addEventListener('click', () => {
  console.log('Parent heard the click FIRST (capturing)');
}, true); // 👈 this enables capturing

child.addEventListener('click', () => {
  console.log('Child heard the click SECOND');
});

// When you click the button, you'll see:
// "Parent heard the click FIRST (capturing)"
// "Child heard the click SECOND"
```

Without that `true`, the parent would normally fire *after* the child (bubbling order). With it, the parent fires *before*.

---

## How Do I Stop It?

Just like with bubbling, you can call `event.stopPropagation()` to prevent the event from traveling any further — this time, *downward*:

```js
parent.addEventListener('click', (event) => {
  console.log('Parent intercepted the click');
  event.stopPropagation(); // The child will NEVER hear about this click
}, true);

child.addEventListener('click', () => {
  console.log('Child clicked'); // This never runs
});
```

The event gets stopped at the parent and never reaches the button at all.

---

## When Would I Actually Use This?

Capturing is rarely needed, but it shines in a few specific situations:

- **Closing dropdown menus:** Listen for clicks anywhere on the page (`document`) with capturing enabled, so you can close a dropdown the moment a user clicks outside it — before anything else reacts.
- **Blocking events early:** If a click on a child element causes an unintended side effect on the parent, you can use capturing to intercept and stop it *before* it gets to the child.
- **Slight performance gains:** In rare cases, capturing can be marginally faster since the event doesn't have to travel all the way down and back up. In practice, this difference is almost never noticeable.

---

## Bubbling vs. Capturing — Side by Side

| | Bubbling | Capturing |
|---|---|---|
| Direction | Bottom → Up ⬆ | Top → Down ⬇ |
| On by default? | ✅ Yes | ❌ No |
| How to enable | Nothing extra needed | Pass `true` to `addEventListener` |
| How common is it? | Very common | Rarely used |

---

## Quick Summary

Capturing lets you intercept an event *on the way down* to its target, before the target or any bubbling handlers run. It's a niche tool, but useful when you need to catch events high up in the page before they reach their destination.