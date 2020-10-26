---
title: "Vulkan Based Grass Simulator and Renderer"
permalink: /projects/vulkan_grass_rendering
---

The grass simulator and renderer is based on the paper  [Responsive Real-Time Grass Grass Rendering for General 3D Scenes](https://www.cg.tuwien.ac.at/research/publications/2017/JAHRMANN-2017-RRTG/JAHRMANN-2017-RRTG-draft.pdf) and Vulkan API. I used the compute shaders to perform physics calculations on Bezier curves that represent individual grass blades to cull grass blades that don't contribute to a given frame. The remaining blades are passed to a graphics pipeline: Bezier control points are transformed in the vertex shader,  the grass geometry is created dynamically from the Bezier curves in the tessellation shaders, and the grass blades are shaded finally in the fragment shader.

![Vulkan grass rendering gif](/assets/images/projects/vulkan_grass_rendering/grass_rendering.gif)

## Features
- Represent grass as Bezier curves.

  ![](/assets/images/projects/vulkan_grass_rendering/blade_model.jpg)

- Simulate forces with gravity, recovery and wind.

  ![Forces pic](/assets/images/projects/vulkan_grass_rendering/forces.png)

- Cull the blade when the front face direction is perpendicular to the view vector and when blades are outside of the view-frustum.

- Tessellate to varying levels of detail as a function of how far the grass blade is from the camera.

## References

- [Responsive Real-Time Grass Grass Rendering for General 3D Scenes](https://www.cg.tuwien.ac.at/research/publications/2017/JAHRMANN-2017-RRTG/JAHRMANN-2017-RRTG-draft.pdf)
- [Vulkan tutorial](https://vulkan-tutorial.com/)
- [RenderDoc blog on Vulkan](https://renderdoc.org/vulkan-in-30-minutes.html)