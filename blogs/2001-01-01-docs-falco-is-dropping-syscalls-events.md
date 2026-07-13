---
title: "Docs: Falco Is Dropping Syscalls Events"
url: "https://falco.org/docs/troubleshooting/dropping/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
Action Items (TL;DR) Adjust the buf_size_preset in the falco.yaml config. Utilize base_syscalls to limit the syscalls under monitoring. Audit and optimize Falco rules to prevent unnecessary backpressure on the kernel, considering that Falco's main event stream is single-threaded.
