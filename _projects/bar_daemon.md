---
layout: project
title: "Bar Daemon"
description: "A daemon to control and monitor system resources: sending updates to listeners when resources are changed."
image: /assets/images/bar_daemon_code.png
github: https://github.com/tmforshaw/bar_daemon
nav-menu: true
---
<p>
  A daemon which uses <code>tokio::UnixStream</code> sockets to monitor system resources.
  It creates notifications via <code>dunstify</code> when resources change, as well as sending updates to all the <code>UnixListener</code> processes, so they are up to date without needing to wait for polling.
	Default polling rate is <strong>1.5s</strong>, when each resource is updated or polled, the <em>entire</em> JSON for all resources is sent to all listeners.
</p>

<p>
	The CLI for this application is written using <a href="https://docs.rs/clap/latest/clap/">Clap</a> (Command Line Argument Parser), so each command and subcommand can be followed by <code>help</code> to learn more about it.
	Aliases are provided for all commands and subcommands, meaning they can be shortened.<br>
	<code>bar_daemon&nbsp;g&nbsp;v&nbsp;p</code>&nbsp;&#8801;&nbsp;<code>bar_daemon&nbsp;get&nbsp;volume&nbsp;percent</code>
</p>

<div style="text-align: center">
<image src="{{ 'assets/images/bar_daemon_in_action.png' | absolute_url }}" width="80%" title="bar_daemon in action"/>
</div>

<hr class="major" />

<h2>Monitored System Resources</h2>

<div class="table-wrapper">
  <table>
		<thead>
			<tr>
				<th>Resource</th>
				<th>Command Name</th>
				<th>Description</th>
				<th>Notifies</th>
				<th>Polled</th>
			</tr>
		</thead>
		<tbody>
			<tr>
				<td>Volume</td>
				<td><code>wpctl</code></td>
				<td>Used to get and set the volume and mute state, and get the current icon. Performs a conversion from the internal linear volume to a displayed perceptual (logarithmic) volume.</td>
				<td>Yes</td>
				<td>No</td>
			</tr>
			<tr>
				<td>Brightness</td>
				<td><code>brightnessctl</code></td>
				<td>Used to get and set the brightness of both the monitor and the keyboard, and get the current icon. Device IDs are hardcoded within the source code.</td>
				<td>Yes</td>
				<td>No</td>
			</tr>
			<tr>
				<td>Battery</td>
				<td><code>acpi</code></td>
				<td>Used to get the current battery percentage, charging state, time remaining, and icon.</td>
				<td>Yes, at 5%, 15%, 20%, and 30%</td>
				<td>Yes</td>
			</tr>
			<tr>
				<td>Bluetooth</td>
				<td><code>bluetooth</code><br>(<code>bluetoothctl</code>)</td>
				<td>Used to get and set the bluetooth state, and the current icon.</td>
				<td>Yes</td>
				<td>No</td>
			</tr>
			<tr>
				<td>Fan Speed</td>
				<td><code>asusctl</code></td>
				<td>Used to get and set the current Asus fan profile, and get the fan icon. Can be set as particular profile, or cycled using <code>next</code> or <code>prev</code>.</td>
				<td>Yes</td>
				<td>No</td>
			</tr>
			<tr>
				<td>Memory</td>
				<td><code>free</code></td>
				<td>Used to get the currently used bytes, total bytes, used percentage of ram, and the icon.</td>
				<td>No</td>
				<td>Yes</td>
			</tr>
		</tbody>
	</table>
</div>

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
				<td><code>bar_daemon&nbsp;daemon</code></td>
				<td>Runs the daemon which will respond to get, set, and listen commands. Outputs errors and logs</td>
			</tr>
			<tr>
				<td><code>bar_daemon&nbsp;listen</code></td>
				<td>Listens for changes in resources, and polled resources: receives JSON output of all resources on change.</td>
			</tr>
			<tr>
				<td><code>bar_daemon&nbsp;get&nbsp;[RESOURCE] [VALUE_TYPE]</code><br/>
				<code>bar_daemon&nbsp;get&nbsp;volume&nbsp;percent</code><br/>
				<code>bar_daemon&nbsp;get&nbsp;ram&nbsp;icon</code><br/>
				<code>bar_daemon&nbsp;get&nbsp;brightness</code>
				</td>
				<td>Gets the value of the <strong>resource</strong>'s <strong>value_type</strong>, or all values for that <strong>resource</strong> if no <strong>value_type</strong> is supplied.
				If no <strong>resource</strong> is provided, gets <strong>all</strong> values for <strong>all</strong> resources.</td>
			</tr>
			<tr>
				<td><code>bar_daemon&nbsp;set&nbsp;&lt;RESOURCE&gt;&nbsp;&lt;VALUE_TYPE&gt;&nbsp;&lt;VALUE&gt;</code><br/>
				<code>bar_daemon&nbsp;set&nbsp;volume&nbsp;percent&nbsp;-20</code><br/>
				<code>bar_daemon&nbsp;set&nbsp;fan&nbsp;profile&nbsp;next</code><br/>
				<code>bar_daemon&nbsp;set&nbsp;brightness&nbsp;monitor&nbsp;50</code><br/>
				</td>
				<td>Sets the value of the requested resource. For <code>brightness</code> and <code>volume</code> delta values can be provided.</td>
			</tr>
			<tr>
				<td><code>bar_daemon&nbsp;update&nbsp;[RESOURCE]&nbsp;[VALUE_TYPE]</code><br/>
				<code>bar_daemon&nbsp;update&nbsp;volume&nbsp;percent</code><br/>
				<code>bar_daemon&nbsp;update&nbsp;fan</code><br/>
				<code>bar_daemon&nbsp;update</code><br/>
				</td>
				<td>Equivalent to <code>set</code>, but doesn't explicitly change the value. Requests the value again and sends a message to all listeners (also creates a notification if value has changed).</td>
			</tr>
		</tbody>
	</table>

<hr class="major" />

<h2>Log to Linear</h2>
The volume is modified so that each percentage step <em>feels</em> like the same increase in volume, while keeping the range as [0-100].
This fixes the issue where <code>pipewire</code> has imperceptible volume at low percentages, but unbearably high volume at high percentages.
After trying various exponential values for the conversion between the perceptual volume and linear volume, I found that the best was actually <sup>1</sup>&frasl;<sub>2</sub>.
<br>
<br>

<pre><code>#[must_use]
pub fn linear_to_logarithmic(linear_percent: f64) -> f64 {
    if linear_percent <= 0.0 {
        return 0.0;
    }

    if linear_percent >= 100.0 {
        return 100.0;
    }

    let normalized = linear_percent / 100.0;
    100.0 * (normalized.sqrt())
}

#[must_use]
pub fn logarithmic_to_linear(log_percent: f64) -> f64 {
    if log_percent <= 0.0 {
        return 0.0;
    }

    if log_percent >= 100.0 {
        return 100.0;
    }

    let normalized = log_percent / 100.0;
    100.0 * normalized.powi(2)
}
</code></pre>
</div>