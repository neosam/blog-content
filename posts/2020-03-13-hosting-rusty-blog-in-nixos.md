---
title: Host rusty-blog with NixOS
date: 2020-03-13T09:00:00+01:00
author: neosam
tags: NixOS rusty-blog
---

It's very simple to deploy rusty-blog with NixOS.  All you need is
a hoster which allows you to boot from a NixOS ISO image so you can
run the installer.  Since rusty-blog is not part of NixOS, an
extension must be installed.  Lets rock!

## 1. CD into your NixOS configuration page

If you run it from an image, you are about to install NixOS on your
system and the system is mounted under /mnt:

```bash
cd /mnt/etc/nixos/
```

If you are already on an installed system

```bash
cd /etc/nixos/
```

## 2. Get the latest NixOS package

```bash
wget https://github.com/neosam/rusty-blog/releases/download/v0.0.2/rusty-blog-nixos-overlay-0.0.2.tgz
```

Check rusty blog releases for specific versions: https://github.com/neosam/rusty-blog/releases

## 3. Extract the files

``` bash
tar xvzf rusty-blog-nixos-overlay-0.0.2.tgz
```

## 4. Include rusty-blog in the nix configuration

Add ./rusty-blog under imports so it look like this:

```nix
   imports = 
     [
       ./hardware-configuration.nix
       ./rusty-blog
     ];
```

# 5. Enable and configure rusty-blog in the configuration

Add these lines to configuration.nix

```nix
    # Enable rusty blog
    services.rusty-blog.enable = true;
    
    # Content of the blog.  An example is here:
    # https://github.com/neosam/rusty-blog
    services.rusty-blog.documentRoot = "/path/to/the/rustyblog/document/root";

    # The root URL of the blog
    services.rusty-blog.context = "https://your-blog.name.com";

    # Use a custom user without 'wheel' permission
    services.rusty-blog.user = "blog";
```

# 6. Set up NGINX to run it on HTTPS

Add these lines to your configuration.nix

```nix
   services.nginx = {
     enable = true;
     virtualHosts."your-domain.com" = {
       # Use SSL
       forceSSL = true;

       # Let let's encrypt take care of valid certificates
       enableACME = true;

       default = true;

       # Redirect to the blog
       locations."/" = {
         proxyPass = "http://localhost:8080";
       };
     };
   };

   # Open SSH, HTTP and HTTPS in the firewall.
   networking.firewall.allowedTCPPorts = [ 22 80 443 ];
```

# Done

This should be it!  Install or update the system.
