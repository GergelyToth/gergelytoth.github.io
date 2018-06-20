---
layout: post
date: 2018-06-20 18:00:00
---

# How to transition auto height

Given an element, with a `height: auto`, we want to transition it from 0 to
its natural height.

## TLDR;

Use `element.scrollHeight`, getting the element from *ref* or *event.target*.

# Exploring different options

## CSS

CSS only transition normally is  achieved by controlling a toggling class with
JavaScript. This way it is quiet easy to apply a class to our element and have
it applied as css styles. The two techniques I found are:

* I know this is not a pure CSS solution, but as close as we are going to get in this post.

### max-height

CSS transition can't be done with setting the height attribute from 0 to auto.  In order to work around it, we could use max-height, setting it to a value, what the element should never reach.

There are two problems with this.

Firstly, what if the element is going to reach that height? Well, we can just
always set it to a higher value, right?  Here is where the second problem
comes up...

The element is going to animate the max height with the transition time we
specified. This means, if we set the max-height to 1000px, with 1s transition
time, and our div is only 100px tall, that means that the actual animation
time will be 0.1s `(60/1000*100)`. For smaller elements the result will be
a very quick transition.

Have a look at the following example:

```css
/* our basic component style */
.component {
	height: auto;
	max-height: 0;
	overflow: hidden;
	transition: max-height 1s ease-in-out;
}
```

```css
.component:hover {
	max-height: 100px;
}
```

```css
.component:hover {
	max-height: 1000px;
}
```

<p data-height="500" data-theme-id="dark" data-slug-hash="XYWRgQ" data-default-tab="result" data-user="GergelyToth" data-embed-version="2" data-pen-title="css max-height transition" class="codepen">See the Pen <a href="https://codepen.io/GergelyToth/pen/XYWRgQ/">css max-height transition</a> by Greg Toth (<a href="https://codepen.io/GergelyToth">@GergelyToth</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### scaleY

The second way, is to toggle between a 0 and 1 scaleY transform.

```css
.component {
	height: auto;
	transform: scaleY(0);
	transform-origin: top;
	transition: transform 1s ease-in-out;
}

.component:hover {
	transform: scaleY(1);
}
```

We have two problems with this again:

1. The content inside is scaled as well.
2. The page will retain the height occupied by the elements.

<p data-height="330" data-theme-id="dark" data-slug-hash="zaYEqe" data-default-tab="result" data-user="GergelyToth" data-embed-version="2" data-pen-title="css-auto-height: scaleY  transition" class="codepen">See the Pen <a href="https://codepen.io/GergelyToth/pen/zaYEqe/">css-auto-height: scaleY  transition</a> by Greg Toth (<a href="https://codepen.io/GergelyToth">@GergelyToth</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## JavaScript

### clientHeight / offsetHeight

The typical way to animate auto height was to render the element normally,
and once the page loaded, store the element's height, before setting it's
height to 0. Something like this:

```js
let box = document.getElementById('box');
let boxHeight = box.clientHeight;

box.setAttribute('data-height', boxHeight);
box.style.height = 0;
```

One quirk with this is that the user would see the content and quickly
disappear. Also, if you transition it with css, the transition will run for
the initialization also.

By running it with javascript, but personally I prefer not to do that, as it is hard to see after a while where is the transition actually applied.

Lets combine the two:

## CSS & Javascript

What we want to achieve:

- By default the element height should be 0
- Determine the height of the component automatically, without hacks!
- Set the height to the actual amount
- Have the css transition take care of the animation
- Responsive

### scrollHeight

From [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollHeight):

> The Element.scrollHeight read-only property is a measurement of the height
> of an element's content, including content not visible on the screen due to
> overflow.

Lets have a look at an example:
```css
#box {
	height: 0;
	overflow: hidden;
	trainsition: height 0.3s ease-in-out;
}
```
```js
let box = document.getElementById('box');

// opening
box.style.height = `${box.scrollHeight}px`;

// closing
box.style.height = 0;
```

I don't think there is any need for explanation :)

In my opinion this is one of the easiest solutions out there. Please do let me
know how you do it in the comments below.
