---
title: "Blog: Introducing Falco 0.43.0"
url: "https://falco.org/blog/falco-0-43-0/"
date: "Mon, 26 Jan 2026 00:00:00 +0000"
author: ""
feed_url: "https://falco.org/blog/feed.xml"
---
<p>Dear Falco Community, we are happy to announce the release of Falco 0.43.0 today!</p>
<p>This is a stabilization release that consolidates the changes introduced in 0.42.0, including
the <a href="https://falco.org/blog/falco-0-42-0/#drop-enter-initiative">drop-enter initiative</a> and
the <a href="https://falco.org/blog/falco-0-42-0/#capture-recording-feature">capture recording feature</a>. It also introduces
several deprecations to improve maintainability and fixes minor issues across falcoctl, plugins, and libs.</p>
<p>During this release cycle, we merged:</p>
<ul>
<li>31 PRs on Falco, including 11 release note-worthy changes</li>
<li>48 PRs on Falco libs, including 17 release note-worthy changes</li>
<li>8 PRs on Falco drivers, including 3 release note-worthy changes</li>
</ul>
<p>We upgraded libs to version <code>0.23.1</code> and drivers to <code>9.1.0+driver</code>. Thank you to our maintainers and contributors. This
would not have been possible without your support and dedication!</p>
<p>To learn everything about the changes, read on!</p>
<h2 id="what-s-new-tl-dr">What's new? TL;DR</h2>
<ul>
<li><a href="https://falco.org/blog/falco-0-43-0/#deprecations">Deprecations</a></li>
<li><a href="https://falco.org/blog/falco-0-43-0/#gpg-key-rotation">GPG key rotation</a></li>
<li><a href="https://falco.org/blog/falco-0-43-0/#container-plugin-improvements">Container plugin improvements</a></li>
<li><a href="https://falco.org/blog/falco-0-43-0/#falcoctl-tweaks-and-improvements">Falcoctl tweaks and improvements</a></li>
</ul>
<p><em>Key fixes:</em></p>
<ul>
<li><a href="https://falco.org/blog/falco-0-43-0/#evtargfilename-field-reintroduction"><code>evt.arg.filename</code> field reintroduction</a></li>
<li><a href="https://falco.org/blog/falco-0-43-0/#falcoctl-signature-verification-fixes">Falcoctl signature verification fixes</a></li>
<li>overflow and NULL pointer dereferences fixes for the <code>container</code> plugin, shipped with <code>plugins/container/0.6.1</code></li>
<li>race condition fix for the <code>k8smeta</code> plugin, shipped with <code>plugins/k8smeta/0.4.1</code></li>
</ul>
<div class="card card-sm pageinfo pageinfo-warning my-4">
<div class="card-body">
<div class="card-text">
<p>This release also comes with <a href="https://falco.org/blog/falco-0-43-0/#breaking-changes-and-deprecations">breaking changes</a> that you should be aware of before
upgrading.</p>
</div>
</div>
</div>
<h2 id="latest-updates">Latest updates</h2>
<h3 id="deprecations">Deprecations</h3>
<p>In Falco 0.43.0, we are announcing the deprecation of three significant components to streamline the project, reduce
maintenance burden, and focus on modern, more efficient alternatives. All these components are stable, and considering
that the deprecation is first enforced in this version, they could be removed at any future version starting from
0.44.0.</p>
<h4 id="legacy-ebpf-probe-deprecation">Legacy eBPF probe deprecation</h4>
<p>The &quot;legacy&quot; eBPF probe (configured via <code>engine.kind=ebpf</code>) was the original eBPF implementation in Falco. It required
compiling a specific probe for each kernel version, often necessitating the dynamic usage of the <code>falco-driver-loader</code>
or pre-built drivers. The Modern eBPF probe (<code>engine.kind=modern_ebpf</code>), which leverages CO-RE (Compile Once – Run
Everywhere), has reached maturity and feature parity. It offers superior stability, portability (no need to compile
drivers on the fly), flexibility and performance. Maintaining two eBPF drivers splits engineering effort and complicates
the codebase. Users currently using the legacy eBPF probe are strongly encouraged to switch to the Modern eBPF probe by
setting <code>engine.kind=modern_ebpf</code> in their <code>falco.yaml</code>, or to <code>engine.kind=kmod</code> if the used kernel doesn't provide
support for the modern eBPF probe.</p>
<p>See <a href="https://github.com/falcosecurity/falco/blob/master/proposals/20251215-legacy-bpf-grpc-output-gvisor-engine-deprecation.md#legacy-ebpf-probe-deprecation">the relevant section</a>
of the deprecation proposal for the detailed motivation behind the deprecation.</p>
<h4 id="gvisor-deprecation">gVisor deprecation</h4>
<p>The gVisor engine is a dedicated, internal C++ implementation designed to monitor system calls from gVisor sandboxes
leveraging events coming from gVisor itself through gRPC. There is evidence that this engine is little used. Moreover,
gVisor doesn't provide all information required to build all supported event types, indeed resulting in a system call
source not completely equivalent to the ones provided by drivers. Finally, it requires libs being dependent on protobuf,
this latter introducing a non-negligible build time overhead and maintainability burden.</p>
<p>See <a href="https://github.com/falcosecurity/falco/blob/master/proposals/20251215-legacy-bpf-grpc-output-gvisor-engine-deprecation.md#gvisor-libscap-engine-deprecation">the relevant section</a>
of the deprecation proposal for the detailed motivation behind the deprecation.</p>
<h4 id="grpc-output-and-server-deprecation">gRPC output and server deprecation</h4>
<p>The gRPC output was implemented to allow external consumers to subscribe to a stream of Falco security alerts over a
gRPC connection. It was notably utilized by tools like the <code>event-generator</code> (in test mode) and custom integrations
requiring a streaming API for alerts. The gRPC output and the gRPC server embedded in Falco add substantial complexity
to the core codebase, including dependencies on specific protobuf and gRPC framework versions in Falco and libs. Over
time, it has become clear that the community prefers standard, widespread integration patterns for alert consumption -
primarily HTTP and the ecosystem enabled by Falcosidekick. Users consuming alerts via gRPC should migrate to the HTTP
output or use Falcosidekick to forward events to their destination of choice.</p>
<p>See <a href="https://github.com/falcosecurity/falco/blob/master/proposals/20251215-legacy-bpf-grpc-output-gvisor-engine-deprecation.md#grpc-output-deprecation">the relevant section</a>
of the deprecation proposal for the detailed motivation behind the deprecation.</p>
<h3 id="gpg-key-rotation">GPG key rotation</h3>
<p>In anticipation of the previous GPG key's expiration in January 2026, we have rotated the GPG key used to sign the
official RPM and DEB packages. Pre-existing Falco installations (installed via apt or yum before the rotation) must
manually import the new GPG key. Failure to do so may result in errors during package updates or verification failures.
Please follow the &quot;Trust the falcosecurity GPG key&quot; step in the official documentation for your package manager:</p>
<ul>
<li>apt (Debian/Ubuntu): <a href="https://falco.org/docs/setup/packages/#install-with-apt">Install with apt</a></li>
<li>yum/dnf (CentOS/RHEL/Fedora): <a href="https://falco.org/docs/setup/packages/#install-with-yum">Install with yum</a></li>
</ul>
<p>Notice that new installations following the current documentation will automatically receive the updated key bundle and
do not require additional steps.</p>
<p>For more details
see <a href="https://github.com/falcosecurity/falco/issues/3750">[TRACKING] [deadline 2026-01-17] Rotate public GPG key for RPM/DEB package signing</a>.</p>
<h3 id="container-plugin-improvements">Container plugin improvements</h3>
<p>The <code>container</code> plugin, which extracts metadata from container runtimes to enrich Falco events, includes important
updates in version <code>0.6.1</code> to enhance its API capabilities and performance. This release exposes <code>container.id</code>,
<code>container.image</code>, <code>container.name</code>, and <code>container.type</code> through the table API and adds comprehensive logging across
all engines, while also preventing allocations by extensively using zero-allocation tools offered by the C++ (like
<code>std::string_view</code>) and avoiding reflex matcher allocations during resolve operations.</p>
<h3 id="falcoctl-tweaks-and-improvements">Falcoctl tweaks and improvements</h3>
<h4 id="follow-polling-interval-increase-to-1-week"><code>follow</code> polling interval increase to 1 week</h4>
<p>About three years ago, we started distributing Falco artifacts (rules files and plugins) via ghcr.io, and later added
automatic rule updates in falcoctl with a 6h check interval. With years of data now, it’s clear we don’t need checks
that frequent: our rule updates happen far less often. Moreover, due to the growth of Falco adoption, these frequent
checks are now hitting ghcr.io rate limit. These two reasons drove the decision to increase the artifact follow interval
from 6h to 1 week.</p>
<p>For more details
see <a href="https://github.com/falcosecurity/falco/pull/3757">chore(scripts/falcoctl): increase follow interval to 1 week</a>
and <a href="https://github.com/falcosecurity/charts/blob/master/charts/falco/CHANGELOG.md#v702">Falco's Helm chart changelog</a>.</p>
<h4 id="dependency-resolution-improvements">Dependency resolution improvements</h4>
<p>The artifact installation logic has been reworked to handle dependencies and references correctly. Previously,
dependencies could be duplicated or incorrectly resolved, and signature verification was skipped for full registry
references. Now dependencies are properly deduplicated, all refs are correctly resolved, and <strong>signatures are verified
for all resolved dependencies</strong>, not just the top-level artifacts. This provides end-to-end verification of the entire
dependency chain.</p>
<p>For more details
see <a href="https://github.com/falcosecurity/falcoctl/issues/868">Inefficient deduplication logic and incorrect input handling for dependency resolution</a></p>
<h4 id="support-for-cosign-v3">Support for cosign v3</h4>
<p>Falcoctl now supports <strong>Cosign v3 bundle format</strong> for signature verification. This is the new standard for signing OCI
artifacts, replacing the legacy <code>.sig</code> tag format.</p>
<p><strong>What this means for you:</strong></p>
<ul>
<li>Artifacts signed with cosign v3 are now fully supported</li>
<li>Backward compatibility with cosign v2 signatures is maintained</li>
</ul>
<p>For more details see <a href="https://github.com/falcosecurity/falcoctl/pull/880">feat: Upgrade to Cosign v3 with Bundle Format</a></p>
<h2 id="key-fixes">Key fixes</h2>
<h3 id="evt-arg-filename-field-reintroduction"><code>evt.arg.filename</code> field reintroduction</h3>
<p>As part of the recent &quot;drop enter&quot; optimization initiative (which removed enter events for most syscalls to improve
performance), the filename argument - historically available only in the enter event for <code>execve</code> and <code>execveat</code> - was
inadvertently made unavailable. This caused a regression where specific context, such as the exact path provided to the
syscall (e.g., a symlink path versus the resolved binary path), was lost in the remaining exit event.</p>
<p>In Falco 0.43.0 (via libs <code>0.23.0</code>), this has been fixed. The filename argument is now correctly populated in the exit
events for these syscalls. Users can once again access this data using the evt.arg.filename field in their rules,
ensuring that the critical execution context is preserved without needing the deprecated enter events.</p>
<p>For more details
see <a href="https://github.com/falcosecurity/libs/issues/2709">Missing &quot;filename&quot; argument to execve syscall in libscap 0.22.x</a>.</p>
<h3 id="falcoctl-signature-verification-fixes">Falcoctl signature verification fixes</h3>
<h4 id="signature-verification-fix-for-full-reference-artifacts">Signature verification fix for full reference artifacts</h4>
<p>Fixed an issue where <strong>signature verification</strong> was skipped for artifacts specified with a full registry reference (
e.g., <code>ghcr.io/falcosecurity/plugins/plugin/container:0.4.1</code>). Now all artifacts are verified regardless of how they are
referenced.</p>
<h4 id="signature-verification-fix-for-authenticated-registries">Signature verification fix for authenticated registries</h4>
<p>Signature verification now works correctly on <strong>private/authenticated registries</strong>. Previously, verification would fail
with authentication errors even though the artifact pull succeeded, and credentials were not being passed to the
signature verification component.</p>
<p><strong>Supported authentication methods:</strong></p>
<ul>
<li>Basic auth (Docker credentials)</li>
<li>OAuth2 client credentials</li>
<li>GCP Workload Identity (for GKE deployments)</li>
</ul>
<p>For more details
see <a href="https://github.com/falcosecurity/falcoctl/pull/891">fix(signature): pass registry credentials to cosign for signature verification</a></p>
<h2 id="breaking-changes-and-deprecations">Breaking changes and deprecations</h2>
<p>This version includes breaking changes you should be aware of before upgrading.</p>
<h3 id="bump-drivers-minimum-required-kernel-version-to-3-10">Bump drivers minimum required kernel version to <code>3.10</code></h3>
<p>Falco 0.43.0 introduces a breaking change regarding the Falco drivers. Starting with drivers version <code>9.1.0+driver</code>, the
minimum required Linux kernel version has been bumped to <code>3.10</code>. In practice, this only affects the <code>kmod</code> driver and
means that the kernel module will explicitly fail to compile on kernels older than <code>3.10</code>. This choice is motivated by
the fact that even Linux <code>3.10</code> is a 12-year-old kernel, and its support ended in 2017: maintaining support for older
kernels is a maintenance burden and limits progress. This change enables the team to focus on modernizing the codebase
and improving stability for current environments.</p>
<h3 id="deprecation-warnings">Deprecation warnings</h3>
<p>Falco 0.43.0 introduces several deprecation warnings to help users migrate to newer components:</p>
<ul>
<li><strong>Legacy eBPF probe deprecation</strong>: using the legacy eBPF probe (<code>engine.kind=ebpf</code>) will now generate warnings</li>
<li><strong>gVisor engine deprecation</strong>: using the gVisor engine (<code>engine.kind=gvisor</code>) will now generate warnings</li>
<li><strong>gRPC deprecation</strong>: using the gRPC output or the gRPC server (<code>grpc_output.enabled=true</code> or <code>grpc.enabled=true</code>),
will now generate warnings</li>
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
<p>Join us on social media and in our community calls, held every other Wednesday! It's always great to have new members in
the community, and we're looking forward to hearing your feedback and ideas.</p>
<p>You can find all the most up-to-date information at <a href="https://falco.org/community/">https://falco.org/community/</a>.</p>
