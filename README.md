<h1><a href="https://eladkarako.github.io/sort/">Sort&Unique</a></h1>

<a href="https://eladkarako.github.io/sort/">eladkarako.github.io/sort/</a>

A Simplified Online-List Sorter And Unique'ify,
Fully Compatible With Mobile-Devices.

Paste Any Amount Of Lines,
And Either Click The <kbd>Sort&amp;Unique</kbd> Button Or <kbd>CTRL</kbd>+<kbd>Enter</kbd>.

It Will:

- Remove Duplicated Lines (Unique) .
- Sort Lines Naturally (1<2<10<20)
- Copy To Result To Clipboard, Automatically!

Notes:

- For Your Convenience, You Can Download Whatever Content You Currently Have, As A Text File: Simply Click The <kbd>Save</kbd> Button.
- Line Endings Are Normalized To Linux EOL (a.k.a "LF" or "\n")
- After Action The Cursor Position At The Text Will Be Set To First Line.
- The Text-Area Is Naturally Capable Of Auto-Complete And Spell-Check If Your Browser Supports It (It Does Not Requires Any JavaScript Support Either).
- <kbd>Sort&amp;Unique</kbd> Button And The <kbd>Save</kbd> Button Functionality Requires JavaScript Running On Your Browser.
- The WebSite <strong>Does Not</strong> Make Use Of Any Cookies, <strong>Nor</strong> It Tracks Or Collect Any Information. If You Download It Or Fork It, It Will Work Completely Offline..

<br/>

<hr/>

Extractors. - For Developers

Say your lines are a bit more complex than just a line of text, 
for example - each line looks like this: <code>pref("accelerometer.enabled", false);</code> (<a href="https://hg.mozilla.org/releases/mozilla-release/file/tip/modules/libpref/init/all.js">Mozilla preferences</a>), 
where the actual "value" is the one wrapped with <code>"</code>-character, 
if you'll sort the lines eventually you'll sort the <code>, false);</code> part as well against actual data...
...for this you better provide an improved "sorter function" - you can base your sorter-program on the native-sort one, 
but add an "extractor" before the actual work:

```js
function natural_compare(a, b) {
  var ax = [], bx = [];

  if("undefined" !== typeof natural_compare.extractor){ //optional. avoid comparing the whole value by extracting part of it (part the has more meaning then the whole line)
    a = extractor(a);
    b = extractor(b);
  }

  a.replace(/(\d+)|(\D+)/g, function(_, $1, $2) { ax.push([$1 || Infinity, $2 || ""]) });
  b.replace(/(\d+)|(\D+)/g, function(_, $1, $2) { bx.push([$1 || Infinity, $2 || ""]) });

  while(ax.length && bx.length) {
    var an,bn,nn;
    an = ax.shift();
    bn = bx.shift();
    nn = (an[0] - bn[0]) || an[1].localeCompare(bn[1]);
    if(0 !== nn) return nn;
  }

  return ax.length - bx.length;
}


//natural_compare.extractor = undefined;    //by default.

natural_compare.extractor = function(s){
                              s = s.replace(/[^\"]+\"([^\"]+)\".*$/m, "$1");  //leave just the first "..." block - Android build.pref (missing? keep whole-line).
                              return s;
                            };

//-------------------------

var textarea = document.querySelector('textarea')
textarea.value = textarea.value.replace(/[\r\n]+/gm, "\n")
                               .split("\n")
                               .sort(natural_compare)
                               .join("\n")
                               ;
```

