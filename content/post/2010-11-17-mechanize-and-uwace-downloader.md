---
date: 2010-11-17T00:00:00Z
meta:
  _edit_last: "1"
  _edit_lock: "1290224986"
  _wp_old_slug: ""
published: true
unlisted: true
status: publish
tags: []
title: Mechanize and UWAce Downloader
type: post
url: /2010/11/17/mechanize-and-uwace-downloader/
---

A while ago I made something in php that would let you download things form UW-Ace (the University of Waterloo's course management system) from the command-line. Michael Chang had the excellent idea of making something that would simply download everything available to you from Ace so you could have a local copy of everything.

His solution was an extension of my php solution.

Because I wanted to make the product easier to distribute and because I wanted to make use of Mechanize, I rewrote the solution in ruby and packaged it as a gem.

UWAce Downloader
============
The new version looks like this:

![uwace gem screenshot](http://jlfwong.github.com/images/uwacegem.png)

To get it running, you'll need a copy of RubyGems, which you can get here: [Download RubyGems][]. It's a simple package management and distribution system for ruby, which is used by more or less every ruby project you can think of.

Once you have that set up, just run

    sudo gem install uwace

Wait for it to finish installing, then run

    uwace

Which will prompt you for a username and password.
Of course, I wouldn't release anything that asks for your password without releasing source, so here it is:

* [uwace gem @ github][]
* [uwace gem @ RubyGems][]

[uwace gem @ github]: https://github.com/jlfwong/UWAngel-CLI
[uwace gem @ RubyGems]: https://rubygems.org/gems/uwace
[Download RubyGems]: http://rubygems.org/pages/download

Mechanize
=======

This version of the downloader is written in ruby with the aid of Mechanize for ruby.
Mechanize is a set of tools for automating webpage interactions and retrieving data. It can identify links and forms on a page, fill them in, submit them and grab any data you want. Perfect for this task.

There's a simple guide on github here: [Getting Started with Mechanize][]

To demonstrate how simple form interaction is, here's the method for login:

```ruby
def login
  @username ||= ask("Username: ")
  @password ||= ask("Password: ") {|q| q.echo = '*'}

  say "Logging in ..."

  login_page = angel_get 'home.asp'
  form = login_page.form_with(:name => "frmLogon")
  form.username = @username
  form.password = @password

  login_submit_page = @agent.submit form

  if login_submit_page.uri.to_s.include? 'authenticate.asp'
    raise InvalidLogin
  end
rescue InvalidLogin
  say 'Invalid Username/Password'
  exit
end
```

While I've only used the Mechanize bindings for ruby, there bindings for many other languages:

* [Python's Mechanize](http://wwwsearch.sourceforge.net/mechanize/)
* [Perl's WWW::Mechanize](http://search.cpan.org/dist/WWW-Mechanize/lib/WWW/Mechanize/Cookbook.pod) - This was actually the original Mechanize

[Getting Started with Mechanize]: https://github.com/tenderlove/mechanize/blob/master/GUIDE.rdoc
