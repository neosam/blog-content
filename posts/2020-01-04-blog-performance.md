---
title: Blog modernization and performance upgrades
date: 2020-01-04
author: neosam
---

Finally I had some time to work on the blog software.  This is mainly
modernization of the libraries and performance optimizations.

## Server update
The server actix-web is now upgraded from 1.0 to 2.0.  It uses async/await
now.  Upgrading just required a few changes in the code - mainly adding .await
and make a few functions async.  This was pretty easy.  To make the main
function await, I needed to add actix-rt 1.0 but I didn't really understand how
this works.

## Using a different template engine
On every page request, all required template files were read from disk and
passed to the template engine.  I wanted load and initialize the template
engine once during startup. 

In order to optimize the template system, I needed to replace the whole
templating library.  As it turned out, TinyTemplate uses &str internally and so
the whole TinyTemplate object requires a livetime parameter.  The documention
of the library points out that it only works properly with template strings
which have a static livetime but since I load them from files, this doesn't
work for me.  

Now I switched to handlebars.  Artix allows to pass data which are used
to store state in the application.  I created a state struct which contains
all the state objects I need and pass it to Artix.  The initialized handlebars
is one of them.

The template language is different than the one from TinyTemplate and so I need
to adjust the blog theme.  This was a pretty minor change, though.

## Caching markdown results
Loading pages was pretty slow.  I tried to debug this and discovered that
converting markdown to html takes a significant long time.  This is several
seconds per 30 lines markdown.  To solve this issue, I decided to cache the
html string in memory once it's generated.  Every markdown just needs to be
parsed once now.

Rust did a very good job in making sure that the data is thread safe and I
think I found a good implementation.  Several worker threads can now access
the cache at the same time except if the markdown must be loaded.  The first
will load and the others will wait until they can read the cached value.

## Downsides of the optmimization
Since blog template and blog entries are only loaded once I have to restart
the server if I want to see any changes.  For development, this can be annoying.
To solve this, I plan to add an option in the config which causes the software
to read this data and never cache anything.

## Future optimizations
One other solution I can think of is to store the html output of the markdown
somewhere in a work directory instead of the memory.  If the modification date
of the markdown is newer than the html file it must be recreated.
