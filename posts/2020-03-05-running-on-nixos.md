---
title: Running on NixOS
date: 2020-03-05T09:00:00+02:00
author: neosam
---

This blog now runs on [NixOS](https://nixos.org).  NixOS is a very exotic and
interesting GNU/Linux distribution.  It uses the purely
functional package manager [Nix](https://nixos.org/nix/) and provides atomic
upgrades and downgrades.

I try to give a very simplified explanation.
The whole system is stored inside a sub directory and a
symlink called 'current-system' points to the directory which contains the - 
surprise - the current system.  During an
upgrade, a new directory is assembled while the current system stays untouched.
If the upgrade assembly process runs without any errors, it will atomically
modify the current-system-symlink and it becomes the new system.
Since the old system is still in another sub directory, it's possible to switch back.
Even during the boot process, one can choose to boot from an older state.

This sounds like a huge waste of data but Nix works a lot with symlinks.  Each
version of each package is only downloaded once and the system directories just
contains symlinks to the binaries.  Old systems can be deleted and not required
packages garbage collected

The whole system is configured by one configuration file which is located
under __/etc/nixos/configuration.nix__.  During the first contact with NixOS, this file looks
like an ordinary configuration file with a strange syntax.  First, I have thought, it's pretty
nice but I'm messed up if the service I want is not supported by the system.
But as already mentioned, Nix is a purely functional language.

It was not very hard to create a function which clones rusty-blog from
github, compile and provide it.  Rusty-blog doesn't support TLS yet and so I
needed a proxy server.  It was amazingly easy to set up a
nginx server which acts as proxy and automatically handles lets-encrypt to get
a valid certificate.

This is the file which is running at the moment I write this text:
[https://github.com/neosam/nixos-blog-setup/blob/942cff4/configuration.nix](https://github.com/neosam/nixos-blog-setup/blob/942cff4/configuration.nix).

This file basically holds everything which is required to run the server.
Everyone could clone the Git repository, adjust some values and set up a whole
system which hosts another blog.

Since things like nginx, ssh, ohmyzsh, etc are already supported by NixOS, they
can be set up by just configuring them in the configuration file.  Extending the configuration with rusty-blog needed more tweaks which is done by including the
rusty-blog.nix file on line 7 which again uses a technique called overlay to
include current Rust and build the blog software from source if no binaries are
built yet.

I will come up with another post about the
details on how I set up the rusty-blog as a service later.

