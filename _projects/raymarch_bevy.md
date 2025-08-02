---
layout: project
title: "Raymarch Bevy"
description: "A Bevy program which utilises a fullscreen shader to display a ray-marched scene."
image: /assets/images/raymarch_bevy_1.png
github: https://github.com/tmforshaw/Raymarch-Bevy
nav-menu: true
---

Using <code>Bevy</code>, a custom shader material is applied to a rectangular texture which is resized whenever the window is changed.
The shader material accounts for the screen width, so textures are not stretched when differernt window sizes are provided, only the aspect ratio changes.

<div class="table-wrapper">
  <table>
		<thead>
			<tr>
				<th>Material Value</th>
				<th>Type</th>
				<th>Possible values</th>
				<th>Description</th>
			</tr>
		</thead>
		<tbody>
			<tr>
				<td>Shape Type</td>
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
				<td>Defines the type of shape which will be drawn.</td>
			</tr>
			<tr>
				<td>Union Type</td>
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
				<td>Smoothness</td>
				<td><code>f32</code></td>
				<td>Larger values mean that the shapes will be smoothed together from a greater distance.</td>
				<td>Used when the <strong>smooth minimum</strong> union_type is selected, combines the shapes by smoothing their SDFs.</td>
			</tr>
			<tr>
				<td>Shader Light</td>
				<td><pre><code>struct ShaderLight {
  pos: Vec3&lt;f32&gt;,
  colour: Vec3&lt;f32&gt;,
}</code></pre><br>(<code>bluetoothctl</code>)</td>
				<td>Used to get and set the bluetooth state, and the current icon.</td>
			</tr>
			<tr>
				<td>Camera</td>
				<td><code>asusctl</code></td>
				<td>Used to get and set the current Asus fan profile, and get the fan icon. Can be set as particular profile, or cycled using <code>next</code> or <code>prev</code>.</td>
			</tr>
		</tbody>
	</table>
</div>


The material takes a <code>Vec</code> of <code>Shape</code> types, which can be spheres or cubes; a smoothness value
