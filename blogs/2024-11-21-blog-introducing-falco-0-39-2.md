---
title: "Blog: Introducing Falco 0.39.2"
url: "https://falco.org/blog/falco-0-39-2/"
date: "Thu, 21 Nov 2024 00:00:00 +0000"
author: ""
feed_url: "https://falco.org/blog/feed.xml"
---
Today we announce the release of Falco 0.39.2 🦅! Fixes Falco's 0.39.2 is a small patch release that includes some important bugfixes for modern eBPF driver: check cred field is not NULL before the access; this enables Falco back with modern eBPF driver to work on GKE address verifier issues on kernel versions >=6.11.4 : there was a kernel-breaking change in the tail call ebpf API merged into the 6.11.4 to fix a CVE . Adapt our code to work again on these new versions.
