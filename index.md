---
layout: home
title: Home
landing-title: 'Tmforshaw Portfolio'
description: null
image: null
author: null
show_tile: false
---

Nullam et orci eu lorem consequat tincidunt vivamus et sagittis libero. Mauris aliquet magna magna sed nunc rhoncus pharetra. Pellentesque condimentum sem. In efficitur ligula tate urna. Maecenas laoreet massa vel lacinia pellentesque lorem ipsum dolor. Nullam et orci eu lorem consequat tincidunt. Vivamus et sagittis libero. Mauris aliquet magna magna sed nunc rhoncus amet pharetra et feugiat tempus.

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
