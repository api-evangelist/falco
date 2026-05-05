---
title: "Blog: Introducing Falco Operator 0.2.0"
url: "https://falco.org/blog/falco-operator-0-2-0/"
date: "Mon, 23 Mar 2026 00:00:00 +0000"
author: ""
feed_url: "https://falco.org/blog/feed.xml"
---
<p>Dear Falco Community, today we are excited to announce the release of <strong>Falco Operator 0.2.0</strong>, the first production-ready release of the <a href="https://github.com/falcosecurity/falco-operator">Kubernetes operator for Falco</a>!</p>
<p>Since the <a href="https://falco.org/blog/falco-0-41-0/#kubernetes-operator">technical preview announced with Falco 0.41.0</a>, we have been working hard to make the operator robust, extensible, and ready for real-world environments. This release brings a redesigned API, a new Component controller for managing the Falco ecosystem, new artifact management capabilities, enhanced observability, and a significantly improved operational model, all grounded in Kubernetes-native patterns.</p>
<p>We merged <strong>58 commits</strong> since v0.1.1, delivering major new features, 10 bug fixes, and comprehensive architectural improvements. Thank you to all our contributors and the community for your feedback along the way!</p>
<p>Going forward, the Falco Operator is the recommended way to deploy and manage Falco on Kubernetes. While the existing <a href="https://falco.org/docs/setup/kubernetes/">Helm chart</a> remains fully supported, we plan to transition to the operator as the standard deployment method. More details on the transition timeline will follow in a future announcement.</p>
<p>To learn everything about the changes, read on!</p>
<h2 id="what-s-new-tl-dr">What's new? TL;DR</h2>
<p><em>Key features:</em></p>
<ul>
<li><a href="https://falco.org/blog/falco-operator-0-2-0/#ecosystem-components">Ecosystem components</a> - deploy Falcosidekick, Falcosidekick UI, and k8s-metacollector as managed components</li>
<li><a href="https://falco.org/blog/falco-operator-0-2-0/#configmap-support-for-rules-and-configuration">ConfigMap support</a> for rules and configuration, alongside OCI artifacts and inline definitions</li>
<li><a href="https://falco.org/blog/falco-operator-0-2-0/#structured-api-types">Structured API types</a> for inline rules and configuration - YAML objects instead of strings</li>
<li><a href="https://falco.org/blog/falco-operator-0-2-0/#redesigned-oci-artifact-api">Redesigned OCI artifact API</a> with separate image and registry configuration</li>
<li><a href="https://falco.org/blog/falco-operator-0-2-0/#reference-tracking-with-finalizers">Reference tracking with finalizers</a> to prevent accidental deletion of Secrets and ConfigMaps</li>
<li><a href="https://falco.org/blog/falco-operator-0-2-0/#enhanced-observability">Enhanced observability</a> with Kubernetes events and status conditions across all controllers</li>
<li><a href="https://falco.org/blog/falco-operator-0-2-0/#update-strategy-support">Update strategy support</a> for DaemonSet and Deployment modes</li>
<li><a href="https://falco.org/blog/falco-operator-0-2-0/#server-side-apply">Server-Side Apply migration</a> for safer, conflict-free reconciliation</li>
</ul>
<p><em>Key fixes:</em></p>
<ul>
<li>Plugin <code>initConfig</code> now supports nested configuration objects</li>
<li>RBAC compatibility with Kubernetes 1.32+</li>
<li>Spurious update prevention via managed fields comparison</li>
<li>Correct event recording with node-level attribution</li>
</ul>
<div class="card card-sm pageinfo pageinfo-warning my-4">
<div class="card-body">
<div class="card-text">
<p>This release comes with <a href="https://falco.org/blog/falco-operator-0-2-0/#breaking-changes">breaking changes</a> that require updating your existing custom resources before upgrading. Please read the <a href="https://github.com/falcosecurity/falco-operator/blob/main/docs/migration-guide.md">migration guide</a> carefully before proceeding.</p>
</div>
</div>
</div>
<h2 id="the-road-to-production-readiness">The road to production readiness</h2>
<p>When we introduced the Falco Operator as a technical preview in Falco 0.41.0, the vision was clear: provide a Kubernetes-native way to deploy and manage Falco that goes beyond what Helm charts and static manifests can offer. Since then, every aspect of the operator has been refined.</p>
<p>The reconciliation logic now uses Server-Side Apply for conflict-free updates. Status conditions follow Kubernetes conventions (<code>Programmed</code>, <code>ResolvedRefs</code>, <code>Available</code>, <code>Reconciled</code>) so that standard tooling and dashboards can monitor operator health out of the box. Finalizers protect referenced resources from accidental deletion. And the new Component controller lays the groundwork for managing the entire Falco ecosystem from a single operator.</p>
<p>Version 0.2.0 is the result of this effort, a release we are confident in for production environments.</p>
<h2 id="major-features-and-improvements">Major features and improvements</h2>
<h3 id="ecosystem-components">Ecosystem components</h3>
<p>The new <code>Component</code> custom resource (<code>instance.falcosecurity.dev/v1alpha1</code>) enables the operator to deploy and manage the full Falco ecosystem from a single control plane. Three component types are supported:</p>
<table>
<thead>
<tr>
<th>Type</th>
<th>Component</th>
<th>What it does</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>metacollector</code></td>
<td><a href="https://github.com/falcosecurity/k8s-metacollector">k8s-metacollector</a></td>
<td>Centralized Kubernetes metadata for Falco instances</td>
</tr>
<tr>
<td><code>falcosidekick</code></td>
<td><a href="https://github.com/falcosecurity/falcosidekick">Falcosidekick</a></td>
<td>Fan-out daemon - routes Falco events to 70+ integrations (Slack, Elasticsearch, S3, Kafka, and more)</td>
</tr>
<tr>
<td><code>falcosidekick-ui</code></td>
<td><a href="https://github.com/falcosecurity/falcosidekick-ui">Falcosidekick UI</a></td>
<td>Web dashboard for real-time event visualization</td>
</tr>
</tbody>
</table>
<p>Deploying Falcosidekick is as simple as:</p>
<div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">apiVersion</span>:<span style="color: #bbb;"> </span>instance.falcosecurity.dev/v1alpha1<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">kind</span>:<span style="color: #bbb;"> </span>Component<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">metadata</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">name</span>:<span style="color: #bbb;"> </span>sidekick<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">spec</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">component</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">type</span>:<span style="color: #bbb;"> </span>falcosidekick<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">replicas</span>:<span style="color: #bbb;"> </span><span style="color: #666;">2</span><span style="color: #bbb;">
</span></span></span></code></pre></div><p>The operator handles the Deployment, Service, ServiceAccount, and RBAC automatically. Each component type ships with production-ready defaults (health probes, security context, resource profiles) that can be fully customized via <code>podTemplateSpec</code>.</p>
<p>For Falcosidekick UI, note that an external Redis instance is required. If Redis is not available, the pod stays in <code>Init:0/1</code> state, the built-in <code>wait-redis</code> init container blocks until Redis is reachable. See the <a href="https://github.com/falcosecurity/falco-operator/blob/main/docs/crds/component.md">component documentation</a> for setup examples including a bundled Redis StatefulSet.</p>
<p>As part of this work, the internal controller structure was reorganized under <code>controllers/instance/</code> with shared reconciliation logic extracted into reusable packages, improving maintainability and consistency across all instance-level controllers.</p>
<h3 id="configmap-support-for-rules-and-configuration">ConfigMap support for rules and configuration</h3>
<p>Rulesfile and Config resources can now source their content from Kubernetes ConfigMaps, in addition to OCI artifacts and inline definitions. This provides a familiar, Git-friendly workflow for teams that manage configuration through standard Kubernetes tooling.</p>
<p><strong>Rulesfile from a ConfigMap:</strong></p>
<div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">apiVersion</span>:<span style="color: #bbb;"> </span>artifact.falcosecurity.dev/v1alpha1<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">kind</span>:<span style="color: #bbb;"> </span>Rulesfile<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">metadata</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">name</span>:<span style="color: #bbb;"> </span>custom-rules<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">spec</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">configMapRef</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">name</span>:<span style="color: #bbb;"> </span>my-rules-configmap<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">priority</span>:<span style="color: #bbb;"> </span><span style="color: #666;">50</span><span style="color: #bbb;">
</span></span></span></code></pre></div><p><strong>Config from a ConfigMap:</strong></p>
<div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">apiVersion</span>:<span style="color: #bbb;"> </span>artifact.falcosecurity.dev/v1alpha1<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">kind</span>:<span style="color: #bbb;"> </span>Config<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">metadata</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">name</span>:<span style="color: #bbb;"> </span>custom-config<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">spec</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">configMapRef</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">name</span>:<span style="color: #bbb;"> </span>my-config-configmap<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">priority</span>:<span style="color: #bbb;"> </span><span style="color: #666;">50</span><span style="color: #bbb;">
</span></span></span></code></pre></div><p>The referenced ConfigMap must contain the content under a key named <code>rules.yaml</code> (for Rulesfile) or <code>config.yaml</code> (for Config). All three sources (OCI, inline, and ConfigMap) can be combined in a single resource when needed.</p>
<h3 id="structured-api-types">Structured API types</h3>
<p>The <code>inlineRules</code> field in the Rulesfile CRD and the <code>config</code> field in the Config CRD are now structured YAML objects instead of plain strings. This enables proper validation, better editor support, and eliminates the need for YAML-in-YAML escaping.</p>
<p><strong>Before (v0.1.x):</strong></p>
<div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">spec</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">config</span>:<span style="color: #bbb;"> </span>|-<span style="color: #b44; font-style: italic;">
</span></span></span><span style="display: flex;"><span><span style="color: #b44; font-style: italic;"> engine:
</span></span></span><span style="display: flex;"><span><span style="color: #b44; font-style: italic;"> kind: modern_ebpf</span><span style="color: #bbb;">
</span></span></span></code></pre></div><p><strong>After (v0.2.0):</strong></p>
<div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">spec</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">config</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">engine</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">kind</span>:<span style="color: #bbb;"> </span>modern_ebpf<span style="color: #bbb;">
</span></span></span></code></pre></div><p>The same applies to <code>inlineRules</code>: rules are now defined as structured YAML lists rather than pipe-literal strings.</p>
<h3 id="redesigned-oci-artifact-api">Redesigned OCI artifact API</h3>
<p>The OCI artifact reference model has been completely redesigned for clarity and extensibility. The previous flat <code>reference</code> and <code>pullSecret</code> fields are replaced with a structured <code>image</code> and <code>registry</code> model.</p>
<p><strong>Before (v0.1.x):</strong></p>
<div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">spec</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">ociArtifact</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">reference</span>:<span style="color: #bbb;"> </span>ghcr.io/falcosecurity/rules/falco-rules:latest<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">pullSecret</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">secretName</span>:<span style="color: #bbb;"> </span>my-secret<span style="color: #bbb;">
</span></span></span></code></pre></div><p><strong>After (v0.2.0):</strong></p>
<div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">spec</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">ociArtifact</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">image</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">repository</span>:<span style="color: #bbb;"> </span>falcosecurity/rules/falco-rules<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">tag</span>:<span style="color: #bbb;"> </span>latest<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">registry</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">name</span>:<span style="color: #bbb;"> </span>ghcr.io<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">auth</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">secretRef</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">name</span>:<span style="color: #bbb;"> </span>my-secret<span style="color: #bbb;">
</span></span></span></code></pre></div><p>This separation makes the API more explicit and enables per-registry TLS configuration, plain HTTP support, and a consistent credential model. See the <a href="https://github.com/falcosecurity/falco-operator/blob/main/docs/migration-guide.md">migration guide</a> for details on updating your resources.</p>
<h3 id="reference-tracking-with-finalizers">Reference tracking with finalizers</h3>
<p>The operator now adds finalizers to Secrets and ConfigMaps that are referenced by artifact resources. This prevents accidental deletion of credentials or configuration data that would break Falco deployments. When a referenced resource is deleted, the operator blocks the deletion until all referencing artifacts are updated or removed.</p>
<h3 id="enhanced-observability">Enhanced observability</h3>
<p>All controllers now emit Kubernetes events for significant operations: artifact creation, updates, removals, and priority changes. Events include the node name for artifact controllers, making it straightforward to trace which operations happened on which nodes.</p>
<p>Status conditions have been overhauled to follow Kubernetes conventions:</p>
<ul>
<li><strong>Artifact resources</strong> report <code>Programmed</code> (whether the artifact is successfully applied) and <code>ResolvedRefs</code> (whether referenced ConfigMaps/Secrets exist)</li>
<li><strong>Falco instances</strong> report <code>Reconciled</code> and <code>Available</code></li>
<li>All artifact CRDs now include <code>printcolumns</code> for readable <code>kubectl get</code> output</li>
</ul>
<h3 id="update-strategy-support">Update strategy support</h3>
<p>The Falco CRD now accepts update strategy configuration for both deployment modes:</p>
<div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span><span style="color: #080; font-style: italic;"># DaemonSet mode</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">spec</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">type</span>:<span style="color: #bbb;"> </span>DaemonSet<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">updateStrategy</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">type</span>:<span style="color: #bbb;"> </span>RollingUpdate<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">rollingUpdate</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">maxUnavailable</span>:<span style="color: #bbb;"> </span><span style="color: #666;">1</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #080; font-style: italic;"># Deployment mode</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">spec</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">type</span>:<span style="color: #bbb;"> </span>Deployment<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">strategy</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">type</span>:<span style="color: #bbb;"> </span>RollingUpdate<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">rollingUpdate</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">maxUnavailable</span>:<span style="color: #bbb;"> </span><span style="color: #666;">1</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">maxSurge</span>:<span style="color: #bbb;"> </span><span style="color: #666;">1</span><span style="color: #bbb;">
</span></span></span></code></pre></div><h3 id="server-side-apply">Server-Side Apply</h3>
<p>Under the hood, the operator has migrated from the dry-run/update pattern to <a href="https://kubernetes.io/docs/reference/using-api/server-side-apply/">Server-Side Apply</a> (SSA) for all reconciliation operations. This brings:</p>
<ul>
<li><strong>Conflict detection</strong>: concurrent modifications to managed fields are detected and reported</li>
<li><strong>Ownership tracking</strong>: the operator only manages fields it owns, leaving user-applied changes intact</li>
<li><strong>Reduced spurious updates</strong>: managed fields comparison prevents unnecessary API calls</li>
</ul>
<h2 id="breaking-changes">Breaking changes ⚠️</h2>
<p>Version 0.2.0 includes several API breaking changes. All existing custom resources must be updated before upgrading. A detailed <a href="https://github.com/falcosecurity/falco-operator/blob/main/docs/migration-guide.md">migration guide</a> is available in the repository documentation.</p>
<h3 id="summary-of-breaking-changes">Summary of breaking changes</h3>
<table>
<thead>
<tr>
<th>Change</th>
<th>Impact</th>
<th>Migration</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>ociArtifact.reference</code> replaced by <code>ociArtifact.image</code> + <code>ociArtifact.registry</code></td>
<td>All Rulesfile and Plugin CRs using OCI artifacts</td>
<td>Split the reference into <code>image.repository</code>, <code>image.tag</code>, and <code>registry.name</code></td>
</tr>
<tr>
<td><code>ociArtifact.pullSecret</code> replaced by <code>ociArtifact.registry.auth.secretRef</code></td>
<td>CRs with private registry credentials</td>
<td>Update the credential reference path</td>
</tr>
<tr>
<td>Config <code>spec.config</code> changed from string to structured YAML</td>
<td>All Config CRs</td>
<td>Remove the <code>|-</code> pipe literal, write YAML directly</td>
</tr>
<tr>
<td>Rulesfile <code>spec.inlineRules</code> changed from string to structured YAML</td>
<td>Rulesfile CRs with inline rules</td>
<td>Remove the <code>|-</code> pipe literal, write YAML directly</td>
</tr>
<tr>
<td>Plugin <code>spec.config.initConfig</code> changed from <code>map[string]string</code> to JSON</td>
<td>Plugin CRs with init config</td>
<td>Re-apply CRD; flat maps still validate</td>
</tr>
<tr>
<td>Falco resource <code>shortName</code> changed from <code>prom</code> to <code>falco</code></td>
<td>Scripts using <code>kubectl get prom</code></td>
<td>Use <code>kubectl get falco</code> instead</td>
</tr>
<tr>
<td>Condition types renamed (<code>ConditionReconciled</code> → <code>Reconciled</code>, <code>ConditionAvailable</code> → <code>Available</code>)</td>
<td>Monitoring tools filtering on condition types</td>
<td>Update condition type filters</td>
</tr>
<tr>
<td><code>kubectl get</code> column output changed for all CRDs</td>
<td>Dashboard parsing or scripts</td>
<td>Update parsers to match new column names</td>
</tr>
<tr>
<td>RBAC permissions expanded</td>
<td>Security-sensitive environments</td>
<td>Review the updated ClusterRole</td>
</tr>
</tbody>
</table>
<p>After upgrading, re-apply all CRDs and update your custom resources following the migration guide. The operator will reconcile the new format automatically.</p>
<h2 id="a-helm-chart-is-on-its-way">A Helm chart is on its way</h2>
<p>We are currently developing a Helm chart for installing the Falco Operator itself, which will simplify deployment and configuration of the operator in production environments. Stay tuned for the announcement.</p>
<h2 id="meet-us-at-kubecon">Meet us at KubeCon</h2>
<p>We will be talking about the Falco Operator during the <strong>maintainer track</strong> at the upcoming KubeCon. If you are interested in learning more, asking questions, or sharing feedback, come find us at the <strong>CNCF Falco kiosk</strong>, we would love to chat!</p>
<h2 id="try-it-out">Try it out</h2>
<p>Install the operator:</p>
<div class="highlight"><pre tabindex="0"><code class="language-bash"><span style="display: flex;"><span><span style="color: #b8860b;">VERSION</span><span style="color: #666;">=</span>latest
</span></span><span style="display: flex;"><span><span style="color: #a2f; font-weight: bold;">if</span> <span style="color: #666;">[</span> <span style="color: #b44;">"</span><span style="color: #b8860b;">$VERSION</span><span style="color: #b44;">"</span> <span style="color: #666;">=</span> <span style="color: #b44;">"latest"</span> <span style="color: #666;">]</span>; <span style="color: #a2f; font-weight: bold;">then</span>
</span></span><span style="display: flex;"><span> kubectl apply --server-side -f https://github.com/falcosecurity/falco-operator/releases/latest/download/install.yaml
</span></span><span style="display: flex;"><span><span style="color: #a2f; font-weight: bold;">else</span>
</span></span><span style="display: flex;"><span> kubectl apply --server-side -f https://github.com/falcosecurity/falco-operator/releases/download/<span style="color: #b68; font-weight: bold;">${</span><span style="color: #b8860b;">VERSION</span><span style="color: #b68; font-weight: bold;">}</span>/install.yaml
</span></span><span style="display: flex;"><span><span style="color: #a2f; font-weight: bold;">fi</span>
</span></span></code></pre></div><p>Then choose how you want to get started:</p>
<h3 id="full-stack-quickstart">Full stack quickstart</h3>
<p>Deploy the entire Falco ecosystem in the <code>falco</code> namespace with one command:</p>
<div class="highlight"><pre tabindex="0"><code class="language-bash"><span style="display: flex;"><span><span style="color: #b8860b;">VERSION</span><span style="color: #666;">=</span>latest
</span></span><span style="display: flex;"><span><span style="color: #a2f; font-weight: bold;">if</span> <span style="color: #666;">[</span> <span style="color: #b44;">"</span><span style="color: #b8860b;">$VERSION</span><span style="color: #b44;">"</span> <span style="color: #666;">=</span> <span style="color: #b44;">"latest"</span> <span style="color: #666;">]</span>; <span style="color: #a2f; font-weight: bold;">then</span>
</span></span><span style="display: flex;"><span> kubectl apply --server-side -f https://github.com/falcosecurity/falco-operator/releases/latest/download/quickstart.yaml
</span></span><span style="display: flex;"><span><span style="color: #a2f; font-weight: bold;">else</span>
</span></span><span style="display: flex;"><span> kubectl apply --server-side -f https://github.com/falcosecurity/falco-operator/releases/download/<span style="color: #b68; font-weight: bold;">${</span><span style="color: #b8860b;">VERSION</span><span style="color: #b68; font-weight: bold;">}</span>/quickstart.yaml
</span></span><span style="display: flex;"><span><span style="color: #a2f; font-weight: bold;">fi</span>
</span></span></code></pre></div><p>This deploys Falco, container and k8smeta plugins, detection rules, Falcosidekick, Falcosidekick UI with Redis, and k8s-metacollector - all pre-wired.</p>
<p>Verify:</p>
<div class="highlight"><pre tabindex="0"><code class="language-bash"><span style="display: flex;"><span>kubectl get falco,plugins,rulesfiles,configs,components -n falco
</span></span><span style="display: flex;"><span>kubectl get pods -n falco
</span></span></code></pre></div><h3 id="step-by-step">Step by step</h3>
<p>Deploy Falco:</p>
<div class="highlight"><pre tabindex="0"><code class="language-bash"><span style="display: flex;"><span>cat <span style="color: #b44;">&lt;&lt;EOF | kubectl apply -f -
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">apiVersion: instance.falcosecurity.dev/v1alpha1
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">kind: Falco
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">metadata:
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> name: falco
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">spec: {}
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">EOF</span>
</span></span></code></pre></div><p>Add the container plugin (required by the official rules for container metadata fields):</p>
<div class="highlight"><pre tabindex="0"><code class="language-bash"><span style="display: flex;"><span>cat <span style="color: #b44;">&lt;&lt;EOF | kubectl apply -f -
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">apiVersion: artifact.falcosecurity.dev/v1alpha1
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">kind: Plugin
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">metadata:
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> name: container
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">spec:
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> ociArtifact:
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> image:
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> repository: falcosecurity/plugins/plugin/container
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> tag: latest
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> registry:
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> name: ghcr.io
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">EOF</span>
</span></span></code></pre></div><p>And add detection rules:</p>
<div class="highlight"><pre tabindex="0"><code class="language-bash"><span style="display: flex;"><span>cat <span style="color: #b44;">&lt;&lt;EOF | kubectl apply -f -
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">apiVersion: artifact.falcosecurity.dev/v1alpha1
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">kind: Rulesfile
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">metadata:
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> name: falco-rules
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">spec:
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> ociArtifact:
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> image:
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> repository: falcosecurity/rules/falco-rules
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> tag: latest
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> registry:
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> name: ghcr.io
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> priority: 50
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">EOF</span>
</span></span></code></pre></div><p>Optionally, add Falcosidekick to route events to your favorite integrations:</p>
<div class="highlight"><pre tabindex="0"><code class="language-bash"><span style="display: flex;"><span>cat <span style="color: #b44;">&lt;&lt;EOF | kubectl apply -f -
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">apiVersion: instance.falcosecurity.dev/v1alpha1
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">kind: Component
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">metadata:
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> name: sidekick
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">spec:
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> component:
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> type: falcosidekick
</span></span></span><span style="display: flex;"><span><span style="color: #b44;"> replicas: 2
</span></span></span><span style="display: flex;"><span><span style="color: #b44;">EOF</span>
</span></span></code></pre></div><p>For the complete documentation, including the CRD reference, configuration options, and architecture overview, visit the <a href="https://github.com/falcosecurity/falco-operator">Falco Operator repository</a> and the <a href="https://falco.org/docs/setup/operator/">operator setup guide</a>.</p>
<h2 id="stay-connected">Stay connected</h2>
<p>Join us on social media and in our community calls! It's always great to have new members in the community, and we're looking forward to hearing your feedback and ideas.</p>
<p>You can find all the most up-to-date information at <a href="https://falco.org/community/">https://falco.org/community/</a>.</p>
