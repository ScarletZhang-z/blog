---
layout: default
title: "📘 JavaScript Design Patterns — Vol.1: The Factory Pattern"
description: ""
tags:
  - JavaScript
  - Front-end
  - Design Patterns
---

# From Zoo to Factory 🦁

*— For Front-End Developers in 2025*

> Don’t rush. The world will wait for you to grow.

---

## 1. Why Should Front-End Developers Learn Design Patterns?

Design patterns were born from object-oriented software engineering, crafted to solve recurring problems in object creation and composition.

As front-end development evolved from “building static pages” to “managing state and complex business logic,” we began facing the same architectural challenges: countless components, tangled dependencies, and tight coupling between client and server.

Now, with AI-assisted code generation and automated build tools, our code changes faster than ever — making it harder to maintain clarity and structure amid constant change.

That’s exactly where design patterns help. **They keep what changes flexible, and what doesn’t change stable.**

Of course, not every pattern fits front-end development. Take the five SOLID principles — the ones that truly resonate with front-end engineers are **Single Responsibility** and **Open–Closed**.

In this series, I’ll break down design patterns using simple, vivid examples. My hope is that in this noisy, fast-moving industry, we can slow down, learn with intention, and grow together as more thoughtful developers.



## 2. Constructor Pattern: Building a Zoo 🐒

Let’s say you’re building a zoo management system. You start by recording animals like this:

```js
const monkey = {
  name: 'King Kong',
  age: 1
}

const tiger = {
  name: 'Tiger',
  age: 3
}
```

It works — until your zoo gets crowded and you’re trapped in **copy-paste hell**.
To avoid this, you can use a **constructor** to encapsulate the object creation logic:

```js
class Animal {
  constructor(name, age) {
    this.name = name
    this.age = age
  }
}

const monkey = new Animal('King Kong', 1)
const tiger = new Animal('Tiger', 3)
```

In JavaScript, a **constructor** is a special function used to create and initialize objects.
We invoke it with the `new` keyword.

The **constructor pattern** simply means wrapping the initialization logic inside a function or class so that object creation becomes standardized and consistent.

Each instance of `Animal` has the same structure (`name`, `age`), but different values.
This pattern separates what’s *stable* (the structure) from what’s *variable* (the data).

> **The constructor pattern abstracts the “change within a single object.”
> The factory pattern abstracts the “change between different constructors.”**

In other words:

* The constructor helps you **build one animal**.
* The factory helps you **decide which kind of animal to build**.

When your system has multiple object types with complex creation logic, constructors alone are not enough — you’ll need a factory to manage the creation process.

---

## 3. Simple Factory Pattern 🏭

Now the zoo has a new requirement:

> Each animal should be assigned different food based on its diet.

That means our `Animal` class isn’t flexible enough — we need specialized types:

```js
class Vegetarian {
  constructor(name, age) {
    this.name = name
    this.age = age
    this.favorite = 'fruit'
    this.food = ['apple', 'banana']
  }
}

class Carnivore {
  constructor(name, age) {
    this.name = name
    this.age = age
    this.favorite = 'meat'
    this.food = ['beef', 'pork']
  }
}
```

But as you add more animals, you’ll end up with **a jungle of classes** —
and they all share similar properties (`name`, `age`).

So let’s extract the common parts and let a **factory function** handle the differences:

```js
class Animal {
  constructor(name, age, favorite, food) {
    Object.assign(this, { name, age, favorite, food })
  }
}

// Simple factory: create animals by diet type
const createAnimal = (type, name, age) => {
  const animalConfig = {
    fruit: { favorite: 'fruit', food: ['apple', 'banana'] },
    meat: { favorite: 'meat', food: ['beef', 'pork'] },
    vegetable: { favorite: 'vegetable', food: ['cabbage'] }
  }

  const config = animalConfig[type] || { favorite: 'unknown', food: [] }
  const { favorite, food } = config

  return new Animal(name, age, favorite, food)
}

// Simple and elegant!
const monkey = createAnimal('fruit', 'King Kong', 1)
const tiger = createAnimal('meat', 'Tiger', 3)
```

Now, no matter how many animal types we add,
we only need to extend the factory — not rewrite every class.

That’s the **core idea of the Simple Factory Pattern**:

> *Let one function encapsulate the creation logic.*
> The caller just needs to say **what** they want,
> without worrying about **how** it’s created.

---

## 4. Factory Pattern in Modern Frameworks

The factory pattern is everywhere — you just might not notice it.

### React Example

```jsx
import React from 'react'
import { Button, Card } from './components'

const createComponent = (type, props) => {
  const components = {
    button: Button,
    card: Card
  }

  const Component = components[type] || 'div'
  return <Component {...props} />
}

const App = () => (
  <div>
    {createComponent('button', { label: 'Click me!' })}
    {createComponent('card', { title: 'Hello React' })}
  </div>
)

export default App
```

This is essentially a **component factory** — a function that centralizes how components are created and rendered, making the code more flexible and dynamic.

### Vue Example

```js
import { h } from 'vue'
import AlertDialog from './AlertDialog.vue'
import ConfirmDialog from './ConfirmDialog.vue'

const createDialog = (type) => {
  const components = {
    alert: AlertDialog,
    confirm: ConfirmDialog
  }
  return h(components[type] || 'div')
}
```

Vue’s `h()` (short for *hyperscript*) function is literally a **virtual node factory** —
and your `createDialog()` builds on top of it as a **business-level component factory**.

---
## Next Up 🌿

In the next chapter, we’ll talk about the **Abstract Factory Pattern** —

When your zoo expands across **continents** (Asian Zoo, African Zoo, etc.),
and each has its own ecosystem and food chain,
the simple factory won’t be enough.

The abstract factory will show us how to manage a **“factory of factories”**,
making even the creation logic itself modular and scalable.
