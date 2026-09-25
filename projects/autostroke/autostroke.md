---
layout: default
title: AutoStroke
---

<div class="one-column" markdown="1">

# AutoStroke

**AutoStroke** is a Blender add-on that turns any mesh into a painterly, flat-per-stroke texture in a few seconds, and keeps the result updating live in the viewport while you adjust it. It continues the painterly texture pipeline I built for *[A Gentlemen's Dispute](/projects/agd/painterly-texture.html)*, rebuilt to remove that pipeline's biggest production blocker.

*v0.10.9 · Blender 5.0+ · GPL-3.0 · [Source on GitHub](https://github.com/JSS6088/AutoStroke) · Last updated September 2026*

<!-- TODO(media): assets/videos/AutoStroke/AutoStroke_Showcase.mp4 + AutoStroke_Showcase.jpg — uncomment the block below once encoded -->
<!--
<div class="video-container no-ui">
  <video autoplay muted loop playsinline preload="metadata"
         poster="/assets/videos/AutoStroke/AutoStroke_Showcase.jpg">
    <source src="/assets/videos/AutoStroke/AutoStroke_Showcase.mp4" type="video/mp4">
  </video>
</div>
-->

## The blocker it removes

When developing *A Gentlemen's Dispute*, I built a painterly filter tool in Substance Designer that painted over baked object-space normal maps. The [full v1 story is on the Painterly Texture page](/projects/agd/painterly-texture.html).

Using it through production showed three limits:

- **It needed a paid license.** Substance Designer is not free, which puts the pipeline out of reach of anyone on the team without a seat.
- **It carried redundant steps.** Getting one asset textured meant a round trip out of Blender and back.
- **The visuals could go further.** The filter worked on the flattened UV layout, so every seam in that layout showed up as a seam in the paint.

AutoStroke runs in vanilla Blender, lets artists look-dev in the viewport, and places strokes on the surface itself, so the seams are gone.

</div>

<!-- TODO(media): assets/images/AutoStroke/AutoStroke_painterly_off.png and _on.png — uncomment the block below once rendered -->
<!--
<div class="two-column" markdown="1">

![Without AutoStroke](/assets/images/AutoStroke/AutoStroke_painterly_off.png)
*Source mesh, flat shading*

![With AutoStroke](/assets/images/AutoStroke/AutoStroke_painterly_on.png)
*Same mesh, one bake — no hand painting*

</div>
-->

<div class="one-column" markdown="1">

## How it works

### Placing strokes

In order to eliminate UV seams, I need to place strokes directly on the object's surface in world space. My first attempt used **geometry nodes** to randomly place points on the model. I hit a wall immediately: placing random strokes means small faces might not get a single stroke, thus losing details. Besides, there was no flexible way to tie a stroke's size to the face it sat on, so strokes came out too large on small faces and too sparse on large ones.

The fix was to give every face of the mesh a stroke, with its size driven by that face's area. This makes sure even the smallest face gets at least one stroke. I then extended it to place multiple strokes on bigger faces, inspired from [Matt Pharr's articles on sampling points on triangles](https://pharr.org/matt/blog/2019/02/27/triangle-sampling-1).

### Sampling strokes

Each stroke is turned to run along the direction the surface bends least, so strokes follow the form of the model and are least likely to obscure its details.
The strokes are then ordered by its size, when multiple strokes are overlapping one pixel, the smallest one is picked. This ensures smaller strokes never gets covered by large ones.

### Output

The main output is a painterly normal map, just like the old v1 version. It interacts with lighting the same way, and the strokes stay anchored to the model.

The bake also writes a second map recording which stroke painted each pixel. Feed any ordinary, non-painterly texture through it and that texture comes out painterly too, by redirecting where each pixel reads from in the source image.

The full algorithm — the sampling, how each pixel resolves to a stroke, and the optimisation work — is written up in [ALGORITHM.md](https://github.com/JSS6088/AutoStroke/blob/main/ALGORITHM.md) in the repo.

## Live preview

Judging a painterly look is a visual decision that works best if artists can see results in real-time. Therefore, I decided to include a Live preview feature that shows the result immediately in the viewport, with no bake and no waiting.

The preview is a separate, faster path that only has to *look* right. The bake stays the one thing that produces the final textures, so the two can never disagree in a way that ships.

## Using it in Blender

The panel exposes stroke count, brush size, rotation and tonal variation. The artist tweaks the parameter and sees the result in real-time. Once they are satisfied, they hit BAKE, which takes only a few seconds at full resolution.

</div>

<!-- TODO(media): assets/images/AutoStroke/AutoStroke_panel.png — uncomment the block below once captured -->
<!--
<div class="figure-portrait" markdown="1">

![AutoStroke panel](/assets/images/AutoStroke/AutoStroke_panel.png)
*The add-on panel, with live preview on*

</div>
-->

<div class="one-column" markdown="1">

### Walkthrough

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/2QAXQjJjKY4?autoplay=0&controls=1&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

## How it was built

AutoStroke was built with AI assistance. I set the direction and made the calls; the implementation and the API work were delegated. The real gain was being able to test a design idea without first reading the Blender Python API end to end. The bottleneck on a tool like this was never typing — it was how fast an idea could be put in front of a mesh and judged.

## Future Directions

This tool is distributed to an artist working on new maps for *A Gentlemen's Dispute*, and I am actively taking his feedback to improve its flexibility and usability.

Another caveat is with *sampling* indirection maps. It only works with **nearest** sampling: blending two neighbouring values averages two different redirections and lands on an unrelated part of the source image, which shows up as wrong pixels along stroke edges. Nearest avoids that, but it gives up smooth filtering, because every point inside a pixel then reads from the same spot. Getting both — clean edges and smooth filtering — is the next thing I want to solve.

</div>
