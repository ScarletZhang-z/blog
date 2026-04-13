---
layout: default
title: "FrontEnd Intervew Questions — describe-event-bubbling"
description: ""
tags:
  - Front-end
  - JS
  - InterviewQuestion
  - quiz
---

## What is Event Bubbling?

**Simple version:** When you click something on a webpage, the click doesn't just affect that one element — it also "bubbles up" and notifies all the parent elements above it in the page structure.

Think of it like dropping a stone in water. The ripple starts where the stone lands and spreads outward.

---

## How Does It Work?

Imagine this HTML structure:

```
Page
 └── div#parent
      └── button#child  ← you click here
```

When you click the button, JavaScript fires the click event on the button first, then on the div, then on the page root — automatically, in that order.

```js
parent.addEventListener('click', () => {
  console.log('Parent was clicked');
});

child.addEventListener('click', () => {
  console.log('Child was clicked');
});

// When you click the button, you'll see BOTH messages:
// "Child was clicked"
// "Parent was clicked"
```

---

## How Do I Stop It?

Call `event.stopPropagation()` inside your handler to prevent the event from traveling any further up the tree.

```js
child.addEventListener('click', (event) => {
  console.log('Child was clicked');
  event.stopPropagation(); // The parent will NOT hear about this click
});
```

---

## Why Is This Useful? (Event Delegation)

Instead of adding a click listener to every single button in a list, you can add **one listener to the parent** and let bubbling do the rest.

**The old, repetitive way:**
```js
document.getElementById('btn-1').addEventListener('click', handleClick);
document.getElementById('btn-2').addEventListener('click', handleClick);
document.getElementById('btn-3').addEventListener('click', handleClick);
// ...and so on for every button
```

**The smarter way using bubbling:**
```js
document.getElementById('list').addEventListener('click', (event) => {
  if (event.target.tagName === 'BUTTON') {
    console.log('You clicked:', event.target.id);
  }
});
```

One listener handles all buttons, even ones added to the page later.

---

## Things to Watch Out For

- **Unintended triggers:** A parent might react to a click you only meant for a child. Always check `event.target` to confirm which element was actually clicked.
- **Order matters:** Events bubble up through every ancestor in order, so if multiple parents have listeners, they all fire — highest in the chain last.
- **Don't over-delegate:** Attaching a listener too high up (e.g., directly on `document`) can accidentally catch events from completely unrelated parts of the page.

---

## Quick Summary

| Concept | What it means |
|---|---|
| Event bubbling | A click travels up from the clicked element to the top of the page |
| `stopPropagation()` | Stops the event from traveling any further up |
| Event delegation | Using bubbling to handle many elements with one listener |
