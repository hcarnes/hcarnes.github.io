---
layout: post
title: "Making Filth Finder Faster with GCP Storage"
date: 2019-5-21 00:09:15 -0500
permalink: making_filth_finder_faster
---

I've written a bit about Filth Finder, an app that surfaces health inspections from NYC restaurants near you. Most recently, I [wrote](https://codehannah.nyc/nyc_geo_client_api) about the API that served the restaurants to the frontend. While this implementation worked, it was pretty slow to load. In an effort to speed up the loading time, I [rewrote the backend](https://github.com/hcarnes/filth_finder/commit/40ea0759ae863bd5b6b020c28b671b92275cdcbf) to pull the of index restaurants from Google Cloud Storage. Check out how much faster it [loads](https://filth-finder.codehannah.nyc/).

> If you're interesed, here is another [post](https://codehannah.nyc/filth_finder_react_hooks) about rewriting Filth Finder with React Hooks.

## Why was it slow?

Previously, the app was hosted on Heroku as two different apps, each of which required 5-10 seconds to spin up since they were on the free plan. The frontend served up the React app, and the backend served up all the restaurants. Also, these apps were not concurrent, which also slowed things down. Specifically, the backend only would spin up once the frontend had finished.

## Moving to the cloud

With the help of the [Google Cloud Storage: Node.js Client](https://www.npmjs.com/package/@google-cloud/storage), we now have a [script](https://github.com/hcarnes/filth_finder/blob/40ea0759ae863bd5b6b020c28b671b92275cdcbf/scripts/build-geocode-index.js) that grabs all the restaurants from the NYC Open Data API and puts them in a Google Cloud Storage bucket. The city updates the health inspection info pretty often, so this script will allow us to grab the new restaurants and the updated inspection info. It takes about 45 minutes to load the restaurants.

## Updating the Establishment model

> According to Wikipedia, the haversine formula determines the great-circle distance between two points on a sphere given their longitudes and latitudes.

I also updated the [Establishment model](https://github.com/hcarnes/filth_finder/blob/40ea0759ae863bd5b6b020c28b671b92275cdcbf/src/models/Establishment.js) to fetch data from the newly created Google Cloud Storage bucket instead of the old backend. The [haversine library](https://www.npmjs.com/package/haversine) allowed the Establishment model to calculate the distance between the user (the latitude and longitude is passed in the through the params) and the restaurants. The restaurants are still returned in ascending order based on the length of the distance between the user and the restaurants. The closest 20 restaurants are still rendered for the user.

## Final notes

Now the app runs faster because the React frontend can spin up and immediately get the restauraunts from the Google Cloud Storage bucket.

Google Cloud Storage + Filth Finder = friends forever (or at least until something better comes along!).

<a href="/img/friends.svg"><img src="/img/friends.svg" alt="drawing of cat and shiba hugging" class="friends" /></a>
