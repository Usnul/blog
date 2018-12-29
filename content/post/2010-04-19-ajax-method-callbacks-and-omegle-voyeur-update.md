---
date: 2010-04-19T00:00:00Z
meta:
  _edit_last: "1"
  _edit_lock: "1274333018"
published: true
unlisted: true
status: publish
tags:
- AJAX
- callbacks
- jquery
- method
- omegle
- omegle voyeur
- Projects
title: AJAX Method Callbacks and Omegle Voyeur Update
type: post
url: /2010/04/19/ajax-method-callbacks-and-omegle-voyeur-update/
---

I finally got back around to updating Omegle Voyeur with the ability to
interfere, and decided to re-implement the whole thing in jQuery while I'm at
it. Since jQuery doesn't come with a built-in method of building classes, I used
<a href="http://www.danwebb.net/2008/1/31/low-pro-for-jquery">lowpro for
jQuery</a>. It's a port of a class building scheme from Prototype. It doesn't do
everything I could have hoped for, but it served most of my needs.

The other thing I implemented was a way of knowing when Omegle is blocking
requests. They have a more robust form of detection now - it isn't just manual
IP ban. Once you request too many things from them too fast, they start
requesting a captcha. Locally, this isn't a problem - I simply embed an iframe
with Omegle in it and provide instructions to the user. Hosted, this is a more
troublesome problem, since the captcha is directed towards an IP, so it must be
responded to from that IP. I have no solution to this problem at the moment, but
I'm going to look into implementing the whole thing using Greasemonkey so this
isn't an issue at all.

For now, you can see the latest version here: <a
href="http://jamie-wong.com/omegle/">Omegle Voyeur</a>.
Don't be surprised if it's down, and please go grab your own copy: <a
href="http://github.com/jlfwong/Omegle-Voyeur">Omegle-Voyeur @ github</a>.

Now on to the customary technical concept to go along with my own self promotion.

<h1>AJAX Method Callbacks</h1>
 While passing functions as arguments is a pretty standard thing among almost
 all languages, attempting to pass methods of specific instances as arguments in
 JavaScript presents an interesting problem. Consider the following:

```js
function car(price) {
    this.price = price;

    this.setPrice = function(price) {
        this.price = price;
    };
}

function pass666(func) {
    func(666);
}

var redcar = new car(2000);
alert(redcar.price);
redcar.setPrice(123);
alert(redcar.price);
pass666(redcar.setPrice);
alert(redcar.price);
```

As you might expect, the first two alerts will say 2000 and 123 respectively.
But the last one also says 123. Why?

It all has to do with what `this` refers to. Both in the initialization of
`redcar` and the modifier call `redcar.setPrice`, "this" refers to the instance
of the function car given the identifier name `redcar`. In the `pass666`
version, `this` refers to the function `pass666`. As a result, it does nothing
to modify the properties of the car because it isn't told anything about
`redcar`.

One way to fix this is to use a placeholder variable. I used "self". Change the
definition of car to the following yields the desired result.

```js
function car(price) {
    this.price = price;

    var self = this;
    this.setPrice = function(price) {
        self.price = price;
    };
}
```

In this example, it's difficult to see why you would ever want to use this in
the first place. The reason I encountered this problem is my need to use
instance methods as callback functions for AJAX calls. Here's an excerpt of the
jQuery version of Omegle Voyeur to see what I'm talking about.

```js
sendQuery: function(target,respFunc) {
  // Send a query to the omegle server
  var self = this;
  if (respFunc == null) {
    respFunc = function(self,data) {}
  }
  $.ajax({
    url: 'omegle.php?'+target,
    type: 'GET',
    dataType: 'json',
    success: function(data) {
      respFunc(self,data);
    }
  });
},
```

Sending a request to the Omegle server is a very common task in Omegle Voyeur,
so I wanted all the AJAX requests leaving from the same method. This means I
have to accept the callback function as a parameter. I've written all the
callback methods to accept a parameter `self` which will refer to the instance
of interest.

This aspect is one of the many things that makes Prototype's class system
superior to jQuery's. However, since jQuery makes a lot of other things nicer
and the two libraries don't play together very well, I decided to port over to
jQuery nonetheless. In Prototype, there's a function called <a
href="http://www.prototypejs.org/api/function/bind">bind</a> (not to be confused
with <a href="http://api.jquery.com/bind/">jQuery's bind</a> which does
something completely different,) which solves this problem elegantly.

To fix the redcar problem with the aid of Prototype without having to use a
placeholder variable, you can use bind like so:

```js
function car(price) {
    this.price = price;

    this.setPrice = function(price) {
        this.price = price;
    };
}

function pass666(func) {
    func(666);
}

var redcar = new car(2000);
alert(redcar.price);
redcar.setPrice(123);
alert(redcar.price);

pass666(redcar.setPrice.bind(redcar));

alert(redcar.price);
```

The line `pass666(redcar.setPrice.bind(redcar));` is what makes this work out.
We're explicitly saying that we want `setPrice` executed from the scope of the
instance.

If I ever have to hire someone for a web development job, I'll be sure to ask
something about this.
