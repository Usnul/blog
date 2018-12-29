---
date: 2011-10-03T00:00:00Z
published: true
unlisted: true
status: publish
tags:
- jekyll
- pygments
- sass
- compass
title: Meet Doctor Jekyll
type: post
url: /2011/10/03/meet-doctor-jekyll/
---

My wordpress blog has lasted longer than any site I've used before, but I
finally got sick of it and switched. So now I'm on [Jekyll][].

Wordpress is an incredible blogging-platform-turned-CMS that provides many
things I wouldn't want to do myself. It's been an incredible SEO asset and
worked beautifully on shared hosting. I would still recommend it to anyone who
wants to write a blog and doesn't want to spend much time customizing or setting
things up. There are plenty of nice looking pre-made themes and plugins to do
pretty much anything you want.

## Wordpress to Jekyll - Why I switched

[Jekyll][] is "a blog-aware static site generator in Ruby". Basically what this
means is that all the pages you see on this blog are generated once - not
generated on demand every time they're requested. There's no DB backend, no
scripting language running on the server side. These are just plain HTML pages.

I started off with a template I got off here:
[github.com/krisb/jekyll-template][]. But if you're planning on starting a blog
similar to mine, don't start from scratch like I did - go check out
[Octopress][]. It provides a lot of niceties for blogging hackers that Jekyll
doesn't out of the box.

[github.com/krisb/jekyll-template]: https://github.com/krisb/jekyll-template

So why did I switch?

### Styling

While Wordpress is completely skinnable - it's a pain in the ass. I really just
wanted to start from scratch and only build the things I absolutely needed.
There is one unifying layout and only 2 types of page on this site: the posts
list and posts. It means I can set my own structure for everything and keep it
much, much simpler than it would have been on Wordpress.

### Workflow

I love vim. I feel like I'm dragging a huge weight when I need to edit text -
especially code - without vim. Since Jekyll just generates my site from code, it
means I can edit everything including this post in vim. All I do is spawn up a
local Jekyll server (WEBrick) and there's my preview. Once I'm done, all I have
to do is commit the changes to git, and then I pull down the changes on my
server - which is now hosted on EC2, with help from [Andrew Tinits][].


### Novelty

I'll be honest here - I feel cooler running Jekyll than Wordpress. And having
the source for my blog on github just feels right. Seriously:
[github.com/jlfwong/blog][].

Plus there's nothing better to motivate you to write a blog post than redoing
your entire blog. I promise I won't be another person who just writes a blog so
they can write about how they set up their blog and then never write again (you
know who you are.)

## The Tech

Here's the part where I justify you spending the time reading this post by
rambling about technology you might be interested in.

### Markdown

[Markdown][] is a content writing language for the web which compiles
(primarily) down to HTML. The idea is that it enables you to write documents
that are still readable in plaintext, but compile to useful HTML. This means
having a nice syntax for headers, emphasis, links, images, lists and a couple of
other things. It's definitely much nicer than raw HTML for writing up blog
posts.

As a quick example, it takes this:

    ### Markdown

    Markdown was written by John Gruber of [Daring Fireball][].

    As a quick example, it takes this:

        Hello World
        -----------

        I am pretty awesome

    and produces this:

        <h2>Hello World</h2>

        <p>I am pretty awesome</p>

    [Daring Fireball]: http://daringfireball.net/

and produces this:

```html
<h3>Markdown</h3>

<p>Markdown was written by John Gruber of <a href="http://daringfireball.net/">Daring Fireball</a>.</p>

<p>As a quick example, it takes this:</p>

<pre><code>Hello World
-----------

I am pretty awesome
</code></pre>

<p>and produces this:</p>

<pre><code>&lt;h2&gt;Hello World&lt;/h2&gt;

&lt;p&gt;I am pretty awesome&lt;/p&gt;
</code></pre>
```

### Sass

[Sass][] is awesome syntactic sugar for CSS. It deals with a bunch of things
that are simply a giant pain in vanilla CSS. One of my favorite things it does
is deal with nesting for you.

```sass
.container
  h1
    font-size: 20px
  h2
    font-size: 18px

    &:hover
      text-decoration: underline
```

Turns into:

```css
.container h1 {
  font-size: 20px; }
.container h2 {
  font-size: 18px; }
  .container h2:hover {
    text-decoration: underline; }
```


Since it runs through a compiler before producing CSS, it lets you do some very
useful things before producing the raw CSS. One of the most valuable things for
me while working on this site was variables. It means you can do things like
this:

```sass
$base-color: #281e17
$content-width: 500px
$sidebar-width: 150px

.container
  background-color: lighten($base-color, 50%)
  $padding: 20px
  padding: $padding
  width: $content-width + $sidebar-width - 2 * $padding

  .sidebar
    width: $sidebar-width
    background-color: lighten($base-color, 25%)

  .content
    width: $content-width
    color: $base-color
```

And get back this:

```css
.container {
  background-color: #b99a85;
  padding: 20px;
  width: 610px; }
  .container .sidebar {
    width: 150px;
    background-color: #795b46; }
  .container .content {
    width: 500px;
    color: #281e17; }
```

The really cool thing about the color functions is that you can base your entire
website off only a few colours, then make everything relative to those. This
site (at time of writing) actually only has two colours specifically defined -
the content font color and the body background. Everything else is relative to
those.

### Compass

[Compass][] is a collection of mixins and utilities for sass. It's especially
useful for CSS3 related tasks, since it does all the proprietary browser
prefixes (`-O-, -moz-, -webkit-`) for you when you do things like `+box-radius`.

It also comes with [Blueprint][], which gives you a great starting point to have
all the default styles for things (margins, typography, resets) look nice out of
the box.

### Pygments

[Pygments][] is a syntax highlighter which produces HTML output which can then
be styled with CSS. Github uses a wrapper around it called [Albino][] to do all
the syntax highlighting you see on the site, and it's what I'm using to make
pygments work in Jekyll.

I'm actually using a custom Jekyll plugin to deal with all of this because I
didn't want to have to stick liquid tags in my posts. In retrospect, this was
probably really unnecessary, but it's done now.

You can see the plugin here: [markdown.rb][]

[markdown.rb]: https://github.com/jlfwong/blog/blob/master/_plugins/markdown.rb

### Google Web Fonts

[Google Web Fonts][] let you use a number of beautiful fonts that may not be
available on your visitor's machine on your websites. I'm currently using it for
my logo, sidebar and headers.

Using it is beautifully simple. All you have to do is drop a link tag in the
head like the one I'm using:

```html
<link href='http://fonts.googleapis.com/css?family=Questrial|Sansita+One' rel='stylesheet' type='text/css'>
```

And then use it like normal in a CSS rule, like so:

```css
font-family: Questrial, "Helvetica Neue", Arial, Helvetica, sans-serif
```

### Nginx

Since I had to set up my own web server to run this, I decided I'd try messing
around with [nginx][] since it looks much nicer to configure than apache. For
the longest time, I thought it was pronounced "en-jinx". The actual
pronounciation, "engine-ex" made a lot more sense once I heard it.

Setting up this server was extremely simple. Since I'm only serving static
files, all I really needed was the following:

```nginx
# Redirect any subdomain to the main domain
server {
  server_name *.jamie-wong.com;
  rewrite ^ http://jamie-wong.com$request_uri? permanent;
}

server {
  listen 80 default;
  server_name jamie-wong.com;
  server_name_in_redirect off;

  access_log /var/log/nginx/jamie-wong.com.log;

  index index.html index.php;

  try_files $uri $uri/ /404/ =404;

  # If it's in any of these folders I migrated from
  # my shared hosting on dreamhost, load it directly
  location ~ ^/(experiments|asciiconvert|life|Jobmine-Improved|V10|1yr)/ {
    root /var/www/jamie-wong.com/;
  }

  # Otherwise, it's probably a blog page, so load it
  # from Jekyll's generated _site directory
  location ~ / {
    root /var/www/jamie-wong.com/blog/_site/;
  }

  error_page  404  /404/;
}
```

This got a lot longer than I expected it to. Time for sleep - will proofread
tomorrow.

[nginx]: http://nginx.net/
[Google Web Fonts]: http://www.google.com/webfonts
[Pygments]: http://pygments.org/
[Albino]: https://github.com/github/albino
[Compass]: http://compass-style.org/
[Blueprint]: https://github.com/Compass/compass-blueprint
[Sass]: http://sass-lang.com/
[Markdown]: http://daringfireball.net/projects/markdown/
[github.com/jlfwong/blog]: https://github.com/jlfwong/blog
[Andrew Tinits]: http://twitter.com/#!/amtinits
[Jekyll]: https://github.com/mojombo/jekyll
[Solarized]: http://ethanschoonover.com/solarized
[Octopress]: http://octopress.org/
