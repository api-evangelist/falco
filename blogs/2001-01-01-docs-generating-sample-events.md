---
title: "Docs: Generating sample events"
url: "https://falco.org/docs/concepts/event-sources/kernel/sample-events/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
If you'd like to check if Falco is working properly, we have the event-generator tool that can perform an activity for both our syscalls and k8s audit related rules. The tool provides a command to run either some or all sample events. event-generator run [regexp] Without arguments it runs all actions, otherwise only those actions matching the given regular expression.
