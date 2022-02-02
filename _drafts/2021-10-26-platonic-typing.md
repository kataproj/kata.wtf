---
layout: post
title: "K#0001: Kata's Type System: Platonic Typing"
author: Cade Brown
email: me@cade.site
tags: [lang, types]
series: kata
image: /files/src/K0001/types_all.dot.webp
---

Buckle up! We're about to dive into the world of math, programming, and language. Particularly, how they interact and how they can be used in Kata via Platonic Typing, and how Kata's approach relates to existing ones

<!--more-->

  * TOC 
{: toc}

## First, An Aside

Let me start by introducing an excerpt from a dialogue between Parmenides and Socrates:

> **Parmenides**: if you say that things participate in ideas, must you not say either that everything is made up of thoughts, and that all things think; or that there are thoughts but have no thought?
>
> **Socrates**: in my opinion, the ideas are patterns fixed in nature, and other things are like them, and resemblances of them -- what is meant by the participation of other things in the ideas, is really assimilation to them.
>
> -- in [*Parmenides*](https://en.wikipedia.org/wiki/Parmenides_(dialogue)) by [Plato](https://en.wikipedia.org/wiki/Plato). 


If you're unfamiliar or need a refresher on what Platonism is, here's a good definition (emphasis mine):

> Platonism about mathematics (or mathematical platonism) is the metaphysical view that there are **abstract mathematical objects whose existence is independent** of us and our language, thought, and practices. 
> 
> **Just as electrons and planets exist independently of us, so do numbers and sets**. And just as statements about electrons and planets are made true or false by the objects with which they are concerned and these objects’ perfectly objective properties, so are statements about numbers and sets. 
>
> **Mathematical truths are therefore discovered, not invented.**
>
> -- Stanford, [*Platonism in the Philosophy of Mathematics*](https://plato.stanford.edu/entries/platonism-mathematics/)


Try to keep this in mind, as we'll revisit this later in the post. Specifically, try to apply it to programming languages, concepts, and objects. If you're interested, see our [philosophy page](/philosophy)


### Skip Ahead

**NOTE:** if you want to skip ahead to Kata's, system, [click here](#katas-approach)


## Type Systems, Semantics

When we talk about programming, we (primarily) talk about concepts. For example, when we program on whiteboards using [psuedocode](https://en.wikipedia.org/wiki/Pseudocode), loose definitions of types and functions are used. We don't care about the actual implementation, we care about the semantics of the code (i.e. the underlying processes and algorithms). We could say that we are working in a **top down** fashion.

However, when we're programming in a language, we're usually dealing with an actual implementation. We typically tie functionality directly to the name we choose for a given concept. In this case, programmers are working in a **bottom up** fashion. For example, most object-oriented type systems with inheritance are bottom up, since we start with nothing and build up to more specific types.

I've noticed that whiteboard programming is the best way to convey ideas to colleagues when we're working on a difficult problem, because all the "noise" in most programming languages is gone. When you understand a whiteboard full of psuedocode, the distance between math, computer science, and normal language (in our case, English) is reduced by (at least) an order of magnitude. as a result it takes less time to:

  * Write/describe an algorithm
  * Understand psuedocode written by someone else

If only there was some way to succinctly represent that in a way that would actually run on a physical machine... but before we can describe such a system, let's remind ourselves of some existing type systems that are being used my millions of programmers in the real world:

### C, C++, C# (3 generations of bottom-up design)

With most programming languages, there is a coupling between the abstract ideas, and the datastructures used to implement them. For example, in C the abstract idea of a `list` of some type `T` cannot be expressed -- a specific concrete data structure must be used. This is problematic for a few reasons:

  * There is no default implementation available, so a library or custom implementation must be used. any particular implementation may:
    * Be slow/inefficient for some types or all, or for different hardware
    * Have bugs present
    * Be incompatible with other implementations
    * Choose odd/non-standard names which is less readable, invites bugs from misconceptions, and slows development
  * Switching between different implementations is difficult
  * The idea is not composable (i.e. having a list of lists is difficult...)

Of course, C is an old language and is not meant to be a very high level language. Even C++ fixes many of these problems with its templating system although, arguably, it creates many more:

  * There is still the problem of switching between different implementations (i.e. if you use `std::vector<T>` everywhere it is still a specific implementation)
  * Since the template system is ad-hoc, it is very unnatural to use and has odd syntax. You have to jump through hoops that aren't intuitive or fundamental to the operation you're trying to do
    * This also results in indecipherable error messages, slowing development
  * For more complex types/models, multiple inheritance is required
    * This can degrade performance, as it requires virtual method dispatching

Going further, C# (aka (C++)++) fixes these (and provides a syntax with more forethought). In C#, multiple inheritance is outlawed, and [interfaces](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/types/interfaces) are used to organize functionality. A single type can implement multiple interfaces, but can only derive from 1 super type (i.e. single inheritance).

This means that, for C#:

  * The type hierarchy is a tree (as opposed to C++, which is a directed acyclic graph (DAG))
  * The interface hierarchy is a DAG (i.e. there can't be a cycle of interfaces)
    * Don't ask me how i found out...

As a result, no multiple inheritance dispatch is required, so concrete types always have a single dispatch table (this means it can be more efficient at runtime)

So, we're done, right? surely a DAG interface hierarchy on top of a tree type hierarchy is suitable for a modern programming language...

### Dynamic Approaches

Many modern languages with vastly different approaches to type systems have arisen. Many, however, are probably too dynamic, or are implemented in a way that makes it impossible to write efficient and robust code.

#### Python

[Python](https://www.python.org), for example, is a duck-typed language that also supports multiple inheritance. instead of using dispatch tables (like C++), it traverses the supertypes(s) at runtime using [MRO](http://python-history.blogspot.com/2010/06/method-resolution-order.html). Of course, Python code is typically slow even without using multiple inheritance... so, when the runtime is having to search the tree of types each time a method/attribute is used, it can slow your code down considerably.

Things are complicated further when you realize that the existence of duck-typing and multiple inheritance in Python is kind of redundant; shouldn't they just stick to one scheme or the other for polymorphism? after all, the [Zen of Python](https://www.python.org/dev/peps/pep-0020/) says that "There should be one-- and preferably only one --obvious way to do it"... and yet there are 2 ways to do it, neither of which obvious or complete

I don't think Python should be our "success story" for modern languages... conceptually, however, having multiple abstract base types/interfaces describing an object is a useful way to represent the concept of polymorphism. the biggest problem is that it's not clear how to implement this in a way that is both efficient and robust (yet).

#### Lua (and LuaJIT)

Lua ([LuaJIT](https://luajit.org/)) solves the typing problem a very interesting way: simply restrict the list of all types to a set of known types (specifically, there are [7 types](https://www.lua.org/manual/2.2/section3_3.html)). This means that the type system is a static type system, and the runtime can be optimized to use a single dispatch table. However, it has the restriction that no custom types can be defined.

So, although Lua is very fast, it can't really do everything you'd want out of a general purpose programming language (although, it's very useful for scripting when you need performance). 

#### Julia

A language that stands out is [Julia](https://julialang.org/). Although Julia is a high level dynamic language it can be just as fast as native code, and is being used for High Performance Computing (HPC) and Data Science (check their [benchmarks](https://julialang.org/benchmarks/)):

![Julia benchmarks](https://julialang.org/assets/benchmarks/benchmarks.svg){: .img-M .bk-inv }

What?? I was **just** told that performance comes from static type systems! How is Julia achieving this performance while allowing for dynamic type systems?

The answer is basically that Julia has a [unique type system](https://docs.julialang.org/en/v1/manual/types/) that is exploited by its runtime:

  * Most types are abstract, and cannot be instantiated directly
  * Types that can be instantiated are called concrete types, and all concrete types are final/[sealed (C#)](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/sealed) (which means no subtypes can be created)
    * Therefore, the type hierarchy for *real* objects is just a single concrete type

This restriction of interfaces/abstract types to a DAG, while concrete types are final, allows for a very efficient implementation of the type system using a JIT (Just-In-Time) compiler (some people call it JAOT (Just-Ahead-Of-Time) compiler)

Since the type can be exactly known, and it is known that no subtypes are allowed, there is no need to dispatch to a virtual method. The JIT compiler can directly call the method defined on the concrete type. this means the function can be inlined, or translated to a direct call without any indirection. It also means that values can be unboxed and optimized/inlined during code generation

Now, we must **really** be done... right?


### Kata's Approach

Kata's approach to types is different than all the aforementioned languages, although it is most similar to Julia's. Kata's type system is based on [mathematical platonism](https://en.wikipedia.org/wiki/Philosophy_of_mathematics#Platonism). You can read about more about [Kata's philosophy here](/philosophy), but in terms of traditional type systems, Kata's can be described as:

  * Having abstract types that cannot be instantiated directly, but can be related to other abstract types
    * Kata does not restrict abstract type relationships to a DAG or tree; instead, it allows for multiple inheritance, self-inheritance, and cyclic-inheritance
  * Having concrete types/templates that have abstract supertypes, which provide implementations for the abstract methods
    * These can be hardware specific, or can include performance hacks on certain platforms
  * Religious usage of [Design by Contract (DBC)](https://en.wikipedia.org/wiki/Design_by_contract)

Concrete types in Kata can be thought of as one of the following categories:

  * Datum types: represent some data that can be digitally encoded (examples: numbers, text data, etc.)
    * These are either immutable (constant), or can be frozen into an immutable object
  * Struct types: represent a collection of datum types, or other struct types
    * These are typically reference counted and garbage collected, and may be mutable (modifiable)

For example, the following figure describes builtin types and concepts that make up the `Datum` types (i.e. atomic, hashable, immutable data elements):

![type graph (Datum)](/files/src/K0001/types_datum.dot.webp){: .img-L }

Here is a figure describing even more of the default concepts:

![type graph (Any)](/files/src/K0001/types_all.dot.webp){: .img-L }

A few notes:

  * Notice that there is a cyclic relationship between `List[T]` and `Dict[K,V]`.Tthis is intentional, and can be understood as "a `List[T]` is a dictionary from `Natural` (index integer) to `T` (value), and a `Dict[K,V]` is a list of `(K,V)` tuples"
  * A `List` is a `Seq` (sequence), but a `Seq` is not neccessarily a `List`. For example, sequences can be infinite, but lists are finite


#### Platonic Typing

I refer to any type system that follows these principles as having "Platonic Typing" rules (AFAIK, this is a new concept). Within individual Kata languages (KataZero, KataScript, etc), concepts and implementations can be shared freely through different syntaxes that may be better suited for certain purposes. 

For `Datum` types, these are typically reducable to a bit string (i.e. a sequence of bytes) that can be interpreted as the same value, regardless of the hardware. Think of it as a universal data encoding for numbers and text.

While the builtin abstract types (like `List[T]`, `Dict[K,V]`, and so on) describe most useful types and cover >95% of everyday development tasks, more concepts can be described for a particular purpose that extends or specializes existing ones (using `is`/`as` keywords).

Take, for example, the study of biology -- it may require types for the concept of DNA, the concept of a protein, and the concept of a cell, which may interact with each other, existing structures, and ideas. The very basis of Kata's type system is this graph of concepts and their interactions, which can be understood by an abstract model of computation.


#### Different Than Interfaces

I don't use the term 'interfaces' to describe these abstract types, even though they are similar in nature. Instead, the term "concept" is used, because it is more accurate. Strictly speaking, all interfaces are concepts, but not all concepts are interfaces.

For example, interfaces cannot be cyclic, and still form a hierarchy (DAG). Concepts may be cyclic, self-describing, and thus form a graph; the only requirement requirement is that all concepts are related to the `Any` concept (which is, by definition, the broadest concept that encompasses any and all concepts and objects), and thus the `Any` concept has no out-going edges (i.e. is not described by any other concept)

To organize and describe this functionality, PLATO (Platform/Language Agnostic Typed Ontology) contains many common numeric, textual, and container concepts. Many of the names in `plato` namespace are aliased in the global namespace (i.e. `List[T]` is equivalent to `plato.List[T]`)

#### How To Implement On Real Hardware?

A small bootstrapping phase is required to implement some concrete types and objects that can be run on an actual physical machine. By default, Kata uses [LLVM](https://llvm.org/)'s types and operations (typically in the `std` namespace). Conceptually, though, code is being compiled by a mechanical mind, and ran in a digital simulation. 

Even "builtin" types (like `f64`, a 64 bit floating point number (alias of `plato.f64`)) are abstract. They typically map directly to an `std` type (such as `std.f64`) which provides a sane and efficient default implementation. The default implementation for any concept can be retrieved by using the `default()` function (i.e. `default(plato.f64) == std.f64`).

### Conclusion

Now, we begin the difficult part of actually implementing an initial compiler and formal language spec. I plan on making this blog a recurring fixture as i continue to work on and explore these ideas

<!--

while the builtin concepts (like `list[T]`, `dict[K,V]`, and so on) 


![type graph (all)](/files/src/K0001/types_all.dot.webp){: .img-L }

  sum(range(100) where $.isprime)


  sum(v for k, v in ents)


  sum(each x in range(100) <expr>)
  sum(<expr> for x in range(100))
  sum(<expr> for x in range(100))

  for x in range(100) where $.isprime {
    print(x)
  }


-->



<!--

```plato
# collatz.kc

# here, we declare a singleton type template 'CollatzGraph[T]'
# this implements the singleton pattern and allows us
single CollatzGraph[T] is graph[T is int] {

  seen: cache[T,bool]

  # ret edges from 'n'
  func edges(n: T)->iter[T] {
    if n % 2 == 0, ret [n // 2]

    ret [3 * n + 1]
  }

  # ret whether 'n' passes the collatz test (i.e. ends up at '1')
  func check(n: T)->bool @CacheLRU {
    # check if 'n' is in the depth first search
    ret (this.dfs(n) as iter[T]).contains(1)
  }
}

# get a reference to the singleton for a collatz graph
g: CollatzGraph[u32] = default

# now, check 1 through 10 (these should all print 'yes')
for n in range(1, 10) {
  print($'{n}: {g.check(n)}')
}

# now, import modules for graph vizualization
import graphviz
import kos.fs

# write to a text file, which can be rendered online
# for example, paste in this website: https://dreampuf.github.io/GraphvizOnline/
with fp = kos.fs.open('collatz.dot', 'T') {
  # this will render the graph to a text file, showing all traversals required
  #   to determine that 1 through 10 pass the collatz test
  fp.write(graphviz.dot(g, range(1, 10)))
}

```
-->