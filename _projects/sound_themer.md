---
layout: project
title: "Sound Themer"
description: "A program to play sounds from a configured sound theme based only on the name/keyword provided."
image: /assets/images/sound_themer_code.png
show-image: false
link: https://github.com/tmforshaw/sound_themer
nav-menu: true
date: 2026-03-02
tools:
  - Rust
---
<section>
<h2>Project Summary</h2>
<ul>
	<li>Built a configurable <strong>CLI</strong> tool for playing themed system sounds based on keyword input.</li>
	<li>Decouples <strong>sound naming</strong> from <strong>file structure</strong>, enabling a single interface across multiple themes.</li>
	<li>Designed for integration with <strong>system notifications</strong> and automation workflows.</li>
</ul>
</section>

<hr class="minor">

<section>
<h2>Technical Challenges</h2>
<ul>
	<li>Abstracting over <strong>inconsistent filesystem layouts</strong> across themes.</li>
	<li>Designing a flexible mapping system without ambiguity.</li>
	<li>Maintaining a simple <strong>CLI</strong> without compromising high configurability.</li>
	<li>Handling multiple file extensions and nested directories efficiently.</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Core Design</h2>
<ul>
	<li>Keyword-driven playback system:
		<ul>
			<li><code>sound_themer play &lt;KEYWORD&gt;</code> resolves to a mapped sound file.</li>
			<li>Falls back to direct filename matching if no mapping exists.</li>
		</ul>
	</li>
	<li>Theme Abstraction Layer:
		<ul>
			<li>Each theme defines its own directory structure, file extensions, and naming scheme.</li>
			<li>Ensures consistent <strong>CLI</strong> usage across incompatible sound packs.</li>
		</ul>
	</li>
	<li>Runtime Flexibility:
		<ul>
			<li>Themes can be overridden via <strong>CLI</strong> flags without modifying the config.</li>
		</ul>
	</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Configuration System</h2>
<ul>
	<li>Uses <code>TOML</code> for human-readable, extensible configuration.</li>
	<li>Defines:
		<ul>
			<li>Active theme.</li>
			<li>Theme directories and file extensions.</li>
			<li>Keyword &rarr; filename mappings.</li>
		</ul>
	</li>
	<li>Supports:
		<ul>
			<li>Nested directory structures.</li>
			<li>Non-uniform naming conventions across sound themes.</li>
		</ul>
	</li>
	<li>Mapping system enables:
		<ul>
			<li>Normalisation of inconsistent naming.</li>
			<li>Reuse of the same commands across all themes.</li>
		</ul>
	</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Command Line Interface</h2>
<ul>
	<li>Built using <a href="https://docs.rs/clap/latest/clap/"><code>Clap</code></a> for structured command parsing.</li>
	<li>Supports:
		<ul>
			<li>Subcommands with built-in help output.</li>
			<li>Command aliases (<code>play</code> &rarr; <code>p</code>, <code>list</code> &rarr; <code>ls</code>).</li>
		</ul>
	</li>
	<li>Example usage:
	  <table>
			<thead>
				<tr>
					<th>Command</th>
					<th>Description</th>
				</tr>
			</thead>
			<tbody>
				<tr>
					<td>
						<code>sound_themer&nbsp;play&nbsp;&lt;SOUND_NAME&gt;</code><br/>
						<code>sound_themer&nbsp;p&nbsp;&lt;SOUND_NAME&gt;</code>
					</td>
					<td>
						<ul>
							<li>Plays a sound from the theme folder.</li>
							<li>Maps from <code>SOUND_NAME</code> to its associated value in the selected theme (if one exists).</li>
						</ul>
					</td>
				</tr>
				<tr>
					<td>
						<code>sound_themer&nbsp;list</code><br/>
						<code>sound_themer&nbsp;ls</code>
					</td>
					<td>
						<ul>
							<li>Lists the sound files in the currently selected theme's folder.</li>
						</ul>
					</td>
				</tr>
				<tr>
					<td>
						<code>sound_themer&nbsp;--&#8288;theme&nbsp;&lt;THEME_NAME&gt;&nbsp;&lt;COMMAND&gt;</code><br/>
						<code>sound_themer&nbsp;-t&nbsp;&lt;THEME_NAME&gt;&nbsp;&lt;COMMAND&gt;</code><br/>
					</td>
					<td>
						<ul>
							<li>Overrides the theme which is selected in <code>config.toml</code>.</li>
						</ul>
					</td>
				</tr>
			</tbody>
		</table>
	</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Extensibility</h2>
<ul>
	<li>New themes can be added without making code changes.</li>
	<li>Configuration-driven design supports:
		<ul>
			<li>Custom keyword dictionaries.</li>
			<li>Arbitrary directory layouts.</li>
			<li>Additional sound formats.</li>
		</ul>
	</li>
	<li>Suitable for integration with:
		<ul>
			<li>Window managers.</li>
			<li>Notification systems.</li>
			<li>Automation scripts.</li>
		</ul>
	</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Example Config</h2>
<pre><code class="language-toml"># Name of the selected sound theme
theme_name = "freedesktop"

[[themes]]
# Name of the sound theme folder
name = "freedesktop"

# Extension on the sound files
sound_ext = "oga"

# Directories where the sounds are found
directories = ["stereo"]

# Provide a mapping between certain phrases and their respective sound file name
mapping = {
  audio-change = "audio-volume-change",
  login = "service-login",
  logout = "service-logout",
  message = "message",
  power-plug = "power-plug",
  power-unplug = "power-unplug",
  dialog-info = "dialog-information",
  dialog-warning = "dialog-warning",
  dialog-error = "dialog-error",
  screen-capture = "screen-capture",
  device-added = "device-added",
  device-removed = "device-removed",
  camera-shutter = "camera-shutter",
  trash-empty = "trash-empty",
  complete = "complete"
}
</code></pre>
</section>
