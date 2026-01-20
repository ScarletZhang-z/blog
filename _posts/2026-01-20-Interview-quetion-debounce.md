---
layout: default
title: "FrontEnd Intervew Questions — Debounce"
description: ""
tags:
  - Front-end
  - JS
  - InterviewQuestion
---

# Debounce

Debouncing is a technique used to control how many times we allow a function to be executed over time. When a JavaScript function is debounced with a wait time of X milliseconds, it must wait until after X milliseconds have elapsed since the debounced function was last called. 

You almost certainly have encountered debouncing in your daily lives before — when entering an elevator. Only after X duration of not pressing the "Door open" button (the debounced function not being called) will the elevator door actually close (the callback function is executed).

Implement a `debounce` function which accepts a callback function and a `wait` duration. Calling `debounce()` returns a function which has debounced invocations of the callback function following the behavior described above.

## Real Example - Elevator Door

```javascript
const closeElevatorDoor = debounce(() => {
    console.log('Elevator door closed')
}, 3000)

// User keeps pressing "open" button
closeElevatorDoor()  // timer starts
closeElevatorDoor()  // timer resets
closeElevatorDoor()  // timer resets
// Door closes 3000ms after last press
```

## Debounce

```javascript
function debounce(func, wait) {
    let timer = null
    return function(...args) {
        clearTimeout(timer)
        const context = this
        timer = setTimeout(() => {
            func.apply(context, args)
        }, wait)
    }
}
```

## Arrow Function vs Normal Function

|                     | Normal Function           | Arrow Function                    |
| ------------------- | ------------------------- | --------------------------------- |
| **this**            | Who calls it, this is who | No own this, inherits from parent |
| **bind/call/apply** | Works                     | Cannot change this                |

**When this is lost** → Use `apply`, `call`, or `bind` to pass it (only works for normal functions)

## Debounce vs Throttle

Core Difference

|                  | Debounce                            | Throttle                             |
| ---------------- | ----------------------------------- | ------------------------------------ |
| **On each call** | Reset timer                         | Ignore if timer exists               |
| **Key code**     | `clearTimeout(timer)`               | `if (timer) return`                  |
| **Behavior**     | Wait until calls stop, then execute | Execute once, ignore during cooldown |
| **Use case**     | Search input, resize                | Scroll, button spam                  |

