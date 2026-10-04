---
layout: default
title: Destruction Pipeline
---

<div class="one-column" markdown="1">

# Destruction Pipeline

*[A Gentlemen's Dispute](/projects/agd/a-gentlemens-dispute.html)* is a physics brawler, and breaking pieces is part of what makes the game fun. Therefore, each destructible asset is pre-fractured in Blender and imported to Unity.

However, the vanilla Blender fracture system has a couple issues that slows down the fracture workflow. To make artists life easier, I built two tools: a **Blender add-on** that post-processes the fracture, and a **Unity editor tool** that handles the materials on the way in.


## The problem

Blender ships a Cell Fracture add-on that shatters a model into pieces automatically. That part was never the issue. Everything *after* it was:

- Pieces arrive with machine-generated names and no hierarchy.
- The newly cut interior faces have no UVs, so the inside of a broken object renders wrong.
- Cell Fracture leaves tiny shards behind, and each one costs physics time for something nobody will ever see.
- In Unity, every piece shows up carrying its Blender material, waiting to be relinked one slot at a time.

Therefore, the artist has to manually
- set up the hierarchy for fractured pieces, the original model, and their parent.
- select and unwrap all interior faces, and assign them with a seperate material
- remove small fractured shards
- relink materials for exterior and interior faces for EACH fractured piece when its imported to Unity

For a single model, that is an annoyance. For a game where most of the set can be broken, it is a tax charged on every asset an artist makes — and it is the kind of work that quietly discourages people from making destructible props at all.

## The solution

### Fracture Piece Setup — Blender

One button, run on the fracture output. It renames and numbers the pieces and parents them under a single empty, so the whole asset travels to Unity as one Game Object. It unwraps the interior cut faces so the broken surfaces can take a texture, and tags those interior vertices as a group so a shader can treat inside and outside differently. It deletes shards below a size threshold, with a preview that selects what *would* go before anything is removed. And where a model had to be split apart to fracture cleanly, it stitches the intact version back into one mesh at the end.

<div class="video-container">
  <video controls muted playsinline preload="metadata"
         poster="/assets/videos/DestructionPipeline/DestructionPipeline_Blender.jpg">
    <source src="/assets/videos/DestructionPipeline/DestructionPipeline_Blender.mp4" type="video/mp4">
  </video>
</div>

### Batch Material Remap — Unity

Select the imported model, right-click, and a window lists every unique material used anywhere beneath it, each with a slot to drop the Unity replacement into. Apply writes the mapping onto the model's importer rather than onto the scene objects, so the swap survives every future reimport instead of being redone each time the asset changes.

<div class="video-container">
  <video controls muted playsinline preload="metadata"
         poster="/assets/videos/DestructionPipeline/DestructionPipeline_Unity.jpg">
    <source src="/assets/videos/DestructionPipeline/DestructionPipeline_Unity.mp4" type="video/mp4">
  </video>
</div>

## What it changed

Destruction stopped being a special case. An artist can fracture a prop and have it working in the engine without coming through me, and without a checklist of manual steps where any one of them can be forgotten.

That is the part I care about most. A tool that saves me an afternoon is worth an afternoon. A tool that lets everyone else do the thing they were avoiding is worth considerably more.

</div>
