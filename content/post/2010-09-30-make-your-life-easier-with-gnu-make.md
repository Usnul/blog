---
date: 2010-09-30T00:00:00Z
meta:
  _edit_last: "1"
  _edit_lock: "1287294610"
  _wp_old_slug: ""
published: true
status: publish
tags:
- cs241
- make
- school
title: Make your life easier with GNU Make
type: post
url: /2010/09/30/make-your-life-easier-with-gnu-make/
---

I've been trying to keep at least one post per month going, and I was sick with stomach flu today, so here goes:

As our CS assignments get more and more complex, it's starting to become time consuming to build, test, and submit each assignment. I could either do this every time I want to test:

```bash
java cs241.binasm < a2p8.asm > a2p8.mips
java mips.array a2p8.mips < a2p8.sample.in
java mips.array a2p8.mips < a2p8.onenode.in
```

Or I could just run `make`.

What is make?
=========

[GNU Make](http://www.gnu.org/software/make/) is a system for constructing executables or other non-source code files from source code. It allows you to specify dependencies for files and define rules for constructing them from source. For example, the rule to build `a2p8.mips` would look like this:

```make
a2p8.mips: a2p8.asm
    java cs241.binasm < a2p8.asm > a2p8.mips
```

This says, to make `a2p8.mips`, run `java cs241.binasm < a2p.asm > a2p8.mips`. To use `make`, stick your ruleset in a file called `Makefile` in the same directory as your source then run `make`. This command will only run if `a2p8.asm` has changed since the last time it was run. These dependencies stack as well. For instance, say I need to join two files together before I compile them. I can define a ruleset like this:

```make
linked.mips: linked.asm
    java cs241.binasm < linked.asm > linked.mips

linked.asm: a2p7.asm print.asm
    cat a2p7.asm print.asm > linked.asm
```

This will link the files together before it tries to compile them. It'll also make sure it's up to date whenever you change either `a2p7.asm` or `print.asm`. The build rule at the top of the `Makefile` is called the default rule. This is the one that gets run when you just run `make` at the command line. You can also pass a parameter to tell `make` to construct something specific. For instance, I could run `make linked.asm` here to just build the linked file without actually compiling it to machine code.

Automatic Variables in Make
=================
One of the principles of writing good code is DRY: Don't Repeat Yourself. Make provides various ways to support this through the use of automatic variables. Automatic variables are ones that are set for you with useful values. For instance, the following rule:

```make

linked.asm: a2p7.asm print.asm
    cat a2p7.asm print.asm > linked.asm
```

can be reduced to this:

```make
linked.asm: a2p7.asm print.asm
    cat $^ > $@
```

The variable `$^` is a space separated list of all the dependencies. `$@` is the target. See [GNU make - Automatic Variables](http://www.gnu.org/software/make/manual/make.html#Automatic-Variables) for the full list and description of such variables.

Writing Implicit Rules
=============
Another unnecessary piece of code duplication exists for compiling multiple files of the same type. For instance, the following is repetitive:

```make
a2p8.mips: a2p8.asm
    java cs241.binasm < $< > $@

a2p7.mips: a2p7.asm
    java cs241.binasm < $< > $@
```

Instead, I can define my own implicit rule for building all `*.mips` files like so:

```make
%.mips: %.asm
    java cs241.binasm < $*.asm > $*.mips
```

Testing Using Make
============
One of the things I took away from my co-op term at The Working Group was the benefits of test driven development. Untested code is broken code, and manual testing is tedious and annoying. As your code gets more and more complicated, there will be more and more edge cases to deal with. These cannot be dealt with a single test case. Normally, you would be forced to write multiple input files and pass them each individually to the executable to see if everything worked. I don't like having to do this. So instead, I defined my own ruleset:

```make
ASSIGNMENT = a2p7

test: linked.mips Empty.test All.test

%.test: $(ASSIGNMENT).%.in
    @echo ----------- $* ------------------
    java mips.array linked.mips < $^

%.mips: %.asm
    java cs241.binasm < $*.asm > $*.mips

linked.asm: $(ASSIGNMENT).asm print.asm
    cat $^ > $@
```

This one merits a bit of explanation. The default task here is `test`, and it has no recipe associated with it. Instead, it just makes sure all of its dependencies are up to date. `Empty.test` and `All.test` here aren't actual files. They're simple methods of referring to another rule, in this case the `%.test` implicit rule.

The `%.test` implicit rule depends on a corresponding input file. For example, `Empty.test` will rely on `a2p7.Empty.in`, which will then be passed in as input to the executable. The result from running looks like this:

```bash
$  make
----------- Empty ------------------
java mips.array linked.mips < a2p7.Empty.in
Enter length of array: MIPS program completed normally.
$01 = 0x000001c4   $02 = 0x00000000   $03 = 0x00000000   $04 = 0x00000000
$05 = 0x00000000   $06 = 0x00000000   $07 = 0x00000000   $08 = 0x00000000
$09 = 0x00000000   $10 = 0x00000000   $11 = 0x000001c4   $12 = 0x00000000
$13 = 0x00000000   $14 = 0x00000000   $15 = 0x00000000   $16 = 0x00000000
$17 = 0x00000000   $18 = 0x00000000   $19 = 0x00000000   $20 = 0x00000000
$21 = 0x00000000   $22 = 0x00000000   $23 = 0x00000000   $24 = 0x00000000
$25 = 0x00000000   $26 = 0x00000000   $27 = 0x00000000   $28 = 0x00000000
$29 = 0x00000000   $30 = 0x01000000   $31 = 0x8123456c
----------- All ------------------
java mips.array linked.mips < a2p7.All.in
Enter length of array: Enter array element 0: Enter array element 1: Enter array element 2: 123
0
-456
MIPS program completed normally.
$01 = 0xfffffe38   $02 = 0x00000003   $03 = 0x00000001   $04 = 0x00000000
$05 = 0x00000000   $06 = 0x00000000   $07 = 0x00000000   $08 = 0x00000000
$09 = 0x00000000   $10 = 0x00000000   $11 = 0x000001d0   $12 = 0x00000000
$13 = 0x00000000   $14 = 0x00000000   $15 = 0x00000000   $16 = 0x00000000
$17 = 0x00000000   $18 = 0x00000000   $19 = 0x00000000   $20 = 0x00000000
$21 = 0x00000000   $22 = 0x00000000   $23 = 0x00000000   $24 = 0x00000000
$25 = 0x00000000   $26 = 0x00000000   $27 = 0x00000000   $28 = 0x00000000
$29 = 0x00000000   $30 = 0x01000000   $31 = 0x8123456c
```

The `echo` line above that simple outputs the name of the test before running it so it looks nicer on the console. The `@` before the echo prevents make from displaying the command, otherwise it would look like this:

```bash
$  make
cat a2p7.asm print.asm > linked.asm
java cs241.binasm < linked.asm > linked.mips
echo ----------- Empty ------------------
----------- Empty ------------------
```

Submitting to Marmoset from the Commandline
=============================
Just for kicks and because I wanted to mess around with [Mechanize](http://github.com/tenderlove/mechanize), I built a ruby script which will submit to Marmoset for me. You can try it out yourself if you have Ruby and [Rubygems](https://rubygems.org/) by running

```bash
sudo gem install marmoset
```

Or you can browse the code yourself here: <http://github.com/jlfwong/MarmosetSubmit>

Here's how you can integrate the submission process into your `Makefile`:

```make
submit: $(ASSIGNMENT).asm
    marmoset -u jlfwong -c cs241 -a $(ASSIGNMENT) -f $(ASSIGNMENT).asm
```

For now, it only submits - there's no way of checking to see if it was successful or running the release tests. I can build that if people are really interested in that, or you can fork the github repo and do it yourself.

Well, that's all for now. Time to study up for Electrodeath.
