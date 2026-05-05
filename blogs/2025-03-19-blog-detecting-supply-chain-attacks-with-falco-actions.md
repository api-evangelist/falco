---
title: "Blog: Detecting Supply Chain Attacks with Falco Actions"
url: "https://falco.org/blog/detecting-supplychain-attacks-with-falco-action/"
date: "Wed, 19 Mar 2025 00:00:00 +0000"
author: ""
feed_url: "https://falco.org/blog/feed.xml"
---
<p>The recently discovered CVE for the GitHub action <code>tj-actions/changed-files</code> brought to light a topic that is really critical for companies: supply chain attacks. With that, we want to discuss and show a bit about how Falco can help your organization detect this kind of attack and other suspect behaviors inside your CI/CD pipeline.</p>
<h2 id="what-is-falco">What is Falco?</h2>
<p>Falco is a cloud native security tool that provides runtime security across hosts, containers, Kubernetes, and cloud environments. It leverages custom rules on Linux kernel events and other data sources through plugins, enriching event data with contextual metadata to deliver real-time alerts. Falco enables the detection of abnormal behavior, potential security threats, and compliance violations.</p>
<h2 id="what-is-falco-actions">What is Falco Actions?</h2>
<p><a href="https://github.com/falcosecurity/falco-actions">Falco Actions</a> enable you to run Falco in GitHub Actions to detect suspicious behavior in your CI/CD workflows. If you run it in a pull request, the action will create a comment with the findings.</p>
<p>Thanks to ad-hoc Falco rules specific to this use case, these GitHub actions can monitor your GitHub runner and detect software supply chain attacks.</p>
<h2 id="using-falco-actions">Using Falco Actions</h2>
<p>To have Falco inside your pipeline, you need to add these two actions:</p>
<ul>
<li><code>falcosecurity/falco-actions/start</code></li>
<li><code>falcosecurity/falco-actions/stop</code></li>
</ul>
<p>Below you can see an example:</p>
<div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">name</span>:<span style="color: #bbb;"> </span>CI<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">on</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">push</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">pull_request</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">jobs</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">build</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">runs-on</span>:<span style="color: #bbb;"> </span>ubuntu-latest<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">permissions</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">contents</span>:<span style="color: #bbb;"> </span>read<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">actions</span>:<span style="color: #bbb;"> </span>read<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">steps</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span>- <span style="color: #008000; font-weight: bold;">uses</span>:<span style="color: #bbb;"> </span>actions/checkout@v4<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span>- <span style="color: #008000; font-weight: bold;">name</span>:<span style="color: #bbb;"> </span>Start Falco<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">uses</span>:<span style="color: #bbb;"> </span>falcosecurity/falco-actions/start@main<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">with</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">mode</span>:<span style="color: #bbb;"> </span>live<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">falco-version</span>:<span style="color: #bbb;"> </span><span style="color: #b44;">'0.40.0'</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">verbose</span>:<span style="color: #bbb;"> </span><span style="color: #a2f; font-weight: bold;">true</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span>- <span style="color: #008000; font-weight: bold;">name</span>:<span style="color: #bbb;"> </span>My Custom Step<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">run</span>:<span style="color: #bbb;"> </span>|<span style="color: #b44; font-style: italic;">
</span></span></span><span style="display: flex;"><span><span style="color: #b44; font-style: italic;"> echo "This is my custom step"</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span>- <span style="color: #008000; font-weight: bold;">name</span>:<span style="color: #bbb;"> </span>Stop Falco<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">uses</span>:<span style="color: #bbb;"> </span>falcosecurity/falco-actions/start@main<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">with</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">mode</span>:<span style="color: #bbb;"> </span>live<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">verbose</span>:<span style="color: #bbb;"> </span><span style="color: #a2f; font-weight: bold;">true</span><span style="color: #bbb;">
</span></span></span></code></pre></div><p><strong>OBS: main is being used here only to simplify how it works, you should always pin your dependencies to a specific commit SHA.</strong></p>
<p>After the execution, you will be able to see the results at the github action summary.</p>
<p>If you want a more detailed report, you can use the action <code>falcosecurity/falco-actions/analyze</code>; it will allow you to have a better report with information like:</p>
<ul>
<li>Falco rules triggered during steps' execution.</li>
<li>Contacted IPs</li>
<li>Contacted DNS domains</li>
<li>SHA256 hash of spawned executables</li>
<li>Spawned container images</li>
<li>Written files</li>
<li>A summary of the report generated with OpenAI</li>
<li>Reputation of Contacted IPs</li>
<li>Reputation of SHA256 hashes</li>
</ul>
<p>For more informations about the usage, you can check the <a href="https://github.com/falcosecurity/falco-actions">github repository</a> for the actions.</p>
<h2 id="default-rules-file">Default rules file</h2>
<p>By default, Falco action will detect a variety of events, following the <a href="https://github.com/falcosecurity/falco-actions/blob/main/rules/falco_cicd_rules.yaml">default CICD rules</a>, that can be overridden if you want.</p>
<p>In the example from the <code>tj-actions/changed-files</code> exploit, one rule that would be triggered is the <strong>Process Dumping Memory of Others</strong>, which was used during the exploit to dump environment variables from the main process and print them as part of the Github runner execution.</p>
<p>The Falco team is always adding new rules to ensure our users get value out of the box, but you can also write your own rules according to your company policy.</p>
<h2 id="conclusions">Conclusions</h2>
<p>These actions are just the beginning of having the Falco capabilities inside the CI/CD pipelines. You can customize and have your own set of rules, keeping all environments and scenarios covered and protected from supply chain attacks.</p>
<h2 id="let-s-meet">Let's meet!</h2>
<p>As always, we meet every 2 weeks on Wednesday at 4pm UTC in our <a href="https://github.com/falcosecurity/community">community calls</a>,
if you want to know the latest and the greatest you should join us there!</p>
<p>If you have any questions</p>
<ul>
<li>Join the #falco channel on the <a href="https://slack.k8s.io">Kubernetes Slack</a></li>
<li><a href="https://lists.cncf.io/g/cncf-falco-dev">Join the Falco mailing list</a></li>
</ul>
<p>Enjoy 😎,</p>
<p><em>Igor and Edson</em></p>
