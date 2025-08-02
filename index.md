---
layout: home
title: Home
landing-title: 'Programming Projects'
description: null
image: null
author: null
show_tile: false
---

<header class="major">
  <h2>Projects</h2>
</header>

A selection of the programming projects that I have created over the recent years.
I enjoy exploring a broad range of topics, which leads me to discover lots of new and interesting problems to be solved.

{% for project in site.projects %}
<div class="project">
  <header class="major">
    <h3>
      <a href="{{ project.url }}"> {{ project.title }}</a>
    </h3>
  </header>
  <p>{{ project.description }}</p>
  <ul class="actions">
    <li><a href="{{ project.url }}" class="button next">{{ project.title}}</a></li>
  </ul>
</div>
{% endfor %}
