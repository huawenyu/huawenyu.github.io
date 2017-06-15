---
layout: post
title:  "Semaphore, Mutex, Spinlock"
date:   2017-02-16 13:31:01 +0800
categories: linux
tags: ipc
---

* content
{:toc}


# Quick Start:

- Semaphore: Use a semaphore when you (thread) want to sleep till some other thread tells you to wake up.
- Semaphore 'down' happens in one thread (producer) and semaphore 'up' (for same semaphore) happens in another thread (consumer) e.g.: In producer-consumer problem, producer wants to sleep till at least one buffer slot is empty - only the consumer thread can tell when a buffer slot is empty. Mutex: Use a mutex when you (thread) want to execute code that should not be executed by any other thread at the same time. Mutex 'down' happens in one thread and mutex 'up' must happen in the same thread later on. e.g.: If you are deleting a node from a global linked list, you do not want another thread to muck around with pointers while you are deleting the node. When you acquire a mutex and are busy deleting a node, if another thread tries to acquire the same mutex, it will be put to sleep till you release the mutex.
- Spinlock: Use a spinlock when you really want to use a mutex but your thread is not allowed to sleep. e.g.: An interrupt handler within OS kernel must never sleep. If it does the system will freeze / crash. If you need to insert a node to globally shared linked list from the interrupt handler, acquire a spinlock - insert node - release spinlock.

## howto generate core file


# Useful command:


## gdbserver


  [1]: https://sourceware.org/gdb/onlinedocs/gdb/gdbserver-man.html
  [2]: https://sourceware.org/gdb/wiki/FAQ
  [3]: https://blogs.oracle.com/ksplice/entry/8_gdb_tricks_you_should
  [4]: http://sourceware.org/gdb/onlinedocs/gdb/Continuing-and-Stepping.html
  [5]: https://github.com/huawenyu/neogdb.vim
  [6]: http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.dui0489f/CIHDGFEG.html
