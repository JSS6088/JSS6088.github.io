---
layout: default
title: Optimizing Real-time Water Simulation
---

<div class="one-column" markdown="1">

# Real-time Water Simulation with SPH 

For our game, we wanted to add **physically simulated water** for bathtubs and toliets in **real time**. We chose to use **Smoothed Particle Hydrodynamics** which approximates water as lots of particles. By the time I joined the project, we had a working solution but it was very unopimitzed, causing low frame rates each time the water runs even with around **1000 particles**. If we turn down the number of particles, the simulation results get worse very quickly.

Therefore, I improved the simulation algorithm through using **GPU compute shaders**, allowing to simulate more than **60k water particles** in real time.

## 2D Version (60k water particles)
<div class="video-container">
  <iframe  
    src="https://www.youtube.com/embed/eLKjom370AY?autoplay=1&mute=1&loop=1&controls=0&playlist=eLKjom370AY&playsinline=1"
    frameborder="0" 
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
   </iframe>
</div>

## 3D Version (10k water particles)
<div class="video-container">
  <iframe  
    src="https://www.youtube.com/embed/0obHYRuh6r8?autoplay=1&mute=1&loop=1&controls=0&playlist=0obHYRuh6r8&playsinline=1"
    frameborder="0" 
    allow="autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
   </iframe>
</div>
*Credits to Luka who wrote the marching cube algorithm that turns water particles into 3D meshes*

# Breakdown
In SPH, updating each particle’s density, pressure, and viscosity forces requires **accessing all neighboring particles within the kernel radius**. A naive 𝑂(𝑛) loop over all particles is computationally prohibitive for real-time simulations, so an efficient **spatial acceleration structure** is required.

To achieve this, I divided the simulation domain into a uniform grid, where each cell’s width equals the kernel radius. Each particle is mapped to its corresponding grid cell, meaning only particles in adjacent cells need to be checked for interactions.

Each cell coordinate is then converted into a hash value using **Morton encoding (Z-order curve)**. This spatially coherent mapping ensures that nearby cells are also close in memory, improving **spatial locality** and overall performance.

</div>

<div class="two-column" markdown="1">

![SPH Z order indexing](/assets/images/ItsClogged/SPH-z-order%20indexing.png)
*The Z-indexing curve that orders elements according to spatial distance from [wikipedia](https://en.wikipedia.org/wiki/Z-order_curve)*

![SPH Z order indexing 2](/assets/images/ItsClogged/SPH-z-order%20indexing2.png)

</div>

<div class="one-column" markdown="1">

After assigning hash values, the particle array is sorted by hash, grouping particles belonging to the same cell contiguously in memory. I then compute an **offset array**, mapping each cell’s hash to the starting index of its particles in the array.

For sorting algorithm, I chose **radix sort** because I know the range of hash values, which makes the efficiency O(𝑛). It is also highly parallizable. Therefore, I can utilize compute shaders to sort the particle array.
I implemented the algorithm based on [this paper](https://gpuopen.com/download/Introduction_to_GPU_Radix_Sort.pdf) by AMD.

During simulation, the neighbor search becomes highly efficient:
- For each particle, I compute its cell hash.
- I use the offset array to quickly locate the start indices of particles in neighboring cells.

This reduces the complexity of neighborhood queries to **O(1)** with a constant factor, enabling real-time SPH simulations even for tens of thousands of particles.

# Pseudocode

</div>

<div class="two-column" markdown="1">

![SPH Main](/assets/images/ItsClogged/SPH-Algorithm.png)

![SPH Update hash table](/assets/images/ItsClogged/SPH-Update%20hash%20table.png)

</div>




