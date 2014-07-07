---
layout: post
title: "Building a layout system"
description: The journey of building a flexible, customisable, responsive grid system in Sass
date: 2014-06-30 19:09:31 +0100
tags: [css, sass, scss, grid, matryo]
categories: posts
image:
  feature: matryo-code.jpg
  credit: Me
  creditlink:
comments: true
share: true
---


I&rsquo;ve been using grid systems in some way shape or form for the past 3 years. From 960.gs, the 1kb grid and 1140, to fully-fledged frameworks like Bootstrap and inuit.css.

They were (are) excellent tools for quickly knocking together sturdy layouts. However each project is different, and &mdash; especially with responsive builds &mdash; each layout comes with its own nuances.

I&rsquo;m not keen on using front end frameworks as a one-stop solution, preferring to pull together certain libraries to do one thing and do it well. This is what led me to create the layout system that we currently use as the basis for our bigger projects at Studio-40: [MatryoSass](http://github.com/quagliero/matryosass) (Matryo for short).

After some thought, the needs for our grid system boiled down to:

- Sass based
- Responsive, with IE8 support
- (Infintely) Nestable
- Fractional, none of this span-12 malarkey
- 5 base widths for media queries
- Easily customisable per project


### Semantic vs presentational

The first major decision to be made was whether it was to be semantic or presentational. In short, **semantic** means that additional `<div>`s are not used to form layout, the grid styles are applied directly to (semantic) HTML elements or classes, leading to less markup and no presentational  `grid-col`-esque class names. **Presentational** is the  opposite; layouts are formed by a collection of grid and column classes which act as an additional layer around the content and/or semantic elements (`nav`, `aside`, `article`).

I decided on presentational. I&rsquo;m a firm believer in separation of concerns, and keeping the layout away from the content made a lot of sense. I also find being able to glance at the markup and get a very real idea of what should be happening on the screen incredibly useful. Almost invaluable.

Take the following examples, although the semantic version may look cleaner, I have no idea what the expected layout should be.
{% highlight html %}
<!-- Semantic -->
<!-- No idea on layout, could be block, inline, a mixture of both -->
<nav>
    <menu>
        <li><a href="/">Home</a>
    </menu>
</nav>
<article>
    <h1>Title</h1>
    <p>...</p>
</article>
<aside>
    ...
</aside>
{% endhighlight %}
{% highlight html %}
<!-- Presentational -->
<!-- A quick glance and we know it's a 3-column layout,
in the form of [25%][ 50% ][25%] -->
<div class="grid">
    <div class="grid__col 1/4">
        <nav>
            <menu>
                <li><a href="/">Home</a>
            </menu>
        </nav>
    </div>
    <div class="grid__col 1/2">
        <article>
            <h1>Title</h1>
            <p>...</p>
        </article>
    </div>
    <div class="grid__col 1/4">
        <aside>
            ...
        </aside>
    </div>
</div>
{% endhighlight %}
It&rsquo;s not everyone&rsquo;s cup of tea, but it works for us.

We&rsquo;d previously been enforcing this rule when using inuit.css, and the _&ldquo;don't add additional styles to the grid layout&rdquo;_ is a simple and effective methodology to apply across projects.

A lot of the projects we do involve splitting up our HTML into partial files. If this is the case for you or your team, I strongly recommend the presentational grid approach. We have a loose rule that no grid styles are in the partial files. These partials are width agnostic components that can be included from within any column size. Here&rsquo;s an example from a recent Symfony2 project, using Twig files:
{% highlight smarty %}
<div class="grid">
    {# Footer Column #}
    <div class="grid__col 1/3 sm-1/2 xs-1">
        {% raw %}{% include 'S40ProjectBundle:Frontend/Footer:_nav-pages.html.twig' %}{% endraw %}
    </div>

    {# Footer Column #}
    <div class="grid__col 1/3 sm-1/2 xs-1">
        {% raw %}{% include 'S40ProjectBundle:Frontend/Footer:_nav-support.html.twig' %}{% endraw %}
    </div>

    {# Footer Column #}
    <div class="grid__col 1/3 sm-1/2 xs-1">
        {% raw %}{% include 'S40ProjectBundle:Frontend/Footer:_nav-legal.html.twig' %}{% endraw %}
    </div>
</div>
{% endhighlight %}
This separation of components and grid layout partners really well with the concept and mindset of using partials.

### Fractional

You&rsquo;ve probably noticed that the grid class names we&rsquo;re using are - quite literally - the fractional width we want. One of the major gripes I&rsquo;ve had with other grid systems is this need to force 12-column layouts onto you whether you need it or not. Why write `span-4` when you mean `1/3`? Since when does `col-9` mean `3/4`?

Why should we be constrained by making everything have to add up to 12? It prevents you from using things like fifths, sevenths, eighths, and tenths (etc). What if you want a 3 column layout divided as 3/8ths, 4/8ths and 1/8th? Nope.

The answer was to use fractions as the class names. There was some consideration on my part on making them verbose written names, e.g. `one-quarter`, or even dashed like `1-4`, but I was all aboard the fractional train and believed it would aid readability and keep class name collections to a sensible length.

_Bit of a snag_, the `/` character in CSS needs to be escaped. Thus our selector would end up looking like `.3\/4 { width: 75%; }`. It worked, but a better solution was to be had using attribute selectors: `[class~='3/4'] { ... }`. Same specificity, same result, more readable selector.

Notice the tilde in the attribute selector? It makes a big difference! [It even helped out Harry Roberts on the newest inuitcss](https://github.com/inuitcss/trumps.widths/commit/3cda4ab6fd143226125c0ff9424a45247ef3cb79).

### Responsive

A lot of the frameworks or grids we&rsquo;d used previously came with 3 defined breakpoints, usually _&lsquo;phone&rsquo;_, _&lsquo;tablet&rsquo;_, and _&lsquo;desktop&rsquo;_ (or something of that ilk). We never found this to be enough, the gaps between them were too large and it didn&rsquo;t offer enough flexibility.

Five seemed to be the magic number; loosely translating to _&lsquo;very small&rsquo;_, _&lsquo;small&rsquo;_, _&lsquo;medium&rsquo;_, _&lsquo;large&rsquo;_ and _&lsquo;extra large&rsquo;_. The specific widths themselves would differ from project to project &mdash; but we always ended up needing around five.

I opted for a top-down `max-width` approach, this is similar to what the team was used to and slotted nicely into our current workflow. The premise being you assign a base column width, and then adjust the width of that column with namespaced class modifiers that work down through the breakpoints. If we wanted a &#8533; width column which expanded to &#8531; on our medium view, it would look like `<div class="grid__col 1/5 md-1/3">`.



### Customisable; Naming the things

Layouts are always different, and we don&rsquo;t always need a full twelve or sixteen columns. One of the best things about Matryo is the option to pass it in a number of fractional widths you want created, e.g. `$matryo-cols: 12`, or a list of fractions you need, e.g. `$matryo-cols: 1,2,4,6`. This is a great way at the end of the project to get a nice file size reduction. By only using the columns you actually need you strip away all unnecessary classes.

As well as defining the columnal fractions we need, we also define the 5 widths that form the base of our responsive layouts, e.g. `$matryo-bp-xs-width: 30em`

We also give them a name.

If we want to call our smallest width 'lil-sebastian', there&rsquo;s nothing stopping us: `$matryo-bp-xs-name: 'lil-sebastian';`

And now we get unbridled joy every time we target our smallest breakpoint:
{% highlight scss %}
// using the lil-sebastian named width media query in scss
.horse {
    height: $normal-horse;

    @include media(lil-sebastian) {
        height: $tiny-horse;
    }
}
{% endhighlight %}
{% highlight html %}
<!-- using the lil-sebastian named width media query as a grid modifier class -->
<div class="grid">
    <div class="grid__col 1/4 lil-sebastian-1/2">
        ...
    </div>
</div>
{% endhighlight %}
Although 5 breakpoints seems to be the norm for us, it&rsquo;s by no means the rule. That&rsquo;s why Matryo breakpoints can be disabled if not needed. We may only want 3 breakpoints on a small project, so we disable the ones we don&rsquo;t want. And &mdash; just like with the fractions list &mdash; we reduce the file size by not including classes for responsive breakpoints we&rsquo;re not using.

Perf matters :)

If you&rsquo;re wondering what all the double underscores are about in the class names, we use the [BEM naming convention](http://www.integralist.co.uk/posts/maintainable-css-with-bem/) at Studio-40. That&rsquo;s a whole different post in itself, but needless to say if BEM isn&rsquo;t your thing the grid column class names are customisable too. Pretty much everything is.

You can check out the [source code](https://github.com/quagliero/matryosass/blob/master/matryo.scss), [demo](http://quagliero.github.io/matryosass) and [examples](https://github.com/quagliero/matryosass#implementation) if you&rsquo; interested in a fully nestable, reversible, responsive, IE8+ layout system. _And what&rsquo;s next for Matryo?_ Well there&rsquo;s plans to add in more named media queries, such as between two widths, as well as an option to choose between min and max-width media queries by default.
