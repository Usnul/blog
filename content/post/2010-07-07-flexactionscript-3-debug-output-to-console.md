---
date: 2010-07-07T00:00:00Z
meta:
  _edit_last: "1"
  _edit_lock: "1278565881"
  _wp_old_slug: ""
published: true
unlisted: true
status: publish
tags:
- actionscript
- flex
- flixel
- trace
- Tutorials
title: 'Flex/Actionscript 3: Debug Output to Console'
type: post
url: /2010/07/07/flexactionscript-3-debug-output-to-console/
---

I recently started messing around with the [Flixel][] framework - something built on top of [Flex][] to make retro games. One of the first things I noticed was how difficult it is to debug things - especially complex objects.

[Flixel]: http://flixel.org/
[FLex]: http://www.adobe.com/products/flex/

The first problem is capturing _any_ debug output. For whatever reason, I was unable to output traces to anywhere useful.
My first success was with using [De MonsterDebugger][] to capture the output. While this was better than nothing, I needed very little of the functionality of De MonsterDebugger and it still didn't give me what I really wanted: formatted console output.

[De MonsterDebugger]: http://demonsterdebugger.com/

print_r style output in actionscript
-----------------------------

I found a good starting point [here][base86], but the output of this wasn't exactly what I wanted. I messed around with it until I got something more familiar to me.

[base86]: http://dev.base86.com/solo/47/actionscript_3_equivalent_of_phps_printr.html

```actionscript
package learning {
  import org.flixel.*;

  public class Debugger {
    public static function pr(obj:*, level:int = 0, output:String = ""):* {
      var objtype:String = typeof(obj);
      if (objtype == "boolean" || objtype == "string" || objtype == "number") {
        return obj;
      }

      var tabs:String = "";
      for(var i:int = 0; i < level; i++) {
        tabs += "\t"
      }

      output += "{\n";
      for(var child:* in obj) {
        output += tabs +"\t["+ child +"] => ";

        var childOutput:String = pr(obj[child], level+1);
        if(childOutput != '') output += childOutput

        output += "\n";
      }
      output += tabs + "}\n";

      return output;
    }

    public static function log(obj:*):void {
      FlxG.log(pr(obj));
      // This is a flixel thing. If you're not using flixel
      // Just use trace(pr(obj));
    }
  }
}
```

Example output

    {
    	[0] => {
    		[tile] => 4
    		[rule] => {
    			[0] => xxx
    			[1] => x1x
    			[2] => x1x
    		}
    	}
    	[1] => {
    		[tile] => 8
    		[rule] => {
    			[0] => x1x
    			[1] => x1x
    			[2] => xxx
    		}
    	}
    }

trace output to console
--------------------

This, fortunately, was much less of a hassle for me to get working.

Reference: [Configuring the debugger version of Flash Player][Flex 3 Reference]

**Step 1**

Locate/create your `mm.cfg` file. For me this was in `~/mm.cfg`. See [reference][Flex 3 Reference].

Stick this in it: `TraceOutputFileEnable=1`

Or, in one command:

```bash
echo "TraceOutputFileEnable=1" > ~/mm.cfg`
```

**Step 2**

Locate the location of the log file. Mine is in

    ~/Library/Preferences/Macromedia/Flash Player/Logs/flashlog.txt`

See [reference][Flex 3 Reference].

You don't want to type this in every time you want to view the log, so add a function to your `.bash_profile`

This is what I have:

```bash
flashlog() {
  tail -f $* ~/Library/Preferences/Macromedia/Flash\ Player/Logs/flashlog.txt;
}
```

[Flex 3 Reference]: http://livedocs.adobe.com/flex/3/html/help.html?content=logging_04.html

**Step 3**

Start debugging!

```bash
$ flashlog -100
Warning: 'flash' has no property 'prototype'
Warning: 'flash' has no property 'prototype'
flixel v2.35 [debug]
----------------------------------------------------
{
  [0] => {
    [tile] => 4
    [rule] => {
      [0] => xxx
      [1] => x1x
      [2] => x1x
    }
  }
  [1] => {
    [tile] => 8
    [rule] => {
      [0] => x1x
      [1] => x1x
      [2] => xxx
    }
  }
}
  ```
