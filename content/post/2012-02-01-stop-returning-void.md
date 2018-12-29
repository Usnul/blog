---
date: 2012-02-01T00:00:00Z
tags:
- literate programming
- javascript
- c++
- jQuery
- api
title: Chaining, or Why You Should Stop Returning Void
unlisted: true
url: /2012/02/01/stop-returning-void/
---

*Edit from 2018: The tone of this post is a little cringe-worthy to me now in retrospect, and I think that many APIs are probably clearer when you return void. Leaving this here for posterity.*

Do you have code in your codebase that looks like this?

```c++
formPanel.setSize(300, 150);
formPanel.add(usernameLabel);
formPanel.add(usernameField);
formPanel.add(passwordLabel);
formPanel.add(passwordField);

frame.add(formPanel);
```

Well, I'm here to tell you that your API is stupid. You should be trying to do
this instead:

```c++
formPanel
  .setSize(300, 150)
  .add(usernameLabel)
  .add(usernameField)
  .add(passwordLabel)
  .add(passwordField)
  .addTo(frame);
```

Since you're repeatedly dealing with the same variable here, why should you need
to write the name of that variable more than once?

Put another way, this provides all of the same semantic information in fewer
characters. Brevity is bliss.

Implementation
--------------

Chaining, is basically just returning `this` and using that result to call
another member method.  The idiom looks the same in most object oriented
languages, but I'll use C++ in my examples because it presents some interesting
problems.

As a simple example in C++, you could implement a class with chaining getters
and setters like this:

```c++
class Rectangle {
    public:
        Rectangle() :
            _width(0),
            _height(0),
            _top(0),
            _left(0)
        {}

        int width()  { return _width;  }
        int height() { return _height; }
        int top()    { return _top;    }
        int left()   { return _left;   }

        Rectangle& width(int w)  { _width  = w; return *this; }
        Rectangle& height(int h) { _height = h; return *this; }
        Rectangle& top(int t)    { _top    = t; return *this; }
        Rectangle& left(int l)   { _left   = l; return *this; }

    private:
        int _width;
        int _height;
        int _top;
        int _left;
};
```

Then you'll be able to use it like this:

```c++

Rectangle rectangle;

rectangle
    .width(100)
    .height(50)
    .top(10)
    .left(75);

cout << "Rectangle:" << endl
     << "\twidth: "  << rectangle.width()  << endl
     << "\theight: " << rectangle.height() << endl
     << "\ttop: "    << rectangle.top()    << endl
     << "\tleft: "   << rectangle.left()   << endl
     << endl;
```

*As an aside - notice that `cout` facilitates chaining by returning an
`ostream&`*

The setters in most libraries that don't support chaining will either return
`void` or a response code indicating whether the setting was successful.

Returning void doesn't provide any additional information, so I only see its
benefit as a matter of performance, and I suspect dealing with an extra returned
reference is not your performance bottleneck.

Returning a response code has it's merits, but in most language with exceptions,
you can just throw one in the case of an invalid property set instead. There are
certainly legitimate reasons to use response codes, and that's why this article
isn't called "Why You Should Stop Returning Response Codes".

As an added bonus, it means you don't need to have constructor calls like

```c++
Rectangle rectangle(100, 50, 10, 75);
```

For an explanation of why I think constructor calls like this are terrible, see
my post [Name your Arguments!](/2011/11/28/name-your-arguments/).

Fun with Preprocessor Macros
----------------------------

If you notice in the C++ example above, there's a repeated pattern which we can
get rid of if we use a bit of dirty macros. (You probably don't want to use
these macros in production code.) While I was working on schoolwork, I wanted to
see if I could build the API I wanted with less code - something like this:

```c++
class Rectangle {
    public:
        Rectangle() :
            _width(0),
            _height(0),
            _top(0),
            _left(0)
        {}

    PROPERTY(Rectangle, int, width)
    PROPERTY(Rectangle, int, height)
    PROPERTY(Rectangle, int, top)
    PROPERTY(Rectangle, int, left)
};
```

And this can be accomplished with preprocessor macros which look like this:

```c++
#define GETTER(type, var) \
    type& var() { return _##var; }

#define SETTER(classtype, type, var) \
    classtype& var(type _##var) { this->_##var = _##var; return *this; }

#define PROPERTY(classtype, type, var) \
    public: \
        GETTER(type, var) \
        SETTER(classtype, type, var) \
    private: \
        type _##var;
```

**NOTE**: This macro leaves the class in private access, so anything following
the `PROPERTY` directives will be private unless otherwise specified.

Chaining, Static Typing, and Inheritance.
-----------------------------------------

Given `RotatedRectangle` which inherits from `Rectangle`:

```c++
class RotatedRectangle : public Rectangle {
    public:
        RotatedRectangle() :
            _rotation(0)
        {}

    int rotation() { return _rotation; }
    RotatedRectangle& rotation(int r) { _rotation = r; return *this; }

    private:
        int _rotation;
};
```

if you try to do this

```c++
rectangle
    .width(100)
    .height(50)
    .top(10)
    .left(75)
    .rotation(45);
```

you'll wind up with this compiler error:

     error: ‘class Rectangle’ has no member named ‘rotation’

which is true. This happens because the return type of `Rectangle::left` is
`Rectangle&`. The unfortunate reality of chaining in statically typed languages
is that it doesn't play nice with inheritance.

There are various solutions to this, like reimplementing all the methods or
making a bunch of pure virtual functions on the parent classes, but none of them
are very pretty. See [Method chaining + inheritance don't play well
together?](http://stackoverflow.com/questions/551263/method-chaining-inheritance-dont-play-well-together)
on Stack Overflow for more information.

This isn't a problem in dynamically typed languages like Python and JavaScript
because they'll just see if the method exists on that object at runtime.

Chaining in jQuery
------------------

The first time I saw this kind of syntax was jQuery, and the API for it is an
incredible amount better because of it. For example, it lets you do stuff like
this:

```javascript
$('a#button')
  .text('Click Me!')
  .css({
    backgroundColor : 'red',
    font-size       : '72pt'
  })
  .click(function() {
    alert('I have been clicked!');
  });
```

jQuery also facilitates chaining between objects. Take a look at the following
example which fades in a container div then turns all of the links green.

```javascript
$('.container')
  .fadeIn()
  .find('a')
    .css('color', 'green');
```

Since this just requires having methods that return other chainable objects,
this is nothing special. What *does* make jQuery's chaining interesting is the
ability to go back up the chain with `.end()`.

I learned about this reading [Ben Kamens][]' post [.end() makes jQuery DOM
Traversal Beautiful][bjkend], so I'm just going to take his excellent example
verbatim.

```javascript
$("#container")
    .show()
    .find(".error")
        .hide()
        .end()
    .find(".zoo")
        .css("background-color", "white")
        .find(".monkeys")
            .empty()
            .end()
        .find(".title")
            .text("The zoo is empty")
            .end()
        .find("input")
            .val("")
            .end()
        .animate({height: 250});
```

`.end()` lets you structure your code in a way that mimics the structure of the
elements - namely its tree structure.

[Ben Kamens]: http://bjk5.com/
[bjkend]: http://bjk5.com/post/14819494947/end-makes-jquery-dom-traversal-beautiful

In need of a better API? Why not Chaining?
------------------------------------------

Next time you start seeing the same variable start every line, consider
chaining, especially if you're working in a dynamically typed language.
