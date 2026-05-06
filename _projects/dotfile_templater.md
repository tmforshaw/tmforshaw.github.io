---
layout: project
title: "Dotfile Templater"
description: "A program which can act on code which is written within the comments of a config file to replace text within the config."
image: /assets/images/dotfile_templater.png
show-image: true
link: https://github.com/tmforshaw/DotfilesTemplater
nav-menu: true
date: 2025-06-06
tools:
  - Rust
  - Regex
---

<section>
<h2>Project Summary</h2>
<ul>
	<li>Built a lightweight <strong>templating tool</strong> in <code>Rust</code> for modifying configuration files in-place.</li>
	<li>Implements a <strong>minimal inline templating language</strong> embedded within comments.</li>
	<li>Designed for <strong>dotfile theming</strong> and <strong>automation</strong>, enabling dynamic config updates without separate template files.</li>
</ul>
</section>

<hr class="minor">

<section>
<h2>Technical Challenges</h2>
<ul>
	<li>Designing a <strong>minimal templating language</strong> that works within existing config syntax.</li>
	<li>Parsing inline comment-based instructions without interfering with file semantics.</li>
	<li>Ensuring <strong>safe</strong>, <strong>deterministic replacements</strong> using a fixed-length constraint.</li>
	<li>Managing multiple sequential transformations on a single line.</li>
	<li>Supporting flexible file formats with different comment styles.</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Core Design</h2>
<ul>
	<li>Inline templating system:
		<ul>
			<li>Template logic is written inside <strong>comments on the same line</strong> as the target config.</li>
			<li>Avoids separate template files, keeping configuration self-contained.</li>
		</ul>
	</li>
	<li>Marker-Based Parsing:
		<ul>
			<li>Each file defines a <code>marker_char</code> (e.g: <code>//</code>, <code>;</code>, <code>%</code>).</li>
			<li>Template code is detected when the marker is repeated <code>marker_repetition_num</code> times.</li>
		</ul>
	</li>
	<li>Pattern-Based Replacement:
		<ul>
			<li>Uses <code>Regex</code> patterns to locate target text.</li>
			<li>Replaces matched segments with values defined in config (<code>keyword</code>).</li>
		</ul>
	</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Safety Model</h2>
<ul>
	<li>Enforces <strong>fixed-length replacements</strong>:
		<ul>
			<li>Replacement text must match the length of the original.</li>
			<li>Prevents unintended shifts in file structure or formatting.</li>
		</ul>
	</li>
	<li>Deterministic matching:
		<ul>
			<li>Each function operates on a specific match index.</li>
			<li>Multiple functions on a line act on successive matches (<code>n</code>th match based on their order).</li>
		</ul>
	</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Templating System</h2>
<ul>
	<li>Keyword-driven replacement:
		<ul>
			<li>A <code>keyword</code> maps to a value defined in the <code>TOML</code> config.</li>
			<li>Used to replace matched patterns in config files.</li>
		</ul>
	</li>
	<li>Execution Model:
		<ul>
			<li>Functions are parsed left-to-right on a line.</li>
			<li>Each function targets a specific match within the line.</li>
		</ul>
	</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Supported Functions</h2>
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
				<td>Find first text which matches <code>#&#xFEFF;[&#xFEFF;A&#8209;Za&#8209;z&#xFEFF;\&#xFEFF;d&#xFEFF;]&#xFEFF;{&#xFEFF;6&#xFEFF;}</code>, then find first match within this match based on <code>pattern_2</code> and replace that with the <code>keyword</code>.</td>
			</tr>
		</tbody>
	</table>
</div>
</section>

<hr class="major">

<section>
<h2>Configuration System</h2>
<ul>
	<li>Uses <code>TOML</code> for configuration.</li>
	<li>Located at <code>$XDG_CONFIG_HOME/dotfile-templater/config.toml</code>.</li>
	<li>Defines:
		<ul>
			<li>Active theme.</li>
			<li>Marker behaviour (<code>marker_char</code>, <code>marker_repetition_num</code>).</li>
			<li>Target files and their parsing rules.</li>
		</ul>
	</li>
	<li>File configuration:
		<ul>
			<li>Supports <strong>relative paths</strong> (from <code>.config/</code>) and absolute paths.</li>
			<li>Each file must define its own <code>marker_char</code>.</li>
		</ul>
	</li>
</ul>
</section>

<hr class="minor">

<section>
<h2>Example Config</h2>
<pre><code class="language-toml">theme = "purple-night"
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
name = "blue-banana"

primary_col = "#48FFD1"
secondary_col = "#4876FF"
tertiary_col = "#FF4876"
quaternary_col = "#FFD148"

bg_col = "#0A0A40"
bg_col_light = "#11116C"
fg_col = "#9999F8"</code></pre>
</section>

<hr class="major">

<section>
<h2>Theme System</h2>
<ul>
	<li>Themes are defined as named sections in the config.</li>
	<li>Each theme:
		<ul>
			<li>Must include a <code>name</code>.</li>
			<li>Can define arbitrary variables (e.g: colours, strings).</li>
		</ul>
	</li>
	<li>Enables:
		<ul>
			<li>Centralised value management.</li>
			<li>Easy switching between configurations (e.g: colour schemes).</li>
		</ul>
	</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Example Usage</h2>
<pre><code class="language-css">test = This will not be configured % Even if there are comments

$primary: rgb(9549FF); %%% @replace-pattern('rgb\([A-Za-z\d]{6}\)', primary_col, '[A-Za-z\d]{6}')
$secondary: #FF4958; %%% @replace-pattern-col(secondary_col, '[A-Za-z\d]{6}')
$tertiary: #B3FF49; #123456; %%% @replace-col(fg_col) @replace-col(bg_col)
$quaternary: #49FFF0; %%% @replace-col(quaternary_col)

$background: #1A1B26; %%% @replace-col(bg_col)
$background-lighter: #24283B; %%% @replace-col(bg_col_light)
$foreground: #A9B1D6; %%% @replace-col(fg_col)</code></pre>
</section>

<hr class="major">

<section>
<h2>Extensibility</h2>
<ul>
	<li>New template functions can be added without changing file format.</li>
	<li>Supports:
		<ul>
			<li>Arbitrary regex-based transformations.</li>
			<li>Custom themes with user-defined variables.</li>
			<li>Multiple file types with different comment syntaxes.</li>
		</ul>
	</li>
	<li>Suitable for:
		<ul>
			<li>Dotfile management.</li>
			<li>Theming systems.</li>
			<li>Automated config generation.</li>
		</ul>
	</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Current Limitations</h2>
<ul>
	<li>Fixed-length replacement constraint limits flexibility.</li>
	<li>Regex-based approach may be brittle for complex syntax.</li>
	<li>No validation for malformed template expressions.</li>
	<li>Limited error feedback for failed matches or replacements.</li>
</ul>
</section>
