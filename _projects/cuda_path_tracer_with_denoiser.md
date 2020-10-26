---
title: "CUDA Path Tracer with Denoiser: GPU Based Renderer"
permalink: /projects/cuda_path_tracer_with_denoiser
---

The path tracer uses CUDA to render image parallelly, based on Monte Carlo path tracing technique to render a scene and denoise the originally rendered images with the A-trous wavelet filter.

![Vulkan grass rendering gif](/assets/images/projects/cuda_path_tracer/pathtracing_first_demo.gif)

## Features
- Ray sorting by material
- Ideal Diffuse Shading and Perfect Specular Reflection
- Stream Compaction and Cache first bounce
- Refraction with Frensel effects using Schlick's approximation.
- Physically-based depth-of-field
- Stochastic Sampled Antialiasing
- Arbitrary mesh loading and rendering glTF files with toggleable bounding volume intersection culling
- Better hemisphere sampling methods
- Direct lighting
- Motion blur by averaging samples at different times in the animation
- Denoise with A-trous wavelet filter

## Rendered Images
- Kirby
![Kirby rendered pic](/assets/images/projects/cuda_path_tracer/cornell_kirby.png)
- Lion
![Lion rendered pic](/assets/images/projects/cuda_path_tracer/lion.png)
- Spitfire
![Spitfire rendered pic](/assets/images/projects/cuda_path_tracer/spitfire.png)
- Galleon  
![Galleon rendered pic](/assets/images/projects/cuda_path_tracer/galleon.png)
- Goku  
![Galleon rendered pic](/assets/images/projects/cuda_path_tracer/goku.png)
- Texture mapping and bump mapping
<figure class="half">
    <a href="/assets/images/projects/cuda_path_tracer/mjolnir.png"><img src="/assets/images/projects/cuda_path_tracer/mjolnir.png"></a>
    <a href="/assets/images/projects/cuda_path_tracer/basketball.png"><img src="/assets/images/projects/cuda_path_tracer/basketball.png"></a>
</figure>

## References
- Matt Pharr, Wenzel Jakob, and Greg Humphreys. *[Physically Based Rendering: From Theory To Implementation](http://www.pbr-book.org/)*
- [Header only C++ tiny glTF library](https://github.com/syoyo/tinygltf/)
- [Edge-Avoiding A-Trous Wavelet Transform for fast Global Illumination Filtering](https://jo.dreggn.org/home/2010_atrous.pdf)
- ocornut/imgui - [https://github.com/ocornut/imgui](https://github.com/ocornut/imgui)
- Gaussian filter - [https://en.wikipedia.org/wiki/Gaussian_blur](https://en.wikipedia.org/wiki/Gaussian_blur)