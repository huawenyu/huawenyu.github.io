---
layout: post
title:  "boost: bind"
date:   2012-02-16 13:31:01 +0800
categories: lang
tags: c
---

* content
{:toc}


# How to use Boost.Bind

[Comefrom][http://www.radmangames.com/programming/how-to-use-boost-bind]

Function pointers are a powerful programming tool but
are extremely difficult to use with only the standard
C++ syntax at your disposal. Boost.Bind steps into this
breach, transforming the syntax from obtuse and
confusing to intuitive and accessible. This tutorial
will walk you through the basics of Boost.Bind in a way
that will have you being productive in no time.

Please note that Boost.Bind has a sister library that it
should be used in conjunction with; this is 
Boost.Function. In a nutshell Boost.Bind allows you to
dynamically bind a function into a function pointer
variable and Boost.Function allows you to create
variables that store function pointers. Also in some
situations it is useful to have at least a basic
understanding of standard C++ function pointer syntax so
if you're interested check out this tutorial.

If you are going to work with function pointers then
make sure you get to know both Boost.Function and
Boost.Bind as they are orders of magnitude better than
using just standard C++.

Binding a non-member function

Without Parameters

boost::bind(namespace::functionName);

This is the simplest possible use case and all that is
necessary here is to provide the address of the fully
qualified name of the function to bind. Note that an &#038;
is not required to address the function although you can
include one if you think it reads better semantically,
both forms are correct.

With Parameters

boost::bind(namespace::functionName, _1, _N);

Things get a bit more tricky here but not much. After
specifying the address of the function to bind, the
parameters also need to be specified. At its simplest a
function with three parameters would be bound with:

boost::bind(namespace::functionName, _1, _2, _3);

.

There is more that can be done here with reordering
parameters and specifying values to be used, see here

Binding a Member function

boost::bind(&Class::functionName, objPtr); // no parameters
boost::bind(&Class::functionName, objPtr, _1, _N); // with parameters

Binding a member function is much the same as binding
any other function with a couple of differences. Most
importantly, an instance of an object on which the
member function can be called must be provided to bind.
This is because a member function has access to class
data and if not called from an instance any access to
class data would result in undefined behaviour. The
other minor difference is that when addressing the
function to be bound the &#038; operator cannot be omitted.

Binding a template function

boost::bind(namespace::functionName<type1, typeN>);

So this looks much the same as a regular function bind
with the addition of template parameters to the
functionName. This is necessary so that the correct
template version of the function can be instantiated and
bound. To bind a template member function simply add an
object pointer as described in the member function
section.

Binding an overloaded function

// non-member
typedef void (*OverloadFuncType)(paramType1, paramTypeN);
boost::bind(static_cast<OverloadFuncType>(::overload), _1, _N);

// member
typedef void (Class::*OverloadFuncType)(paramType1, paramTypeN);
boost::bind(static_cast<OverloadFuncType>(&Class::overload), objPtr, _1, _N);

NOTE: You only need to use the preceding template if you
are trying to bind a function that has one or more
overloads with the exact same number of parameters,
otherwise you can do it the normal way.

You should immediately notice here that we need to
define a C++ function pointer type without using
Boost.Function and then need to cast the fully qualified
function name to that type before passing it to bind.
This is necessary to make sure we get the right overload
of the function (the cast conforms the passed pointer to
the correct signature) and without this step you will
receive a compile error about an "unresolved overloaded
function type" when you try to bind.

Extended example

Overloaded functions are somewhat trickier than the
other cases so I have included an extended example below
to help clarify things. Also if you are confused about
how to work with C++ function pointer types then check
out this tutorial for a crash course.

#include "boost/bind.hpp"

namespace
{
  void overload(int param1, float param2, int param3) {}
  void overload(int param) {}
  void overload(float param) {}

  class Class
  {
  public: // interface
    void overload(float param) {}
    void overload(int param) {}
  };

} // namespace

int main(int arg, char** argv)
{
  // non-member
  boost::bind(::overload, _1, _2, _3); // can bind normally
  typedef void (*NonMemberFuncType)(int);
  boost::bind(static_cast<NonMemberFuncType>(::overload), _1);

  // member
  Class* objPtr = new Class();
  typedef void (Class::*OverloadFuncType)(float);
  boost::bind(static_cast<OverloadFuncType>(&Class::overload), objPtr, _1);

} // main

Rebinding Function Signatures

As alluded to in Bind a non-member function this section
is on how boost::bind can be used to change the
signature of a method by changing its parameters. So
imagine that you have a function with signature:

void function(int, float, std::string)

now when you bind that method you could change its
interface to be any of the following:

void function(int, std::string)
void function(std::string, float, int)
void function()

This is all controlled by the

, _1, _2, _3);

at the end of the bind. The easiest way to think about
this is that for a function with N parameters being
bound there must be N comma separated specifications in
the bind call; one for each of the real parameters that
is in the function signature. So for a non-member
function with three parameters the bind call would look
like

boost::bind(namespace::functionName, slot_one, slot_two, slot_three);

. These slots correspond with the parameters of the
function being bound in order. A slot can contain one of
two things: A placeholder (_N), or a specified value. If
it is a specified value then that parameter has been
satisfied and will not need to be provided by the client
that calls the bound function. If it is a placeholder
then this is simply replaced by the value of the
corresponding parameter passed by a client when calling
the function pointer. So if a method was called like:

function(int(5), float(3.f))

_1 would be int(5) and _2 would be float(3.f).

Using placeholders and specified values allows you to
change the signature of the function pointer created.
See below for a fully compilable and extended example
that illustrates this feature.

#include "boost/function.hpp"
#include "boost/bind.hpp"

#include <string>
#include <iostream>

namespace
{
  void function(int number, float floatation, std::string string)
  {
    std::cout << "Int: \"" << number << "\" Float: \"" << floatation
              << "\" String: \"" << string << "\"" << std::endl;
  }

} // namespace

int main(int c, char** argv)
{
  // declare function pointer variables
  boost::function<void(std::string, float, int)> shuffleFunction;
  boost::function<void(void)> voidFunction;
  boost::function<void(float)> reducedFunction;

  // bind the methods
  shuffleFunction = boost::bind(::function, _3, _2, _1);
  voidFunction = boost::bind(::function, 5, 5.f, "five");
  reducedFunction = boost::bind(::function, 13, _1, "empty");

  // call the bound functions
  shuffleFunction("String", 0.f, 0);
  voidFunction();
  reducedFunction(13.f);

} // main

Boost.Function

If you've noticed that nothing has been said of how to
store function pointers returned from Boost.Bind then
you would be right. The best way to store function
pointers is through use of the Boost.Function library.
Check out how to use Boost.Function here.

A Final Word

There are more uses and intricacies to boost::bind than
those that are listed here, it is advised that you
familiarise yourself with the official documentation.
You can find the documentation at boost.org or you can
jump directly to the bind documentation that this
tutorial was written with reference to here.


  [1]: http://www.radmangames.com/programming/how-to-use-boost-bind
