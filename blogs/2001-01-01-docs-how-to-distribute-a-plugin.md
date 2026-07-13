---
title: "Docs: How to distribute a plugin"
url: "https://falco.org/docs/developer-guide/plugins/how-to-distribute/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
Introduction In this article, we'll focus on the steps to build the OCI artifacts containing the plugin and its rules and how to distribute them on Github Packages. To get more familiar with the OCI artifacts, you can read our blog posts about falcoctl and GitOps for rules In the next sections we'll describe how to: set up a Github Actions workflow to: create a release with GoReleaser when a tag is pushed build the OCI artifacts of the plugin and its rules create the index.yaml used by falcoctl Requirements This tutorial is based on a Github repo, with the possibility to run workflows in Githu
