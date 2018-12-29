---
date: 2009-11-14T00:00:00Z
meta:
  _edit_last: "1"
  _edit_lock: "1259218981"
published: true
unlisted: true
status: publish
tags:
- AJAX
- javascript
- omegle
- php
- Projects
- prototype
title: Omegle Voyeur - Multiple Connections
type: post
url: /2009/11/14/omegle-voyeur-multiple-connections/
---

In case you haven't read the post about all my projects, here's a description of what Omegle Voyeur is:
<blockquote>
Omegle is a website where you are connected to a stranger for a chat. It is dominated mostly by trolls whose primary purpose is to coerce you into a cyber session and then switch genders or to make you lose the game. Talking to these people is a rather tiresome endeavour, but seeing exactly what happens in these conversations is interesting. Omegle Voyeur is a way of watching a conversation which you aren't part of. What Voyeur does is form two simultaneous connections and then pass the input of one to the output of the other. This sets you up as a conversation proxy, allowing you to watch. Currently, this is exclusively a "sit and watch" program. Later I intend to add functionality to add more than 2 people into a conversation, automatically name the participants so it will be obvious that there are more than 2 people in the conversation, and allow the ability to interfere (mute participants/say things yourself) with a conversation. This concept was spawned during discussion (read: boredom) at CCC Stage 2, 2009.
</blockquote>

In terms of technology, Omegle Voyeur is primarily one big Javascript Prototype class. <a href='http://www.prototypejs.org/'>Prototype</a> is a Javascript Framework which makes the creation and maintenance of classes, conversion of data into JSON for transfer, and sending AJAX requests much, much easier.

There's also a very small bit of code in php which is able to be so short because it uses the incredible program cUrl. <a href="http://curl.haxx.se/">cUrl</a> is a command line utility for grabbing data from websites using their URL. libcurl facilititates the use of curl in php without having to write your own wrapper.

Below is some php code I use to make curl even easier than it already is. simple_get($url) will return the HTTP GET result from the url specified. simple_post works similarly, but delivers data using the payload.

```php
<?
// Simple cUrl
// Simple get and post requests
function simple_get($url) {
    $c = curl_init();
    curl_setopt($c, CURLOPT_URL, $url);
    curl_setopt($c, CURLOPT_RETURNTRANSFER, 1);
    $result = curl_exec($c);
    curl_close($c);
    return $result;
}

function simple_post($url,$payload) {
    $c = curl_init();
    curl_setopt($c, CURLOPT_URL, $url);
    curl_setopt($c, CURLOPT_POST, true);
    curl_setopt($c, CURLOPT_POSTFIELDS, $payload);
    curl_setopt($c, CURLOPT_RETURNTRANSFER, 1);
    $result = curl_exec($c);
    curl_close($c);
    return $result;
}
?>
```

When I started working on this project today (well, I suppose that would be last night now... wonder if I'll see the sunrise) I figured it would be a good time to get used to using git, so I made a repository using github.
So far I'm enjoying git. Everything seems to act pretty much the way you'd expect to, and I already had to do a revert once I realized my logic was wrong for the way I was structuring my code.

You can see the github for Omegle Voyeur here: <a href="http://github.com/jlfwong/Omegle-Voyeur">http://github.com/jlfwong/Omegle-Voyeur</a>
Feel free to design your own stuff with the all the code there - just be sure to link back here, or to the github page.

In any case, the thing the majority of the people reading this are probably interested in are the result.
Things I've updated since last time are primarily aesthetic and behind the scenes, but I did add the ability to connect one person to more than one other person, and the connections don't have to be mutual. In the first 3 way conversation example, 1 can only speak to 2, 2 can only speak to 3 and 3 can only speak to 1. It leads to some rather confused people.

<del datetime="2009-11-26T06:58:15+00:00">You can see the current running version here: <a href="/omegle/">Omegle Voyeur</a>.</del>
EDIT: It seems that after being posted on reddit, omegle has (manually?) IP blocked me. The source should still work, so feel free to try it out yourself.
You can go grab <a href="http://www.apachefriends.org/en/xampp.html">XAMPP</a> to run it locally.
For the time being, you can view it here: <a href="http://petersobot.com/omegle/">Omegle Voyeur</a>

A quick note on how I figured out the Omegle communication protocol.
The entire code governing the process is conveniently kept here: <a href="http://omegle.com/static/omegle.js?27">http://omegle.com/static/omegle.js?27</a>
Unless you enjoy reading 1000s of characters on a single line, you can use the <a href='http://jsbeautifier.org/'>JS Beautifier</a> to clean it up to a readable state.
