---
layout: default
title: Painterly Texture
---

<div class="one-column" markdown="1">

# Painterly Texture

A pipeline for giving 3D assets a hand-painted, oil-painting look that holds up under dynamic lighting. The first version, built in Substance Designer, shipped with *A Gentlemen's Dispute*. The second version, **AutoStroke**, rebuilds it as a Blender add-on.

## AutoStroke (v2)

*v2 in active development · Last updated September 2026*

**AutoStroke** is a **Blender** add-on that generates painterly textures without Substance Designer, running entirely in vanilla Blender.

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/2QAXQjJjKY4?autoplay=0&controls=1&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

### Why I rebuilt it

The first version held up through production on *A Gentlemen's Dispute*, but using it exposed three limits:

- **It needed a license.** Substance Designer is paid software. AutoStroke runs in vanilla Blender with nothing else to install.
- **It had redundant steps.** The Substance workflow involved steps that didn't need to be there. AutoStroke removes them.
- **The visuals could go further.** Rebuilding the tool gave me room to push the quality of the strokes.

### What's next

- Improving the visual quality of the generated strokes
<!-- TODO: add 1–2 specific next steps, and update the "Last updated" date above when you change this section -->

## Where it started: v1 in *A Gentlemen's Dispute*

As the Art Director for *A Gentlemen's Dispute*, a party game featuring stylized gentlemanly characters, I drew heavy inspiration from classical oil paintings — an aesthetic closely tied to portraiture and refined society. However, in traditional paintings, lighting is static and baked into the image through painted color. In contrast, our game features dynamic lighting and animated characters, which makes preserving a consistent painterly look more challenging. Relying solely on diffuse texture maps wasn’t enough — the illusion would break as lighting conditions changed.

Fortunately, after some research, I came across a technique to keep 3D assets looking painterly under dynamic lighting. The key is to **bake an object-space normal map** of the model and then *paint over it* using the **normal map's base colors** to create a painterly-stylized normal map. This painted normal map retains the essential surface detail for lighting while giving it a handcrafted, artistic appearance. As a result, the asset maintains its painterly style even as lighting changes — blending traditional artistic sensibility with the demands of real-time rendering.

![Painterly Texture reference](/assets/images/AGD/PainterlyShader_ref.png)
*Painterly Texture reference - from [this video](https://www.youtube.com/watch?v=s8N00rjil_4&t=2s) at timestamp 7:00*

However, bringing this style to every 3D asset manually would be overwhelming — especially as a solo artist. Hand-painting textures for every model was not scalable. To automate this, I used **Substance Designer** to generate stroke patterns and apply a painterly filter across texture maps procedurally.

![Painterly Texture Substance Designer graph](/assets/images/AGD/PainterlyShader_sdgraph.png)

To achieve a more convincing painterly look, the tool generates stroke patterns with constraints that mimic the way humans naturally paint — following surface direction, form, and areas of visual emphasis.

![Painterly Texture features](/assets/images/AGD/PainterlyShader_3.png)

This tool gave each asset a stylized base layer, which I could then selectively refine by hand in **Blender** where needed. This workflow allowed for consistent, high-quality painterly visuals while keeping production efficient and manageable.

![Painterly Texture before after comparison](/assets/images/AGD/PainterlyShader_2.png)

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
