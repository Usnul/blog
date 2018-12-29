---
date: 2016-10-26
published: true
status: publish
title: Bending the Dynamic vs Static Language Tradeoff
type: post
url: post/bending-the-pl-curve
aliases: [bending-the-pl-curve]
---

Here are some things I think people want out of a programming language.

1. **Iteration Speed**: A sub-second edit-run cycle
2. **Correctness Checking**: A compiler that can tell me that my code is
   probably wrong without me having to run every single codepath
3. **Concise syntax**: Express intent fluidly without a lot of boilerplate
4. **Editing support**: Autocompletion, go to definition, go to usage,
   refactoring support that always works
5. **Debugging support**: Errors that are easy to debug

First, I want to talk about the tradeoffs that two historical camps make here,
then about how these tradeoffs are being bent, and finally how people bend them
the opposite way and put themselves in sad, unnecessary hell.

{:toc}

# The Dynamically Typed Camp

The dynamically typed camp of languages is where Python, JavaScript, PHP, Ruby,
and Scheme live. This is where I've spent most of my professional career,
including nearly all of the time I spent at [Khan Academy][2].

## Iteration Speed

Dynamically typed languages do great here. You edit a file, re-run your program
(possibly it automatically restarts itself), and you're probably back in
business in less than a second. This is absolutely wonderful for iterating on
ideas quickly and especially for doing things like pixel pushing in UIs.

## Correctness Checking

This is where dynamically typed languages fall flat on their faces. Let's say
you have a whole bunch of classes with a method called `.render(title)`, and
they all currently take one argument. Now you want to change *one* of those
functions to look like `.render(title, description)`. Someone sends you a code
review with this in it, and you ask if they updated all the right places.  They
respond "I hope so", and if you have a 100KLOC codebase, with minimal test
coverage, then that's pretty much as good as you're going to get.

## Concise Syntax

Dynamic languages here are generally pretty solid. With no type definitions,
there's just generally less to type.  To use opposite extremes here, the
difference here is pretty clear.

```python
# Python
a = [1, 2, 3]
```

```java
// Old versions of Java
ArrayList<Integer> list = new ArrayList<Integer>();
list.add(1);
list.add(2);
list.add(3);
```

## Editing Support

For the same reason as correctness checking, doing refactorings that get more
complex than a global string search and replace start to seriously suck in
dynamic languages. If you want to delete a method with a common name, or get
autocomplete on an object passed into a function, you might be shit out of luck.

You can still get *useful* autocomplete, but it'll be at least a little crippled
because of the fundamental ambiguity of what a line of code does until it's
actually run in dynamically typed languages.

## Debugging Support

The ability to drop down into a REPL with the full power of the language
whenever something crashes is actually one of the major reasons I've been so
happy in the dynamic camp for so long. In Python this looks like
`pdb.set_trace()`, in JavaScript, like `debugger`, in Ruby, like `binding.pry`.
In the middle of your breakpoint, you can define new functions, invoke arbitrary
functions, write data to files -- whatever you want.

# The Statically Typed Camp

Industry staples like C++, Java, and Objective-C live here with their functional
comrades Haskell and OCaml, plus some new company of Go, Swift, Scala, Rust, and
Elm. I'm living in this land for the foreseeable at [Figma][0] working in C++
and TypeScript.

## Iteration Speed

This is arguably the biggest downside of statically typed languages. Type
checking, as it turns out, is frequently slow. And since in most of these
languages, type resolution is pre-requisite to code generation, slow type
checking means slow compiling. Slow compiling means slow iteration time.

While I was an intern at Facebook, I needed to make some changes to WebKit. The
compile time on my Macbook was ~15 minutes for every change. Suffice to say,
this was not a fun experience. On the upside, I did read most of [Pro Git][1]
while I was waiting for XCode to build.

## Correctness Checking

If your `Post` and `Picture` classes both have a `.render`, and you want to
change the signature on `Post` but not on `Picture`, you've got no troubles in
static land.  An IDE will make this as easy as right clicking and "Change
Signature".  And if you do decide to do it manually because you need to go
manually decide on that second argument value at all the new call-sites, no
problem -- your compiler will quite happily tell you if you done goofed or not.

The level of safety you get here varies wildly by language. Most notable, most
compilers don't work too hard to try to figure out if a pointer is `null` before
telling you your code is A-OK.

In C++, the compiler will quite happily let you do this:

```c++
User* a = nullptr;
a->setName("Gertrude")
```

Haskell and Scala do their best to dodge this problem by not letting you have
`null`, instead representing optional fields explicitly with a
`Maybe User`/`Option[User]`, where it forces you to deal with the fact that it
might be missing, and not just assume it's there.

## Editing Support

Statically typed languages kill it here. Since, by definition, the type of every
variable must be known without needing to execute the code, your editor can be
quite confident which operations are valid on which variables, and helpfully
autocomplete them. It can also facilitate things like field renaming, automatic
documentation lookup, consistently working go-to definition, and go-to usages.

## Debugging Support

My experience varies here, but for the most part have been displeased by my
debugging experiences in statically typed languages. While gdb and friends will
let you evaluate certain expressions, you lose the ability to do arbitrary
manipulations like define debugging helper functions or easily write function
invocations on anything templated.

A particularly nasty class of this where you don't get any interactive console
at all to debug is complex compile errors. In C++, clang improved this
dramatically, but for code like this:

```c++
#include <vector>
#include <algorithm>
int main()
{
    int a;
    std::vector< std::vector <int> > v;
    std::vector< std::vector <int> >::const_iterator it = std::find( v.begin(), v.end(), a );
}
```

gcc used to output > 15000 characters of errors ([see the rest here][5]), which
starts like this:

```
/usr/include/c++/4.6/bits/stl_algo.h: In function ‘_RandomAccessIterator
std::__find(_RandomAccessIterator, _RandomAccessIterator, const _Tp&,
std::random_access_iterator_tag) [with _RandomAccessIterator =
__gnu_cxx::__normal_iterator*, std::vector > >, _Tp = int]’:
/usr/include/c++/4.6/bits/stl_algo.h:4403:45:   instantiated from ‘_IIter std::find(_IIter, _IIter, const _Tp&) [with _IIter = __gnu_cxx::__normal_iterator*, std::vector > >, _Tp = int]’
error_code.cpp:8:89:   instantiated from here
/usr/include/c++/4.6/bits/stl_algo.h:162:4: error: no match for ‘operator==’ in ‘__first.__gnu_cxx::__normal_iterator::operator* [with _Iterator = std::vector*, _Container = std::vector >, __gnu_cxx::__normal_iterator::reference = std::vector&]() == __val’
/usr/include/c++/4.6/bits/stl_algo.h:162:4: note: candidates are:
```

So that sucks, and I don't even have anything I can play with to introspect what
the issue is.

# Stuck

So for an era of programming, it felt like you were kind of stuck between two
worlds, each of which had pretty crappy tradeoffs. Then the two camps stopped
yelling across the river at each other and started to recognize that the other
team was maybe onto something. You see a similar middle ground emerging in
the Object Oriented vs. Functional holy war with languages like Scala and Swift
taking an OO syntax, functional thinking approach, and JavaScript being kind of
accidentally multi-paradigm.

But back to types. Let's talk about how people are trying to have their cake and
eat it too.

# The Best of Both Worlds

## Type Inference

Something about the following line of Java just feels insulting.

```java
ArrayList<Integer> list = new ArrayList<Integer>();
```

Why do I need to specify the type information twice? This feels super dumb. More
generally, if I do:

```
a = somefunction()
```

And the compiler knows the return type of `somefunction`, I, as the programmer,
shouldn't be forced to tell the computer information it already knows.

The more complete version of this idea is type inference, and the first time I
saw it was in Haskell, as concisely explained in [Learn You a Haskell for Great
Good!: Types and Typeclasses][9], and is explained with a bit more detail in
[TypeScript's Type Inference Documentation][11].

So we get concise syntax without sacrificing type information.

It's also now made its way into C++ via the [C++11 auto keyword][12], and is a
feature of most modern statically typed languages like Scala, Swift, Rust, and
Go.

## Decoupling Type Checking from Code Generation

If you define your language very carefully, you can make the compiler output not
dependent on the types (i.e. ignore the type information completely), and then
run type checking completely separately. The easiest way of defining a language
like this is to start with a dynamically typed language and start adding type
annotations. Facebook's [Flow][6] does this by adding type annotations to
JavaScript.

For instance, a bit of Flow annotated JavaScript might look like this:

```javascript
// @flow
function bar(x): string {
  return x.length;
}
bar('Hello, world!');
```

Compilation here is incredibly fast, because all it does is strip the type
annotations to produce this:

```javascript
function bar(x) {
  return x.length;
}
bar('Hello, world!');
```

While the type checker runs in the background, or possible only on demand.
With this type information, you can get better correctness guarantees, and much
better IDE support. We get all of this without the normal increase in iteration
time that comes with a blocking type checker.

Facebook took a similar approach to type annotating PHP with its language
[Hack][7]. Python 3.5 introduced progressive typing too, as described in the
[`typing` module][13].

An interesting side effect of having a compile target that closely resembles the
source is that you get all the benefits of the interactive debugging mentioned
before. Microsoft's [TypeScript][8] takes a very similar approach to Flow,
except that you can set a flag to localize type checking to file-internal type
checking, making the assumption that all imports are of the correct type, which
speeds up type checking considerably.

## Linting

Linting is a form of static analysis that happens outside of a compiler, and
typically on a dynamic language. One of the earliest ones I'd heard of was
Douglas Crockford's [JSLint][14] that, despite the possibly dynamic nature of
your program, might be able to confidently point out mistakes. There are
countless tools that do this for various languages, and I go into more depth
about the value of them in [Linters as Invariants][15]. This gives you a small
subset of the correctness guarantees that you get from a statically typed
language, like the guarantee that you aren't using a variable that isn't
declared anywhere, but typically isn't very helpful for inter-file analysis.

Good linters, like good IDE support, will allow for automatic fixing of errors,
like with the [ESLint `--fix` flag][17].

## Faster Compilers

This one is pretty self explanatory. If your compiler is super fast, the
edit-run iteration time isn't an issue. Boom.

To do this properly, you need to design the language carefully with that as a
design goal. Go did this.

> Go is an attempt to combine the ease of programming of an interpreted,
dynamically typed language with the efficiency and safety of a statically typed,
compiled language. It also aims to be modern, with support for networked and
multicore computing. Finally, working with Go is intended to be fast: it should
take at most a few seconds to build a large executable on a single computer.
>
> -- [Go FAQ][27]

## Better Compiler Error Messages

There have been numerous attempts to make debugging compilation errors a
non-issue by having sensible human-readable error messages, notably in Elm,
which Evan Czaplicki describes in his post [Compiler Errors for Humans][3]. The
cool thing about this is that it's seeing adoption by slightly more main-stream
languages like Rust, as Jonathan Turner explains in [Shape of errors to
come][4]. Much earlier, improvements to C++ error messages were a selling point
of clang over gcc as described in [Expressive Diagnostics][16]. But Elm and Rust
take it steps further.

So if you have a smart enough compiler, ideally one that can suggest to you how
to fix your problem, then some of the frustration of debugging those issues melt
away.

# All the Downside

Conversely, some very old techniques in the static world, and some very new
techniques in the dynamic world put you in a world of pain by taking downsides
from both camps.

## Runtime Failures in a Static Language

One of my least pleasant recurring memories working as an intern at Square in
Java was waiting a few minutes for a Java build, only to have it crash on boot
with a runtime error. This was happening because of fancy dynamic runtime
dependency injection with [Guice][18], which is why Square later wrote a library
that does it at compile time called [Dagger][19] to fix the problem.

The more general Bad Situation to avoid here are things that pass type checking,
cause runtime crashes, and require a recompile to fix.

Examples of this include things like:

- using runtime dependency injection like Guice
- using raw pointers instead of [smart pointers][20] in C++.
- liberal use of `void*` in C/C++ or using the `Object` type in Java then doing
  run-time downcasts all over the place

Now you're waiting for 15s for compilation, only to find that your code crashes
on boot repeatedly.

## Slow Transpiling/Bundling in a Dynamic Language

Just as the example above takes a static language that should be safe and makes
it unsafe, you can take a dynamic language and make it slow to iterate on! The
most common culprit of this is doing complex transpilation for a lot of code,
and doing that on every code change.

"Transpiling" started gaining momentum in the web development world when
compile-to-css languages like [Less][21] and [Sass][22], and compile-to-js
languages like [CoffeeScript][23] rolled around, but really blew up when
[React][26], [webpack][24] and [babel][25] started becoming a trio of choice.

The idea of using a more expressive languages than CSS and JavaScript to write
safer, more readable code is wonderful, but if you're not careful, you've now
managed to inherit the increased edit-run cycle time of a statically typed
language without inheriting any of the correctness guarantees.

Congratulations, you now have an unresponsive, unmaintainable mess.

# Closing thoughts

Overall, I'm pretty happy with the direction that things are going in PL world.
I hope much of the near future will be built on TypeScript (with
[`--strictNullChecks`][28]) instead of JavaScript on the front end, Rust instead
of C++, Scala instead of Java, Go instead of Node.js, and Swift instead of
Objective-C.

I haven't had the chance to play much with Go, Rust, or Swift, but things sound
kinda rosy.

If you like thinking about language tradeoffs and want stories more informed by
experience, you should read through Steve Yegge's [Is Weak Typing Strong
Enough?][29].

**EDIT**: My fellow uWaterloo Software Engineering 2014 classmate, [Ming-Ho
Yee][31],
is a PhD student in programming language design, so he naturally had some things
to say about this post. You can read his thoughts in [his response][30].


[0]: https://www.figma.com/
[1]: https://git-scm.com/book/en/v2
[2]: https://www.khanacademy.org
[3]: http://elm-lang.org/blog/compiler-errors-for-humans
[4]: https://blog.rust-lang.org/2016/08/10/Shape-of-errors-to-come.html
[5]: http://codegolf.stackexchange.com/questions/1956/generate-the-longest-error-message-in-c
[6]: https://flowtype.org/
[7]: http://hacklang.org/
[8]: https://www.typescriptlang.org/
[9]: http://learnyouahaskell.com/types-and-typeclasses
[10]: http://docs.scala-lang.org/tutorials/tour/local-type-inference.html
[11]: https://www.typescriptlang.org/docs/handbook/type-inference.html
[12]: http://en.cppreference.com/w/cpp/language/auto
[13]: https://docs.python.org/3/library/typing.html
[14]: http://www.jslint.com/
[15]: http://jamie-wong.com/2015/02/02/linters-as-invariants/
[16]: http://clang.llvm.org/diagnostics.html
[17]: http://eslint.org/docs/user-guide/command-line-interface#fix
[18]: https://github.com/google/guice
[19]: http://square.github.io/dagger/
[20]: http://stackoverflow.com/questions/106508/what-is-a-smart-pointer-and-when-should-i-use-one
[21]: http://lesscss.org/
[22]: http://sass-lang.com/
[23]: http://coffeescript.org/
[24]: https://webpack.github.io/
[25]: https://babeljs.io/
[26]: https://facebook.github.io/react/
[27]: https://golang.org/doc/faq#creating_a_new_language
[28]: https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html
[29]: https://sites.google.com/site/steveyegge2/is-weak-typing-strong-enough
[30]: http://mhyee.com/blog/pl_blog_response.html
[31]: http://mhyee.com/
