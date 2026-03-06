---
layout: default
title: PCG Gallery (WIP)
---

<div class="one-column" markdown="1">

# PCG Gallery (WIP)

As an opportunity to hone in my **Unreal Engine** and **Houdini** skills, I started to work on a tool for world artists to build a gallery space using **Unreal PCG Graph and Blueprint** The end goal is to have a infinite real-time generated gallery that resembles a liminal space when walking inside. 

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/A8gbQZqvLJ4?autoplay=1&mute=1&loop=1&playlist=A8gbQZqvLJ4&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

## Breakdown

Right now I have implemented a feature that allows paintings to generate automatically onto walls, saving a lot of unnecessary times for world artists to place paintings manually. 

### Different generation modes

The painting generator has three modes: **Random**,  **Grid**, and **Manual**. Each affects painting layout in its unique way.

</div>

<div class="three-column" markdown="1">

![Random Mode](/assets/images/PCGGallery/PCGGallery_random.png)
*The **random mode** scatters the painting onto the wall randomly using the **Surface Sampler** node*

![Grid Mode](/assets/images/PCGGallery/PCGGallery_grid.png)
*The **grid mode** produces a regular grid layout using the **Create Points Grid** node*

![Manual Mode](/assets/images/PCGGallery/PCGGallery_manual.png)
*The **manual mode** allows artists to provide a **custom painting layout** through reading a data table of points*

</div>

<div class="one-column" markdown="1">

### Artist-friendly parameters 

The painting generator offers various parameters for artists to control and refine the outlook of the painting layout.

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/eIxxAQjhv7s?mute=1&loop=1&playlist=eIxxAQjhv7s&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

</div>

<div class="one-column" markdown="1">

### Context aware generation

I added a couple checks to make sure the paintings spawned are in valid positions. 

I used the *Self pruning* node to **remove overlapping paintings**.

</div>

<div class="two-column" markdown="1">

![Without Self Pruning](/assets/images/PCGGallery/PCGGallery_selfprune_off.png)
*Without self pruning*

![With Self Pruning](/assets/images/PCGGallery/PCGGallery_selfprune_on.png)
*With self pruning*

</div>

<div class="one-column" markdown="1">

To **avoid spawning paintings partially outside the wall**, I calculated the distance of each point to the closest wall boundary and compared it with the width and height of the painting mesh. 

</div>

<div class="two-column" markdown="1">

![Without Edge Distance Check](/assets/images/PCGGallery/PCGGallery_edgedist_off.png)
*Without edge distance checks*

![With Edge Distance Check](/assets/images/PCGGallery/PCGGallery_edgedist_on.png)
*With edge distance checks*

</div>

<div class="one-column" markdown="1">

For grid mode, I added an option to **respect the aspect ratio** of the each cell. If each cell is stretched out horizontally, then it makes sense to spawn paintings with a landscape orientation.  

</div>

<div class="two-column" markdown="1">

![No Respect Aspect Ratio](/assets/images/PCGGallery/PCGGallery_aspectratio_off.png)
*Respect aspect ratio off*

![Respect Aspect Ratio](/assets/images/PCGGallery/PCGGallery_aspectratio_on.png)
*Respect aspect ratio on*

![No Respect Aspect Ratio Line](/assets/images/PCGGallery/PCGGallery_aspectratio_off_line.png)
*Underlying cell size, notice the middle two protraits dont fit the landscape dimension well*

![Respect Aspect Ratio Line](/assets/images/PCGGallery/PCGGallery_aspectratio_on_line.png)
*Respecting cell size allows selected painting to fill the cell better*

</div>

<div class="one-column" markdown="1">

I organized and added comments on the PCG graph to make it clean and easy to understand.

![PCG Graph Main Generation Logic](/assets/images/PCGGallery/PCGGallery_pcggraph.png)
*PCG Graph of the main generation logic*

</div>

<div class="one-column" markdown="1">

### Procedural painting frame

I also built a houdini data asset(HDA) in **Houdini** that generates a procedural painting frame for my PCG Painting generator tool. 

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/oFTGIPgIBp4?autoplay=1&mute=1&loop=1&playlist=oFTGIPgIBp4&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

![Painting HDA Graph](/assets/images/PCGGallery/PCGGallery_hda.png)
*Procedural painting graph in Houdini*

## Future Direction

Right now, the manual mode reads a data table of points that represents a layout, but it has a strict format and can be hard to understand for general artists. In the future, I plan to add **artist-friendly** ways to create a custom layout that can be later converted into a data table.

In addition, I want to write a script that automatically exports various FBX meshes from Houdini into Unreal Engine with their metadatas to **automate the procedural generation pipeline** even further.

</div>




