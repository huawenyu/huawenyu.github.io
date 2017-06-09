---
layout: post
title:  "Load shared libraries error"
date:   2013-02-16 13:31:01 +0800
categories: linux
tags: troubleshoot
---

* content
{:toc}


# Load shared libraries error

Linux error while loading shared libraries: cannot open shared object file: No such file or directory

As AbiusX pointed out: If you have just now installed the library, you may simply need to run [ldconfig][1].
Usually your package manager will take care of this when you install a new library,
but not always, and it won't hurt to run ldconfig even if that is not your issue.

    sudo ldconfig -v

> ldconfig creates the necessary links and cache to the most recent
> shared libraries found in the directories specified on the command
> line, in the file /etc/ld.so.conf, and in the trusted directories
> (/lib and /usr/lib).

By default, ldconfig looks in:
  - `/lib`, `/usr/lib`,
  - and directories listed in `/etc/ld.so.conf`
  - and directories listed in `$LD_LIBRARY_PATH`.

## find the library

For the library is a dynamic library. You need to tell the operating system where it can locate it at runtime.
To do so, we will need to do those [easy steps][3]:

(1) Find where the library is placed if you don't know it.

    $ locate that_library.so

    $ cd /
    $ sudo find ./ | grep that_library.so

(2) Check for the existence of the dynamic library path environnement variable(LD_LIBRARY_PATH)

    $ echo $LD_LIBRARY_PATH

    ### if there is nothing to be display we need to add the default path value (or not as you wish)
    $ LD_LIBRARY_PATH=/usr/local/lib

(3) We add the desire path and export it and try the application

    $ expert LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/my_library/path.so.something
    $ ./my_app

## install the library

Here are a few solutions you can try:

### Dev package or wrong version

If that doesn't work, I would also check out [Paul's suggestion][2] and look for a "-dev" version of the library.
Many libraries are split into dev and non-dev packages. You can use this command to look for it:

    apt-cache search <libraryname>

This can also help if you simply have the wrong version of the library installed.
Some libraries are published in different versions simultaneously, for example, Python.

### Library location

If you are sure that the right package is installed, and ldconfig didn't find it, it may just be in a nonstandard directory.
By default, ldconfig looks in `/lib`, `/usr/lib`, and directories listed in `/etc/ld.so.conf` and `$LD_LIBRARY_PATH`.

If your library is somewhere else, you can:
  - either add the directory on its own line in `/etc/ld.so.conf`,
  - or append the library's path to `$LD_LIBRARY_PATH`,
  - or move the library into `/usr/lib`.
      sudo ln -sf /lib/$(arch)-linux-gnu/libudev.so.1 /lib/$(arch)-linux-gnu/libudev.so.0
  - Then run `ldconfig` to reload into system cache.

To find out where the library is, try this:

    sudo find / -iname *libraryname*.so*

(Replace `libraryname` with the name of your library)

If you go the `$LD_LIBRARY_PATH` route, you'll want to put that into your `~/.bashrc` file so it will run every time you log in:

    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/path/to/library

  [1]: http://linux.die.net/man/8/ldconfig
  [2]: https://stackoverflow.com/a/480786/22781
  [3]: http://www.gnu.org/software/gsl/manual/html_node/Shared-Libraries.html
