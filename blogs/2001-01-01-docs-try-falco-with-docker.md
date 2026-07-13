---
title: "Docs: Try Falco with Docker"
url: "https://falco.org/docs/getting-started/falco-docker-quickstart/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
Install Falco First, ensure you have a Linux machine with a recent version of Docker installed. Note that the following will not work on Windows or macOS running Docker Desktop. Run the following command: docker run --rm -it \ --name falco \ --privileged \ -v /sys/kernel/tracing:/sys/kernel/tracing:ro \ -v /var/run/docker.sock:/host/var/run/docker.sock \ -v /proc:/host/proc:ro \ -v /etc:/host/etc:ro \ falcosecurity/falco:0.44.1 Falco is now monitoring your system using the pre-installed set of rules that alert you upon suspicious behavior.
