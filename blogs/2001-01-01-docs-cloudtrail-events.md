---
title: "Docs: CloudTrail Events"
url: "https://falco.org/docs/concepts/event-sources/plugins/cloudtrail/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
The Falco cloudtrail plugin can read AWS CloudTrail logs and emit events for each CloudTrail log entry. This plug-in also includes out-of-the-box rules that can be used to identify interesting/suspicious/notable events in CloudTrail logs, including: Console logins that do not use multi-factor authentication Disabling multi-factor authentication for users Disabling encryption for S3 buckets Configuration See the README for information on how to configure the plugin. The plugin initialization and open params strings/objects can be added to falco.yaml under the plugins configuration key .
