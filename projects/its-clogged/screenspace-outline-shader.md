---
layout: default
title: Screenspace Outline Shader
---

<div class="one-column" markdown="1">

## Screenspace Outline Shader

I created an outline shader to further fit the möbius drawing style.  

![Screenspace Outline Shader](/assets/images/ItsClogged/OutlineScreenSpace_render.png)
*Scene with screenspace outline shader, models by our artist Jerry*

Artists can change the **edge thickness** and **edge sensitivity detection** based on relative normal difference. It ensures not drawing outlines for smoother edges.

<div class="video-container">
  <iframe  
    src="https://www.youtube.com/embed/On5cpfuy6Cc?autoplay=1&mute=1&loop=1&controls=0&playlist=On5cpfuy6Cc&playsinline=1"
    frameborder="0" 
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
   </iframe>
</div>

## Breakdown

This shader detects two areas to decide whether to draw an edge: A sudden change in depth or a sudden change in normal direction. It then combines these two edge detection images for the final result. It currently takes 9 samples for each pixel, if it is too expensive for low-end devices then only 4 samples is needed.  

### Depth detection

The depth edge detection is calcalated from applying a 3x3 sobel filter to the depth buffer. 

```cpp
for (int y = -1; y <= 1; y++)
{
    for (int x = -1; x <= 1; x++)
    {
        if (x == 0 && y == 0) continue;
        float2 offsetUV = i.uv + displace + _Thickness * _CameraDepthTexture_TexelSize.xy * float2(x, y);

        // assume ortho camera bc our game is ortho view
        float sampleDepth = SAMPLE_DEPTH_TEXTURE(_CameraDepthTexture, sampler_CameraDepthTexture, offsetUV);
        sampleDepth = _ProjectionParams.x == 1? sampleDepth: 1 - sampleDepth; // near plane = 0.0, far plane = 1.0
        float rawDepth = lerp(_ProjectionParams.y, _ProjectionParams.z, sampleDepth);
        sobelXDepth += abs(rawCenterDepth - rawDepth) * sobelXMat[1-y][x+1];
        sobelYDepth += abs(rawCenterDepth - rawDepth) * sobelYMat[1-y][x+1];
    }
}
```

![Depth detection](/assets/images/ItsClogged/OutlineScreenSpace_depth.png)
*Edges from depth detection*

### Normal detection

The normal edge detection is calculated from approximating the magnitude of gradient of the normal vector field.

```cpp
for (int x = -1; x <= 1; x++)
{
    for (int y = -1; y <= 1; y++)
    {
        float2 offsetUV = i.uv + displace + _Thickness * _CameraNormalsTexture_TexelSize.xy * float2(x, y);

        float3 sampleNormal = SAMPLE_TEXTURE2D(_CameraNormalsTexture, sampler_CameraNormalsTexture, offsetUV).xyz;
        float intensity = abs(dot(centerNormal, sampleNormal));

        // Exclude areas with small normal difference and areas with no normal(skybox)
        float normalFlag = (intensity < smoothAngle) * (length(centerNormal) > 0.01);

        edgeNormal += normalFlag * (1 - intensity);
    }
}
```

![Normal detection](/assets/images/ItsClogged/OutlineScreenSpace_normal.png)
*Edges from normal detection*

</div>