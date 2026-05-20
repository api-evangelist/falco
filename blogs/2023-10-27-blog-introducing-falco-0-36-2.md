---
title: "Blog: Introducing Falco 0.36.2"
url: "https://falco.org/blog/falco-0-36-2/"
date: "Fri, 27 Oct 2023 00:00:00 +0000"
author: ""
feed_url: "https://falco.org/blog/feed.xml"
---
Today we announce the release of Falco 0.36.2 🦅! Fixes Falco's 0.36.2 release is a small patch addressing a few bugs. It includes the following: Fixed a possible segfault caused by uninitialized variable in libsinsp::next() method call. ( https://github.com/falcosecurity/falco/issues/2878 ) Improved supported program type detection for modern BPF; this ensures we can actually be sure that our BPF program type is unsupported when returning an error to the user. ( https://github.com/falcosecurity/libs/pull/1404 ) Fixed a subtle bug in rawarg filtercheck for non-string types. (…
