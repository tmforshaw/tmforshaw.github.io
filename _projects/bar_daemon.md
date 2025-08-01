---
layout: project
title: "Bar Daemon"
description: "A daemon to control and monitor system resources: sending updates to listeners when resources are changed."
image: /assets/images/project1.jpg
github: https://github.com/tmforshaw/project1
---
<p>
  A daemon which uses <code>UnixStream</code> sockets to monitor system resources.
  It creates notifications via <code>dunstify</code> when resources change, as well as sending updates to all <code>UnixListener</code>, so they are always up to date.
</p>

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
				<td>Yes, at 5%, 15%, 20%, 30%</td>
				<td>Yes</td>
			</tr>
			<tr>
				<td>Bluetooth</td>
				<td><code>bluetooth</code> (<code>bluetoothctl</code>)</td>
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

<h2>Usage</h2>
<div class="table-wrapper">
  <table>
		<thead>
			<tr>
				<th>Command</th>
				<th>Output</th>
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
				<td><code>bar_daemon&nbsp;get&nbsp;[COMMAND]</code></td>
				<td>Receives all of the values for the given <code>COMMAND</code>, or all resources if none is supplied.</td>
			</tr>
			<tr>
				<td><code>bar_daemon&nbsp;set&nbsp;&lt;COMMAND&gt;&nbsp;&lt;RESOURCE&gt;&nbsp;&lt;VALUE&gt;</code></td>
				<td>Sets the value of a resource (e.g: volume)<code>COMMAND</code>, or all resources if none is supplied.</td>
			</tr>
		</tbody>
	</table>
</div>