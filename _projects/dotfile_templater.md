---
layout: project
title: "Dotfile Templater"
description: "A program which can act on code which is written within the comments of a config file to replace text within the config."
image: /assets/images/dotfile_templater.png
show-image: true
link: https://github.com/tmforshaw/DotfilesTemplater
nav-menu: true
date: 06/06/2025
---

A program which acts on a minimal templating language which can replace parts of the config, based on a provided pattern.
The templating language is written within commented code, on the same line that it will act upon.
For each file which will be acted on, a <code>marker_char</code> is defined within the <strong>TOML</strong> config.
When <code>marker_char</code> is repeated <code>marker_repetition_num</code> times, the text after this point will be parsed as a list of the templating functions.

To ensure safe replacing, the templater will only replace text with text of the same length, this ensures that only the correct part of the file is modified.

Each function usually acts on the first match for its <code>pattern</code>; if there are multiple consecutive functions on a line, each function acts on its <strong>n</strong>th match, based on its order relative to the first function on the line.

Config text which matches the provided pattern is replaced by a <code>keyword</code>.

<dl>
  <dt><code>keyword</code></dt>
  <dd>
    <p>A string that will replace the matched pattern.</p>
  </dd>
  <dd>
    <p>A variable in the <strong>TOML</strong> config, which holds a string that will replace the pattern.</p>
  </dd>
</dl>

<hr class="major">

<h2>Function Usages</h2>

<div class="table-wrapper">
  <table>
		<thead>
			<tr>
				<th>Function</th>
				<th>Arguments</th>
				<th>Description</th>
			</tr>
		</thead>
		<tbody>
			<tr>
				<td><code>@replace</code></td>
				<td><code>keyword</code>&nbsp;<code>pattern</code></td>
				<td>Replace first text which matches <code>pattern</code> with the <code>keyword</code>.</td>
			</tr>
			<tr>
				<td><code>@replace&#8209;col</code></td>
				<td><code>keyword</code></td>
				<td>Replace first text which matches <code>#&#xFEFF;[&#xFEFF;A&#8209;Za&#8209;z&#xFEFF;\&#xFEFF;d&#xFEFF;]&#xFEFF;{&#xFEFF;6&#xFEFF;}</code> with the <code>keyword</code>.</td>
			</tr>
			<tr>
				<td><code>@replace&#8209;pattern</code></td>
				<td><code>pattern_1</code>&nbsp;<code>keyword</code>&nbsp;<code>pattern_2</code></td>
				<td>Find first text which matches <code>pattern_1</code>, then find first match within this match based on <code>pattern_2</code> and replace that with the <code>keyword</code>.</td>
			</tr>
			<tr>
				<td><code>@replace&#8209;pattern&#8209;col</code></td>
				<td><code>keyword</code>&nbsp;<code>pattern_2</code></td>
				<td>Find first text which matches <code>#&#xFEFF;[&#xFEFF;A&#8209;Za&#8209;z&#xFEFF;\&#xFEFF;d&#xFEFF;]&#xFEFF;{&#xFEFF;6&#xFEFF;}</code>, then find first match within this match based on <code>pattern</code> and replace that with the <code>keyword</code>.</td>
				<td></td>
			</tr>
		</tbody>
	</table>
</div>

<hr class="major">

<h2>TOML Config</h2>

The config file is located at <code>$XDG_CONFIG_HOME/dotfile-templater/config.toml</code>

<code>marker_repetition_num</code> can be optionally defined to adjust how many <code>marker_char</code> need to be placed before template code, its default value is <code>3</code>.

Each <code>file</code> in <code>files</code> can be relative to the <code>.config</code> folder, or an absolute path.
Every object in <code>files</code> must have a <code>marker_char</code> defined, so that the templating code can be found.

Themes can be defined in the <code>themes</code> section, every theme must include <code>name</code>, but all other variables can have any name, so long as it is consistent across your themes.

<pre><code>theme = "purple-night"
marker_repetition_num = 3
files = [
  {file = "eww/eww.scss", marker_char = "//"},
  {file = "eww/bar/bar.yuck", marker_char = ";"},
  {file = "wofi/style.css", marker_char = ";"},
  {file = "hypr/hyprland.conf", marker_char = "%"},
]

[[themes]]
name = "purple-night"

primary_col = "#9549FF"
secondary_col = "#FF4958"
tertiary_col = "#B3FF49"
quaternary_col = "#49FFF0"

bg_col = "#1A1B26"
bg_col_light = "#24283B"
fg_col = "#A9B1D6"

[[themes]]
name = "blue-bannana"

primary_col = "#48FFD1"
secondary_col = "#4876FF"
tertiary_col = "#FF4876"
quaternary_col = "#FFD148"

bg_col = "#0A0A40"
bg_col_light = "#11116C"
fg_col = "#9999F8"</code></pre>

<hr class="major">

<h2>Test.conf</h2>

This test file shows the functions in action within a test configuration file.

<pre><code>test = This wont be configured % Even if there are comments

$primary: rgb(9549FF); %%% @replace-pattern('rgb\([A-Za-z\d]{6}\)', primary_col, '[A-Za-z\d]{6}')
$secondary: #FF4958; %%% @replace-pattern-col(secondary_col, '[A-Za-z\d]{6}')
$tertiary: #B3FF49; #123456; %%% @replace-col(fg_col) @replace-col(bg_col)
$quaternary: #49FFF0; %%% @replace-col(quaternary_col)

$background: #1A1B26; %%% @replace-col(bg_col)
$background-lighter: #24283B; %%% @replace-col(bg_col_light)
$foreground: #A9B1D6; %%% @replace-col(fg_col)</code></pre>
