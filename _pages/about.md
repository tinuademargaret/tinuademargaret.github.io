---
layout: about
title: about
permalink: /
subtitle:
profile: false

news: false
selected_papers: false
social: false
---

<section class="about-split">
  <aside class="about-sidebar">
    <img class="about-avatar" src="{{ 'assets/img/standard_avatar_2.jpg' | relative_url }}" alt="Portrait of Tinuade Margaret">
    <h2 class="about-name">Tinuade Margaret</h2>

    <div class="about-social-icons">
      {% include social.liquid %}
    </div>

  </aside>

  <div class="about-main">
    <h1>Hi!</h1>
    <p>
      I am Tinuade Margaret, a research engineer working on reasoning, evaluation, and alignment-minded systems for frontier language models.
    </p>
    <p>
      My goal is to build AI that can augment humans: systems that make people more capable, more thoughtful, and more effective at solving difficult problems.
    </p>
    <p>I've worked on:</p>
    <ul>
      <li>reasoning and reliability evaluation pipelines for LLMs</li>
      <li>question generation systems and NLP model diagnostics</li>
      <li>machine learning and data systems in production settings</li>
    </ul>
    <p>
      Previously, I completed an MSc in AI at Heriot-Watt and worked across education and startup environments building practical ML systems.
    </p>

    <section class="about-research">
      <h2>Research</h2>
      <div class="publications">
        {% bibliography --group_by none %}
      </div>
    </section>
  </div>
</section>
