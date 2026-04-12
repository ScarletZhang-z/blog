---
layout: default
title: "FrontEnd Intervew Questions — Debounce"
description: ""
tags:
  - Front-end
  - JS
  - InterviewQuestion
---
# getElementsByTagName
## What it is
`getElementsByTagName` is a **JavaScript method** (a built-in function) for HTML/JS.

It lets you **find all HTML elements on a page (or inside a specific element)** that have the **same tag name** (like `div`, `span`, `p`, `button`).

---

## Key Rules 
1. It works on the **whole document** OR a **single specific element**.
2. It only looks at **child/descendant elements** (elements inside the parent) — **NOT the parent itself**.
3. The real browser version returns an `HTMLCollection` (a special list of elements).
4. For our coding task: we return a normal **array** of elements instead.

---

## Example in plain words
If you have this HTML:
```html
<div id="foo">
  <span>Span</span>
  <p>Paragraph</p>
  <div id="bar">Div</div>
</div>
```
When you run:
`getElementsByTagName(document.body, 'div')`

It finds **all div elements inside the body** → returns `[div#foo, div#bar]` (an array).

---

## Our Task 
We need to write our own version of this function:
- Input: a starting element + a tag name (string)
- Logic: search **all descendants** (children, grandchildren, etc.) of the starting element
- Output: an **array** of matching elements (not HTMLCollection)


Here's the English version:Click **Next step** to walk through the traversal one node at a time.

---

# Core idea

`getElementsByTagName(element, tagName)` needs to collect all **descendant** elements (not the element itself) whose tag name matches. The natural approach is a depth-first traversal of the subtree.

---

## Solution 1: Recursive DFS (cleanest)

```js
function getElementsByTagName(element, tagName) {
  const result = [];
  const tag = tagName.toUpperCase(); // DOM tagNames are always uppercase

  function dfs(el) {
    for (const child of el.children) {   // .children = Element nodes only
      if (child.tagName === tag) {
        result.push(child);
      }
      dfs(child); // always recurse, even if the tag didn't match
    }
  }

  dfs(element);
  return result;
}
```

**Key decisions:**
- `el.children` instead of `el.childNodes` — `children` gives only Element nodes; `childNodes` also includes text nodes and comments, which have no `tagName`.
- Normalize case: `el.tagName` is always uppercase (`"DIV"`, `"SPAN"`), so the input needs `.toUpperCase()` before comparing.
- Push first, then recurse → results come out in **document order** (pre-order traversal).
