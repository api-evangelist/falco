---
title: "Docs: Kernel Events Architecture"
url: "https://falco.org/docs/concepts/event-sources/kernel/architecture/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
This document describes the overall architecture that allows events from kernel sources to be ingested by Falco, how to use the libraries to inspect the data collection flow and how Falco manages the boundary between the kernel and userspace. In order to make Falco compatible with a very large number of Linux Kernel versions, the internal APIs and low level communication mechanisms that are employed to cross the kernel and userspace boundary vary greatly between driver types and may be different between driver versions or kernel versions. However, they all implement the same event collection i
