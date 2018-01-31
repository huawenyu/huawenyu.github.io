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

## curl

### options
    -k, https
    -v, verbose
    -o, output to file
    --limit-rate,  Example: curl -k -o /dev/null --limit-rate 8b www.tired.com
    --speed-limit, Example: --speed-limit 100 and it will exit if less than 100 bytes per second are downloaded over a 30 second period.
    -#, --progress-bar, show percentage

### sample

    ### Last long time to get a large file to /dev/null
    curl -o /dev/null -x 10.1.1.123:8080 -U guest:guest --limit-rate 8b http://172.18.2.169/upload/linoxu/debug_out

### statistics

#### data + speed

    $ curl -kv -o ~/tmp/log.1 --limit-rate 8b www.tired.com
    100   184  100   184    0     0      8      0  0:00:23  0:00:22  0:00:01     7
    
    
    ### headers: total,download,upload,speed,time
    % Total    % Received % Xferd  Average Speed          Time             Curr.
                                   Dload  Upload Total    Current  Left    Speed
    0  151M    0 38608    0     0   9406      0  4:41:43  0:00:04  4:41:39  9287

#### % percentage

    -#, --progress-bar
    $ curl -kv -# -o ~/tmp/log.1 --limit-rate 8b www.tired.com
    [##########################                                                37.0%]

### press OK

    $ curl --form upload=@localfilename --form press=OK [URL]

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

### rename-window <name>

    For example, if the top-line show like this:
      > 1:zsh  2:ssl-log- 3:urlfilter  4:block

### swap-window -s(src) 4 -t(dst) 2
    tmux-command: move-window/swap-window -s 4 -t 2         <=== move/swap s(src) to t(dest)

### swap-pane -s(src) 4 -t(dst) 2

    Prefix+q: show pane number

# minicom

    1. set env var value
      MINICOM="-w"
      export MINICOM

    2. sudo also using current shell's env: sudo -E
      $ sudo -E minicom -b 115200 | tee ~/tmp/log.minicom

# nomachine

## remote control over ssh

```
Error Message:

    Running NoMachine sessions over a SSH connection is disabled on this server.

The suggested workaround is:
  $ sudo vi /usr/NX/etc/server.cfg
      ClientConnectionMethods NX,HTTP,SSH

  $ sudo /usr/NX/bin/nxserver --restart
```

# Static code analyzers/checker for C

## cppchecker

### install
    $ git clone https://github.com/danmar/cppcheck.git
    $ cd cppcheck
    $ make SRCDIR=build CFGDIR=/usr/local/bin/ HAVE_RULES=yes CXXFLAGS="-O2 -DNDEBUG -Wall -Wno-sign-compare -Wno-unused-function"

    ### 'make install' also build and requires the CFGDIR option to be passed.
    $ sudo make install CFGDIR=/usr/local/bin/ HAVE_RULES=yes CXXFLAGS="-O2 -DNDEBUG -Wall -Wno-sign-compare -Wno-unused-function"

### usage

    $ cppcheck --enable=all wad_http_cache.c

## cpplint

This is automated checker to make sure a C++ file follows Google's C++ style
guide (https://google.github.io/styleguide/cppguide.html).

    https://github.com/google/styleguide/tree/gh-pages/cpplint

# Howtos

## Upgrade GTK 3.14

The GTK 3.10 is default version of Ubuntu (Linux Mint 14.04.1-Ubuntu).

### list current GTK version

    $ dpkg -l libgtk2.0-0 libgtk-3-0

  [1]: http://www.tuxarena.com/2014/03/20-great-terminal-replacements-for-gui-applications/
