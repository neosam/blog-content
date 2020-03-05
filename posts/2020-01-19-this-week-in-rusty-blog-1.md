---
title: This week in rusty blog
date: 2020-01-19
author: neosam
---

The main improvements the last two weeks was the improvement of the caching
system.  The old version cached all the compiled markdown code in memory.  Once
a file was read and parsed, it was stored there forever.  For a deployed blog,
this works fine because I usually don't change anything when it's already
online.  For local editing on the other hand, this is very annoying.  There is
a switch to turn off the cache which reloads the markdown file on every page
request which is very slow with the current markdown crate.

The new solution stores the parsed html values to a working directory.  Now,
the first thing the cache does is to look into if there is already something in
the working directory.  If there is one and it's newer than the markdown file,
it is shared.  If there is one which is older than the markdown file, it means
that it is outdated and it must be regenerated.  Another benefit of this
solution is that the cached values are still available after a restart of the
server.

Another change is simply the documentation of the source files.

