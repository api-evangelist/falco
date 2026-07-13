---
title: "Docs: Controlling Rules"
url: "https://falco.org/docs/concepts/rules/controlling-rules/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
Disable Default Rules Even though Falco provides a quite powerful default ruleset, you sometimes need to disable some of these default rules since they do not work properly in your environment. Luckily Falco offers you multiple possibilities to do so. Via Falco Configuration or Parameters Since Falco 0.38.0, you can control which rules are loaded by adding relevant entries to the rules section of the falco.yaml configuration file or by passing appropriate command line parameters.
