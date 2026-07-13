---
title: "Docs: How Falco Uses Plugins"
url: "https://falco.org/docs/concepts/plugins/usage/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
Falco loads plugins based on configuration in falco.yaml . Currently, if a plugin with event sourcing capability is loaded then the only events processed are from that plugin; syscall events are disabled. There are other restrictions on loaded plugins (see below).
