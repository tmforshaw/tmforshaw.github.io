---
layout: project
title: "Raymarch Bevy"
description: "A Bevy program which utilises a fullscreen shader to display a ray-marched scene."
image: /assets/gifs/RaymarchBevy.gif
show-image: true
link: https://github.com/tmforshaw/Raymarch-Bevy
nav-menu: true
date: 05/02/2026
tools:
  - Rust
  - Bevy
---

<p>
A ray-marching renderer built in <code>Rust</code> using <code>Bevy</code> and <code>WGSL</code> shaders.
A custom fullscreen shader is used to replace the camera with an in-shader camera that performs ray marching to display the scene.
Integrated with <code>eGUI</code> to allow for real-time modification of parameters at runtime.
</p>

<hr class="major"/>

<section>
<h2>Project Summary</h2>
<ul>
	<li>Built a real-time ray-marched 3D scene renderer using a fullscreen shader pipeline in <code>Bevy</code>.</li>
	<li>Implemented responsive rendering that compensates for window rescaling to remove texture stretching.</li>
	<li>Created a fly camera system which pans via mouse input, translates based on keyboard inputs, and includes sprint controls for faster movement.</li>
	<li>Designed a modular shape system built using <strong>Signed Distance Functions</strong>, currently implemented for spheres, cubes, and planes.</li>
	<li>Added smooth unions or boolean blending for combining <strong>SDFs</strong>, with configurable blending constants.</li>
	<li>Implemented dynamic lighting, surface shading, and gamma correction in the fragment shader, consistent with the <strong>Phong</strong> shading model.</li>
	<li>Built a custom ray-march loop with collision detection, outlines for near-misses, and a gradient sky background.</li>
	<li>Structured shader data using Rust-side materials passed directly into <code>WGSL</code> uniforms.</li>
	<li>Included development-build <code>eGUI</code> tooling to modify in-game data in real-time.</li>
</ul>
</section>

<hr class="minor"/>

<section>
<h2>Technical Challenges</h2>
<ul>
	<li>Balancing of ray-march step count against rendering performance.</li>
	<li>Maintaining visual quality while avoiding excessive shader calculations.</li>
	<li>Preventing stretched output across varying window aspect ratios.</li>
	<li>Ensuring stable camera controls with smooth rotation, while avoiding gimbal locking issues.</li>
	<li>Passing structured data from <code>Rust</code> into the shader efficiently using uniforms, including dynamic shape data arrays.</li>
</ul>
</section>


<hr class="major"/>

<section>
<h2>Rendering Pipeline</h2>
<ul>
	<li>A fullscreen quad rendered each frame.</li>
	<li>Fragment shader casts a ray from the camera for each pixel.</li>
	<li>Ray marches through the scene using <strong>Signed Distance Functions</strong>.</li>
	<li>Closest hit point is shaded using normals and lighting, near-misses are highlighted and become outlines.</li>
</ul>
</section>

<hr class="major"/>

<section>
<h2>Shader Camera</h2>
<ul>
	<li>Replaced the traditional in-engine camera with an in-shader camera.</li>
	<li>Calculated a ray direction, per-pixel, using screen-space coordinates and the calculated view frustum (based on camera parameters).</li>
	<li>Implemented free-fly movement with keyboard translation across forward, right, and up axes of the camera.</li>
	<li>Added mouse-look controls to rotate camera smoothly in real-time.</li>
	<li>Camera rotations are stored and modified as quaternions to avoid gimbal lock.</li>
	<li>Passed camera position, rotation, FOV, and basis vectors from <code>Rust</code> into <code>WGSL</code> uniforms on each frame.</li>
	<li>Included sprint movement for faster scene navigation (Controlled via <emph>Shift</emph>).</li>
	<li>Integrated camera parameters with the <code>eGUI</code> windows to allow real-time modification while the scene is running.</li>
</ul>
</section>

<hr class="major"/>

<section>
<h2>Shape System</h2>
<ul>
	<li>Scene geometry represented using modular <strong>Signed Distance Functions</strong> primitives.</li>
	<li>Currently supports spheres, cubes, and planes.</li>
	<li>New shapes can be added by implementing additional <strong>SDF</strong> definitions.</li>
	<li>Each shape stores its position, scale, and colour, as well as its shape type (represented as an integer).</li>
	<li>Shapes can be blended using smooth unions or combined using boolean operations.</li>
	<li>Blending parameters are exposed at runtime and can be modified using the <code>eGUI</code> windows.</li>
</ul>
</section>

<hr class="major"/>

<section>
<h2>Lighting & Shading</h2>
<ul>
	<li>Surface normals are approximated from neighbouring SDF samples.</li>
	<li>Phong-style lighting model used for the diffuse and specular shading.</li>
	<li>Single dynamic light source with configurable position and colour.</li>
	<li>Gamma correction applied before final colour output for improved colour accuracy.</li>
	<li>Background rendered as a sky gradient when the ray didn't collide with any geometry.</li>
	<li>Silhouette outlines are generated from near-miss ray distances.</li>
	<li>Hard shadows are calculated to darken areas where the shapes block the light from the light source.</li>
</ul>
</section>

<hr class="major"/>

<section>
<h2>Future Improvements</h2>
<ul>
	<li>Portal rendering using non-linear ray traversal through <strong>SDF</strong> space.</li>
	<li>Soft shadows, and ambient occlusion to improve realism of the rendering.</li>
	<li>Multi-light support with extended material properties (roughness, emissiveness, etc).</li>
	<li>Adaptive ray-march step optimisation based on scene complexity.</li>
	<li>Additional SDF primitives: cylinders, capsules, and toruses.</li>
</ul>
</section>
