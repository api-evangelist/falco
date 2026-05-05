---
title: "Blog: Introducing Falco 0.41.0"
url: "https://falco.org/blog/falco-0-41-0/"
date: "Thu, 29 May 2025 00:00:00 +0000"
author: ""
feed_url: "https://falco.org/blog/feed.xml"
---
<p>Dear Falco Community, today we are happy to announce the release of Falco 0.41.0!</p>
<p>This version brings several new features, performance enhancements, and bug fixes that streamline Falco’s detection capabilities.
During this release cycle, we merged more than 50 PRs on Falco and around 130 PRs for libs and drivers, version 0.21.0 and version 8.1.0, respectively. Thank you to our maintainers and contributors. This would not have been possible without your support and dedication!</p>
<p>To learn everything about the changes, read on!</p>
<h2 id="what-s-new-tl-dr">What’s new? TL;DR</h2>
<p><em>Key features:</em></p>
<ul>
<li><a href="https://falco.org/blog/falco-0-41-0/#reimplemented-container-engines-support">Reimplemented container engines support from scratch</a>;</li>
<li><a href="https://falco.org/blog/falco-0-41-0/#kubernetes-operator">A Kubernetes operator is taking shape</a>;</li>
<li>Falco's <code>config_files</code> configuration gained support to specify the merge strategy;</li>
<li>Modern eBPF driver is now capable of trying to load multiple programs for each event; consequently, <code>sendmmsg</code> and <code>recvmmsg</code> will now make use of <code>bpf_loop</code> eBPF helper where available, boosting their performances;</li>
<li>New <code>proc.aargs</code> field available, ie: a lookup for an ancestor args field;</li>
<li><code>proc.args</code> gained support for indexed access, to only check a certain argument;</li>
<li><code>json_include_output_fields</code> configuration key for Falco to control whether output fields are included in the JSON message;</li>
<li>Ongoing work to improve libs code modularity;</li>
</ul>
<p><em>Key fixes:</em></p>
<ul>
<li>Avoid kmod crashing when a CPU gets enabled at runtime;</li>
<li>Fixed Falco Prometheus metrics with multiple event sources enabled;</li>
<li>Fixed RPM packages evaluation of RPM scripts;</li>
<li><code>-o</code> options do now correctly override included <code>config_files</code>;</li>
</ul>
<div class="card card-sm pageinfo pageinfo-warning my-4">
<div class="card-body">
<div class="card-text">
<p>This release also comes with <a href="https://falco.org/blog/falco-0-41-0/#breaking-changes-and-deprecations">breaking changes</a> that you should be aware of before upgrading.</p>
</div>
</div>
</div>
<h2 id="major-features-and-improvements">Major features and improvements</h2>
<p>The 0.41.0 release contains a number of features and UX improvements. Here is a list of some of the key new capabilities.</p>
<h3 id="reimplemented-container-engines-support">Reimplemented container engines support</h3>
<p>In the Falco 0.41.0 release, the Falco team has completely revised its support for container engines.
Key improvements include:</p>
<ul>
<li>Container support is now a plugin;</li>
<li>The plugin will attach a listener to the engine's SDKs <code>onCreate</code> signal; since <code>onCreate</code> comes way before <code>onStart</code>, we have plenty of time to deliver the container's metadata before the first process in the container is even started;</li>
<li>For now, it is bundled within Falco to avoid breaking changes, but in the future, it will need to be downloaded through <code>falcoctl</code>;</li>
</ul>
<p>These changes should address all issues related to missing container metadata.</p>
<h3 id="kubernetes-operator">Kubernetes operator</h3>
<p>In Falco 0.41.0, we worked hard to create a Falco k8s operator: <a href="https://github.com/falcosecurity/falco-operator/">https://github.com/falcosecurity/falco-operator/</a>.
For now, this is considered a technical preview, but we will deliver a fully functional operator very soon. Expect more news in a new blog post!</p>
<h2 id="security-best-practices-improvements">Security best practices improvements</h2>
<p>We are grateful for the suggestions we received from security experts and adopters in our community, and so we implemented the following enhancements:</p>
<p>The modern eBPF probe will no longer store security sensitive settings in the <code>.bss</code> mmapable segment but will use dedicated maps instead. This is a security best practice because it prevents other processes running with elevated privileges from tampering with the map file descriptor, which would be harder to detect. We would like to thank <a href="https://github.com/mouadk">Mouad Kondah</a> for suggesting this change!</p>
<p>Falco will no longer consider rule files or contents of rule directories that do not have a <code>.yml</code>/<code>.yaml</code> extension. This prevents accidental processing of files that are not related to rules. We would like to thank our user <a href="https://github.com/tks98">Travis Smith</a> for suggesting this change!</p>
<h2 id="breaking-changes-and-deprecations">Breaking changes and deprecations ⚠️</h2>
<p>This version comes with breaking changes, mostly in the configuration interface.</p>
<h3 id="removed-command-line-options-and-equivalent-configuration-options">Removed command line options and equivalent configuration options</h3>
<p>We removed the already deprecated options <code>-S</code>/<code>--snaplen</code>, <code>-A</code>, and <code>-b</code>, and it is now possible to achieve the same result through the Falco configuration:</p>
<ul>
<li>for <code>-S/--snaplen</code>: <code>falco_libs.snaplen</code> config key;</li>
<li>for <code>-A</code>: <code>base_syscalls.all</code> config key;</li>
<li>for <code>-b</code>: <code>buffer_format_base64</code> config key;</li>
</ul>
<p>The configuration options for the container engines, added in 0.40.0, have been completely dropped in favor of the new plugin init configuration which can be found at <a href="https://github.com/falcosecurity/plugins/tree/main/plugins/container#configuration">https://github.com/falcosecurity/plugins/tree/main/plugins/container#configuration</a>.</p>
<p>You can find more information on breaking changes in the <a href="https://github.com/falcosecurity/falco/issues/3497">tracking issue</a>.</p>
<h3 id="behavior-changes">Behavior changes</h3>
<p>Falco will now only consider and consequently load rules whose name ends in <code>.yml</code> or <code>.yaml</code>.</p>
<h3 id="dropped-features">Dropped features</h3>
<p><code>syslog</code> related fields were dropped by libs, since they were unused.</p>
<p>Also, as a consequence of the new <code>container</code> plugin, some breaking changes had to take place:</p>
<ul>
<li>the musl build is inherently not able to load plugins; that means that it loses container metadata support;</li>
<li><code>falcosecurity_scap_n_containers</code> and <code>falcosecurity_scap_n_missing_container_images</code> metrics are now moved to the plugin, and their name now have the <code>falcosecurity_plugins_</code> prefix;</li>
<li><code>-pc</code> and <code>-pk</code> command line options are now ineffective; it is up to the container and k8smeta plugins to declare suggested fields to be used as output fields; consequently, <code>container_image=%container.image.repository</code> and <code>k8s_ns=%k8s.ns.name</code> changed their name to <code>container_image_repository=</code> and <code>k8s_ns_name=</code>;</li>
</ul>
<h3 id="deprecations">Deprecations</h3>
<p>In Falco 0.41.0, we have deprecated the following options:</p>
<ul>
<li><code>-p</code> cli flag; the only remaining user for it is gVisor, which will be ported to a plugin sooner or later and will then make use of the suggested output fields plugin API;</li>
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
<h2 id="what-s-next">What’s next?</h2>
<p>Falco is more mature with each release. Following its <a href="https://falco.org/blog/falco-graduation/">graduation</a>, we have published the <a href="https://falco.org/docs/roadmap/#road-to-falco-1-0-0">roadmap</a> for version 1.0.0, which is guiding us in the next steps. For the next release, you can expect more stability, a refined k8s operator, improved performance, and, as always, new detections and fixes.</p>
<h2 id="stay-connected">Stay connected</h2>
<p>Join us on social media and in our weekly community calls! It’s always great to have new members in the community, and we’re looking forward to hearing your feedback and ideas.</p>
<p>You can find all the most up-to-date information at <a href="https://falco.org/community/">https://falco.org/community/</a>.</p>
