---
layout: page
title: Apps & Projects
---

A selection of apps and projects I work on.

<div class="project-grid">
  {%- for project in site.data.projects -%}
  <div class="project-card">
    <img class="project-icon" src="{{ project.icon | relative_url }}" alt="{{ project.name }} icon">
    <h3 class="project-name">{{ project.name }}</h3>
    <p class="project-description">{{ project.description }}</p>
    <a class="project-link" href="{{ project.url }}">{{ project.link_text }}</a>
  </div>
  {%- endfor -%}
</div>
