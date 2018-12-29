---
date: 2010-07-16T00:00:00Z
meta:
  _edit_last: "1"
  _edit_lock: "1285900399"
  _wp_old_slug: ""
published: true
unlisted: true
status: publish
tags:
- github
- javascript
- jquery
- Projects
title: JSONimal - Elegant DOM Contruction with jQuery
type: post
url: /2010/07/16/jsonimal-elegant-dom-contruction-with-jquery/
---

**EDIT** There is a more complete project achieving exactly the goals I set out
for called jquery-haml: <http://github.com/creationix/jquery-haml>

Occasionally for Javascript projects, I found myself building a lot of HTML programatically, and I wasn't satisfied with any of the techniques available, so I built JSONimal. I was originally going to just call it JSONML, but that was taken.

What's it do? This example should demonstrate my goal fairly well.

```js
$(function() {
    $.mktag("#demo").jsonimal([
        ["h1", {text: "JSONimal!"}],
        ["table",{style: 'border: 1px solid black'},[
            ["thead",[
                ["tr",{style: 'text-transform: uppercase'},[
                    ["th", {text: "one"}],
                    ["th", {text: "two"}],
                    ["th", {text: "three"}]
                ]]
            ]],
            ["tbody", [
                ["tr",[
                    ["td", {html: "<u>a</u>"}],
                    ["td", {text: "b"}],
                    ["td", {text: "c"}]
                ]],
                ["tr",[
                    ["td",[
                        ["a", {href: "http://www.google.ca", text: "Google"}]
                    ]],
                    ["td", {text: "b"}],
                    ["td", {text: "c"}]
                ]],
                ["tr",[
                    ["td", {text: "a"}],
                    ["td", {text: "b"}],
                    ["td", {text: "c"}]
                ]]
            ]]
        ]]
    ]).appendTo("body");
});
```

Which will add this to the body:

<h1>JSONimal!</h1><table style="border: 1px solid black;"><thead><tr style="text-transform: uppercase;"><th>one</th><th>two</th><th>three</th></tr></thead><tbody><tr><td><u>a</u></td><td>b</td><td>c</td></tr><tr><td><a href="http://www.google.ca">Google</a></td><td>b</td><td>c</td></tr><tr><td>a</td><td>b</td><td>c</td></tr></tbody></table>

For more information and examples, check out the github page: [JSONimal @ github][github].

I also posted it as on the jQuery plugins page - but that just points to the github page anyway. [JSONimal @ plugins.jquery.com][plugin]

[github]: http://github.com/jlfwong/JSONimal
[plugin]: http://plugins.jquery.com/project/jsonimal
