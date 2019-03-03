---
layout: post
title:      "JavaScript ES6+ Symbols"
date:       2019-2-24 012:08:15 -0500
permalink:  javascript_symbol
---

In the last post, we learned that we can access the index of an array in the modern `for...of` loop by using the `entries()` iterator which implements a method of the new primitive type Symbol. You are probably familiar with the older primitive types: undefined, null, boolean, Number, String, and Object. Symbol is a **new** primitive type! What can Symbols be used for?

1. to define properties for objects for iteration
2. to define a global registry of objects
3. to define some special well-known methods in objects

This post will review these 3 three use cases, so let's start at the top.

## defining properties for objects in iteration
Symbols are defined using the `Symbol()` function. The argument passed to this function serves as a description of the Symbol. Each Symbol defined with `Symbol()` is distinct, even if they have the same argument value.

```javascript
Symbol('bicycle') === Symbol('bicycle');

// return value
// false
```

Here is another example that shows how each Symbol defined with `Symbol()` is unique:

```javascript
const subway = Symbol('transit');

const streetcar = Symbol('transit');

subway === streetcar

// return value
// false
```

Below, we have a `car` object with properties of `make`, `model`, and `color`.

```javascript
const make = Symbol('makeValue');
const model = Symbol('modelValue');
const color = 'colorValue';

const car = {
  id: 1,
  [make]: 'Honda',
  [model]: 'Civic',
  [color]: 'green'
};
```
Let's iterate over the properties of car using `for...in` and see we what we get:

```javascript
for (const property in car) {
  console.log(property + ': ' + car[property]);
}

// return value
// id: 1
// colorValue: green
// undefined
```

Hmm, the properies defined as Symbols seem to be hidden. Now, let's query for the `car` property names and their values:

```javascript
console.log(Object.getOwnPropertyNames(car));

["id", "colorValue"]
```
Hmm, the properies defined with `Symbol()` also seem to be hidden here too. This is part of what makes Symbol useful: all Symbol properties are hidden from normal iteration. Keep in mind, they are not private, meaning that any code with access to the object can access and change the value for the Symbol property. Because the properties are hidden, it's nice to have a method that will expose an object's hidden Symbol properties. The code below shows how to view, access, and change a Symbol property.

We can view all the Symbols of an object.

```javascript
console.log(Object.getOwnPropertySymbols(car));

// return value
// [Symbol(makeValue), Symbol(modelValue)]
```

We can access the Symbols of an object.

```javascript
console.log(car[make]);

// return value
// Honda
```

And we can change that property value.

```javascript
car[make] = "Toyota"

console.log(car[make]);

//return value
// "Toyota"
```

## Symbol can be used to define a global registry of objects

Remember when I said earlier that each Symbol is distinct, even if they have the same argument value? Well, there's an exception to that if you define Symbol using the `for()` method. In the code below, each property is defined using `Symbol.for()` with the same description passed in. The first call to `for()` creates a unique Symbol and the second call to `for()` fetches the Symbol created in the last call through the global registry. The call to `keyFor()` returns the key associated with the Symbol in the global registry.

```javascript
const hoverBoard = Symbol.for('transit');
const electricScooter = Symbol.for('transit');

console.log(typeof(hoverBoard));
console.log(hoverBoard);
console.log(hoverBoard === electricScooter);

console.log(Symbol.keyFor(electricScooter));
console.log('transit' === Symbol.keyFor(electricScooter));

// return value
// symbol
// Symbol(transit)
// true
// transit
// true
```
## Symbol can be used to define some special well-known methods in objects
The ecma has all the well-known Symbols listed on their [website](http://www.ecma-international.org/ecma-262/6.0/#sec-well-known-symbols), but here are few common ones:

* Symbol.iterator
* Symbol.match
* Symbol.replace
* Symbol.search

String's `search()` method depends on a special well-known Symbol. If the argument given to search is not an intance of `RegExp`, it the creates a `RegExp` using the given argument as the constructor argument.

Below we have an instance of motorcyles with 2 attributes: make and color. The `Symbol.search()` method takes a value as a parameter and searches for the content in the `make` field. The method also prints an informational message about the value of `this` and the value of the `value` argument that is passed into the `Symbol.search()` method. 

```javascript
class Motorcycle {
	constructor(make, color) {
		this.make = make;
		this.color = color; 
	}

	toString() { return this.make; }

	[Symbol.search](value) {
		console.info('this: ' + this + ', value: ' + value);
		return value.search(this.make);
	}
}

const motorcycles = [
  new Motorcycle('Honda', 'red'),
  new Motorcycle('BMW', 'red'),
  new Motorcycle('Kawasaki.', 'green'),
  new Motorcycle('Harley-Davidson', 'black'),
  new Motorcycle('Yamaha', 'red') ];

const makes = 'Honda, Harley-Davidson, BMW';
for (const motorcycle of motorcycles) {
  console.log(`Result of search: ${makes.search(motorcycle)}`)
}

// return value 
// this: Honda, value: Honda, Harley-Davidson, BMW
// Result of search: 0
// this: BMW, value: Honda, Harley-Davidson, BMW
// Result of search: 24
// this: Kawasaki., value: Honda, Harley-Davidson, BMW
// Result of search: -1
// this: Harley-Davidson, value: Honda, Harley-Davidson, BMW
// Result of search: 7
// this: Yamaha, value: Honda, Harley-Davidson, BMW
// Result of search: -1
```
Below the class `Motorcycle`, the code creates an array set to the variable `motorcycles`, which includes a bunch of instances of `Motorcycle`. Below that, we can see the code sets the variable `makes` to a string that includes 3 different motorycyle brands (`Honda, Harley-Davidson, BMW`) Below, that we can see that the code loops through the `motorcycles` array and passes each instance of `Motorcycle` found in the `motorcycles` array into the argument of `makes.search()`. You can see in the code that the specially defined `Symbol.search` method found in the `Motorcycle` class is called in `make.search(motorcycle)`.

Because the special well-known method `Symbol.search()` is defined in the class, when we call `String's search()` method, it uses the special well-known method defined in the class to execute the search.

## closing thoughts
I've never actually seen or used Symbols in my professional work or "just for fun" work. This Mozilla blog [post](https://hacks.mozilla.org/2015/06/es6-in-depth-symbols/) helped me to get a better idea of how Symbols can help with reducing errors and debugging.


