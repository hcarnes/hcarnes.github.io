---
layout: post
title:      "Diving into Symbols in JavaScript"
date:       2019-2-28 00:08:15 -0500
permalink:  javascript_for_loop
published: false
---

In the last post, we explored JavaScript traditional `for loop` versus the modern `for loop`. We learned that the modern `for loop` has many advantages that include increased readability, maintainability, and also having an immutable index, which reduces the chances of error in the code. We also learned that we can access the index using the modern `for loop` by using the `entries()` iterator which relies implement a method of the primitive type `Symbol`. You are probably very familiar with the older primitive types: `number`, `string`, `boolean`, and `undefined`. So, what how does the new primitive type `Symbol` help programmers solve problems?

It can be used to define properties for objects for iteration
It can be used to easily define a global registry or dictionary of objects
It can be used to define some special well-known methods in objects (some say this is the major key of Symbol)
