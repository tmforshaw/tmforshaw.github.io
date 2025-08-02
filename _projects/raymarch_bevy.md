---
layout: project
title: "Raymarch Bevy"
description: "A Bevy program which utilises a fullscreen shader to display a ray-marched scene."
image: /assets/images/raymarch_bevy_1.png
github: https://github.com/tmforshaw/Raymarch-Bevy
nav-menu: true
---

Using <code>Bevy</code>, a custom shader material is applied to a rectangular texture which is resized to the full screen size whenever the window is changed.
The shader material accounts for the screen width, so textures are not stretched when differernt window sizes are provided, only the aspect ratio changes.

There is a camera controller which can be moved with mouse and keyboard, and it includes moving faster when holding the shift key.

<hr class="major"/>

<h2>Shape Struct</h2>

<div class="table-wrapper">
  <table>
		<thead>
			<tr>
				<th>Shape Variable</th>
				<th>Type</th>
				<th>Possible values</th>
				<th>Description</th>
			</tr>
		</thead>
		<tbody>
			<tr>
				<td><code>shape_type</code></td>
				<td><code>u32</code></td>
        <td>
          <div class="table-wrapper">
            <table class="alt">
          		<thead>
          			<tr>
          				<th>Value</th>
          				<th>Meaning</th>
          			</tr>
          		</thead>
          		<tbody>
          			<tr>
          				<td>1</td>
          				<td>Sphere</td>
          			</tr>
          			<tr>
          				<td>2</td>
          				<td>Cube</td>
          			</tr>
          			<tr>
          				<td>3</td>
          				<td>Plane</td>
          			</tr>
          			<tr>
          				<td>4</td>
          				<td>Portal <strong>W.I.P</strong></td>
          			</tr>
          			<tr>
          				<td>Other</td>
          				<td>No Shape</td>
          			</tr>
          		</tbody>
          	</table>
          </div>
        </td>
				<td>Defines the type of shape which will be drawn.
				Portals are currently not working, but the hope is that the rays will be able to traverse them as if they don't exist in the SDF, and will instead return the distance to the objects on the other side of the portal.</td>
			</tr>
			<tr>
				<td><code>pos</code></td>
				<td><code>Vec3&lt;f32&gt;</code></td>
				<td>World-space position of this shape.</td>
				<td>The position of this shape (Used to offset the SDF).</td>
			</tr>
			<tr>
				<td><code>size</code></td>
				<td><code>Vec3&lt;f32&gt;</code></td>
				<td>Allows positive <code>f32</code> values. Negative values also sometimes work, but can lead to undefined behaviour.</td>
				<td>The 3D scale of this shape (Used to scale the SDF).</td>
			</tr>
		</tbody>
	</table>
</div>

<hr/>

<h2>Shape Material</h2>

<div class="table-wrapper">
  <table>
		<thead>
			<tr>
				<th>Material Variable</th>
				<th>Type</th>
				<th>Possible Values</th>
				<th>Description</th>
			</tr>
		</thead>
		<tbody>
			<tr>
				<td><code>shapes</code></td>
        <td><code>Vec&lt;Shape&gt;</code></td>
				<td>
        </td>
				<td>A vector with all the shapes which will be drawn in the scene.</td>
			</tr>
			<tr>
				<td><code>union_type</code></td>
        <td><code>u32</code></td>
				<td>
          <div class="table-wrapper">
            <table class="alt">
          		<thead>
          			<tr>
          				<th>Value</th>
          				<th>Meaning</th>
          			</tr>
          		</thead>
          		<tbody>
          			<tr>
          				<td>0</td>
          				<td>Minimum</td>
          			</tr>
          			<tr>
          				<td>other</td>
          				<td>Maximum</td>
          			</tr>
          		</tbody>
          	</table>
          </div>
        </td>
				<td>Defines whether the shapes will be shown by taking the smooth minimum of all shapes, or the regular maximum.</td>
			</tr>
			<tr>
				<td><code>smoothness_val</code></td>
				<td><code>f32</code></td>
				<td>Larger values mean that the shapes will be smoothed together from a greater distance. Negative values cause shapes to become inside out. </td>
				<td>Used when the <strong>smooth minimum</strong> union_type is selected, combines the shapes by smoothing their SDFs.</td>
			</tr>
			<tr>
				<td><code>light</code></td>
				<td><pre><code>struct ShaderLight {
  pos: Vec3&lt;f32&gt;,
  colour: Vec3&lt;f32&gt;,
}</code></pre></td>
				<td>A starting position for the scene's light, and its colour in normalised [0-1] rgb values.</td>
				<td>Defines the information about the scene's lighting, which will be used in the shader to adjust the colour of scene objects.</td>
			</tr>
			<tr>
				<td><code>camera</code></td>
				<td><pre><code>struct ShaderCamera {
  pos: Vec3&lt;f32&gt;,
  zoom: f32,
  rotation: Vec4&lt;f32&gt;,
  forward: Vec3&lt;f32&gt;,
  right: Vec3&lt;f32&gt;,
  up: Vec3&lt;f32&gt;,
}
</code></pre></td>
				<td>
					<code>pos</code> defines the world-space position of the camera.
					Zoom defines the field of view.
					<code>rotation</code> is a quaternion matrix.
					<code>forward</code>, <code>right</code>, and <code>up</code> define the axes of the camera, and are all orthogonal.
				</td>
				<td>Provides information about the camera, which is rotated using mouse controls and translated using keyboard controls. The control scheme is a fly camera setup, similar to flying movement in many games.</td>
			</tr>
			<tr>
				<td><code>time</code></td>
				<td><code>f32</code></td>
				<td>Values above 0.0</td>
				<td>The amount of time (in seconds) which has elapsed since the rendering began.</td>
			</tr>
			<tr>
				<td><code>shapes_len</code></td>
				<td><code>u32</code></td>
				<td>Positive Integers</td>
				<td>The amount of shapes which will be rendered from the shapes <code>Vec</code>.</td>
			</tr>
		</tbody>
	</table>
</div>

<hr class="major">

<h2>Fragment Shader</h2>

The co-ordinates are scaled so that changing the aspect ratio of the window doesn't stretch the outputted image.
The ray-marching loop is completed, and the resulting point which the ray has collided with is calculated.
The lighting of the scene at this point is calculated, and the final colour is gamma corrected.

<pre><code>@fragment
fn fragment(in: VertexOutput) -&gt; @location(0) vec4&lt;f32&gt; {
    let coords = centre_and_scale_uv_positions(in.position.xy, view.viewport.zw);
    let camera_pos = material.camera.pos;

    let ray_dir = get_ray_dir(material.camera, coords);
    let get_dist_input = GetDistanceInput(material.union_type, material.smoothness_val, material.time);

    let ray_march_out = ray_march(camera_pos, ray_dir, get_dist_input);

    let point_on_surface: vec3&lt;f32&gt; = camera_pos + ray_dir * ray_march_out.dist;
    let light_strength = get_light(point_on_surface, -ray_dir, material.light.pos, get_dist_input);

    var colour: vec3&lt;f32&gt; = ray_march_out.object_colour * material.light.colour * light_strength;

    // Gamma correction
    let gamma = 2.2;
    colour = pow(colour, vec3&lt;f32&gt;(1.0 / gamma));

    return vec4&lt;f32&gt;(colour, 1.0);
}</code></pre>

<hr>

<h2>Ray March Loop</h2>

The ray checks the distance to the closest object, and marches forward that distance, this continues until either the ray has collided with a shape, or the maximum distance/steps have been reached.
The closest distance reached is remembered so that outlines can be given to pixels where the ray has missed the an object by a small amount.
If the ray neither collided with a shape nor went sufficiently close to a shape, then a background colour, which is a mix of the sky and bottom sky colours centered at the horizon, is shown.

<pre><code>fn ray_march(ray_origin: vec3&lt;f32&gt;, ray_dir: vec3&lt;f32&gt;, get_dist_input: GetDistanceInput) -&gt; RayMarchOutput {
    var ray = Ray(ray_origin, ray_dir);

    // Keep track of the minimum distance that the ray reached
    var min_dist = max_dist;

    var ray_dist = 0.;
    var total_ray_dist = ray_dist;
    var march_steps = 0;

    while(total_ray_dist &lt; max_dist) {
        march_steps++;

        let dist_output = get_distance(ray.origin, get_dist_input);
        var dist = dist_output.dist;
        let object_col = dist_output.colour;
        let shape_type = dist_output.shape_type;

        // Set the minimum distance reached if this distance is smaller
        if dist &lt; min_dist {
            min_dist = dist;
        }

        // Exit the loop if we have traversed for too many iterations
        if march_steps &gt; max_steps {
            break;
        }

        // Have intersected something
        if dist &lt;= epsilon {
            // Intersected Portal
            if shape_type == 4 {
                ray.dir = vec3&lt;f32&gt;(0., 0., -1.);
                ray.origin = vec3&lt;f32&gt;(2., 0., 2.) + ray_dir * 0.5;

                min_dist = 0.15;
                continue;
                // return RayMarchOutput(vec3&lt;f32&gt;(0., 0., 1.), 1,0.001);
            }

            return RayMarchOutput(object_col, ray_dist, min_dist);
        }

        // Move the ray
        ray.origin += ray.dir * dist;
        ray_dist += dist;
        total_ray_dist += dist;
    }

    // Draws an outline of shapes where the ray missed by only a small amount
    if min_dist &lt; 0.1 {
        return RayMarchOutput(vec3&lt;f32&gt;(1., 1., 1.), ray_dist, min_dist);
    }

    let sky_col = vec3&lt;f32&gt;(0.1, 0.2, 0.7);
    let bottom_sky_col = vec3&lt;f32&gt;(0.3, 0.2, 0.5);

    let background = mix(bottom_sky_col, sky_col,  (1.5 + dot(vec3&lt;f32&gt;(0.,1.,0.), ray.dir)));

    return RayMarchOutput(background, ray_dist, min_dist);
}</code></pre>
