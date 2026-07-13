---
title: "Docs: Rules Examples"
url: "https://falco.org/docs/reference/rules/examples/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
Here are some examples of the types of behavior falco can detect. For a more comprehensive set of examples, see the full rules file at falco_rules.yaml . A shell is run in a container - macro : container condition : container.id != host - macro : spawned_process condition : (evt.type in (execve, execveat)) - rule : run_shell_in_container desc : a shell was spawned by a non-shell program in a container.
