---
layout: default
title: Progressive Photon Mapping
---

<div class="one-column" markdown="1">

## Progressive Photon Mapping

As the final project of Physics-Based rendering course from Carngie Mellon University, I decided to implement a rendering algorithm called **Progressive Photon Mapping** to improve the quality of water rendering. Everything is implemented in **C++**.

Rendering water is difficult because of its special light path that traditional path tracing cannot capture. The pipeline captures nuanced lighting effects such as caustics and indirect lighting and showcases improvement in rendering quality compared to path tracing without sacrificing performance.

![Progressive Photon Mapping](/assets/images/PPM/PPM_water_PPM_100.png)
*Render result using progressive photon mapping*

## Breakdown

I first implemented the normal, non-progressive version of photon mapping as a head start.

### Photon emission, tracing and storage

The algorithm starts by **shooting photons onto the scene** in from the light source. Each time the photon hits a surface I record its hit location, incoming direction, and current photon power. After the photon power diminishes under a certain extent, I terminate the process. One caveat is that we do not store any photon on specular surfaces because during rendering, the probability of having a matching photon from a specific direction is very low, so the photons do not contribute to anything during rendering phase.

![Visualizing photons](/assets/images/PPM/PPM_photons100k_bright.png)
*Photons visualized, the scene has a red light on the left and blue light on the right*

### Photon power(radiance) estimate 

Afterwards, we start the rendering process just like normal path tracing. We shoot a ray from the camera until it hits a non-specular surface, we then **run an estimate of power(radiance)** at the hit position according to nearby photons. To get nearby photons, I store photons in a **kd-tree** to achieve logrithmatic speed during searching phase.
To improve the quality, I only did power estimate for **indirect lighting** and used **Multi-Important Sampling** for **direct lighting**.

![Rendering phase](/assets/images/PPM/PPM_toyMISr0.01.png)
*Renderings from photon mapping*

### Progressive Photon Mapping

Currently, the quality of rendering depends on the number of photons, which puts a challenge on memory consumption. On the other hand, progressive photon mapping renders independent image with **decreasing photon search radius**, then produces the final image as a **running average**. Therefore, it can produce high quality renders without putting any bound on number of stored photons.

</div>

<div class="two-column" markdown="1">

![Path tracing](/assets/images/PPM/PPM_water_MIS.png)
*Traditional path tracing rendering*

![PPM](/assets/images/PPM/PPM_water_PPM_100.png)
*Progressive photon mapping after 100 iterations*

</div>