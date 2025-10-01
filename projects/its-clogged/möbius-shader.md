---
layout: default
title: Möbius Shader
---

<div class="one-column" markdown="1">

# Möbius Shader

In _It’s Clogged_, we wanted players to feel cozy while cleaning the room. To support this mood, I explored a cartoony, illustrated look inspired by the Möbius art style—simple flat colors at a distance, enriched with hand-drawn hatching details up close.

![Möbius Render](/assets/images/ItsClogged/Mobius_render.png)
*Scene using the Möbius shader, models by our artist Jerry*

## Breakdown

### Hatch pattern

The most defining element of the Möbius style is the **hand-drawn hatching**. From studying Möbius’s illustrations, I noticed that his hatching lines not only look **sketched**, but also **wrap naturally around the forms**. To recreate this, I built a shader that samples a hatch texture and splits it into horizontal, vertical, and diagonal line patterns. These patterns are then layered by brightness to mimic the look of cross-hatched ink.

```cpp
// Split hatch texture into horizontal, vertical, and diagonal line directions
float flatLine = lineColor.r;
float vertLine = lineColor.g;
float diagLine = lineColor.b;

// Get brightness region
float brightness = shadowAtten? Luminance(lighting): 0.0; 
float darkRegion = step(brightness, 0.15) * flatLine;
float grayRegion = step(brightness, 0.3) * vertLine;
float brightRegion = step(brightness, 0.6) * diagLine;
float hatchMask = max(max(darkRegion, grayRegion), brightRegion);
```

Initially, I tried using mesh UVs to make the hatching follow the surface, but this led to inconsistent orientations and scales across different objects. Assigning unique parameters per material would have been inefficient and hard to manage. Instead, I implemented triplanar mapping, which automatically projects the hatching with consistent texel density and orientation across all meshes. This approach not only preserves the illustrated style, but also removes the need for artists to unwrap meshes when texture maps aren’t required—streamlining the workflow while keeping the visuals cohesive.

```cpp
// Apply hatch with triplanar mapping
float3 position = i.positionWS - unity_ObjectToWorld._m03_m13_m23; // make WS position translation invariant
float3 lineColorX = SAMPLE_TEXTURE2D(_HatchesTex, sampler_HatchesTex, _HatchesTex_ST.xy * position.yz + displace + _HatchesTex_ST.zw).xyz;
float3 lineColorY = SAMPLE_TEXTURE2D(_HatchesTex, sampler_HatchesTex, _HatchesTex_ST.xy * position.xz + displace + _HatchesTex_ST.zw).xyz;
float3 lineColorZ = SAMPLE_TEXTURE2D(_HatchesTex, sampler_HatchesTex, _HatchesTex_ST.xy * position.xy + displace + _HatchesTex_ST.zw).xyz;

float3 normalWS = length(normal)? normal: float3(1,0,0);
float3 blend = pow(abs(normalWS), 8);
blend = smoothstep(0, 0.8, blend / (blend.x + blend.y + blend.z)); //reduce hatch fading
float3 lineColor = saturate(blend.x * lineColorX + blend.y * lineColorY + blend.z * lineColorZ);
```
</div>

<div class="two-column" markdown="1">

![Mobius Render](/assets/images/ItsClogged/Mobius_UVHatch.png)
*Hatching with UV hatch, notice the inconsistent orientation and scale*

![Mobius Render](/assets/images/ItsClogged/Mobius_TriplanarHatch.png)
*Hatching with Triplanar project, ensures consistent hatching pattern with no UV needed*

</div>

<div class="one-column" markdown="1">

<div class="video-container">
  <iframe  
    src="https://www.youtube.com/embed/vLHbuWhXGiM?autoplay=1&mute=1&loop=1&controls=0&playlist=vLHbuWhXGiM&playsinline=1"
    frameborder="0" 
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
   </iframe>
</div>
 
</div>

<div class="one-column" markdown="1">

### Color gradient

Another hallmark of Möbius’s art is the use of **bold color gradients** across objects. A simple implementation would be to lerp colors based on object-space coordinates and dimensions, but this breaks when the same material is applied to meshes of different sizes. To solve this, I stored the gradient information directly in the vertex colors and interpolated those instead. To streamline the workflow, I also built a C# automation script that calculates the gradient values for each vertex and writes them to the mesh automatically. The script runs whenever a mesh is updated or imported, so artists can apply gradients consistently across assets without manual setup.

</div>

<div class="two-column" markdown="1">

![Mobius Render](/assets/images/ItsClogged/Mobius_GradientBad.png)
*Applying gradient through predefined object dimensions makes it diffcult to apply the same material on different objects*

![Mobius Render](/assets/images/ItsClogged/Mobius_GradientGood.png)
*Applying gradient through vertex color fixes the issue*

</div>

<div class="one-column" markdown="1">

The artist can easily change the gradient color and direction.

<div class="video-container">
  <iframe  
    src="https://www.youtube.com/embed/RlaCnG2udGI?autoplay=0&mute=1&loop=1&controls=0&playlist=RlaCnG2udGI&playsinline=1"
    frameborder="0" 
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
   </iframe>
</div>

</div>

<div class="one-column" markdown="1">

### Texture maps

To give artists more flexibility, I added support for traditional texture maps alongside the stylized hatching. The shader can sample a basecolor and normal map for color and lighting, while offering three blend modes that control how textures mix with gradient colors. This lets artists freely choose between a purely illustrated look, a more detailed textured style, or anything in between.

```cpp
// Mix color using shader variant
float4 output = gradient;
#if defined(_MIXMODE_MULTIPLY)
    output = lerp(basecolor, basecolor * gradient, _BlendStrength);
#elif defined(_MIXMODE_SCREEN)
    output = lerp(basecolor, 1 - (1 - basecolor) * (1 - gradient), _BlendStrength);
#else
    output = lerp(basecolor, gradient, _BlendStrength);
#endif
```
Showcasing texture maps

<div class="video-container">
  <iframe  
    src="https://www.youtube.com/embed/5ZFga5Lz3pM?autoplay=0&mute=1&loop=1&controls=0&playlist=5ZFga5Lz3pM&playsinline=1"
    frameborder="0" 
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
   </iframe>
</div>

Showcasing blending texture maps and original color

<div class="video-container">
  <iframe  
    src="https://www.youtube.com/embed/8Y2Jzy8hNVE?autoplay=0&mute=1&loop=1&controls=0&playlist=8Y2Jzy8hNVE&playsinline=1"
    frameborder="0" 
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
   </iframe>
</div>

</div>
