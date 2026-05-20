---
title: "Blog: Falco Talon v0.2.0"
url: "https://falco.org/blog/falco-talon-v0-2-0/"
date: "Wed, 27 Nov 2024 00:00:00 +0000"
author: ""
feed_url: "https://falco.org/blog/feed.xml"
---
Today we announce the release of Falco Talon 0.2.0 🦅! Falco Talon 0.2.0 is a minor release that includes new actionners and outputs, add parameters to existing actionners, along one small fix on the check and print commands. Features Add gcp:function actionner: Now users can call GCP function to automate GCP tasks, with authentication and authorization out of the box. - action : Invoke GCP function actionner : gcp:function additional_contexts : - aws parameters : gcp_function_name : simple-http-function gcp_function_location : us-central1 Add gcp:gcs output Now users can send output directly…
