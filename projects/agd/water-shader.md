---
layout: default
title: Water Shader
---

<div class="one-column" markdown="1">

# Water Shader

A few maps in our game *A Gentlemen's Dispute* feature water, and as the primary 3D artist, I was excited to develop a custom water shader for them. This was a valuable learning experience that helped me deepen my understanding of shader programming and game engine APIs. More importantly, it taught me how to bridge the gap between real-world phenomena and their digital counterparts — or, more often, their stylized abstractions.

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/zncquhNhr8U?autoplay=1&mute=1&loop=1&playlist=zncquhNhr8U&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

The water shader automatically applies foam to any intersecting geometry, adding an extra layer of visual detail without requiring manual setup.

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/nZqe86BLFC0?autoplay=1&mute=1&loop=1&playlist=nZqe86BLFC0&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

## Breakdown

### Color each zone

Each water surface has four components: shallow zone, deep zone, fog zone, caustic zone. The color of each zone are exposed as parameters for easy artistic control.

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/Qa3hhHixHrE?mute=1&loop=1&playlist=Qa3hhHixHrE&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

### Define each zone 

The **caustic zone** is defined through a **worley noise**. 

```cpp
float worley = worley_noise(i.uv, 5.0*_WorleyScale, 0.2);
float causticMask = smoothstep(0.45, 0.65, worley);
```

The **shallow and deep zone** is defined through a depth mask calculated from **height difference** between water surface and ocean floor. We can get the distance between camera to ocean surface from the clip-space position of the pixel. We can get the distance between camera to ocean floor sampling from the z-depth texture. 

```cpp
// Calculate depth mask
float2 screenSpaceUV = i.screenSpace.xy / i.screenSpace.w;
float sceneDepth = LinearEyeDepth(SAMPLE_DEPTH_TEXTURE(_CameraDepthTexture, screenSpaceUV));
float3 viewDirection = i.wsViewDir;
      
/** 
    i.screenSpace.w is the distance from camera to water surface
    sceneDepth is distance from camera to the underwater object
    Subtracting them gets the distance between underwater object to the water surface
    Extract only y component to get the vertical distance
**/
float rawDepth = ((sceneDepth - i.screenSpace.w) * viewDirection).y;
```

**Foam** is a byproduct of this depth mask with a **_step_ function** to threshold the value. It is combined with the caustic zone because I dont think these two need to have different colors.

```cpp
// Calculate foam mask (where to place foams)
float speed = 0.01 * _Time.y * _IsMoving * _Speed;
float foamDepth = rawDepth / (_FoamSize*0.5*_DepthFade);
float foamMask = step(0.5 + 0.5 * simplex_noise(float3(i.uv, speed), _FoamScale*100.0, 2.0), foamDepth); 

// Calculate caustic mask
float causticMask = lerp(float4(1,1,1,1), noiseCaustic(i, _GlobalScale), foamMask); 
```

The **fog zone** is defined using distance to the center in UV space, since the ocean is UV unwrapped in a way where the center is cloest to player. 

```cpp
float fogDepth = saturate(2.0*pow(pow(i.uv.x-0.5, 2) + pow(i.uv.y-0.5, 2), 0.5)-0.2); 
float fogFactor = exp2(-pow(fogDepth*3.0*_HasFog,2));
```

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/In52MVVBxYQ?mute=1&loop=1&playlist=In52MVVBxYQ&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

### Animate each zone 

The caustic zone is animated through distorting the UV space of the worley noise with **a simplex noise** in fragment shader.

```cpp
//Offset water
float2 direction = float2(cos(_OffsetDirection), sin(_OffsetDirection));
i.uv += 0.01 * _OffsetSpeed * direction * _Time.y * _IsMoving;

float speed = 0.05 * _Time.y * _IsMoving * _Speed;
float scale = 4.0*_SimplexScale;                
float noise1 = simplex_noise(float3(i.uv, speed), scale, 2.0);
float noise2 = simplex_noise(float3(i.uv+10.0, speed), scale, 2.0);

i.uv += 0.05*float2(noise1, noise2);

//Use worley noise to generate caustic...
```

The ocean plane geometry is animated using **simplex noise** to distort the vertex position in vertex shader.

```cpp
// Displace vertices to simulate ocean waves
float speed = 0.0025 * _Time.y * _IsDeforming;
float scale = 4.0*_SimplexScale;
float noise = simplex_noise(float3(v.uv, speed), scale, 1.0);
// Move vertex positions
v.vertex.z += 0.008*_Amp*noise;
```

<div class="video-container">
  <iframe
    src="https://www.youtube.com/embed/u2hBoh7S5-I?mute=1&loop=1&playlist=u2hBoh7S5-I&controls=0&playsinline=1"
    frameborder="0"
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
  </iframe>
</div>

As a learning opportunity, I wrote the simplex and worley noise by myself in **HLSL**. [The Book of Shaders](https://thebookofshaders.com/) is a specific helpful resource in assisting my shader creation process. 

</div>