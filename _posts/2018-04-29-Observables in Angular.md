---
layout: post
title:      "Angular Basics: Handling Async Data"
date:       2018-04-29 15:12:15 -0500
permalink:  observables_in_angular
---

I am learning a new JavaScript framework, Angular 5. Last week, after working through the [Tour of Heroes](https://angular.io/guide/quickstart) tutorial in the Angular 5 docs, I still wanted to better understand some concepts that were new to me, specifically services and observables. Thus, [Tour of Cats](https://github.com/hcarnes/tour-of-cats)  🐱 was born. This post will cover the fundamentals of handling async data through observabls and services.

# Services
One way that Angular differs from React is the through the concept of services. Services are classes with a specific purpose (for example, handling data) that are separate from the view but also shared across components.

<img src="https://i.imgur.com/vwLiiVa.jpg?1" title="observables" height="500" width="500" class="img-responsive">

```javascript
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs/Observable';
import { of } from 'rxjs/observable/of';
import { Cat } from './hero';
import { HttpClient, HttpHeaders } from '@angular/common/http';
import { catchError, map, tap } from 'rxjs/operators';

const httpOptions = {
  headers: new HttpHeaders({ 'Content-Type': 'application/json'})
}

@Injectable()
export class CatService {

  constructor(
    private http: HttpClient, { }

    private lambdaCats = 'https://ast9z666ll.execute-api.us-east-2.amazonaws.com/dev/cats'

  getCats (): Observable<Cat[]> {
    return this.http.get<Cat[]>(this.lambdaCats)
      .pipe(
        tap(heroes => this.log(`fetched heroes`)),
        catchError(this.handleError('getCats', []))
      );
  }

  /** GET hero by id. Will 404 if id not found */
  getCat(id: number): Observable<Cat> {
    const url = `${this.lambdaCats}/${id}`
    return this.http.get<Cat>(url).pipe(
      tap(_ => this.log(`fetched hero id=${id}`)),
      catchError(this.handleError<Cat>(`getCat id=${id}`))
    )
  }
}
```

#Observables

>An array whose items arrive asynchronously over time. Observables help you manage asynchronous data, such as data coming from a backend service. Observables are used within Angular itself, including Angular's event system and its HTTP client service. To use observables, Angular uses a third-party library called Reactive Extensions (RxJS). Observables are a proposed feature for ES2016, the next version of JavaScript. (source: Angular.io)



