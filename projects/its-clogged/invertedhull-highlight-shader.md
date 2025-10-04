---
layout: default
title: Inverted Hull Highlight Shader
---

<div class="one-column" markdown="1">

## Inverted Hull Highlight Shader

In our game, players can select and interact objects. Therefore, I helped the programmer to create a highlight shader that draws the object's silhouette around the selected object. I chose to implement it using the inverted hull method because it can create really thick silhouettes. Besides, unlike screenspace outlines, I can selectively choose which objects to use the shader, which is perfect for highlighting an object.

<div class="video-container">
  <iframe  
    src="https://www.youtube.com/embed/DHwGmwOl8UQ?autoplay=1&mute=1&loop=1&controls=0&playlist=DHwGmwOl8UQ&playsinline=1"
    frameborder="0" 
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
   </iframe>
</div>

## Breakdown

In the vertex shader, I offset each vertex acccording to its vertex normal to expand the model. Then, I only render the backface of the expanded model to create an outline around the object.

```cpp
float4 newPos = v.vertex + 0.1f * v.normal * _Thickness / _Object_Scale;
```

However, this creates two issues. 
- The silhouette look disconnected on objects with sharp edges. 
- Some extra faces are visible other than the silhouette for complex models.

</div>

<div class="two-column" markdown="1">

![SharpNormalIssue](/assets/images/ItsClogged/OutlineInvertedHull_SharpNormalIssue.png)
*Issue 1: Discontinuous silhouette*

![InsideEdgeIssue](/assets/images/ItsClogged/OutlineInvertedHull_InsideEdgeIssue.png)
*Issue 2: Extra edges in front of object*

</div>

<div class="one-column" markdown="1">

### Continuous silhouette
To make the silhouette continuous, the vertex normal needs to be smooth. However, this requires artists to add fencing edges around each sharp edge. It adds unnecessary polycounts for the model and creates extra work for the artist. 

Therefore, I wrote a script that **automatically calculates the smoothed vertex normal** for each imported model, and stores the new normal inside each vertex. 

![SharpNormalFixed](/assets/images/ItsClogged/OutlineInvertedHull_SharpNormalFixed.png)
*Correct silhouette with smoothed normal*


### Only show silhouette
To make the highlight shader only show the object's silhouette, I need to tweak the render pipeline to make sure the original object is rendered on **on top of** the silhouette object.

I approached it by creating **two shader passes**. The first one renders the selected object mask on the stencil buffer so that areas inside the object has value 1 and areas outside the object has value 0.

![Inverted hull outline Stencil](/assets/images/ItsClogged/OutlineInvertedHull_stencil.png)
*Utilizing stencil buffer to mask out object interior*

The second shader pass checks the current stencil buffer, and only draws fragment on pixels with stencil value not equal to 1. Therefore, it will not draw anything inside the object, leaving only the silhouette visible. In addition, I let the shader always pass Z test to make sure the silhouette is drawn in front of everything.

![Inverted hull outline Render](/assets/images/ItsClogged/OutlineInvertedHull_render.png)
*Selected object with correct silhouette*

</div>


