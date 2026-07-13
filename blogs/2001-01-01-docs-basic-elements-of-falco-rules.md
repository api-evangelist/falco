---
title: "Docs: Basic Elements of Falco Rules"
url: "https://falco.org/docs/concepts/rules/basic-elements/"
date: "2001-01-01"
feed_url: "https://falco.org/docs/feed.xml"
---
Rules A rule is a YAML object, part of the rules file, whose definition contains at least the following fields: - rule : shell_in_container desc : notice shell activity within a container condition : > (evt.type in (execve, execveat)) and container.id != host and (proc.name = bash or proc.name = ksh) output : > shell in a container | user=%user.name container_id=%container.id container_name=%container.name shell=%proc.name parent=%proc.pname cmdline=%proc.cmdline priority : WARNING Conditions The key part of a rule is the condition field. A condition is a Boolean predicate expressed using the 
