---
layout: post
title:  "Linux howtos"
date:   2017-02-15 13:31:01 +0800
categories: linux admin
tags: admin howto
---

* content
{:toc}


# Add a new user 'newname' but reuse existed user 'oldname' env

If there have radius authentication, unplug the wire, login to your old local user.

## create a temp user

    $ sudo useradd -G sudo temp        //create a temp user in sudo group
    $ sudo passwd temp                           //create password for temp user

    ### logout any user, or just reboot.
    ### press Ctrl+alt+F2 to switch to tty2, login with user 'temp'.

## replace the user 'newname' env with user 'oldname'

    $ sudo userdel -fr newname          # delete the user with newname first
    $ sudo usermod -l newname oldname   # Change username from old to new
    $ sudo groupmod -n newname oldname  # Change the default groupname
    $ sudo usermod -md /home/newname newname    # this step may take a while, you can stop it by Ctrl+C, as far as I know, it will still work
    $ [optional] sudo usermod -c "New_real_name" newname        # Just to change Display Name

# Peek the output of a running process

## Using strace

    $ sudo strace -p<pid> -s9999 -e write
      (-s9999 avoids having strings truncated to 32 characters, and write the system call that produces output.)
    $ strace -p1234 -e trace= -e write=3        <<<===(a particular file descriptor)
    $ strace -o trace.log                       <<<=== If the output is scrolling by too fast

## Using `tail`

    $ cd /proc/1199

    Then look for the `fd` directory underneath which hold the file-descriptors (0: stdin, 1: stdout, 2: stderr):
    $ cd fd
    $ tail -f 1

