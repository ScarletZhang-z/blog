---
layout: default
title: "Scarlet Zhang"
---

## Welcome to My Blog

👋 Hey, hello, hiya, g'day, mabuhay, kia ora, 你好, hallo, vítejte!

I'm **Scarlet Zhang** — a front-end engineer and product thinker who loves design and storytelling.  
Here I share my journey through **front-end development**, **product design**, and **building things people love**.

**Topics I write about:**
- Front-end development (React, CSS, Next.js)
- Product thinking & UX
- Design and creativity

## My latest posts
{% assign recent_posts = site.posts | slice: 0, 5 %}
{% if recent_posts.size > 0 %}
<ul class="home-post-list">
  {% for post in recent_posts %}
  <li>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    <span class="home-post-date">{{ post.date | date: "%Y-%m-%d" }}</span>
  </li>
  {% endfor %}
</ul>
{% else %}
还没有文章，但我正在写第一篇！
{% endif %}
