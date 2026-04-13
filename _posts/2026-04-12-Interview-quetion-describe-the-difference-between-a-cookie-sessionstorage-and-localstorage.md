---
layout: default
title: "FrontEnd Intervew Questions — describe-the-difference-between-a-cookie-sessionstorage-and-localstorage"
description: ""
tags:
  - Front-end
  - JS
  - InterviewQuestion
---

## What's the difference between cookies, `sessionStorage`, and `localStorage`?

All three are ways to save data in a user's browser. Think of them like different types of storage boxes — each with its own rules for how long the data sticks around and who can see it.

**Quick summary:**

| | Cookie | `localStorage` | `sessionStorage` |
|---|---|---|---|
| Who creates it? | Browser or server | Browser only | Browser only |
| How long does it last? | You decide (or until browser closes) | Forever, until deleted | Until the tab closes |
| Survives closing the browser? | Only if you set an expiry date | ✅ Yes | ❌ No |
| Sent to the server automatically? | ✅ Yes, every time | ❌ No | ❌ No |
| How much can it store? | ~4KB | ~5MB | ~5MB |
| Accessible from other tabs? | ✅ Yes | ✅ Yes | ❌ No |
| Can be hidden from JavaScript? | ✅ Yes (`HttpOnly`) | ❌ No | ❌ No |

---

## The basics

All three share a few things in common:

- Users (or JavaScript code) can read and change the values stored in them (with one exception for cookies, explained below).
- They store data as **key-value pairs** — like a dictionary where each label maps to a value.
- They can only store **text strings**, so if you want to save something like a list or object, you need to convert it first (using `JSON.stringify()`).

---

## When should you use each one?

### 🍪 Cookies — for talking to the server

Cookies are the only one of the three that gets **automatically sent to the server** with every request your browser makes. That makes them perfect for things the server needs to know about, like whether you're logged in.

Because they're sent with every request, they're intentionally kept small (max 4KB). Storing too much in a cookie would slow down every page load.

**Best for:** Login tokens, session IDs, language preferences, analytics tracking.

**Bonus security features:**
- `HttpOnly` — hides the cookie from JavaScript (protects against hackers stealing it)
- `Secure` — only sends the cookie over HTTPS
- `Expires` / `Max-Age` — automatically deletes the cookie after a set time

```js
// Save a cookie that expires at the end of 2024
document.cookie = 'auth_token=abc123; expires=Fri, 31 Dec 2024 23:59:59 GMT; path=/';

// Read cookies (returns one big string — you have to parse it yourself)
console.log(document.cookie); // "auth_token=abc123"

// Delete a cookie by setting an expiry date in the past
document.cookie = 'auth_token=; expires=Thu, 01 Jan 1970 00:00:00 GMT; path=/';
```

Reading and writing cookies is a bit clunky. A library like [`js-cookie`](https://github.com/js-cookie/js-cookie) makes it much easier.

---

### 🗄️ `localStorage` — for data you want to keep long-term

`localStorage` saves data that **sticks around even after the browser is closed**. It's like writing something down in a notebook that stays on your desk.

**Best for:** User preferences like dark mode or layout settings — things that should still be there the next time the user visits.

```js
localStorage.setItem('theme', 'dark');       // Save
localStorage.getItem('theme');               // Read → "dark"
localStorage.removeItem('theme');            // Delete one item
localStorage.clear();                        // Delete everything
```

---

### 📋 `sessionStorage` — for temporary data in one tab

`sessionStorage` only keeps data **while the current tab is open**. Close the tab and it's gone. It also can't be seen by other tabs, even ones on the same website.

**Best for:** Saving form progress so data isn't lost if the user accidentally refreshes the page.

```js
sessionStorage.setItem('formStep', '2');     // Save
sessionStorage.getItem('formStep');          // Read → "2"
sessionStorage.removeItem('formStep');       // Delete one item
sessionStorage.clear();                      // Delete everything
```

---

## One more thing

If you ever need to store large or complex data in the browser (like an offline database), there's a more powerful option called [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) — though it's more complicated to use than the three options above.