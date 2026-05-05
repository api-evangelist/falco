---
title: "Blog: Introducing Falco 0.42.0"
url: "https://falco.org/blog/falco-0-42-0/"
date: "Wed, 22 Oct 2025 00:00:00 +0000"
author: ""
feed_url: "https://falco.org/blog/feed.xml"
---
<p>Dear Falco Community, today we are happy to announce the release of Falco 0.42.0!</p>
<p>This release brings exciting new capabilities, including the capture feature, significant performance improvements, and important bug fixes that enhance Falco's capabilities.
During this release cycle, we merged:</p>
<ul>
<li>52 PRs on Falco, including 23 release note-worthy changes</li>
<li>110 PRs on Falco libs, including 47 release note-worthy changes</li>
<li>102 PRs on Falco drivers, including 29 release note-worthy changes</li>
</ul>
<p>We upgraded libs to version 0.22.1 and drivers to v9.0.0+driver. Thank you to our maintainers and contributors. This would not have been possible without your support and dedication!</p>
<p>To learn everything about the changes, read on!</p>
<h2 id="what-s-new-tl-dr">What's new? TL;DR</h2>
<p><em>Key features:</em></p>
<ul>
<li><a href="https://falco.org/blog/falco-0-42-0/#capture-recording-feature">Capture recording feature</a>;</li>
<li><a href="https://falco.org/blog/falco-0-42-0/#drop-enter-initiative">Drop enter initiative for performance</a>;</li>
<li><a href="https://falco.org/blog/falco-0-42-0/#plugin-event-schema-versioning">Plugin event schema validation</a>;</li>
<li><a href="https://falco.org/blog/falco-0-42-0/#thread-table-auto-purging-configuration">Thread table auto-purging configuration</a>;</li>
<li><a href="https://falco.org/blog/falco-0-42-0/#static-fields">Static fields</a>;</li>
</ul>
<p><em>Key fixes:</em></p>
<ul>
<li>Fix thread table memory leak when parsing vfork (or equivalent clone/clone3 with CLONE_VFORK) exit from the caller process;</li>
<li>Enable handling of multiple actions configured with <code>syscall_event_drops.actions</code>;</li>
<li>Disable dry-run restarts when Falco runs with config-watching disabled;</li>
<li>Fix abseil-cpp for Alpine build;</li>
<li>Fix detection sandbox containers for CRI and containerd runtimes (container plugin);</li>
<li>Stability improvements for container plugin and static linking of libgcc/libstdc++ for legacy compatibility;</li>
</ul>
<div class="card card-sm pageinfo pageinfo-warning my-4">
<div class="card-body">
<div class="card-text">
<p>This release also comes with <a href="https://falco.org/blog/falco-0-42-0/#breaking-changes-and-deprecations">breaking changes</a> that you should be aware of before upgrading.</p>
</div>
</div>
</div>
<h2 id="major-features-and-improvements">Major features and improvements</h2>
<p>The 0.42.0 release contains a new capture feature and significant performance improvements. Here is a list of the key new capabilities.</p>
<h3 id="capture-recording-feature">Capture recording feature</h3>
<p>Falco 0.42.0 introduces the new capture recording feature, now available at sandbox maturity. This capability allows Falco to generate <code>.scap</code> files whenever a detection rule is triggered automatically.</p>
<p>Each capture contains a detailed trace of system calls around the event, providing forensic-level visibility into what happened. The recordings can be opened directly in Stratoshark for Wireshark-style analysis of runtime behavior.</p>
<p>The capture system is fully configurable: you can enable global recording or tie captures to specific Falco rules for targeted runtime snapshots.</p>
<p>When targeting specific Falco rules (by setting <code>mode: rules</code>, as shown in the configuration below), users can modify individual rules to enable capture by adding <code>capture: true</code> and optionally <code>capture_duration</code> to specific rules.
For example:</p>
<div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span><span style="color: #bbb;"> </span>- <span style="color: #008000; font-weight: bold;">rule</span>:<span style="color: #bbb;"> </span>Suspicious File Access<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">desc</span>:<span style="color: #bbb;"> </span>Detect suspicious file access patterns<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">condition</span>:<span style="color: #bbb;"> </span>&gt;<span style="color: #b44; font-style: italic;">
</span></span></span><span style="display: flex;"><span><span style="color: #b44; font-style: italic;"> open_read and fd.name startswith "/etc/"</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">output</span>:<span style="color: #bbb;"> </span>&gt;<span style="color: #b44; font-style: italic;">
</span></span></span><span style="display: flex;"><span><span style="color: #b44; font-style: italic;"> Suspicious file access (user=%user.name command=%proc.cmdline file=%fd.name)</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">priority</span>:<span style="color: #bbb;"> </span>WARNING<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">capture</span>:<span style="color: #bbb;"> </span><span style="color: #a2f; font-weight: bold;">true</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">capture_duration</span>:<span style="color: #bbb;"> </span><span style="color: #666;">10000</span><span style="color: #bbb;"> </span><span style="color: #080; font-style: italic;"># Capture for 10 seconds when this rule triggers</span><span style="color: #bbb;">
</span></span></span></code></pre></div><p>This configuration will capture events for 10 seconds whenever the &quot;Suspicious File Access&quot; rule is triggered, overriding the default duration.</p>
<p>Find below the configuration snippet to enable the capture feature in <code>falco.yaml</code>:</p>
<div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">capture</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #080; font-style: italic;"># -- Set to true to enable event capturing.</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">enabled</span>:<span style="color: #bbb;"> </span><span style="color: #a2f; font-weight: bold;">false</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #080; font-style: italic;"># -- Prefix for capture files. Falco appends a timestamp and event number to ensure unique filenames.</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">path_prefix</span>:<span style="color: #bbb;"> </span>/tmp/falco<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #080; font-style: italic;"># -- Capture mode. Can be "rules" or "all_rules".</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">mode</span>:<span style="color: #bbb;"> </span>rules<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #080; font-style: italic;"># -- Default capture duration in milliseconds if not specified in the rule.</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">default_duration</span>:<span style="color: #bbb;"> </span><span style="color: #666;">5000</span><span style="color: #bbb;">
</span></span></span></code></pre></div><p><strong>Learn more at KubeCon + CloudNativeCon North America 2025:</strong></p>
<ul>
<li><a href="https://kccncna2025.sched.com/event/27d4o/project-lightning-talk-when-falco-spots-trouble-the-shark-swims-in-gerald-combs-falco-core-maintainer">Project Lightning Talk: When Falco Spots Trouble, The Shark Swims In</a> - Gerald Combs, Falco Core Maintainer</li>
<li><a href="https://kccncna2025.sched.com/event/27No0/beyond-the-clouds-falcos-ascent-in-performanc%5B%E2%80%A6%5Deep-visibility-leonardo-grasso-leonardo-di-giovanna-sysdig">Beyond the Cloud(s): Falco's Ascent in Performance and Deep Visibility</a> - Leonardo Grasso &amp; Leonardo Di Giovanna, Sysdig</li>
</ul>
<h3 id="drop-enter-initiative">Drop enter initiative</h3>
<p>We've just shipped a significant performance improvement: syscall enter events have been completely removed from our event pipeline.</p>
<p>In Falco, each system call traditionally used to generate two events: an enter event when syscall kernel processing starts (i.e., before its arguments are processed) and an exit event when the kernel processing completes. Now that we collect all relevant information on exit events, we can drop the generation and processing of enter events.</p>
<p>Nevertheless, for TOCTOU (Time-of-Check to Time-of-Use) mitigation, a few selected enter events are still monitored internally — their relevant data is captured and stored — but these events are no longer pushed downstream to the userspace processing pipeline.</p>
<p>By focusing solely on syscall exit events, we've nearly halved the number of events generated and processed by userspace, eliminating redundant data collection.
This reduces the Falco instrumentation overhead, improving workloads' performance up to 20% (by reducing syscall execution latency).
It also decreases Falco's CPU usage up to 30%, especially in high-syscall environments.</p>
<p>From a developer's perspective, this also removes ambiguity about where syscall parameters should be defined, streamlines event processing logic, and makes event handling code cleaner and easier to maintain.</p>
<p>Overall, you can expect better performance, leaner code, and a more predictable event model moving forward.</p>
<p>For more details, see:</p>
<ul>
<li><a href="https://github.com/falcosecurity/libs/pull/2068">Proposal</a></li>
<li><a href="https://github.com/falcosecurity/libs/issues/2427">[Tracking] Extend syscall exit events with syscall enter events parameters</a></li>
<li><a href="https://github.com/falcosecurity/libs/issues/2407">TOCTOU mitigation</a></li>
<li><a href="https://github.com/falcosecurity/libs/issues/2588">[Tracking] Drop syscall enter events</a></li>
</ul>
<h3 id="plugin-event-schema-versioning">Plugin event schema versioning</h3>
<p>Falco 0.42.0 introduces plugin event schema validation, enabling plugins to specify their compatible event schema version.</p>
<p>It provides an event schema validation system for syscall events consumed by plugins that offer parsing and/or field extraction capabilities, ensuring backward compatibility and clear error reporting for plugins that depend on specific Event Schema Versions.</p>
<p>If the plugin does not declare a required Schema Version, it is assumed to be compatible with 3.0.0, the initial major version when the plugin event schema validation was introduced.</p>
<p>The plugins should implement a new function of the Plugin API to declare the required schema version.
Find below the signature of the new API function:</p>
<pre tabindex="0"><code>// New plugin API functions for schema management
typedef struct {
...
// Event schema version check
//
// Return the minimum event schema version required by this plugin.
// Required: no
// Arguments:
// - s: the plugin state, returned by init(). Can be NULL.
// Return value: the event schema version string, in the following format:
// "&lt;major&gt;.&lt;minor&gt;.&lt;patch&gt;", e.g. "4.0.0".
// If the function is not implemented or NULL is returned, the plugin is assumed to be
// compatible with schema version 3.0.0.
//
const char* (*get_required_event_schema_version)(ss_plugin_t* s);
} plugin_api;
</code></pre><p>For more details, see:</p>
<ul>
<li><a href="https://github.com/falcosecurity/libs/blob/master/proposals/20250923-plugin-system-event-schema-versioning.md">Plugin system event schema versioning proposal</a></li>
</ul>
<h3 id="thread-table-auto-purging-configuration">Thread table auto-purging configuration</h3>
<p>We've added a few new <code>falco_libs</code> configurations for advanced users who want finer control over Falco's performance and resource usage.
It introduces tunable parameters for Falco's internal thread table, which tracks active threads:</p>
<ul>
<li><code>thread_table_size</code> defines the maximum number of entries.</li>
<li><code>thread_table_auto_purging_interval_s</code> controls how often stale threads are cleaned up.</li>
<li><code>thread_table_auto_purging_thread_timeout_s</code> sets how long inactive threads are kept before removal.</li>
</ul>
<p>These options let you balance memory efficiency, CPU usage, and state accuracy, with related metrics (<code>n_drops_full_threadtable</code>, <code>n_store_evts_drops</code>) available to guide tuning.</p>
<h3 id="static-fields">Static fields</h3>
<p>Falco 0.42.0 adds a new <code>static_fields</code> configuration object allowing users to add statically defined fields to the
Falco engine. The following example illustrates how to specify new static fields:</p>
<div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">static_fields</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">foo</span>:<span style="color: #bbb;"> </span>bar<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">foo2</span>:<span style="color: #bbb;"> </span>${bar2}<span style="color: #bbb;">
</span></span></span></code></pre></div><p>Notice that <code>foo2: ${bar2}</code> leverages the Falco's behavior of expanding env variables in config YAML.</p>
<p>After specifying them, these fields can be used in normal rule conditions, by prepending the <code>static.</code> prefix (e.g.:
<code>evt.type=open and static.foo=bar</code>).
Moreover, if <code>append_output.suggested_output</code> is true, they'll be automatically appended to each rule output, in the
form <code>static_foo=bar</code>.</p>
<p>For more details, see:</p>
<ul>
<li><a href="https://github.com/falcosecurity/falco/issues/3554">Feature request</a></li>
<li><a href="https://github.com/falcosecurity/falco/pull/3557">PR adding the feature</a></li>
</ul>
<h2 id="breaking-changes-and-deprecations">Breaking changes and deprecations ⚠️</h2>
<p>This version comes with breaking changes that you should be aware of before upgrading.</p>
<h3 id="event-direction-and-evt-dir-deprecation">Event direction and <code>evt.dir</code> deprecation</h3>
<p>Following the enter events initiative, the <code>evt.dir</code> field, as well as the concept of &quot;direction&quot;, have been deprecated in Falco <code>0.42.0</code> and will be removed in a future release.
Until field removal and since Falco <code>0.42.0</code>, specifying <code>evt.dir='&gt;'</code> will match nothing, while specifying <code>evt.dir='&lt;'</code> will match everything, with a warning informing the user about the deprecation.
Users are encouraged to get rid of any reference to <code>evt.dir</code>, as its presence will result in an error at rules loading time after its removal.</p>
<h3 id="plugin-api-changes">Plugin API changes</h3>
<ul>
<li>Old plugins consuming syscall events not declaring the required event schema version will be incompatible with Falco 0.42.0 and later.</li>
</ul>
<h3 id="deprecation-warnings">Deprecation warnings</h3>
<p>Falco 0.42.0 introduces several deprecation warnings to help users migrate to newer APIs:</p>
<ul>
<li><strong>evt.dir field deprecation</strong>: Rules using the deprecated <code>evt.dir</code> field will now generate warnings;</li>
<li><strong>Enter events drop stats</strong>: Prometheus metrics for enter events drop statistics have been deprecated;</li>
<li><strong>Configuration warnings</strong>: Enhanced warning system for deprecated configuration options;</li>
</ul>
<h2 id="try-it-out">Try it out</h2>
<p>Interested in trying out the new features? Use the resources below to get started.</p>
<ul>
<li><a href="https://falco.org/docs/getting-started/running/#docker">Container Images</a>
<ul>
<li><code>falco</code> (<a href="https://hub.docker.com/r/falcosecurity/falco">DockerHub</a>, <a href="https://gallery.ecr.aws/falcosecurity/falco">AWS ECR Gallery</a>)</li>
<li><code>falco-driver-loader</code> (<a href="https://hub.docker.com/r/falcosecurity/falco-driver-loader">DockerHub</a>, <a href="https://gallery.ecr.aws/falcosecurity/falco-driver-loader">AWS ECR Gallery</a>)</li>
</ul>
</li>
<li><a href="https://falco.org/docs/getting-started/installation/#centos-rhel">CentOS/Amazon Linux</a></li>
<li><a href="https://falco.org/docs/getting-started/installation/#debian">Debian/Ubuntu</a></li>
<li><a href="https://falco.org/docs/getting-started/installation/#suse">openSUSE</a></li>
<li><a href="https://falco.org/docs/getting-started/installation/#linux-binary">Linux binary package</a></li>
</ul>
<h2 id="stay-connected">Stay connected</h2>
<p>Join us on social media and in our community calls, held every other Wednesday! It's always great to have new members in the community, and we're looking forward to hearing your feedback and ideas.</p>
<p>You can find all the most up-to-date information at <a href="https://falco.org/community/">https://falco.org/community/</a>.</p>
