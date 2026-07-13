---
title: "Docs: Plugins Architecture Concepts"
url: "https://falco.org/docs/concepts/plugins/architecture/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
Overview Plugins are dynamic shared libraries (.so files in Unix, .dll files in Windows) that export C calling convention functions. Programs like Falco dynamically load these libraries and call the exported functions to extend Falco's support for event sources/fields. Plugins are versioned using semantic versioning to minimize regressions and compatibility issues.
