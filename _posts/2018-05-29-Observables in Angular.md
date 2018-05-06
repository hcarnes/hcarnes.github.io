---
layout: post
title:      "Angular 5: Observable Basics"
date:       2018-04-29 15:12:15 -0500
permalink:  observable_basics
---

After working through the [Tour of Heroes](https://angular.io/guide/quickstart) tutorial in the Angular 5 docs, I wanted to better understand a concept that was new to me: `Observable`. With that in mind, I started working on my own Tour of Cats 🐱, which I will reference below. For this post, I'm going to focus on how `Observable` handles async data requests. To get the most out of the post, it would be great if you knew the basic fundamentals of Angular 5. 

## RsJx & Observables

To understand `Observable`, you need to learn about RxJS (Reactive Extension for JavaScript). RxJS is an external JavaScript library for handling async data, and this library allows you to work with `Observable` to create programs that work with async data streams AKA reactive programs. There are also reactive extentions for other languages, such as Rx.rb for Ruby. In short, `Observable` is a major key 🔑 of the RxJS library.

## How Does it Work?

In the Tour of Cats, the app fetches cats from a backend service, which is an asynchronous operation. How can we handle this async request? `Observable`, to the rescue! `Observable` is returned by the Http call, and it will include a sequence of items that arrive asychronously over time. 

In Tour of Cats, the user clicked a button that would return a list of cat names (`CatsComponent`), and they could also click a button that would return specific details about one cat (`CatDetails`). Both of these components subscribe to `CatsService`, which provides them with an array of cat objects through `Observable`. Here is a quick overview how it works at a high level:

<img src="https://i.imgur.com/vwLiiVa.jpg?1" title="observables" height="400" width="400" class="img-responsive">

## What about the code?

**Add `Observable` to CatService**

Start with the `service`. You have to import the HTTP symbols and inject `HttpClient` to the constructor in a private property called `http`. A `service` will typically handle a specific purpose separate from the views, such as handling async data requests or logging. Here is an example of the `CatsService`:

```typescript
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs/Observable';
import { of } from 'rxjs/observable/of';
import { Cat } from './hero';
import { HttpClient, HttpHeaders } from '@angular/common/http';

const httpOptions = {
  headers: new HttpHeaders({ 'Content-Type': 'application/json'})
}

@Injectable()
export class CatService {

  constructor(
    private http: HttpClient, { }
    //I connected to AWS lambda, but you can use any url
    private lambdaCats = 'https://ast9z666ll.execute-api.us-east-2.amazonaws.com/dev/cats'

  getCats(): Observable<Cat[]> {
    return this.http.get<Cat[]>(this.lambdaCats)
  }

  getCat(id: number): Observable<Cat> {
    const url = `${this.lambdaCats}/${id}`
    return this.http.get<Cat>(url)
  }
}
```
**Then subscribe to the component(s)**

```typescript
import { Component, OnInit } from '@angular/core';
import { Cat } from '../hero';
import { CatService } from '../hero.service';

@Component({
  selector: 'app-heroes',
  templateUrl: './heroes.component.html',
  styleUrls: ['./heroes.component.css']
})
export class CatsComponent implements OnInit {
  cats: Cat[];

  constructor(private catService: CatService) { }

  ngOnInit() {
    this.getCats();
  }

  getCats(): void {
    this.catService.getCats()
    .subscribe(cats => this.cats = cats);
  }
}
```

Here is an example of the `CatsComponent`. See that the `getCats()` function calls into the service, which will return an `Observable`. Then, it immediately subscribes to the results from the `Observable` with `subscribe`. Once the `Observable` is hydrated with Cat objects, the callback function will be executed, which sets the component's `cats` property, causing the component to render the new list of cats.

<img src="https://i.imgur.com/FO8Lswu.jpg" title="observables" height="300" width="300" class="img-responsive">

As you can see above, only components that are subscribed to receive the `Observable` will receive the new list of cats. They can do whatever they want with the data once they have it. As mentioned before, the CatsComponent may render only the name of cats, while `CatDetails` may render more specific details, such as a picture and the number of lives the cat has left.