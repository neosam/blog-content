---
title: Running on NixOS
date: 2020-03-05
author: neosam
---

This blog now runs on [NixOS](https://nixos.org).  NixOS is a GNU/Linux
distribution but completely different than the others.  It uses the purely
functional package manager [Nix](https://nixos.org/nix/) and provides atomic
upgrades and downgrades.  The whole system is stored inside a sub directory and a
symlink defines which directory is the current active system.  During an
upgrade, a new directory is assembled which doesn't affect the currently running
system.  Only when the update ran without any errors, it will update this
current-system-symlink and the system atomically switches to the new system.
Since the old system is still in a sub directory, it's possible to switch back.
Even during the boot process, one can choose to boot from an older state.

The whole system is configured by one configuration file which is located
under __/etc/nixos/configuration.nix__.  During first contact with NixOS, this file looks
like a configuration file.  First, I have thought, it's pretty
nice but I'm messed up if the service I want is not supported by the system.
But as already mentioned, Nix is a purely functional language.  This language
with combination of a clever data structure allows all kind of customizations. 

This allows me to extend the system to clone the blog from my
GitHub repository, compiles it and runs it.  To have TLS, I could set up a
nginx server which acts as proxy and automatically handles lets encrypt to have
a valid certificate.  SSH access is enable by one specific setting and another
line of the configuration sets up the firewall to only allow access from port
22, 80 and 443.  It performs automatic updates and can even reboot the system
automatically.  If something goes wrong, I can simply boot from a previous
version.  This is all pretty awesome!

This is the file which is running at the moment I write this text:
[https://github.com/neosam/nixos-blog-setup/blob/942cff4/configuration.nix](https://github.com/neosam/nixos-blog-setup/blob/942cff4/configuration.nix).

This file basically holds all configuration for my server.  You could just
deploy the files from the repository, adjust the hostname and you would run
your own rusty-blog server.

There is the whole Grub configuration between line 11 to 13, nginx in line 20 to 30,
the configuration I created for rusty-blog between line 50-53 and two users to
separate the administrator from the server user between line 59 and 66.
Finally, automatically upgrades are enabled in line 68 and 69.

Extending the configuration with rusty-blog is done by including the
rusty-blog.nix file on line 7 which again uses a technique called overlay to
include current Rust and build the blog software from source if no binaries are
built yet.

I really like NixOS and Nix and I will come up with another post about the
details on how I set up the rusty-blog as a service later.