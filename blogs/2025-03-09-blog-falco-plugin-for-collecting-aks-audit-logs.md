---
title: "Blog: Falco plugin for collecting AKS audit logs"
url: "https://falco.org/blog/falco-aks-audit-logs-plugin/"
date: "Sun, 09 Mar 2025 00:00:00 +0000"
author: ""
feed_url: "https://falco.org/blog/feed.xml"
---
<p>Troubleshooting Kubernetes events is challenging due to the multitude of data sources involved: container logs, Kubernetes events, cloud logs, and more. Among these sources, Kubernetes audit logs are especially valuable for identifying threats, as every action passing through the Kubernetes API server is recorded there.</p>
<p>We already provide plugins that let you parse and use Falco to detect threats in audit logs from GKE and EKS clusters. With our latest plugin, you'll now have the same powerful threat detection capabilities for your Azure AKS clusters.</p>
<h2 id="what-is-falco">What is Falco?</h2>
<p>Falco is a Cloud Native Computing Foundation project that provides runtime threat detection. Out of the box, Falco examines syscalls to alert you to any suspicious activity. And, since containers share the same kernel as their host, Falco can monitor not only activity on the host but also activity on all of the containers running on that host. Moreover, Falco pulls data from both Kubernetes and the container runtime to add additional context to its alerts.</p>
<p>With Falco running on your GKE clusters you can be notified of a wide variety of events, such as:</p>
<ul>
<li>Did someone start a container with high privileges?</li>
<li>Has someone shelled into a running container?</li>
<li>Has an executable been added to the container after it was deployed?</li>
</ul>
<p>These are just a few examples. Falco has over 80 rules that can be used to make you aware of not only external threats but also when clusters aren't being operated in accordance with industry best practices.</p>
<h2 id="what-is-the-aks-audit-logs-plugin">What is the AKS audit logs plugin?</h2>
<p>The AKS audit logs plugin extends Falco's capabilities to Microsoft Azure Kubernetes Service (AKS) clusters, providing you with the same security insights and threat detection Falco already offers for GKE and EKS environments. With this plugin, you can seamlessly integrate AKS audit logs into Falco's event processing pipeline, enabling it to identify anomalies, suspicious activities, and policy violations within your AKS-based workloads.</p>
<h2 id="using-aks-audit-logs-plugin">Using AKS audit logs plugin</h2>
<p>In order to use the AKS audit log plugin, you must first configure your AKS cluster to ship the logs where we can fetch them.</p>
<p>The current supported output source is Event hub, so when following the <a href="https://learn.microsoft.com/en-us/azure/aks/monitor-aks#aks-control-planeresource-logs">guide</a> to configure your AKS audit logs, you must have Eventhub enabled. You can also optionally send it to other sources:
<img alt="aks-audit-logs" src="https://falco.org/blog/falco-aks-audit-logs-plugin/falco-aks-audit-logs-plugin1.png" />
</p>
<p>Once you have the stream enabled, you must create or reuse a storage account blob container so that the plugin can track the last event that was consumed, which is done trough checkpoints.</p>
<h2 id="configuring-falco-to-use-aks-audit-logs-plugin">Configuring Falco to use AKS audit logs plugin</h2>
<p>First, using <a href="https://github.com/falcosecurity/falcoctl">falcoctl</a>, download the plugin:</p>
<div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span>sudo falcoctl artifact install k8saudit-aks<span style="color: #bbb;">
</span></span></span></code></pre></div><p>In your falco.yaml file, you must add the plugin configuration and later enable the plugin</p>
<div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">config_files</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span>- /etc/falco/config.d<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">watch_config_files</span>:<span style="color: #bbb;"> </span><span style="color: #a2f; font-weight: bold;">true</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">plugins</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #080; font-style: italic;"># - name: k8saudit</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #080; font-style: italic;"># library_path: libk8saudit.so</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #080; font-style: italic;"># init_config: ""</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #080; font-style: italic;"># open_params: "http://:9765/k8s-audit"</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #080; font-style: italic;"># - name: json</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #080; font-style: italic;"># library_path: libjson.so</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span>- <span style="color: #008000; font-weight: bold;">name</span>:<span style="color: #bbb;"> </span>k8saudit-aks<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">library_path</span>:<span style="color: #bbb;"> </span>libk8saudit-aks.so<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">init_config</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">event_hub_name</span>:<span style="color: #bbb;"> </span>${EVENTHUB_NAME}<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">blob_storage_container_name</span>:<span style="color: #bbb;"> </span>${BLOB_STORAGE_CONTAINER_NAME}<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">event_hub_namespace_connection_string</span>:<span style="color: #bbb;"> </span>${EVENTHUB_NAMESPACE_CONNECTION_STRING}<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">blob_storage_connection_string</span>:<span style="color: #bbb;"> </span>${BLOB_STORAGE_CONNECTION_STRING}<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">load_plugins</span>:<span style="color: #bbb;"> </span>[k8saudit-aks]<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">stdout_output</span>:<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">enabled</span>:<span style="color: #bbb;"> </span><span style="color: #a2f; font-weight: bold;">true</span><span style="color: #bbb;">
</span></span></span></code></pre></div><p>Once they are exported, run Falco and after some seconds you'll logs informing the k8saudit-aks plugin was loaded:</p>
<div class="highlight"><pre tabindex="0"><code class="language-bash"><span style="display: flex;"><span>falco -c /etc/falco/falco.yaml -r /etc/falco/falco_rules.yaml
</span></span></code></pre></div><div class="highlight"><pre tabindex="0"><code class="language-t"><span style="display: flex;"><span>Tue Dec <span style="color: #666;">17</span> <span style="color: #666;">18</span>:<span style="color: #666;">02</span>:<span style="color: #666;">07</span> <span style="color: #666;">2024</span>: Opening <span style="color: #b44;">'k8s_audit'</span> source with plugin <span style="color: #b44;">'k8saudit-aks'</span>
</span></span><span style="display: flex;"><span><span style="color: #666;">2024</span><span style="color: #b68;">/12/</span><span style="color: #666;">17</span> <span style="color: #666;">21</span>:<span style="color: #666;">02</span>:<span style="color: #666;">07</span> [k8saudit<span style="color: #666;">-</span>aks] opened connection to blob storage
</span></span><span style="display: flex;"><span><span style="color: #666;">2024</span><span style="color: #b68;">/12/</span><span style="color: #666;">17</span> <span style="color: #666;">21</span>:<span style="color: #666;">02</span>:<span style="color: #666;">07</span> [k8saudit<span style="color: #666;">-</span>aks] opened blob checkpoint connection
</span></span><span style="display: flex;"><span><span style="color: #666;">2024</span><span style="color: #b68;">/12/</span><span style="color: #666;">17</span> <span style="color: #666;">21</span>:<span style="color: #666;">02</span>:<span style="color: #666;">07</span> [k8saudit<span style="color: #666;">-</span>aks] opened consumer client
</span></span><span style="display: flex;"><span><span style="color: #666;">2024</span><span style="color: #b68;">/12/</span><span style="color: #666;">17</span> <span style="color: #666;">21</span>:<span style="color: #666;">02</span>:<span style="color: #666;">07</span> [k8saudit<span style="color: #666;">-</span>aks] created eventhub processor
</span></span></code></pre></div><h2 id="testing-out">Testing out!</h2>
<p>Append rule to <strong>falco_rules.yaml</strong> file:</p>
<div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span>- <span style="color: #008000; font-weight: bold;">rule</span>:<span style="color: #bbb;"> </span>K8s Audit Event Detected<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">desc</span>:<span style="color: #bbb;"> </span>A test rule that detects any Kubernetes audit event<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">condition</span>:<span style="color: #bbb;"> </span>ka.req exists<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">output</span>:<span style="color: #bbb;"> </span><span style="color: #b44;">"K8s Audit Event Detected: %ka.req"</span><span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">priority</span>:<span style="color: #bbb;"> </span>DEBUG<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">source</span>:<span style="color: #bbb;"> </span>k8s_audit<span style="color: #bbb;">
</span></span></span><span style="display: flex;"><span><span style="color: #bbb;"> </span><span style="color: #008000; font-weight: bold;">tags</span>:<span style="color: #bbb;"> </span>[testing, k8s_audit]<span style="color: #bbb;">
</span></span></span></code></pre></div><div class="highlight"><pre tabindex="0"><code class="language-bash"><span style="display: flex;"><span>$ falco -c /etc/falco/falco.yaml -r /etc/falco/falco_rules.yaml
</span></span></code></pre></div><p>Then, you should see initialization message, followed by some events from your AKS cluster. Since we have debug enabled, you should see some events from the aksService:</p>
<pre tabindex="0"><code>Thu Dec 19 11:44:55 2024: Falco version: 0.39.2 (aarch64)
Thu Dec 19 11:44:55 2024: Falco initialized with configuration files:
Thu Dec 19 11:44:55 2024: /etc/falco/config.d/engine-kind-falcoctl.yaml | schema validation: ok
Thu Dec 19 11:44:55 2024: /etc/falco/falco.yaml | schema validation: ok
Thu Dec 19 11:44:55 2024: System info: Linux version 6.8.0-51-generic (buildd@bos03-arm64-031) (aarch64-linux-gnu-gcc-13 (Ubuntu 13.3.0-6ubuntu2~24.04) 13.3.0, GNU ld (GNU Binutils for Ubuntu) 2.42) #52-Ubuntu SMP PREEMPT_DYNAMIC Thu Dec 5 13:32:09 UTC 2024
Thu Dec 19 11:44:55 2024: Loading plugin 'k8saudit-aks' from file /usr/share/falco/plugins/libk8saudit-aks.so
Thu Dec 19 11:44:55 2024: Loading plugin 'json' from file /usr/share/falco/plugins/libjson.so
Thu Dec 19 11:44:55 2024: Loading rules from:
Thu Dec 19 11:44:55 2024: /etc/falco/falco_rules.yaml | schema validation: ok
Thu Dec 19 11:44:55 2024: /etc/falco/falco_rules.local.yaml | schema validation: none
Thu Dec 19 11:44:55 2024: /etc/falco/falco_aks_audit.yaml | schema validation: ok
Thu Dec 19 11:44:55 2024: The chosen syscall buffer dimension is: 8388608 bytes (8 MBs)
Thu Dec 19 11:44:55 2024: Starting health webserver with threadiness 4, listening on 0.0.0.0:8765
Thu Dec 19 11:44:55 2024: Loaded event sources: syscall, k8s_audit
Thu Dec 19 11:44:55 2024: Enabled event sources: k8s_audit, syscall
Thu Dec 19 11:44:55 2024: Opening 'k8s_audit' source with plugin 'k8saudit-aks'
2024/12/19 14:44:55 [k8saudit-aks] opened connection to blob storage
2024/12/19 14:44:55 [k8saudit-aks] opened blob checkpoint connection
2024/12/19 14:44:55 [k8saudit-aks] opened consumer client
2024/12/19 14:44:55 [k8saudit-aks] created eventhub processor
Thu Dec 19 11:44:55 2024: Opening 'syscall' source with modern BPF probe.
Thu Dec 19 11:44:55 2024: One ring buffer every '2' CPUs.
</code></pre><div class="highlight"><pre tabindex="0"><code class="language-yaml"><span style="display: flex;"><span><span style="color: #008000; font-weight: bold;">10:52:03.348668000: Debug K8s Audit Event Detected</span>:<span style="color: #bbb;"> </span>verb=create, user=aksService, groups=(system:masters,system:authenticated), target=&lt;NA&gt;<span style="color: #bbb;">
</span></span></span></code></pre></div><h2 id="let-s-meet">Let's meet!</h2>
<p>As always, we meet every week in our <a href="https://github.com/falcosecurity/community">community calls</a>,
if you want to know the latest and the greatest you should join us there!</p>
<p>If you have any questions</p>
<ul>
<li>Join the #falco channel on the <a href="https://slack.k8s.io">Kubernetes Slack</a></li>
<li><a href="https://lists.cncf.io/g/cncf-falco-dev">Join the Falco mailing list</a></li>
</ul>
<p>Enjoy 😎,</p>
<p><em>Igor</em></p>
