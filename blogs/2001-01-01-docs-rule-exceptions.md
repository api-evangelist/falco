---
title: "Docs: Rule Exceptions"
url: "https://falco.org/docs/concepts/rules/exceptions/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
Introduction Almost all Falco Rules have cases where the behavior detected by the rule should be allowed. For example, the rule Write below binary dir has exceptions for specific programs that are known to write below these directories as a part of software installation/management: - rule : Write below binary dir desc : an attempt to write to any file below a set of binary directories condition : > open_write and bin_dir and not package_mgmt_procs and not exe_running_docker_save and not python_running_get_pip and not python_running_ms_oms and not user_known_write_below_binary_dir_activities ..
