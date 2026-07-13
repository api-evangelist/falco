---
title: "Docs: Supported Events"
url: "https://falco.org/docs/reference/rules/supported-events/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
Here are the system call event types and args supported by the kernel module and modern eBPF probe via libscap included in the Falco libs. Note that, for performance reasons, by default Falco will only consider a subset of them indicated in the table below with "yes". However, it's possible to make Falco consider all events by using the -A command line switch.
