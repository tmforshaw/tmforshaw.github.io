---
layout: project
title: "Sound Themer"
description: "A program to play sounds from a configured sound theme based only on the name/keyword provided."
image: /assets/images/sound_themer_code.png
show-image: false
link: https://github.com/tmforshaw/sound_themer
nav-menu: true
---
<p>
	A sound theming program which can play sounds from a given sound theme folder.
	Configured using <a href="https://docs.rs/clap/latest/toml/">TOML</a>, allows for different themes to have different internal file structures and sound file extensions, as well as have sounds named differently (the mapping between keywords and sound file names is changed in the config).
	The intended use case is to provide sounds for system notifications, which differ based on the type of notification.
</p>

<p>
	The CLI for this application is written using <a href="https://docs.rs/clap/latest/clap/">Clap</a> (Command Line Argument Parser), so each command and subcommand can be followed by <code>help</code> to learn more about it.
	Aliases are provided for all commands, meaning they can be shortened.<br>
	<code>sound_themer&nbsp;p&nbsp;audio-change</code>&nbsp;&#8801;&nbsp;<code>sound_themer&nbsp;play&nbsp;audio-change</code>&nbsp;&#8801;&nbsp;<code>sound_themer&nbsp;play&nbsp;audio-volume-change</code>
</p>

<hr class="major" />

<h2>Usage</h2>
<div class="table-wrapper">
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
				<td>Plays a sound from the theme folder, mapping from <strong>sound_name</strong> to its associated value for this theme (if one exists).</td>
			</tr>
			<tr>
				<td>
					<code>sound_themer&nbsp;list</code><br/>
					<code>sound_themer&nbsp;ls</code>
				</td>
				<td>Lists the sound files in the currently selected theme's folder.</td>
			</tr>
			<tr>
				<td>
					<code>sound_themer&nbsp;--&#8288;theme&nbsp;&lt;THEME_NAME&gt;&nbsp;&lt;COMMAND&gt;</code><br/>
					<code>sound_themer&nbsp;-t&nbsp;&lt;THEME_NAME&gt;&nbsp;&lt;COMMAND&gt;</code><br/>
				</td>
				<td>Overrides the theme which is selected in <code>config.toml</code></td>
			</tr>
		</tbody>
	</table>

<hr class="major" />

<h2>Config</h2>
<p>
	The configuration is in <a href="https://docs.rs/clap/latest/toml/">TOML</a>, and allows for setting a <code>theme_name</code>, along with defining how each theme works.
	Inner directories of the theme folder can be provided, along with the extension for sound files in this theme.
	Mapping keyword values to their file name counterparts is also configured here, allowing for using the same commands to play sounds from any theme.
	If the provided keyword doesn't exist in the mapping, the program will attempt to play a sound from the theme matching the provided keyword with no mapping.
</p>

<pre><code># Name of the selected sound theme
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
</div>