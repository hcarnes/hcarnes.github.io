---
layout: post
title:      "How to Create a Floating Cat in CSS"
date:       2018-08-28 15:12:15 -0500
permalink:  floating_cat
---

I've been curious about CSS animations for awhile, and I recently took the plunge and learned how to add an animation to one of my cat illustrations. This post will explain how to add a floating animation to an image using css. Here is the final product:

<img src="https://i.imgur.com/H9XhL0e.gif">

```css
.cat {
	box-sizing: border-box;
	overflow: hidden;
	transform: translatey(0px);
	animation: float 4s ease-in-out infinite;
	width: 200px;
}

@keyframes float {
	0% {
		filter: drop-shadow(5px 15px 5px black);
		transform: translatey(0px);
	}
	50% {
		filter: drop-shadow(25px 30px 15px black);
		transform: translatey(-10px);
	}
	100% {
		filter: drop-shadow(5px 15px 5px black);
		transform: translatey(0px);
	}
}
```









