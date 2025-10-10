---
layout: default
title: Real-time Water Simulation with SPH 
---

<div class="one-column" markdown="1">

# Real-time Water Simulation with SPH 

For our game, we wanted to add **physically simulated water** for bathtubs and toliets in **real time**. We chose to use **Smoothed Particle Hydrodynamics** which approximates water as lots of particles. By the time I joined the project, we had a working solution but it was very unopimitzed, causing low frame rates each time the water runs even with around **1000 particles**. If we turn down the number of particles, the simulation results get worse very quickly.

Therefore, I improved the simulation algorithm through using **GPU compute shaders**, allowing to simulate more than **60k water particles** in real time.


# Breakdown
In SPH, updating each particle’s density, pressure, and viscosity forces requires **accessing all neighboring particles within the kernel radius**. A naive 𝑂(𝑛) loop over all particles is computationally prohibitive for real-time simulations, so an efficient **spatial acceleration structure** is required.

To achieve this, I divided the simulation domain into a uniform grid, where each cell’s width equals the kernel radius. Each particle is mapped to its corresponding grid cell, meaning only particles in adjacent cells need to be checked for interactions.

Each cell coordinate is then converted into a hash value using **Morton encoding (Z-order curve)**. This spatially coherent mapping ensures that nearby cells are also close in memory, improving **spatial locality** and overall performance.

After assigning hash values, the particle array is sorted by hash, grouping particles belonging to the same cell contiguously in memory. I then compute an **offset array**, mapping each cell’s hash to the starting index of its particles in the array.

During simulation, the neighbor search becomes highly efficient:
- For each particle, I compute its cell hash.
- I use the offset array to quickly locate the start indices of particles in neighboring cells.

</div>

<div class="two-column" markdown="1">

![SPH Algorithm](/assets/images/ItsClogged/SPH-Algorithm.png)
*SPH Main Algorithm*

![SPH Update hash table](/assets/images/ItsClogged/SPH-Update%20hash%20table.png)
*SPH Update Hash Table*

</div>

<div class="one-column" markdown="1">

This reduces the complexity of neighborhood queries to **O(1)** with a constant factor, enabling real-time SPH simulations even for tens of thousands of particles.


</div>
