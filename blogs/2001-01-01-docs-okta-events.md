---
title: "Docs: Okta Events"
url: "https://falco.org/docs/concepts/event-sources/plugins/okta/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
The Falco Okta plugin can read Okta logs and emit events for each Okta log entry. Falco also distributes out-of-the-box rules that can be used to identify interesting/suspicious/notable events in Okta logs, including: Creating a new OKTA user account Detecting a locked-out user Assigning admin permissions to an okta user Configuration See the README for information on configuring the plugin. This simply involves providing the organization/api token as part of init params.
