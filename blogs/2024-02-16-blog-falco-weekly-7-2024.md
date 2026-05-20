---
title: "Blog: Falco Weekly 7 - 2024"
url: "https://falco.org/blog/falco-w-7-2024-weekly-recap/"
date: "Fri, 16 Feb 2024 00:00:00 +0000"
author: ""
feed_url: "https://falco.org/blog/feed.xml"
---
What happened in Falco this week? Let's go through the major changes that happened in various repositories under the falcosecurity organization. Libs Multiple fixes and some cleanups happened in the libs repo: newfstatat syscall is now configured with UF_ALWAYS_DROP : https://github.com/falcosecurity/libs/pull/1683 Fixed null destination address in sendto and sendmsg in modern bpf: https://github.com/falcosecurity/libs/pull/1687 Added a CT_UNKNOWN container type zero value and properly initialize uninitialized value: https://github.com/falcosecurity/libs/pull/1688 Fix in chisels: don't fail…
