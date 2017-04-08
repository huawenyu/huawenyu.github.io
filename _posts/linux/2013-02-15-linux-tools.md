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


  [1]: http://www.tuxarena.com/2014/03/20-great-terminal-replacements-for-gui-applications/

