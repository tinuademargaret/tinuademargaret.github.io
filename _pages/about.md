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
    <h1>Hello!</h1>
    <p>
      I'm Tinuade (Tee-nu-ah-day), an AI research engineer interested in how language models learn, reason, fail, and change under different data, optimization, architecture, and objective choices.
    </p>
    <p>
      I spent about three years building and deploying software across edtech, fintech, and developer tools, then completed an MSc in Artificial Intelligence at Heriot-Watt University.
    </p>
    <p>
      Since then, I've become increasingly focused on AI research. I've participated in research fellowships including <a href="{{'https://www.arena.education/'}}">ARENA</a> and <a href="{{'https://apartresearch.com/fellowships'}}">APART</a>, and continued developing as a researcher through  <a href="{{'https://algoverseairesearch.org/research'}}">Algoverse</a>, where I mentor research projects and contribute to new research papers. My recent work spans logical reasoning in LLMs, uncertainty-aware evaluation, and steganographic behavior in language models.
    </p>
    <p>
      My current research interests explore how model behavior and internal representations emerge from training choices. This includes questions around data quality, optimization theory, mechanistic interpretability, reasoning, and the science of deep learning.
    </p>
    <!-- <p>
      I'm also building neslacodex, a project focused on making tiny model training experiments easy to run, inspect, and learn from. It started as an exploration of TorchTitan style training infrastructure, but has evolved toward building a small model factory (inspired by poolside's) for small cluster experiments where the core artifact is understanding model behavior.
    </p> -->
    <p>
      I write about what I'm learning and my research process through "weekend-squashed" experiments that help me build intuition quickly. I also maintain a <a href="{{'https://docs.google.com/document/d/1bM6sKwje_A-FVEo84fZOnCDJFoX2xcOam7xN7wPI5uU/edit?tab=t.0'}}">living research ideas document</a>, where I collect questions I think about investigating.
    </p>
    <p>
      Outside of research, I enjoy reading, running, and watching murder mystery and crime series. I'm fascinated by intelligence organizations and how they operate, though I do not support or endorse many of their actions. You can learn more about me through my <a href="{{ '/scrapbook/' | relative_url }}">scrapbook</a>.
    </p>
    <p>
      Feel free to say hi, send feedback on my work, suggest research ideas or tiny experiments, or reach out about collaboration. 
    </p>

  </div>
</section>
