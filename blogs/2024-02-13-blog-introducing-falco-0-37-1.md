---
title: "Blog: Introducing Falco 0.37.1"
url: "https://falco.org/blog/falco-0-37-1/"
date: "Tue, 13 Feb 2024 00:00:00 +0000"
author: ""
feed_url: "https://falco.org/blog/feed.xml"
---
Today we announce the release of Falco 0.37.1 🦅! Fixes Falco's 0.37.1 release is a small patch aimed at addressing a few minor bugs. It includes the following: Added --http-insecure flag to driver loader images Added new env variable FALCOCTL_DRIVER_HTTP_HEADERS understood by driver loader images to pass a comma separated list of http headers for driver download, eg: FALCOCTL_DRIVER_HTTP_HEADERS='x-emc-namespace: default,Proxy-Authenticate: Basic' Falcoctl was bumped to v0.7.2, fixing an issue building Flatcar drivers and a bug withing the kernel release fixup method to build drivers…
