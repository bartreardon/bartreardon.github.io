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
    {%- if project.url contains "apps.apple.com" -%}
    <a class="project-badge-link" href="{{ project.url }}">
      <img class="app-store-badge" src="{{ "/images/Download_on_the_App_Store_Badge_US-UK_RGB_blk_092917.svg" | relative_url }}" alt="Download {{ project.name }} on the App Store">
    </a>
    {%- else -%}
    <a class="project-link" href="{{ project.url }}">{{ project.link_text }}</a>
    {%- endif -%}
  </div>
  {%- endfor -%}
</div>
