---
layout: default
title: Painterly Texture
---

<div class="one-column" markdown="1">

# Painterly Texture

As the Art Director for *A Gentlemen's Dispute*, a party game featuring stylized gentlemanly characters, I drew heavy inspiration from classical oil paintings—an aesthetic closely tied to portraiture and refined society. To bring this look to life across our 3D assets, I used **Substance Designer** to apply a painterly filter to each texture map. This provided a strong visual foundation, which I then refined with hand-painted touch-ups in **Blender**.

![Painterly Texture before after comparison](/assets/images/AGD/PainterlyShader_2.png)

![Painterly Texture features](/assets/images/AGD/PainterlyShader_3.png)

![Painterly Texture Substance Designer graph](/assets/images/AGD/PainterlyShader_sdgraph.png)

To maintain consistency between tools, I created a custom **Shader Graph** shader in **Unity** that uses a standard diffuse lighting calculation to separate the model into light, mid, and dark regions. This gave me intuitive, real-time control over color grading and shading directly in-engine, making it easy to fine-tune the painterly look and keep visuals cohesive across platforms.

![Painterly Texture Unity shader graph](/assets/images/AGD/PainterlyShader_shadergraph.png)

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/7oLSEMuMVPs?autoplay=1&mute=1&loop=1&playlist=7oLSEMuMVPs&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

</div>