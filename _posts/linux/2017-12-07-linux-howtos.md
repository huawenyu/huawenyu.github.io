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

 
