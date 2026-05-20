---
layout: page
title: projects
permalink: /projects/
nav: true
nav_order: 3
---

<section class="editorial-intro">
  <p class="kicker">selected work &nbsp;·&nbsp; an ongoing index</p>
  <h2 class="lede-title">Experiments, instruments, and half-built systems &mdash; in roughly the order I cared about them.</h2>
  <p>
    Entries below are placeholders for now. Each will be replaced with a full write-up: the question, the build, what surprised me, and what I would change.
  </p>
</section>

<div class="project-index">
  {% assign sorted_projects = site.projects | sort: "importance" %}
  {% for project in sorted_projects %}
    {% assign target = project.url | relative_url %}
    {% if project.redirect %}{% assign target = project.redirect %}{% endif %}
    <article class="project-entry">
      <span class="num">{% assign n = forloop.index %}{% if n < 10 %}0{{ n }}{% else %}{{ n }}{% endif %}</span>
      <a class="thumb {% unless project.img %}empty{% endunless %}" href="{{ target }}" aria-hidden="true" tabindex="-1">
        {% if project.img %}<img src="{{ project.img | relative_url }}" alt="">{% endif %}
      </a>
      <div class="body">
        <a class="title" href="{{ target }}">{{ project.title }}</a>
        {% if project.description %}<p class="desc">{{ project.description }}</p>{% endif %}
      </div>
      <span class="meta">{% if project.category %}{{ project.category }}{% endif %}</span>
    </article>
  {% endfor %}
</div>
