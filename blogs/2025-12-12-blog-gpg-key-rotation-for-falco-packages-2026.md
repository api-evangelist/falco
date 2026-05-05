---
title: "Blog: GPG Key Rotation for Falco Packages (2026)"
url: "https://falco.org/blog/gpg-key-rotation-2026/"
date: "Fri, 12 Dec 2025 00:00:00 +0000"
author: ""
feed_url: "https://falco.org/blog/feed.xml"
---
<p>The GPG key used to sign official Falco packages (RPM and DEB) is set to expire on <strong>January 17, 2026</strong>. To ensure the security and continuity of our software distribution, the Falco maintainers will be rotating to a new 4096-bit RSA key.</p>
<p>We have designed a two-phase <strong>&quot;Soft Launch&quot;</strong> strategy to make this transition as smooth as possible, providing a one-month transition window before the old key is retired.</p>
<h2 id="the-rotation-plan">The Rotation Plan</h2>
<p>To avoid immediate disruption, we are rolling out the new key in two distinct phases. You can follow the detailed progress in our <a href="https://github.com/falcosecurity/falco/issues/3750">tracking issue #3750</a>.</p>
<h3 id="phase-1-soft-launch-dec-12-2025">Phase 1: Soft Launch (Dec 12, 2025)</h3>
<ul>
<li><strong>What happens:</strong> The new GPG key has been published and added to our repository configuration.</li>
<li><strong>Dev Builds:</strong> Will begin using the <strong>New Key</strong> immediately.</li>
<li><strong>Stable Builds:</strong> No stable releases are planned for this phase. If any hotfixes are released, they will be signed with <strong>New Key</strong> as well.</li>
<li><strong>Key Bundle:</strong> The <a href="https://falco.org/repo/falcosecurity-packages.asc">official key URL</a> has been updated to serve a <strong>bundle</strong> containing <em>both</em> the Old (valid) and New (valid) keys.</li>
</ul>
<h3 id="phase-2-hard-cut-over-jan-12-17-2026">Phase 2: Hard Cut-Over (Jan 12–17, 2026)</h3>
<ul>
<li><strong>What happens:</strong> This is the maintenance window where we fully switch to the new key.</li>
<li><strong>Mass Resign:</strong> All existing stable packages on <code>download.falco.org</code> will be resigned with the <strong>New Key</strong>.</li>
<li><strong>Revocation:</strong> The Old Key will be officially revoked and removed from the active bundle.</li>
<li><strong>Impact:</strong> If you have not updated your keyring by this date, your package manager (<code>apt</code> or <code>yum</code>) will reject updates with a signature verification error.</li>
</ul>
<h2 id="action-items-for-users">Action Items for Users</h2>
<p>We strongly recommend all users update their GPG keyring <strong>before January 12, 2026</strong> to avoid interruption.</p>
<h3 id="new-users">New Users</h3>
<p>If you are installing Falco for the first time following our <a href="https://falco.org/docs/setup/packages/">Install on a host (DEB,RPM)</a> instructions, no action is required. The installation process will guide you to fetch the new key bundle, ensuring you are ready for both phases.</p>
<h3 id="existing-users">Existing Users</h3>
<p>If you have an existing Falco installation, you must manually import the new key. We have updated the key file at our standard URL to include both the old and new keys, allowing you to transition safely.</p>
<p>For <strong><code>apt</code> users</strong>, to update your keyring, run the following command:</p>
<div class="highlight"><pre tabindex="0"><code class="language-bash"><span style="display: flex;"><span><span style="color: #080; font-style: italic;"># Download the updated key bundle (Old + New) and import it</span>
</span></span><span style="display: flex;"><span>curl -fsSL https://falco.org/repo/falcosecurity-packages.asc | <span style="color: #b62; font-weight: bold;">\
</span></span></span><span style="display: flex;"><span> sudo gpg --dearmor -o /usr/share/keyrings/falco-archive-keyring.gpg
</span></span></code></pre></div><p>For <strong><code>yum</code> users</strong>, to update your keyring, run the following command:</p>
<div class="highlight"><pre tabindex="0"><code class="language-bash"><span style="display: flex;"><span><span style="color: #080; font-style: italic;"># Download the updated key bundle (Old + New) and import it</span>
</span></span><span style="display: flex;"><span>rpm --import https://falco.org/repo/falcosecurity-packages.asc
</span></span></code></pre></div><p><em>Note: These commands overwrite the existing keyring file with the new bundle. Since the bundle contains both keys, your current installation will continue to work immediately, and will remain working after the January cut-over.</em></p>
<p>For more details on <code>apt</code> and <code>yum</code> specific instructions, please refer to the <a href="https://falco.org/docs/setup/packages/">Install on a host (DEB,RPM)</a> page of our documentation.</p>
<h2 id="summary">Summary</h2>
<ul>
<li><strong>Deadline:</strong> Update your keys before <strong>Jan 12, 2026</strong>.</li>
<li><strong>Old Key (Expiring Jan 17, 2026):</strong> <a href="https://falco.org/repo/falcosecurity-14CB7A8D.asc">falcosecurity-14CB7A8D.asc</a> (Fingerprint <code>2005399002D5E8FF59F28CE64021833E14CB7A8D</code>)</li>
<li><strong>New Key:</strong> <a href="https://falco.org/repo/falcosecurity-B35B1B1F.asc">falcosecurity-B35B1B1F.asc</a> (Fingerprint <code>478B2FBBC75F4237B731DA4365106822B35B1B1F</code>)</li>
<li><strong>Tracking Issue:</strong> <a href="https://github.com/falcosecurity/falco/issues/3750">falcosecurity/falco#3750</a></li>
</ul>
<p>If you encounter any issues during this transition, please reach out to us on the <a href="https://www.google.com/search?q=https://kubernetes.slack.com/archives/CMWH3EH32">#falco channel</a> on Kubernetes Slack or open an issue on GitHub.</p>
<p>Thank you for your attention and cooperation in keeping Falco secure!</p>
