---
date: 2009-12-27T00:00:00Z
meta:
  _edit_last: "1"
  _edit_lock: "1265430002"
published: true
unlisted: true
status: publish
tags:
- CLI
- curl
- php
- Projects
- stty
- UWAce
- UWAngel
title: UWAngel-CLI
type: post
url: /2009/12/27/uwangel-cli/
---

<div style='text-align:center'>
  <img src="/images/uwangel-cli-1.png" width="480" height="244" />
</div>

Near the end of the semester, I was getting kind of tired of navigating UW-ACE
so frequently through my browsing and opening all the different tabs to grab all
the files I wanted. While all the pretty graphics make for a decent user
interface, it reduces the speed of the service.

But aside from that, I like being able to do as much as I possibly can from the
console.

So I made a Command Line Interface (CLI) for UW-ACE in php using cUrl. I built
in on my Macbook Pro in Snow Leopard, but it should work just fine on any *nix
machine, and possibly in Cygwin or other emulators.

As always, source is available on github: <a
href="http://github.com/jlfwong/UWAngel-CLI">UWAngel-CLI @ Github</a>.

Since I always like to post snippets of code from my projects that may be
universally useful, I'll do that here too.

CLI Colour in PHP
-----------------

`cli_colours.php` included in the UWAngel-CLI source is just a collection of
constants which allow you to print out colours in your CLI scripts.

```php
<?
$COLOR_BLACK = "\033[0;30m";
$COLOR_DARKGRAY = "\033[1;30m";
$COLOR_BLUE = "\033[0;34m";
$COLOR_LIGHTBLUE = "\033[1;34m";
$COLOR_GREEN = "\033[0;32m";
$COLOR_LIGHTGREEN = "\033[1;32m";
$COLOR_CYAN = "\033[0;36m";
$COLOR_LIGHTCYAN = "\033[1;36m";
$COLOR_RED = "\033[0;31m";
$COLOR_LIGHTRED = "\033[1;31m";
$COLOR_PURPLE = "\033[0;35m";
$COLOR_LIGHTPURPLE = "\033[1;35m";
$COLOR_BROWN = "\033[0;33m";
$COLOR_YELLOW = "\033[1;33m";
$COLOR_LIGHTGRAY = "\033[0;37m";
$COLOR_WHITE = "\033[1;37m";
$COLOR_DEFAULT = "\033[0;37m";

echo <<< EOT
One fish,
two fish,
{$COLOR_RED}red{$COLOR_DEFAULT} fish,
{$COLOR_BLUE}blue{$COLOR_DEFAULT} fish.

EOT;
```

The only gripe I have about this is that `COLOR_DEFAULT` it's the same colour as
I have on by default in iTerm. Anyone know the escape code to make it actually
revert to what it was before instead of just making an assumption about what
color is being used?

Hide Commandline Input
----------------------

One of the first things I looked up when I started this project today was how to
hide user input from the command line. I sure as hell didn't want people typing
their passwords for UWACE on screen and having it actually display.

It turns out you can do this by temporarily telling your TTY to stop echoing
what you type. The command for this is `stty -echo` and can be re-enabled using
`stty echo`. Below is how I implemented as part of the AngelAccess class to meet
my needs.

```php
<?
function Prompt($prompt_text,$hide = false) {
    echo $prompt_text;
    $input = "";
    if ($hide) {
        $input = trim(`stty -echo;head -n1;stty echo`);
        echo "\n";
    } else {
        $input = trim(fgets(STDIN));
    }
    return $input;
}
?>
```

As a complete side note, thanks to a boot-camped installation of Windows 7 (or
at least I'm fairly sure that's the culprit,) my Macbook Pro is now stuck on
Digital Out. This means I can't use the internal speakers on my computer under
Mac OS X. Well... what I should say is that I can't use them without some
annoying tricks. If I plug in my headphones, then tell my mac to use the input
jack for audio input, then my internal speakers appear under the output options
and let me use them. The speakers then work perfectly fine. They also work fine
under Windows 7. A direct side effect of digital out being stuck on is a read
light emanating from the audio jack.

The problem is apparently fairly common, unfortunately the only confirmed fixes
for it are sending it back to Apple or <a
href="http://forums.macrumors.com/showthread.php?t=239287">wiggling a toothpick
around</a> in the audio jack. I had absolutely no luck with the toothpick, or
precision screwdriver, or pen cartridge, or paintbrush handle. If anyone knows
how to fix this problem, I would love to know. Otherwise, I'm just going to take
it into the Apple store in Rideau some time this week and hope they can fix the
problem. I really don't want to have to send my Mac in during my first week at
Velocity.
