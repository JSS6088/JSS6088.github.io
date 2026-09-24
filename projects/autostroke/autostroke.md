---
layout: default
title: AutoStroke
---

<div class="one-column" markdown="1">

# AutoStroke

**AutoStroke** is a Blender add-on that turns any mesh into a painterly, flat-per-stroke texture in a few seconds, and keeps the result updating live in the viewport while you adjust it. It continues the painterly texture pipeline I built for *[A Gentlemen's Dispute](/projects/agd/painterly-texture.html)*, rebuilt to remove that pipeline's biggest production blocker.

*v0.10.9 · Blender 5.0+ · GPL-3.0 · [Source on GitHub](https://github.com/JSS6088/AutoStroke) · Last updated September 2026*

<!-- TODO(media): assets/videos/AutoStroke/AutoStroke_LivePreview.mp4 + AutoStroke_LivePreview.jpg — uncomment the block below once encoded -->
<!--
<div class="video-container no-ui">
  <video autoplay muted loop playsinline preload="metadata"
         poster="/assets/videos/AutoStroke/AutoStroke_LivePreview.jpg">
    <source src="/assets/videos/AutoStroke/AutoStroke_LivePreview.mp4" type="video/mp4">
  </video>
</div>
-->

## Live preview

The viewport above is not showing a baked texture. Stroke parameters change and the shading follows immediately — no bake step, no progress bar.

The preview is a **second implementation** of the placement rules, and deliberately so. It runs the same search as the bake, but as a fragment shader that produces no texture at all: each fragment takes its own object-space position and normal, looks up its cell in a uniform 3D grid of strokes, loops that cell, and keeps the smallest stroke that passes. The baker stays the only thing that produces a map I would ship. The preview only has to *look* right, so a disagreement between the two is a preview bug, never a reason to change the baker.

Two decisions make it fast enough to survive a slider drag:

- **Each stroke is inserted into every cell its bounding box overlaps.** A fragment then reads only its own cell, because that cell already holds every stroke that could reach it. Querying a 3×3×3 neighbourhood instead would be 27× the shader work for the same answer.
- **The grid is built all at once rather than stroke by stroke.** Looping over strokes in Python cost 262 ms at 20,000 strokes — a cost a slider drag pays on every tick. The vectorised build is 34× faster and produces identical grids.

What makes this hold up as scenes get denser is that cell occupancy barely moves: **67, 71 and 79 strokes per cell** at 1,000, 4,000 and 20,000 strokes. Placing more strokes also makes each one smaller, so the shader's inner loop stays about the same length.

I also prototyped the bake itself on the GPU, at roughly 0.5 s against 3.3 s, and dropped it. It would have made shipped output depend on the graphics driver, and none of the exactness guarantees of the CPU path carry over to a shader.

## The blocker it removes

v1 was a Substance Designer filter that painted over baked object-space normal maps, and it shipped with *A Gentlemen's Dispute*. The [full v1 story is on the Painterly Texture page](/projects/agd/painterly-texture.html).

Using it through production showed three limits:

- **It needed a paid license.** Substance Designer is not free, which puts the pipeline out of reach of anyone on the team without a seat.
- **It carried redundant steps.** Getting one asset textured meant a round trip out of Blender and back.
- **The visuals could go further.** The filter's stroke behaviour was as good as I could make it inside a node graph built for materials, not for strokes.

AutoStroke runs in vanilla Blender with nothing else installed, and the round trip collapses into a slider in the viewport.

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

## Breakdown

### What the bake produces

The bake writes an **indirection map**: for every texel, a UV pointer to the representative point of the stroke that paints it (R, G), plus that stroke's tonal random (B). A companion map stores each stroke's normal. The shader reads the pointer, then reads the colour map at that UV, so every texel belonging to one stroke returns the same flat colour — that is where the flat-per-stroke look comes from.

The ownership rule matters more than it sounds. A texel is won by the **first stroke in candidate order whose brush shape covers it and whose normal agrees with the surface there** — not by the nearest stroke. Nearest would let a stroke bleed across a hard edge; requiring the normals to agree keeps each stroke on its own face of the form.

### Where strokes go

This part went through a redesign. I first built placement in **geometry nodes** and hit a wall: there was no flexible way to relate a stroke's size to the area of the face it sat on, so strokes came out too large on small faces and too sparse on large ones. The fix was to change the unit — sample one stroke per face, and drive its size from that face's area. Sampling several points within a face came later, working from [Matt Pharr's articles on sampling points on triangles](https://pharr.org/matt/blog/2019/02/27/triangle-sampling-1).

Three properties came out of that:

- **Counts are dithered, not rounded.** Each face gets a deterministic per-face offset before the count is floored. Without it, every face of a near-uniform mesh crosses the same threshold at the same moment, and the whole model jumps from one stroke per face to two at once.
- **The artist sets a total, not a density.** The density that hits a requested stroke count is solved by bisection, because the per-face minimum and maximum bend the relationship between target and surface area — on a low-poly mesh a few large faces hit the cap and stop contributing, so dividing target by area quietly under-delivers.
- **Positions are nested.** Raising the count adds strokes without moving the ones already placed, so dragging the slider reads as adding detail rather than reshuffling the asset. The same subdivision is shape-aware: along a thin sliver it yields 90 usable positions where a naive four-way split yields 16.

### Which way strokes point

This one was my idea: strokes should run along the direction in which the surface normal changes least, so they follow the form instead of a UV axis.

Finding that direction starts with each stroke's 14 nearest neighbouring strokes. Neighbours whose normal disagrees by more than 60° are thrown out first — nearest-by-distance reaches straight *through* a thin feature, and a normal from the far side of a wall corrupts the fit. Fitting the shape operator to what survives gives the two principal directions, and the one with the smaller curvature is the direction the normal changes least along. A smoothing pass then turns those per-stroke answers into a coherent field, using a double-angle representation because a stroke and its 180° flip are the same stroke — averaged as raw vectors, two neighbours that agree would cancel to zero.

### Making the bake fast

Measured on a 1024 bake, the search came down in three steps:

- Morton ordering plus sphere rejection — 6.98 s
- clipping each stroke's footprint to the box it actually paints — about 4.4 s
- box-versus-box rejection — **2.93 s**

Whole bake, **7.41 s → 3.31 s**.

The more useful result is where the remaining time goes: **numpy dispatch, not arithmetic**. An operation on 1,024 elements costs about 0.34 µs, of which only 0.33 ns per element is real work, so roughly 2.7M calls is about 0.9 s — a third of the resolve. That is the number that decides which optimizations are worth doing at all.

## What I chose not to do

Three I measured and turned down:

- **Short-circuiting the funnel.** 1.10×, byte-identical output. It removes arithmetic, and arithmetic was not the cost.
- **True oriented-box testing against each tile.** 40% fewer candidate pairs, but the test itself cost 2.52 s against 0.09 s — about an 8× net loss, because it trades two numpy operations per tile for roughly sixty.
- **Cheaper mask sampling.** 1.4× faster, but it changed the output. The mask is only 15% of the resolve now, and the output is the product.

## Using it in Blender

The panel exposes stroke count, brush size and tonal variation. Count is the primary dial rather than a density figure, because a count is the number an artist actually has an opinion about — solving for the density that achieves it is the tool's job, not theirs.

</div>

<!-- TODO(media): assets/images/AutoStroke/AutoStroke_panel.png — uncomment the block below once captured -->
<!--
<div class="figure-portrait" markdown="1">

![AutoStroke panel](/assets/images/AutoStroke/AutoStroke_panel.png)
*The add-on panel, with live preview on*

</div>
-->

<div class="one-column" markdown="1">

## Walkthrough

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/2QAXQjJjKY4?autoplay=0&controls=1&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

## How it was built

AutoStroke was built with AI assistance. I set the direction and made the calls; the implementation and the API work were delegated.

What that meant in practice: the geometry-nodes dead end and the switch to per-face sampling were mine, as was the rule that strokes follow the least-curved direction, the decision that the artist sets a stroke count rather than a density, the decision that the live preview must be a separate implementation that only has to look right, and the decision to drop the GPU bake because shipped output would have depended on the driver. The texel-resolution algorithm was proposed by AI; what I contributed there was the candidate-order rule it uses, and which optimizations were kept or rejected.

The real gain was being able to test a design idea without first reading the Blender Python API end to end. The bottleneck on a tool like this was never typing — it was how quickly an idea could be put in front of a mesh and judged.

I can explain what each step does and why it is there; I did not derive every equation behind it.

## Future Direction

The next real speedup is a per-texel gather: bin the strokes spatially and test each texel only against its own cell, Θ(M·k) instead of Θ(M·S). Two things block it. Stroke radii span 10–100× on real meshes, which is the classic failure case for a uniform grid. And candidate order resists vectorisation, because each texel takes the first stroke that passes — a variable-length loop that terminates early.

It is not worth building yet. The trigger is 8K maps or around 100,000 strokes, and nothing I am producing today comes close.

</div>
