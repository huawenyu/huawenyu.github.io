---
layout: post
title:  "ipv6"
date:   2014-02-16 13:31:01 +0800
categories: linux
tags: network
---

* content
{:toc}


# config

We only want to allow http connections to our product on the local network, to prevent customers publicly hosting the product and allowing connections from public IP addresses.

The way we are currently doing this is to look at the request's user host address, and filter out any non-private IP addresses.

For IPv4 this seems to be relatively straightforward, we allow only IP addresses that match the following:

    127.0.0.0/8 loopback
    10.0.0.0/8 private
    172.16.0.0/12 private
    192.168.0.0/16 private
    169.254.0.0/16 link-local

Also for IPv6 we allow only IP addresses that match the following:

    ::1/128 loopback
    fc00::/7 unique-local (private)
    fe80::/10 link-local

    // These days Unique Local Addresses (ULA) are used in place of Site Local. 
    // ULA has two variants: 
    //      fc00::/8 is not defined yet, but might be used in the future for internal-use addresses that are registered in a central place (ULA Central). 
    //      fd00::/8 is in use and does not have to registered anywhere.
    //      fe80::/10 is Link local addresses (prefixed with fe80) are not routable

This all seems to work for the limited test cases so far...

So the question is: Are there any edge cases that will not be covered by this e.g. VPNs, proxies etc or is there simply a better way to approach this problem?

  [1]: https://sourceware.org/gdb/onlinedocs/gdb/gdbserver-man.html
  [2]: https://sourceware.org/gdb/wiki/FAQ
  [3]: https://blogs.oracle.com/ksplice/entry/8_gdb_tricks_you_should
  [4]: http://sourceware.org/gdb/onlinedocs/gdb/Continuing-and-Stepping.html
  [5]: https://github.com/huawenyu/neogdb.vim
  [6]: https://networkengineering.stackexchange.com/questions/14965/icmp-redirect-static-routing

