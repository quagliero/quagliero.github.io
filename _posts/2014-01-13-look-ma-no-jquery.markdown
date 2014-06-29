---
layout: post
title:  "Look Ma, no jQuery!"
date:   2014-01-13 12:45:05
categories: posts js
comments: true
share: true
---

jQuery is everywhere. Everywhere. And that&rsquo;s testament to its ease of use and enormous community, but have you ever stopped to consider if you actually need it?

Scrolling image gallery? Most likely jQuery.

DOM traversing? $('.probably-like-this')...

Form validation? You guessed it.

When I started out in web development in 2008, jQuery&rsquo;s concise syntax and easy toggle, slide and fade methods made it easier to add some pleasing UI features. _I say &lsquo;easier&rsquo;, I still had no idea at the time._

{% highlight javascript %}
$('#foo').on('click', function() {
	$(this).slideToggle(); /* zomg it moves! */
});
{% endhighlight %}


For a lot of folk (including myself) these were the first tentative steps away from the comfort of HTML and CSS.

**jQuery was like our childhood security blanket, protecting us from the big bad monster... _JavaScript_.**

It&rsquo;s time to let go.

A while back I made the conscious decision that I wanted to &mdash; properly &mdash; understand and learn JavaScript. It was eye-opening, I&rsquo;d defaulted to jQuery and its vast array of plugins for so long that you forget it&rsquo;s actually real JS doing the heavy lifting behind the scenes.

Now there are a lot of times when using jQuery (or other libraries) is absolutely required, but it&rsquo;s at a stage now where jQuery is the defacto tool for stuff that could just be done with a little bit of vanilla JavaScript (gasp!). From what I see on Stack Overflow, 9 times out of 10 the answer to a JavaScript question will have a _&ldquo;You could just use jQuery&rdquo;_ answer.

And they&rsquo;re right, you absolutely could, you could probably write it quicker too; but is it worth downloading an entire 90KB library just to do a few manipulations and interactions? I don&rsquo;t think so.

{% highlight javascript %}
// change the text/html of an element with jQuery
$('#foo').html('Oh yeah new text!');

// a native js way
var foo = document.getElementById('bar');
foo.innerHTML = 'Oh yeah new text!';

...

// toggle a class on click with jQuery
$('#bar').on('click', function() {
	$(this).toggleClass('toggled-class');
});

// a native js alternative to toggle a single class
// (now updated to handle existing class)
var foo = document.getElementById('bar');
foo.addEventListener('click', function() {
	var baz = 'toggled-class';
    if (this.className.indexOf(baz) === -1) {
        this.className += ' ' + baz;
    } else {
        this.className = this.className.replace(baz, '');
    }
    // clear some whitespace
    this.className = this.className.replace(/\s+/g, ' ');
});

...

// loop through a list of items with jQuery
$('#bar a').each(function() {
	 $(this)... // do something
});

// native js with a good old fashioned for loop
var foo = document.getElementById('#bar');
var baz = foo.getElementsByTagName('a');

for(var i=0; i < baz.length; i++) {
	baz[i]... // do something
}

{% endhighlight %}

These are just a few crude examples, but ask yourself next time: Do I absolutely, positively, **need** jQuery for this?

Give it up for a week and just use plain ol&rsquo; JavaScript. Go on, try it. It&rsquo;s liberating.
