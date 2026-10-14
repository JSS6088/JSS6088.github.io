---
layout: default
title: We're All In
---

<div class="one-column" markdown="1">

# We're All In

*[We're All In](https://store.steampowered.com/app/4183130/WERE_ALL_IN/)* is a Unity party and dice game on Steam, in the vein of Mario Party and Fall Guys, with a layer on top where players bet on each other. The art direction is **Toy World**: everything in it is the toy version of itself.

![In-game view of one of the maps](/assets/images/WereAllIn/WereAllIn_toyworld.jpg)
*In game. Wood-grain trees, a die for a building, a stack of chips for a tower.*

![A round of Paint The Tiles](/assets/images/WereAllIn/WereAllIn_gameplay.jpg)
*Paint The Tiles, one of the minigames.*

I was the **technical artist and art direction lead**, and the constraint that shaped every decision was time — the art went from nothing to release-ready **in a month**.

- **Material system.** Every surface reads as a real toy material: painted wood, glossy plastic, metal, felt. Each map then gets its own colour palette, chosen to evoke a particular feeling.
- **Toon shading.** A custom-adjusted toon shader tuned for readability, with ambient and reflection driven by a matcap rather than the skybox.
- **Vertex colour.** Used extensively for object colour and detail, which cut the number of material variants the game had to carry.
- **Procedural tooling.** A Blender Geometry Nodes setup for the chips, with colour masking and a per-copy random seed, so variations are generated rather than modelled by hand.

</div>
