---
layout: default
title: AutoStroke
---

<div class="one-column" markdown="1">

# AutoStroke

**AutoStroke** is a Blender add-on that turns any mesh into a painterly, flat-per-stroke texture in a few seconds, and keeps the result updating live in the viewport while you adjust it. It continues the painterly texture pipeline I built for *[A Gentlemen's Dispute](/projects/agd/painterly-texture.html)*, rebuilt to remove that pipeline's biggest production blocker.

Blender 5.0+ · [Source on GitHub](https://github.com/JSS6088/AutoStroke)

<div class="video-container no-ui">
  <video autoplay muted loop playsinline preload="metadata"
         poster="/assets/videos/AutoStroke/AutoStroke_Showcase.jpg">
    <source src="/assets/videos/AutoStroke/AutoStroke_Showcase.mp4" type="video/mp4">
  </video>
</div>

<div class="figure-wide" markdown="1">

![Models textured with AutoStroke](/assets/images/AutoStroke/AutoStroke_combo.jpg)
*Detail survives the bake, and a model's own texture maps come through painterly*

</div>

## The blocker it removes

When developing *A Gentlemen's Dispute*, I built a painterly filter tool in Substance Designer that painted over baked object-space normal maps. The [full v1 story is on the Painterly Texture page](/projects/agd/painterly-texture.html).

Using it through production showed three limits:

- **It needed a paid license.** Substance Designer is not free, which puts the pipeline out of reach of anyone on the team without a seat.
- **It carried redundant steps.** Getting one asset textured meant a round trip out of Blender and back.
- **The visuals could go further.** The filter worked on the flattened UV layout, so every seam in that layout showed up as a seam in the paint.

AutoStroke runs in vanilla Blender, lets artists look-dev in the viewport, and places strokes on the surface itself, so the seams are gone.

</div>

<div class="two-column" markdown="1">

![Without AutoStroke](/assets/images/AutoStroke/AutoStroke_painterly_off.png)
*Source mesh*

![With AutoStroke](/assets/images/AutoStroke/AutoStroke_painterly_on.png)
*Same mesh, one bake — no hand painting*

</div>

<div class="one-column" markdown="1">

## How it works

### Placing strokes

In order to eliminate UV seams, I need to place strokes directly on the object's surface in world space. My first attempt used **geometry nodes** to randomly place points on the model. I hit a wall immediately: placing random strokes means small faces might not get a single stroke, thus losing details. Besides, there was no flexible way to tie a stroke's size to the face it sat on, so strokes came out too large on small faces and too sparse on large ones.

The fix was to give **every face of the mesh a stroke**, with its size driven by that face's area. This makes sure even the smallest face gets at least one stroke. I then extended it to place multiple strokes on bigger faces, inspired from [Matt Pharr's articles on sampling points on triangles](https://pharr.org/matt/blog/2019/02/27/triangle-sampling-1).

### Sampling strokes

Each stroke is turned to **run along the direction the surface bends least**, so strokes follow the form of the model and are least likely to obscure its details.
The strokes are then ordered by its size, when multiple strokes are overlapping one pixel, the smallest one is picked. This ensures smaller strokes never gets covered by large ones.

### Output

The main output is a painterly normal map, just like the old v1 version. It interacts with lighting the same way, and the strokes stay anchored to the model.

The bake also writes a second map recording which stroke painted each pixel. Feed any ordinary, non-painterly texture through it and that texture comes out painterly too, by redirecting where each pixel reads from in the source image.

The full algorithm — the sampling, how each pixel resolves to a stroke, and the optimisation work — is written up in [ALGORITHM.md](https://github.com/JSS6088/AutoStroke/blob/main/ALGORITHM.md) in the repo.

## Live preview

Judging a painterly look is a visual decision that works best if artists can see results in real-time. Therefore, I decided to include a Live preview feature that shows the result immediately in the viewport, with no bake and no waiting.

## Brush presets

The same model and the same stroke count, with three different brush sets. A set is just a folder of stroke images, so the look is swappable without touching the tool.

</div>

<div class="three-column" markdown="1">

![Rough brush](/assets/images/AutoStroke/AutoStroke_brush_rough.jpg)
*Rough*

![Standard brush](/assets/images/AutoStroke/AutoStroke_brush_standard.jpg)
*Standard*

![Thin brush](/assets/images/AutoStroke/AutoStroke_brush_thin.jpg)
*Thin*

</div>

<div class="one-column" markdown="1">

## Using it in Blender

The panel exposes stroke count, brush size, rotation and tonal variation. The artist tweaks the parameter and sees the result in real-time. Once they are satisfied, they hit BAKE, which takes only a few seconds at full resolution.

</div>

<div class="one-column" markdown="1">

<div class="video-container">
  <video controls muted playsinline preload="metadata"
         poster="/assets/videos/AutoStroke/AutoStroke_Usage.jpg">
    <source src="/assets/videos/AutoStroke/AutoStroke_Usage.mp4" type="video/mp4">
  </video>
</div>

</div>

<div class="one-column" markdown="1">


## Future Directions

This tool is distributed to an artist working on new maps for *A Gentlemen's Dispute*, and I am actively taking his feedback to improve its flexibility and usability.

Another caveat is with *sampling* indirection maps. It only works with **nearest** sampling: blending two neighbouring values averages two different redirections and lands on an unrelated part of the source image, which shows up as wrong pixels along stroke edges. Nearest avoids that, but it gives up smooth filtering, because every point inside a pixel then reads from the same spot. Getting both — clean edges and smooth filtering — is the next thing I want to solve.

## Citations

AutoStroke was built with AI assistance. I set the direction and made the calls; the implementation and the API work were delegated. The real gain in AI-assisted programming was being able to test a design idea without first reading the Blender Python API end to end. It significantly reduced my bottleneck in debugging and let my quickly put my ideas in front of a mesh and make judgements.

I modeled some of the example models, with a few exceptions. The sphinx is made by my friend Victor. [Sculpture “Bust of Róża Loewenfeld”](https://sketchfab.com/3d-models/sculpture-bust-of-roza-loewenfeld-fc6e731a0131471ba8e45511c7ea9996)
, [Dead Wood 2](https://sketchfab.com/3d-models/cc0-dead-wood-2-898e076697144cffbbf17011bf2e3ac2)
, [Suzzane](https://commons.wikimedia.org/wiki/File:Suzanne.stl) are public domain models.




</div>
