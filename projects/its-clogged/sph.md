---
layout: default
title: Real-time Water Simulation with SPH 
---

<div class="one-column" markdown="1">

# Real-time Water Simulation with SPH 

For our game, we wanted to add **physically simulated water** for bathtubs and toliets in **real time**. We chose to use **Smoothed Particle Hydrodynamics** which approximates water as lots of particles. By the time I joined the project, we had a working solution but it was very unopimitzed, causing low frame rates each time the water runs even with around **1000 particles**. If we turn down the number of particles, the simulation results get worse very quickly.

Therefore, I improved the simulation algorithm through using **GPU compute shaders**, allowing to simulate more than **60k water particles** in real time.


# Breakdown
The main algorithm for SPH involves updating density, pressure force, and viscosity force values for each particle, which relies on reading values from **neighboring particles** in the last time step. However, a naive search for looping over all particles gives us only **𝑂(𝑛) efficiency**, which is too slow for real-time simulations. Therefore, it becomes necessary to implement a **fast neighborhood search algorithm**.

## Neighborhood search


## GPU radix sort


## Scale to 3D



</div>
