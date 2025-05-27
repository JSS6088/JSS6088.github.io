---
layout: default
title: Foliage Shader
---

<div class="one-column" markdown="1">

# Foliage Shader

Some maps in our game _A Gentlemen's Dispute_ include foliage made from leaf and branch cards. However, simply placing them statically in the scene made them look flat and unnatural. To bring the foliage to life, I created a custom foliage shader using **Shader Graph** that animates the leaves with simple noise-based motion.

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/dvNftNRHNLo?autoplay=1&mute=1&loop=1&playlist=dvNftNRHNLo&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

## Breakdown

The shader exposes two key parameters: **Frequency**, which controls how fast the noise moves (i.e., how often the leaves sway), and **Strength**, which determines how much the leaves move. Higher values represent fiercer wind, resulting in faster and more intense leaf movement. I also added a logic node that pins vertices below ground level, ensuring the base of the foliage remains static — especially useful for grass.

![Foliage Shader Unity shader graph](/assets/images/AGD/FoliageShader_shadergraph.png)

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/zTcjQ25N0YM?mute=1&loop=1&playlist=zTcjQ25N0YM&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

</div>
