---
layout: post
title: "Interviewing TypeScript"
date: 2019-6-18 00:03:15 -0500
permalink: interviewing_typescript
---

Sometimes JavaScript tries to guess what you mean.

For example:

```javascript
true + []

// returns "true"
```

You could say JavaScript is trying to be helpful by not asking any questions and just getting results, but sometimes those results come with a cost. Sometimes you may not find a bug that JavaScript's guesswork produced until your code is in production. TypeScript helps solve this problem by providing error messages in a more timely fashion

For example:

```typescript
true + []

//error TS2365: Operator '+' cannot be applied to types 'true' and 'never[]'.
```

# A scenario
Imagine that one day you find yourself complaining that you are frequently getting type errors in your JavaScript code once it's already in production. It's causing such a headache - can't we hire someone to take care of these JavaScript issues? Can't we get someone who can work hand-in-hand with our JavaScript code and catch type errors before runtime?

# The interview process

*You put a job post up on Hacker News and within 24 hours TypeScript's resume comes into your inbox. They have a decent cover letter, so you call them for a phone screen. This candidate stood out to you because they said they will deliver error messages in your text editor as you write your programs. You invite TypeScript in for an onsite.*

*During the onsite, TypeScript does a whole presentation about how your code will get type checked before it runs. But, you still have a lot of questions about how exactly everything works.*

You say, "Hey, let's walk through this. I know that this is how my code gets compiled now..."

*You grab a marker and write on the board:*

High level overview of how JavaScript code runs:

1. JavaScript source code is parsed by a program called the compiler, which converts the code into an AST (abstract syntax tree), a data structure that ignores whitespace, comments, and spaces.
2. AST is converted to bytecode.
3. Bytecode gets fed into another program called runtime, and this is when code gets evaluated. 

And TypeScript nods in agreement and says, "Yes, that how JavaScript code runs. When I get involved, there are some additional steps that allow the code to be type checked by the compiler".

1. TypeScript source code is compiled into a TypeScript AST
2. **TypeScript AST is type checked**
3. TypeScript AST is converted to JavaScript
4. JavaScript is converted to JavaScript AST
5. JavaScript AST is converted to bytecode.
6. Bytecode is evaluated at runtime.

TypeScript adds, "Please note, I improve your ability to catch type errors before runtime but will not change the results of running your code. Syntactically, all valid JavaScript is valid TypeScript. In other words, by using TypeScript, you aren't actually changing how your JavaScript code works."

> TypeScript sometimes refers to themselves in third person. :D

## Technical questions
*Things are going great and everything seems to be moving along nicely. You decide to dig deeper and ask some tougher questions.*

You ask, "Are you dynamically typed or statically typed?

To which TypeScript replies, "I'm not entirely one or the other. You can use type annotation to define types or you can simply leave them out and I'll do my best to infer the type. I'm at my best when I know the type of everything in the program at compile time, but I can still compile everything if I don't have all of that information.

You follow up, by asking, "If we migrate to you, do we have move everything to TypeScript at once in order for our code to work?"

TypeScript smirks, then replies, "You don't have to have full type coverage from the beginning, you can bring me in gradually. This ties into what I mentioned before: all JavaScript is valid Typescript."

After a short pause TypeScript says, "Look, I'm going to be upfront and tell you that it needs to be fixed when your code is type checked by the compiler. Some people might say I'm a bit of a complainer, but I just call things how I see 'em."

You are a bit put off by the arrogance, but you thank them for their transparency anyways. Next, you transition back to more "soft skills" by asking about how they communicate errors once they find them.

To which they reply, "Well, if you have a halfway decent text editor, you're going to see some of those red squiggly lines under places where you need to make changes. This goes back to how I type check at compile time, which gives you syntax and type errors at compile instead of at runtime."

## Offer extended

**TypeScript, you're hired!**

<div class="friends">
  <a href="/img/hired_shiba.svg">
    <img src="/img/hired_shiba.svg" alt="drawing of shiba raising hands in celebration wearing a shirt that says typescript"/>
  </a>
</div>


*Sources: [Programming TypeScript](http://shop.oreilly.com/product/0636920158059.do), May 2019. Boris Cherny. Chapter 2. TypeScript: A 10_000 Foot View*


