---
title: "Docs: Actions For Dropped System Call Events"
url: "https://falco.org/docs/concepts/event-sources/kernel/dropped-events/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
Introduction With the enhancements introduced in v0.15.0, Falco can now intelligently detect dropped system call events and take remedial actions, such as alerting or even exiting Falco entirely. When system call events are dropped, Falco might encounter problems building its internal view of the processes, files, containers, and orchestrator metadata in use, which in turn might affect the rules that depend on that metadata. The explicit signals that Falco now provides make it easier to detect dropped system calls.
