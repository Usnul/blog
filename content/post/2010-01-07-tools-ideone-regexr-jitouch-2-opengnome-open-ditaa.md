---
date: 2010-01-07T00:00:00Z
meta:
  _edit_last: "1"
  _edit_lock: "1274332878"
published: true
unlisted: true
status: publish
tags:
- ditaa
- ideone
- jitouch
- mac os x
- open
- regexr
- regular expressions
- Tools
title: 'Tools: ideone, RegExr, jitouch 2, open, ditaa'
type: post
url: /2010/01/07/tools-ideone-regexr-jitouch-2-opengnome-open-ditaa/
---

Through reading proggit and hearing about new technology from classmates, every
once in a while, I build up a list of tools which I plan on checking out and see
whether they're useful enough to add to my regular routine. The first four on
this list fall into that category, and I may eventually find a use for the last.

ideone
======

<img src="/images/ideone.png" class="inline"/><a
href="http://www.ideone.com">ideone</a> is an in-browser, syntax-highlighted
code editor complete with interpreters and compilers. Basically, if you've ever
wanted to try out a language but really didn't feel like installing it on your
system, this is the perfect place to start. The site even runs Brainf**k.

There's another site which accomplishes the same task, but less elegantly: <a
href="http://codepad.org/">Codepad</a>. This site is so much less elegant that I
wasn't originally planning on posting it, but decided to once I saw there was a
<a href="http://www.vim.org/scripts/script.php?script_id=2298">Codepad vim
plugin</a>. There's also emacs integrations.

RegExr
======

<img src="/images/regexr-300x111.png" class="inline"/> <a
href="http://gskinner.com/RegExr/">Regexr</a> is an online tool, implemented in
Adobe Flex, to test out regular expressions in real time. If you haven't learned
about regular expressions yet, <a href="http://www.regular-expressions.info/">go
learn</a> right now. They're just about the most powerful text matching, user
verification and error correction tool in existence. They're also implemented in
nearly all languages now in some form or another. Before I saw this site, I
would test out all my regular expressions just using vim, but found it
frustrating when the expressions needed to be changed to be compatible with php.
So I'm likely going to start using RegExr instead.

jitouch 2
=========

<img src="/images/jitouch2.png" class="inline"/> <a
href="http://www.jitouch.com/">Jitouch 2</a> is an application expanding on the
multi-touch gestures available to MacBook Pro users who want to do more with
just the touch pad. The two big things that this enables me to do that I love
are opening links in new tabs using only taps on the keypad, and switching tabs
using a gesture equivalent to ctrl-tab to switch tabs. I actually saw this
reading <a href="http://blag.xkcd.com/">Randal Munroe's blag</a>.

Mac OS X/Gnome open
===================

<img src="/images/osx-open.png" class="inline"/>open & gnome-open are terminal
commands in Mac OS X and gnome respectively, but they do the same thing.
Whenever you double click on a file in Finder or Nautilus, the operating system
has a database of which extensions are opened by which applications. You can
leverage that database by calling `open`. Examples:

    open "Office Space.avi"

Open up Office Space in your default viewer.

    open http://www.jamie-wong.com

Visit my website from the commandline, opening in your default browser

    open -a "Adobe Photoshop CS3"

Launch Photoshop. `open -a` opens files with an application.

ditaa
=====

<img src="/images/ditaa.png" class="inline"/> <a
href="http://ditaa.sourceforge.net/">ditaa</a> is a tool for converting ASCII
art diagrams into graphical diagrams. This is pretty well illustrated in the
picture to the left. I haven't actually found much of a use for this yet, but
some of you might.

<div style='clear:both; height: 10px'></div>
