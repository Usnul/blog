---
date: 2009-12-01T00:00:00Z
meta:
  _edit_last: "1"
  _edit_lock: "1265429990"
published: true
unlisted: true
status: publish
tags:
- conway
- gif
- gifsicle
- life
- optparse
- Projects
- python
title: Game of Life
type: post
url: /2009/12/01/game-of-life/
---

For my latest project, I'm implementing Conway's Game of Life in python into animated GIFs.

Before I even explain what Conway's Game of Life is, be amused by the below, generated animation:

<div style='text-align: center'>
  <img src="/images/queenbee.gif" alt="Queenbee">
</div>

As always, the code I'm using here is open source: <a href="http://www.github.com/jlfwong/GameOfLife/">Game Of Life @ Github</a>.
In addition to the source code, there's also a few animation demos such as the one above.

Conway's Game of Life is a cellular automaton following very simple rules, as outlined in the Wikipedia article. It is a zero player game played on a 2 dimensional grid of squares, each holding either a state of dead or alive. The state of any cell is dependent on the state of the 8 cells neighbouring it in the previous generation. There are only 4 rules.

From the Wikipedia article <a href="http://en.wikipedia.org/wiki/Conway%27s_Game_of_Life">Conway's Game of Life</a>:

>  1. Any live cell with fewer than two live neighbours dies, as if caused by
>  underpopulation.
>  2. Any live cell with more than three live neighbours dies, as if by
>  overcrowding.
>  3. Any live cell with two or three live neighbours lives on to the next
>  generation.
>  4. Any dead cell with exactly three live neighbours becomes a live cell.

The colours you see in the above animation represent the alive status of each cell and also how many neighbours that cell has if it's alive.

This project is my first time making use of two utilities: optparse in python, and gifsicle.

<b>optparse</b> is a python library designed to make the creation of command line application much simpler. Specifically it's targeted towards making application with many possible flags easy to maintain. lifeImage.py falls into this category. From the commandline, you can control the source of input, the colour scheme, the number of generations to output, the scaling of the image and various other things to produce the exact animation or image you want.

You can take a look at optparse here: <a href="http://docs.python.org/library/optparse.html">optparse @ docs.python.org</a>.
The tutorial included there was enough for me to create this application.

<b>gifsicle</b> is a command line application for the creation and modification of animated gifs. It can create gifs out of a sequence of images, convert an image into a sequence of images, or even modify replace a single frame of an animated gif with an external image.

You can see and download gifsicle here: <a href="http://www.lcdf.org/gifsicle/">Official Gifsicle Page</a>

Why did I use gifsicle instead of the much more universal convert in
ImageMagick? Simply put: gifsicle is faster. If someone would like to do a
benchmark to (dis)prove this, I'd be happy to post the results, but from simple
experimentation, it seemed obvious to me that gifsicle took less time to make
the animation.

What's Next?
============

Now that I have a working command line utility, my next goal is to make an AJAX
powered web interface for the thing. This might explain why I have `ProcMonitor`
in the github for Game of Life. The web interface was another key motivator
behind using gifs for the output medium. People may want to use these things for
avatar, and they're simply easier to share and move around than a java applet,
or a flash swf, or some database stored simulation. It also helps that gifs are
designed for palette based images, which works out nicely for optimizing the
file size of these animations. The first 1000 generations of acorn is 2.5 MB as
it is.

Another thing I want to do is make `lifeImage.py` read the `.cells` format from
the <a href="http://www.bitstorm.org/gameoflife/lexicon/">Life Lexicon</a>. This
would save me a lot of time having to code all the states myself. This will be a
very straightforward process, as the `.cells` files are simply plaintext with 2
lines of header.

Suggestions/bug fixes for my implementation of Game of Life are welcomed.

I'm almost 100% sure that some combination of the command line flags of
`lifeImage.py` don't work nicely together, and would like to know what they are.
