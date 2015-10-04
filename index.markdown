---
layout: site
---

**A Ruby platform for composition and collaboration.**

The internet has caused a fundamental change in general computing, yet many
programming languages are solidly centered in the Windows 3.0 era,
providing their equivalent to the Windows for Workgroups add-on to enable
networking. Unfortunately, Ruby is one of those languages.

To be relevant today, a programming language must provide simple yet powerful
facilities for composition and collaboration. A language does **not** need
general immutable state, purely pure functions, or complex type systems, no
matter how inferred.

Rubinius X is an experiment in modernizing Ruby. Rubinius X can be imagined as
a time machine that brings the future to the present, enabling us to write
modern programs now.

The Rubinius X vision to enable composition and collaboration for programs,
components, and systems is detailed below. These ideas will be tested through
building useful concurrent and distributed applications.

## The Shiny New

### Promises and Non-blocking IO

Concurrency is essential for programs that collaborate or compose other
components to efficiently use modern hardware. There are many approaches to
concurrency. [Promises](http://en.wikipedia.org/wiki/Futures_and_promises)
compose well and are naturally represented in an object-oriented language.

Ruby presently only provides threads as a concurrency mechanism. Threads are a
primitive construct and difficult to use well. They do not compose easily and
can be too costly to use as a general solution to many computing tasks.

### Persistent and Concurrent Data Structures

Modeling data well is the single most important concern for writing useful
applications. Wherever collaboration is centered on a data structure in a
program, that data structure must enable correct concurrent operation.

Much is made of immutability in some programming languages. But that
immutability is often paper thin; just below the surface there is rampant
mutation. Efficiently computing with complex structures as _values_ requires
this charade.

In Ruby, there is this fundamentally broken concept of _frozen_ objects. Broken
because its implementation requires littering code with brittle checks. It also
violates the principle of _pay for use_, requiring all code to pay the penalty
of checking whether an object is frozen.

In between a tyranny of immutability on the one hand and hacked together frozen
objects on the other, there exists a simple, elegant domain of persistent, or
_purely functional_ data structures.

### Mirrors and Object Capabilities

Composition is needed both between components and within components. The
semantics of primitive operations and their composition must be clearly
defined. [Mirrors](http://bracha.org/mirrors.pdf) are a construct that
provides structure for composition through encapsulation and separation of
object and meta-object operations.

[Object capabilities](http://en.wikipedia.org/wiki/Object-capability_model)
structure interactions to control collaboration. The ability to evaluate source
code or invoke system operations should not be distributed willy-nilly
throughout a program. Likewise, there is nothing inherently bad or dangerous
about metaprogramming, but for maximum usability, it needs to be structured.

### Code Loading

Code loading is the vital facility to enable combining components into a single
running program. It needs to happen lazily, with clearly expressed
dependencies, and the running program itself must be able to orchestrate it. In
other words, it needs an API just like every other part of the system.

### Primitives, Composition, and Consistency

Consistency is one of the most important characteristics of a system to aid
understanding and the ability to evolve to meet changing demands. In Rubinius
X, syntactic constructs that appear primitive simply delegate to the system.
For example, a Hash literal like `{ a: 1 }` simply delegates to `Hash.new`. If
the system refines `Hash.new`, it operates consistently everywhere.

The primitive operations on objects are defined and the more complex operations
are consistent compositions of those primitive operations. Modifying the system
has knowable consequences.

### Immutable Strings and ByteBuffers

Strings of characters are the most fundamental exchange format on the internet.
They may be human-readable strings or collections of bytes, but they are the
currency of collaboration.

Making Strings immutable permits numerous simplifications and optimizations for
collaboration and composition. Parsing and templated String construction are
fundamental operations.

ByteBuffers provide the facility for IO operations to collect data over time
before handing off a completed object. ByteBuffers also permit encoding
information to flow through a system.

### The Values _nil_ and _undefined_

The singleton object `nil` is useful. It's reasonably equivalent to the empty
set. Set operations with the empty set are well defined. For example, the
intersection of two or more sets may produce the empty set, and the
intersection of any set with the empty set is the empty set.

The singleton object `undefined` means that no value is possible. The
`undefined` object can be tested for _identically equal_ to `some_obj` but
cannot be sent any messages. If you get one and try to operate on it, an
exception is raised.

### Behaviors

Ruby has a confusing idea of "representing an X" versus "becoming an X". For
example, generally, using `obj.to_s` returns a representation of `obj` as a
String while using `obj.to_str` turns `obj` _into_ a String, or something. In
reality, it's very poorly defined in Ruby.

There is no need for this confusion. If we focus on behavior, then we merely
need a way to ask an object to provide something that behaves in a defined
manner. It need not return a different object, but if it doesn't, then after
requesting it behave a certain way, it should do so or return `undefined`.

One of the most common places to describe behavior expectations is at method
boundaries. In Rubinius X, a method definition like the following clearly
expresses that String or Integer behavior is expected in the respective
parameters:

    def exclaim(message: String("hi"), times: Integer())
    end

The operations `String(message)` and `Integer(times)` are automatically
performed before the method begins running so that `message` and `times` have
the expected behavior.

The preceding are not _types_. They are labels for behaviors. Where needed to
disambiguate, they can be useful. They are rarely needed.

## The Cleaning Up

### Encoding

The fact that some encodings are not convertible into one another is not solved
by permitting any object to have any possible encoding.

In Rubinius X, there is a single encoding for any running program. Transcoding
facilities are available at IO boundaries and encoded bytes can be passed
through the system using ByteBuffers.

### Keywords and APIs

All APIs use keywords where necessary. There are no position-significant APIs
like the following:

    IO.readlines( portname, separator=$/ [, options])
    IO.readlines( portname, limit [, options])
    IO.readlines( portname, separator, limit [, options])

### Numerics

Numbers are rather well-defined in mathematics, at least at the level of
undergraduate mathematics. Concepts like the reals embedding the rationals are
basic.

In Rubinius X, the numeric tower has Integer, Rational, Decimal, Complex and a
defined coercion protocol based on the concept of embedding.

### Purge the Perl

In general, anything inspired by Perl should be removed, but especially "magic
globals" like `$_` or `$1`. They are easily replaced by simply sending messages
to objects and providing objects as parameters.

### Global Variables

Gone. Period.

