---
layout: default
title: Home
---

<div class="one-column" markdown="1">

# Hi, I'm Jason!

I'm a technical artist working on shaders, tools and real-time rendering.

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/ggagEBtmy9Q?autoplay=0&mute=0&loop=1&playlist=ggagEBtmy9Q&controls=1&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

## Highlights

</div>

{% assign featured = site.data.projects | where: "featured", true %}
<div class="two-column">
{% for p in featured %}
  {% unless p.draft %}
  <p><a href="{{ p.url }}"><img src="{{ p.card }}" alt="{{ p.title }}" />
<em>{{ p.title }} - {{ p.blurb }}</em></a>
{% if p.tags %}<span class="tags">{% for t in p.tags %}<span class="tag">{{ t }}</span>{% endfor %}</span>{% endif %}</p>
  {% endunless %}
{% endfor %}
</div>

<div class="one-column" markdown="1">

Or browse everything by [Games](/games.html), [Tools](/tools.html), [Shaders](/shaders.html) and [Studies](/studies.html).

</div>
