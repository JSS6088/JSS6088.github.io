---
layout: default
title: Water Shader
---

<div class="one-column" markdown="1">

# Water Shader

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/zncquhNhr8U?autoplay=1&mute=1&loop=1&playlist=zncquhNhr8U&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

The water shader automatically applies foam to any intersecting geometry. 

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/nZqe86BLFC0?autoplay=1&mute=1&loop=1&playlist=nZqe86BLFC0&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

## Breakdown

### Color each zone

Water has four components: shallow zone, deep zone, fog zone, caustic zone. The color of each zone are exposed as parameters for easy artistic control.

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/Qa3hhHixHrE?mute=1&loop=1&playlist=Qa3hhHixHrE&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

### Define each zone 

The **caustic zone** is defined through a **worley noise**. 

The **shallow and deep zone** is defined through a depth mask calculated from **height difference** between water surface and ocean floor. This is calculated through subtracting the _w_ component of camera position with ... and taking its _y_ component. A **_smoothstep_ function** is finally applied to threshold the value.

**Foam** is a byproduct of this depth mask with a **_step_ function** to threshold the value. It is combined with the caustic zone because I dont think these two need to have different colors.

The **fog zone** is defined similarly using camera _w_ component to get the depth of the scene.

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/In52MVVBxYQ?mute=1&loop=1&playlist=In52MVVBxYQ&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

### Animate each zone 

The caustic zone is animated through distorting the UV space of the worley noise with **a simplex noise** in fragment shader.

The ocean plane geometry is animated using **simplex noise** to distort the vertex position in vertex shader.

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/u2hBoh7S5-I?mute=1&loop=1&playlist=u2hBoh7S5-I&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

As a learning opportunity, I wrote the simplex and worley noise by myself in **HLSL**.

</div>