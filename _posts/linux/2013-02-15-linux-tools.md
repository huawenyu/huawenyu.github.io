---
layout: post
title:  "Linux Tools"
date:   2017-02-15 13:31:01 +0800
categories: linux
tags: admin
---

* content
{:toc}


# terminal application

## [great-terminal-replacements-for-gui-applications][1]

# Tools

## meld (GUI diff)

    # Ubuntu (Linux Mint 14.04.1-Ubuntu)
    1. Download from https://github.com/GNOME/meld
    2. The doc say:
        $ python3 setup.py install --prefix=/usr
    3. But that not work for me. If use the doc's build cmd, it works except it require GTK 3.14, current only GTK 3.10:
        $ python3 setup.py --no-compile-schemas install
    4. If meld requre GTK 3.14, so far it's hard to upgrade GTK 3.14 from 3.10, we should use old meld:
        Download a old release version from github/meld, install this old one.

## tmux

    For example, if the top-line show like this:
      > 1:zsh  2:ssl-log- 3:urlfilter  4:block

    tmux-command: swap-window -s 4 -t 2

# minicom

    1. set env var value
      MINICOM="-w"
      export MINICOM

    2. sudo also using current shell's env: sudo -E
      $ sudo -E minicom -b 115200 | tee ~/tmp/log.minicom

# nomachine

## hwoto over ssh

```
Error Message:

    Running NoMachine sessions over a SSH connection is disabled on this server.

The suggested workaround is:
  $ sudo vi /usr/NX/etc/server.cfg
      ClientConnectionMethods NX,HTTP,SSH

  $ sudo /usr/NX/bin/nxserver --restart
```

# Howtos

## Upgrade GTK 3.14

The GTK 3.10 is default version of Ubuntu (Linux Mint 14.04.1-Ubuntu).

### list current GTK version

    $ dpkg -l libgtk2.0-0 libgtk-3-0

  [1]: http://www.tuxarena.com/2014/03/20-great-terminal-replacements-for-gui-applications/
