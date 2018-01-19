---
layout: post
published: true
title:  "Golang"
date:   2017-02-15 13:31:01 +0800
categories: golang
tags: golang
---

* content
{:toc}


# QuickStart

[An Introductory Tutorial][1]

## Install
I used following commands from GoLang official repository, it installed GoLang version 1.6 on my Ubuntu 14.04

``` bash
sudo add-apt-repository ppa:ubuntu-lxc/lxd-stable
sudo apt-get update
sudo apt-get install golang
```

## Set GOPATH

Just add the following lines to `~/.bashrc` and this will persist. However, you can use other paths you like as GOPATH instead of $HOME/go in my sample.

``` bash
export GOPATH=$HOME/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

## Vim-IDE

Add to .vimrc:

``` viml

Plug 'fatih/vim-go'

  let g:go_highlight_functions = 1
  let g:go_highlight_methods = 1
  let g:go_highlight_fields = 1
  let g:go_highlight_types = 1
  let g:go_highlight_operators = 1
  let g:go_highlight_build_constraints = 1

  let g:go_fmt_command = "goimports"
  let g:go_term_enabled = 1
  let g:syntastic_go_checkers = ['golint', 'govet', 'errcheck']
  let g:syntastic_mode_map = { 'mode': 'active', 'passive_filetypes': ['go'] }
  let g:go_list_type = "quickfix"

```

  [1]: https://www.toptal.com/go/go-programming-a-step-by-step-introductory-tutorial
