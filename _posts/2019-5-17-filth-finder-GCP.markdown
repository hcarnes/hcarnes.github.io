---
layout: post
title: "Making Filth Finder Faster with Google Cloud"
date: 2019-5-21 00:09:15 -0500
permalink: making-filth-finder-faster
---

I've written a bit about Filth Finder, an app that surfaces health inspections from NYC restaurants near you. Most recently, I [wrote](https://codehannah.nyc/nyc_geo_client_api) about the API that served the restaurants to the frontend. While this implementation worked, it was pretty slow to load. In an effort to speed up the loading time, I [rewrote the backend](https://github.com/hcarnes/filth_finder/commit/40ea0759ae863bd5b6b020c28b671b92275cdcbf) to pull the of index restaurants from Google Cloud Storage. Check out how much faster it [loads](https://filth-finder.codehannah.nyc/).

> If you're interesed, here is another [post](https://codehannah.nyc/filth_finder_react_hooks) about rewriting Filth Finder with React Hooks.

## Why was it slow?

Previously, the app was hosted on Heroku as two different apps, each of which required 5-10 seconds to spin up since they were on the free plan. The frontend served up the React app, and the backend served up the restaurant listing API. Finally, these apps didn't start up at the same time, meaning the backend only would begin spinning up once the frontend had finished. The end result was a user would need to wait nearly 20 seconds to begin using the app if it hadn't been visited in a while.

## Moving to the cloud

With the help of the [Google Cloud Storage: Node.js Client](https://www.npmjs.com/package/@google-cloud/storage), I now have a [script](https://github.com/hcarnes/filth_finder/blob/40ea0759ae863bd5b6b020c28b671b92275cdcbf/scripts/build-geocode-index.js) that grabs all the restaurants from the NYC Open Data API and puts them in a Google Cloud Storage bucket. The city updates the health inspection info daily, so this script needs to run regularly to allow data from newly opened restaurants to be pulled. It takes about 45 minutes to load the restaurants.

## Updating the Establishment model

> According to Wikipedia, the haversine formula determines the great-circle distance between two points on a sphere given their longitudes and latitudes.

I also updated the [Establishment model](https://github.com/hcarnes/filth_finder/blob/40ea0759ae863bd5b6b020c28b671b92275cdcbf/src/models/Establishment.js#L13) to fetch data from the newly created Google Cloud Storage bucket instead of the old backend. The [haversine library](https://www.npmjs.com/package/haversine) allows the Establishment model to calculate the distance between the user (the latitude and longitude is passed in the through the params) and each of the nearly 27,000 restaurants. The restaurants are then returned in ascending order according to the distance between the user and the restaurants, then limited to 20 total results to keep the frontend zippy. The JSON file is really simple, and looks something like this:

```javascript
[
   {
      "latitude" : 40.7178921762742,
      "camis" : "41631962",
      "longitude" : -74.0008766031009,
      "dba" : "POPEYES LOUISIANA KITCHEN"
   },
   {
      "latitude" : 40.7560475658859,
      "camis" : "50072138",
      "dba" : "HUI'S GARDEN",
      "longitude" : -73.8335953523179
   },
   {
      "longitude" : -73.8279669765882,
      "dba" : "JIN DAL LAE 8",
      "latitude" : 40.7648883680589,
      "camis" : "50087614"
   },
   // 26k+ more restaurants...
]
```

## Firebase
In order to make the app load even faster, I followed [this guide](https://medium.freecodecamp.org/react-and-firebase-are-all-you-need-to-host-your-web-apps-f7ab55919f53) to take advantage of Google Cloud Firebase's free static hosting. This removes the need for Heroku completely, and Firebase static hosting doesn't require any spinning up.

## Final notes

Now the app runs faster because the React frontend loads from Firebase immediately, and quickly fetches the restauraunt list from Google Cloud Storage.

Google Cloud + Filth Finder = friends forever (or at least until something better comes along!).

<div class="friends">
  <a href="/img/friends.svg">
    <img src="/img/friends.svg" alt="drawing of cat and shiba hugging"/>
  </a>
</div>
