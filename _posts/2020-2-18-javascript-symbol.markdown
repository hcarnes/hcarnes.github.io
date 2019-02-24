---
layout: post
title:      "JavaScript ES6+ Symbols"
date:       2019-2-22 00:08:15 -0500
permalink:  javascript_symbol
---

In the last post, we explored JavaScript traditional `for loop` versus the modern `for loop`. We also learned that we can access the index of an arrary using the modern `for loop` by using the `entries()` iterator which implements a method of the new primitive type `Symbol`. 

You are probably familiar with the older primitive types: 

* undefined
* null
* boolean
* Number
* String
* Object (includes Array)

Symbol is a new primitive type! What can Symbol do for programmers?

1. It can be used to define properties for objects for iteration
2. It can be used to easily define a global registry or dictionary of objects
3. It can be used to define some special well-known methods in objects

This post will provide of an overview what symbols can be used for, so let's start at the top.

## defining properties for objects in iteration
Symbol are defined using the `Symbol()` function. The argument passed to this function serves as a description of the Symbol, which is useful when debugging. Each Symbol is distinct, even if they have the same argument value.

```javascript
Symbol('bicycyle') === Symbol('bicycle');

// return value
// false
```

Here is another example that shows how each symbol defined with Symbolo() is unique:

```javascript
const subway = Symbol('transit');

const streetcar = Symbol('transit');

subway === streetcar

// return value
//false
```

Below, we have a car object with properties of make, model, and color.

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
Let's iterate over the properties of car using `for...in` and we what we get:

```javascript
for (const property in car) {
  console.log(property + ': ' + car[property]);
}

//return value
// id: 1
// colorValue: green
// undefined
```

Hmm, the properies defined as Symbols seems to be hidden. Now, let's query for the `car` property names and their values:

```javascript
console.log(Object.getOwnPropertyNames(car));

["id", "colorValue"]
```
Hmm, the properies defined as Symbols seems to be hidden here too. This is actually a big part of what makes Symbols special: all Symbol properties are hidden from normal iteration. Keep in mind, they are not private, meaning that any code with access to the oject can access and change the value for Symbol property. Because the properties are hidden, it's nice to have a method that will expose an object's hidden symbol properties. The code below shows how to view, access, and change a symbol propety.

We can view all the symbols of an object.

```javascript
console.log(Object.getOwnPropertySymbols(car));

// return value
// [Symbol(makeValue), Symbol(modelValue)]
```

We can access the symbols of an object.

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

## symbol can be used to define a global registry object
Remember when I said earlier that each symbol is distinct, even if they have the same argument value? Well, there's an expection to that if you define symbol using the `for()` method. In the code below, each property is defined using `Symbol.for()` with the same description passed in. The first call to `for()` creates a unique symbol and the second call to `for()` fetches the symbol created in the last call through the global registry. The call to `keyFor()` returns the key associated with the Symbol in the global registry.

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
## symbol can be used to define some special well-known methods in objects
The ecma has all the well-known symbols listed on their [website](http://www.ecma-international.org/ecma-262/6.0/#sec-well-known-symbols), but here are few common one:

* Symbol.iterator
* Symbol.match
* Symbol.replace
* Symbol.search

String's `search()` method depends on a special well known Symbol. If the argument given to search is not an intance of `RegExp`, it the creates a `RegExp` using the givent argument as the constructor argument.

Below we have an instance of motorcyles with two fields: make and model. The `Symbol.search()` method takes a value as a parameter and searches for the content in the `make` field.

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
If you want your objects to be used by String's `search()`, you can use a 

For example, if you wanted to pass a (Rosie Language Pattern)[https://gitlab.com/rosie-pattern-language/rosie/blob/master/rpl/time.rpl] into the search instead of a regular expression, you could use symbols.


