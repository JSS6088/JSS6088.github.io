---
layout: default
title: Blossom Shanghai
---

<div class="one-column" markdown="1">

# Blossom Shanghai

This is a semester-long group project on recreating a street in Shanghai called Yellow River Road, which used to be the most popular food street back in the 90s. It started with a Chinese television show, "Blossom Shanghai," which tells the street's past glory. Thus, we want to recreate the street to express a sense of nostalgia. Since our group does not have character artists, we try to express this feeling just through the environment.

I am responsible for creating **procedural materials** using **Substance Designer** and **procedural buildings** in the background using **Houdini**. I am really glad everything is put together, and I have special thanks to my amazing teammates Lisa, Iris, Airan, and Kimi. It is impossible to create such a large and detailed scene without their effort. It gave me insights on making environment art and taught me tricks to make high-quality scenes efficiently.

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/HhM6zRdljp4?autoplay=1&mute=1&loop=1&playlist=HhM6zRdljp4&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

## Breakdown

### Procedural building

The background buildings are procedurally generated with varied facades. I am mainly inspired from this building at Yellow River Road. It has many features that represent a typical old apartment in China: brick wall, colorful curtains, extended clothes hanging rod, and exposed AC units. The chaotic layout breaks the gridded structure of the apartment.

![Yangtze River Apartment](/assets/images/BlossomShanghai/BlossomShanghai_YangtzeRiverApartment.png)

The procedural building tool from [Project Titan](https://www.sidefx.com/titan/) gave me a good startup for generating buildings. However, it only generates buildings from different modular walls, with each modular piece created manually. To showcase the chaotic layout of elements on each wall, I took a step further to generate elements such like pipes, hanging rods, AC units, and awnings onto each wall using **Houdini**.

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/a2ojCgPSwP8?autoplay=1&mute=1&loop=1&playlist=a2ojCgPSwP8&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

I also noticed windows in the reference building are pretty similar, which meant they can also be procedurally created. Therefore, I also procedurally modelled the window in **Houdini** with tweakable parameters to control its shape. By now, the building can be fully procedurally generated.

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/CPWXnoXOW28?autoplay=1&mute=1&loop=1&playlist=CPWXnoXOW28&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

After putting everything together, I am able to generate various buildings easily from an input mass. Unfortunately, the input models are not optimized enough so scattering a lot of elements cause large load time. This is definitely an area of improvement.

![Yangtze River Apartment](/assets/images/BlossomShanghai/BlossomShanghai_procedural.jpg)

It also supports simple curved geometry mass. To get the reference building, I first generated a base version from a  90 degree pie input mass, then added some manual touchups such as balconies and rims.

![Yangtze River Apartment](/assets/images/BlossomShanghai/BlossomShanghai_render.png)

### Procedural material

In addition, I also created a few procedural materials using **Substance Designer** for assets such as walls and floors.

![Procedural materials](/assets/images/BlossomShanghai/BlossomShanghai_material.png)

Substance Designer graph of concrete 

![Concrete Substance Designer Graph](/assets/images/BlossomShanghai/BlossomShanghai_sdgraph_1.png)

Substance Designer Graph of tile floor 

![Tile floor Substance Designer Graph](/assets/images/BlossomShanghai/BlossomShanghai_sdgraph_2.png)

</div>




