---
layout: post
title:  "Delayed flyout menu"
date:   2014-01-17 07:58:45
categories: snippets css
comments: true
share: true
---

Mega nav, super nav, dropdown, flyout... whatever. Big desktop navigation. Just catch a corner of it with your cursor and out it comes. Oy.

This can be incredibly frustrating. I&rsquo;m looking at you <a href="http://www.johnlewis.com/">John Lewis</a>, <a href="http://www.harrods.com/">Harrods</a>, and <a href="http://www.debenhams.com/">Debenhams</a>. Hmm, I&rsquo;m noticing a trend.

You might just be moving your cursor down the page because, y&rsquo;know, you want to click something. The fact you happened to go over a menu item on your way there doesn&rsquo;t mean you want to be stonewalled with an enormous navigation block.

I ran into this same issue in a recent project and opted for a lightweight solution that utilises CSS transitions on opacity, visibility and max-height. And here&rsquo;s the nice part: by adding in a .25s delay attribute, it doesn&rsquo;t fire if you were to just brush over it.

{% highlight scss %}
/* Sass (SCSS syntax) */
// on hover/focus fade in the auto height on the <ul>
.has_children{
  ...
  ul {
  	height: auto;
    max-height: 0;
  	opacity: 0;
  	visibility: hidden;
  }
  &:hover ul,
  &:focus ul{
    visibility: visible;
    opacity: 1;
    max-height: 500px; /* bigger than it ever needs to be */
    // vendor-prefixed
    transition: .35s ease .25s;
 	}
 }

{% endhighlight %}

By setting the `max-height` to an arbitrary number larger than it ever needs to be, you get the 'slide down' effect.

Want something more instant? Toggle between `0` and `auto` on the `height` property instead. You could also ditch the fade by removing the `opacity`.

Need it to ease-in slower/faster, and with more/less delay? Fiddle with the `transiton` attributes.

There&rsquo;s loads of combinations and variations to customise it.

Pen? Pen.


<p data-height="268" data-theme-id="0" data-slug-hash="nBdvs" data-default-tab="result" class='codepen'>See the Pen <a href='http://codepen.io/quagliero/pen/nBdvs'>Delayed hover menu using CSS</a> by Tobias Harison-Denby (<a href='http://codepen.io/quagliero'>@quagliero</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//codepen.io/assets/embed/ei.js"></script>

_<small>N.B For some reason it doesn&rsquo;t fire on focus if I tab to it, but it does if you use the Chrome inspector and set the element state to :focus... any ideas?</small>_
