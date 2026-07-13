---
title: "Docs: Output Channels"
url: "https://falco.org/docs/concepts/outputs/channels/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
Standard Output When configured to send alerts via standard output, a line is printed for each alert. Here is an example: stdout_output : enabled : true 10:20:05.408091526: Warning Sensitive file opened for reading by non-trusted program (user=root command=cat /etc/shadow file=/etc/shadow) Standard output is useful when using Fluentd or Logstash to capture logs from containers. Alerts can then be stored in Elasticsearch , and dashboards can be created to visualize the alerts.
