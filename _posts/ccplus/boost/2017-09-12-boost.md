---
layout: post
title:  "c++ lang: lib boost"
date:   2012-02-16 13:31:01 +0800
categories: lang
tags: c
---

* content
{:toc}


# QuickStart

## ubuntu - install lib

** Installing Boost on Ubuntu with an example of using boost array:**

### Install libboost-all-dev and aptitude

    $ sudo apt-get install libboost-all-dev
    $ sudo apt-get install aptitude
    $ aptitude search boost

### Where installed

    $ whereis boost
    boost: /usr/include/boost

    ### 64bits: /usr/lib/x86_64-linux-gnu/

    ### 32bits
    $ ls /usr/lib/libboost*.so

    /usr/lib/libboost_date_time-mt.so
    /usr/lib/libboost_date_time.so
    /usr/lib/libboost_filesystem-mt.so
    /usr/lib/libboost_filesystem.so
    /usr/lib/libboost_graph-mt.so
    /usr/lib/libboost_graph_parallel-mt.so
    /usr/lib/libboost_graph_parallel.so

### Code

    // file: main.cpp
    #include <iostream>
    #include <boost/array.hpp>

    using namespace std;
    int main(){
      boost::array<int, 4> arr = {{1,2,3,4}};
      cout << "hi" << arr[0];
      return 0;
    }

### Compile

    $ g++ -o s main.cpp

### Run

    $ ./s
    hi1

## build from source

    ### Get Boost
    $ wget http://sourceforge.net/projects/boost/files/boost/1.55.0/boost_1_55_0.tar.bz2
    $ tar xvfo boost_1_55_0.tar.bz2
    $ cd boost_1_55_0

    ### Show available libraries
    $ ./bootstrap.sh --show-libraries

    ### Build all except python bindings ( I don’t have python installed )
    $ ./bootstrap.sh --with-libraries=atomic,chrono,context,coroutine,date_time,exception,filesystem,graph,graph_parallel,iostreams,locale,log,math,mpi,program_options,random,regex,serialization,signals,system,test,thread,timer,wave

    ### Install prerequisites for the iostreams (for Ubuntu)
    $ sudo apt-get update
    $ sudo apt-get install build-essential g++ python-dev autotools-dev libicu-dev build-essential libbz2-dev

    ### Build with c++0x enabled
    ./b2 toolset=gcc cxxflags=-std=gnu++0x

    ### Install
    sudo ./b2 install



  [1]: http://stackoverflow.com/questions/7445331/what-does-the-const-void-mean-in-memmove
