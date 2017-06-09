---
layout: post
title:  "gdb"
date:   2017-02-16 13:31:01 +0800
categories: linux
tags: gdb
---

* content
{:toc}


# Quick Start:

https://github.com/dmoulding/boilermake.git

## the layout of sample

```sh
$ boilermake git:(master) git clone https://github.com/dmoulding/bebs_demo.git

$ boilermake git:(master) tree
.
├── COPYING
├── Makefile
├── MANUAL
├── README
└── test-app
    ├── animals
    │   ├── animal.cc
    │   ├── animal.hh
    │   ├── animals.mk
    │   ├── cat
    │   │   ├── cat.cc
    │   │   └── cat.hh
    │   ├── dog
    │   │   ├── chihuahua
    │   │   │   ├── chihuahua.cc
    │   │   │   ├── chihuahua.hh
    │   │   │   └── chihuahua.mk
    │   │   ├── dog.cc
    │   │   └── dog.hh
    │   ├── main.mk
    │   ├── Makefile -> ../Makefile
    │   └── mouse
    │       ├── mouse.cc
    │       └── mouse.hh
    ├── build
    │   ├── animals
    │   │   └── libanimals.a
    │   │       ├── animal.o
    │   │       ├── animal.P
    │   │       ├── cat
    │   │       │   ├── cat.o
    │   │       │   └── cat.P
    │   │       ├── dog
    │   │       │   ├── chihuahua
    │   │       │   │   ├── chihuahua.o
    │   │       │   │   └── chihuahua.P
    │   │       │   ├── dog.o
    │   │       │   └── dog.P
    │   │       └── mouse
    │   │           ├── mouse.o
    │   │           └── mouse.P
    │   ├── libanimals.a
    │   │   └── animals
    │   │       ├── animal.o
    │   │       ├── animal.P
    │   │       ├── cat
    │   │       │   ├── cat.o
    │   │       │   └── cat.P
    │   │       ├── dog
    │   │       │   ├── chihuahua
    │   │       │   │   ├── chihuahua.o
    │   │       │   │   └── chihuahua.P
    │   │       │   ├── dog.o
    │   │       │   └── dog.P
    │   │       └── mouse
    │   │           ├── mouse.o
    │   │           └── mouse.P
    │   └── talk
    │       ├── talk.o
    │       └── talk.P
    ├── libanimals.a
    ├── main.mk
    ├── Makefile -> ../Makefile
    ├── talk
    ├── talk.cc
    └── talk.mk

20 directories, 46 files
```

# Useful command:


  [1]: https://sourceware.org/gdb/onlinedocs/gdb/gdbserver-man.html
