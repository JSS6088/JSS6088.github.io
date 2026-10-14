---
layout: default
title: VALORANT Mobile
---

<!--
  Confidentiality rules for this page, agreed with the user:
  - Summit is the only map or feature named. It is publicly announced.
  - Officially released art only. No in-engine captures, including the before/after
    comparison of the aurora optimisation.
  - No internal tool names, project codenames, or unreleased feature names.
  - No phrasing that advertises hidden work ("other unannounced work", "more I can't
    discuss"). A flat list of what was worked on is fine; a teaser is not.
  - The glitter R&D is cleared by the user's mentor, which is why it is named here and
    cross-linked to the Starry Bottle page.
-->

<div class="one-column" markdown="1">

# VALORANT Mobile

During my internship at **Tencent** I adapted VALORANT weapon skins and map assets from their PC versions to mobile GPU budgets — cutting shader ALU cost, texture sampling and texture memory (ASTC) while keeping the assets looking like themselves.

## Aurora shader optimization

The aurora on **Summit** was one of those assets. I reduced its texture samples and arithmetic across both of its shaders, without changing how it looks in game.

Measured in the **Mali Offline Compiler**:

- Fragment shader: **110 → 78** ALU instructions
- Vertex shader: **123 → 66** ALU instructions

On mobile, that is the difference between an effect being affordable on the devices the game has to run on and being cut from them.

![Summit](/assets/images/Valorant/Valorant_summit.jpg)
*Summit, released as 天枢云阙 on the Chinese server. Official art, from VALORANT's [map introduction](https://www.bilibili.com/video/BV1ks7h6MEYp/).*

## Also at Tencent

- Adapted a **RenderDoc-based shader-analysis tool** for production use, adding a pitfall ruleset that steers its AI output toward correct results. Analysing a shader went from hours to about **15 minutes**.
- Built an **AI tool that auto-tags several hundred materials** for a material library, so artists can find and reuse what already exists instead of asking a technical artist to rebuild it.
- Prototyped a **glitter material in Unreal Engine**, recreating view-dependent sparkle with parallax-layered shading — written up in full as *[Starry Bottle](/projects/starry-bottle/starry-bottle.html)*.

</div>
