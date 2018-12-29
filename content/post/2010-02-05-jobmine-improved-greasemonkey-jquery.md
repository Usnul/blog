---
date: 2010-02-05T00:00:00Z
meta:
  _edit_last: "1"
  _edit_lock: "1285900502"
published: true
unlisted: true
status: publish
tags:
- greasemonkey
- jobmine
- jquery
- Projects
title: Jobmine Improved (Greasemonkey & jQuery)
type: post
url: /2010/02/05/jobmine-improved-greasemonkey-jquery/
---

<div style='text-align: center'>
  <img src="/images/JobmineImproved.png" />
</div>

_I will no longer be supporting this script, there's a much better version
called [Jobmine Plus](https://github.com/matthewn4444/jobmine-plus-extension)
maintained by Matthew Ng._

I, like many (most) Waterloo Co-op students, am forced to use Jobmine and am
extremely dissatisfied with its functionality. So I decided to kill three birds
with one stone: improve Jobmine, learn Greasemonkey and learn jQuery all at the
same time.

The result is, unsurprisingly, a Greasemonkey script written using jQuery that improves on some features of Jobmine.

<strong>Features</strong>
<ul>
<li><strong>Table sorting</strong> - all major tables are now sortable (Interviews, Job Short List, Applications)</li>
<li><strong>Improved navigation</strong> - no more Student -> Use ridiculousness</li>
<li><strong>No more frames</strong> - you can refresh and it will stay on the same page!</li>
<li><strong>Colour highlighting for tables</strong> -  pictured above, you see the applications page with various statuses highlighted. Selected is green, not selected is red.</li>
<li><strong>No more spacers</strong> - the Jobmine page is riddled with spacer images just sitting there, stealing screen real estate</li>
</ul>

*Edit from 2017: This is now defunct, so I removed the installation
instructions.*

<h1>Greasemonkey</h1>
Greasemonkey is a tool for customizing the way a web page displays and interacts using javascript. More or less, it overlays javascript you write on top of pages you specify by URLs with wildcards (*). It doesn't overlay it directly, but wraps it in some way as to prevent it from messing things up in the global scope. It also seems to run once the page is done loading, not when the page head is loaded. There are plenty of tutorials out there for doing cool stuff with Greasemonkey, but I started here: <a href="http://diveintogreasemonkey.org/helloworld/index.html">Dive into Greasemonkey</a>. I know it says it's hideously outdated, but the metadata information it provides is still good enough. If you want more up to date information, go here: <a href="http://wiki.greasespot.net/Main_Page">GreaseSpot (Greasemonkey Wiki)</a>.

<h1>jQuery</h1>
jQuery is a javascript framework specifically designed for doing things involving the DOM tree absurdly quickly. Example: highlighting alternating rows of a table (zebra-striping).

```js
// Standard Javascript method:
var tables = document.getElementsByTagName("table");
for (var i = 0; i < tables.length; i++) {
    var rows = tables[i].tBodies[0].rows;
    for (var j = 0; j < rows.length; j++) {
        var rowColor;
        if (j % 2 == 1) {
            rowColor = "#eef";
        } else {
            rowColor = "#fff";
        }

        var cells = rows[j].cells;
        for (var k = 0; k < cells.length; k++) {
            cells[k].style.backgroundColor = rowColor;
            cells[k].style.borderBottom = "1px solid #ccc";
        }
    }
}

// jQuery way:
$("td").css("border-bottom","1px solid #ccc");
$("tr:even > td").css("background-color","#fff");
$("tr:odd > td").css("background-color","#eef");
```

Now before someone says it, I know usually you can set the background-color for the whole row, and the cells will inherit it. But since, for some crazy reason, each cell is assigned a background colour on Jobmine, each cell needs to be set individually. In any case, you can see that things are made substantially easier with jQuery. I figured out jQuery mostly just using the API and looking at other people's code, but this is a decent place to start: <a href="http://docs.jquery.com/Tutorials:Getting_Started_with_jQuery">Getting Started with jQuery</a>.

For the table sorting functionality, I decided to use a jQuery plugin as opposed to write my own (I'd rather be able to distribute this sooner). You can read all about it here: <a href="http://tablesorter.com/docs/">jQuery Plugin: Tablesorter 2.0</a>

What features do you want to see in this? By the way, the source is all available on the userscripts site, so feel free to tinker with it yourself.

<strong>EDIT:</strong> As <a href="http://trevorcreech.com/">Trevor</a> points out, the script in its current state won't work in Chrome due to the @require. You can grab his fix to make it work in chrome here: <a href="http://trevorcreech.com/files/jobmine_improved_chome.user.js">Jobmine Improved (Chrome)</a>
