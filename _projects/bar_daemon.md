---
layout: project
title: "Bar Daemon"
description: "A daemon to control and monitor system resources: sending updates to listeners when resources are changed."
image: /assets/images/bar_daemon_code.png
show-image: false
link: https://github.com/tmforshaw/bar_daemon
nav-menu: true
date: 2026-03-03
tools:
  - Rust
  - Tokio
---

<div style="text-align: center">
	<image src="{{ 'assets/images/bar_daemon_in_action.png' | absolute_url }}" width="80%" title="bar_daemon in action"/>
</div>

<hr class="minor" />

<section>
<h2>Module Summary</h2>
<ul>
	<li>Built a system resource daemon in <code>Rust</code> using <code>tokio</code>.</li>
	<li>Uses <code>UnixStream</code> sockets for IPC between daemon and clients.</li>
	<li>Push-based update system which broadcasts state changes to all listeners.</li>
	<li>Sends desktop notificatons via <code>dunstify</code> when resources change.</li>
	<li>Default polling interval of <strong>2.0s</strong> for resources that require polling.</li>
</ul>
</section>

<hr class="minor" />

<section>
<h2>Technical Challenges</h2>
<ul>
	<li>Designing a push-based system to eliminate client-side polling.</li>
	<li>Maintaining consistent state across multiple concurrent listeners.</li>
	<li>Balancing polling and event-driven updates across different resource types.</li>
	<li>Broadcasting full system state efficiently without excessive overhead.</li>
	<li>Ensuring reliable IPC communication using <code>UnixStream</code> sockets.</li>
	<li>Separating data acquisition from transformation, for increased maintainability.</li>
	<li>Designing a modular system to support multiple interchangable data sources.</li>
</ul>
</section>

<hr class="major" />

<section>
<h2>Daemon Architecture</h2>
<ul>
	<li>Central daemon handles all resource monitoring and state changes.</li>
	<li>Pushes full <strong>JSON</strong> state to all connected listeners, triggered when any resource changes.</li>
	<li>Combines event-driven updates with interval-based polling.</li>
	<li>Listeners receive updates instantly without polling delays.</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Command Line Interface</h2>
<ul>
	<li>Built using <a href="https://docs.rs/clap/latest/clap/"><code>Clap</code></a> for structured command parsing.</li>
	<li>Supports commands: <code>daemon</code>, <code>listen</code>, <code>get</code>, and <code>set</code>.</li>
	<li>Full help support for all commands and subcommands.</li>
	<li>Provides aliases for faster command usage.</li>
	<li>Usage:
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
						<td>
							<ul>
								<li>Runs the daemon.</li>
								<li>Only allows one daemon to be active on the socket at a time.</li>
								<li>Will respond to <code>get</code>, <code>set</code>, and <code>listen</code> commands.</li>
								<li>Outputs errors and logs to log file.</li>
							</ul>
						</td>
					</tr>
					<tr>
						<td><code>bar_daemon&nbsp;listen</code></td>
						<td>
							<ul>
								<li>Listens for changes in resources.</li>
								<li>Receives <strong>JSON</strong> output of all resources when there's a change.</li>
							</ul>
						</td>
					</tr>
					<tr>
						<td><code>bar_daemon&nbsp;get&nbsp;[RESOURCE] [VALUE_TYPE]</code><br/>
						<code>bar_daemon&nbsp;get&nbsp;volume&nbsp;percent</code><br/>
						<code>bar_daemon&nbsp;get&nbsp;ram&nbsp;icon</code><br/>
						<code>bar_daemon&nbsp;get&nbsp;brightness</code>
						</td>
						<td>
							<ul>
								<li>Gets the value of the <strong>resource</strong>'s <strong>value_type</strong>.</li>
								<li>Gets all values for the <strong>resource</strong> if no <strong>value_type</strong> is supplied.</li>
								<li>If no <strong>resource</strong> is provided, gets <strong>all</strong> values for <strong>all</strong> resources.</li>
							</ul>
						</td>
					</tr>
					<tr>
						<td><code>bar_daemon&nbsp;set&nbsp;&lt;RESOURCE&gt;&nbsp;&lt;VALUE_TYPE&gt;&nbsp;&lt;VALUE&gt;</code><br/>
						<code>bar_daemon&nbsp;set&nbsp;volume&nbsp;percent&nbsp;-20</code><br/>
						<code>bar_daemon&nbsp;set&nbsp;fan&nbsp;profile&nbsp;next</code><br/>
						<code>bar_daemon&nbsp;set&nbsp;brightness&nbsp;monitor&nbsp;50</code><br/>
						</td>
						<td>
							<ul>
								<li>Sets the value of the requested <strong>resource</strong>'s <strong>value_type</strong>.</li>
								<li>For <code>brightness</code> and <code>volume</code> delta values can be provided:<br/><code>+10</code>, <code>-50</code>.</li>
								<li>For <code>brightness</code> percentages are accepted: <code>+50%</code>.</li>
							</ul>
						</td>
					</tr>
				</tbody>
			</table>
		</div>
	</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Resource System</h2>
<ul>
	<li>All resources implement a shared <code>Monitored</code> trait.</li>
	<li>Polling behaviour defined via <code>Polled</code> trait.</li>
	<li>Optional notification behaviour via <code>Notify</code> trait.</li>
	<li>Supports interchangeable backends (e.g: <code>wpctl</code> &rarr; <code>pactl</code>).</li>
	<li>Separates logic into:
		<ul>
			<li><code>source.rs</code> &rarr; data acquisition.</li>
			<li><code>value.rs</code> &rarr; data processing and representation.</li>
		</ul>
	</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Monitored Resources</h2>
<ul>
	<li>Mix of event-driven and polled resources, depending on resource acquisition method.</li>
	<li>Supports multiple system resources:
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
						<td>
							<ul>
								<li>Monitors volume state.</li>
								<li>Controls volume state.</li>
								<li>Perceptual scaling applied.</li>
							</ul>
						</td>
						<td>Yes</td>
						<td>No</td>
					</tr>
					<tr>
						<td>Brightness</td>
						<td><code>brightnessctl</code></td>
						<td>
							<ul>
								<li>Uses hard-coded device IDs for now.</li>
								<li>Monitors display and keyboard brightness.</li>
								<li>Controls display and keyboard brightness.</li>
							</ul>
						</td>
						<td>Yes</td>
						<td>No</td>
					</tr>
					<tr>
						<td>Battery</td>
						<td><code>acpi</code></td>
						<td>
							<ul>
								<li>Polls battery state.</li>
								<li>Provides threshold-based notifications.</li>
							</ul>
						</td>
						<td>Yes</td>
						<td>Yes</td>
					</tr>
					<tr>
						<td>Bluetooth</td>
						<td><code>bluetooth</code><br>(<code>bluetoothctl</code>)</td>
						<td>
							<ul>
								<li>Monitors bluetooth state.</li>
								<li>Controls bluetooth state.</li>
							</ul>
						</td>
						<td>Yes</td>
						<td>No</td>
					</tr>
					<tr>
						<td>Fan Speed</td>
						<td><code>asusctl</code></td>
						<td>
							<ul>
								<li>Monitors fan profile.</li>
								<li>Controls fan profile.</li>
							</ul>
						</td>
						<td>Yes</td>
						<td>No</td>
					</tr>
					<tr>
						<td>Memory</td>
						<td><code>free</code></td>
						<td>
							<ul>
								<li>Polls system <strong>RAM</strong> usage.</li>
							</ul>
						</td>
						<td>No</td>
						<td>Yes</td>
					</tr>
				</tbody>
			</table>
		</div>
	</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Data Flow</h2>
<ul>
	<li>Resource updates trigger full-state <strong>JSON</strong> broadcast.</li>
	<li>Listeners receive complete system snapshot on each update.</li>
	<li>Simplifies client logic by avoiding partial updates.</li>
	<li>Ensures all clients remain synchronised.</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Volume Scaling (Perceptual Scaling)</h2>
<ul>
	<li>Implements perceptual (logarithmic) volume scaling.</li>
	<li>Fixes non-linear behaviour of <code>pipewire</code> volume.</li>
	<li>Uses square root mapping for consistent perceived increments.</li>
	<li>Maintains user-facing range of <code>[0-100]</code>.</li>
</ul>
</section>

<hr class="major">

<section>
<h2>Current Limitations</h2>
<ul>
	<li>Brightness device IDs are currently hardcoded.</li>
	<li>Full-state broadcasts may be inefficient for high-frequency updates.</li>
</ul>
</section>
