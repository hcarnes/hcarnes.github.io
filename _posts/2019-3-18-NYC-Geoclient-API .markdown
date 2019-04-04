---
layout: post
title:      "Geocoding with NYC's GeoClient API"
date:       2019-3-24 00:010:15 -0500
permalink:  nyc_geo_client_api
published: true
---

Filth Finder is an application that loads NYC restaurants near you and allows you to view health inspection violations at each restaurant. One problem that had to be solved when developing Filth Finder was how to calculate the distance between the user and all of the amazing restaurants NYC has to offer. The NYC Open Data API provided the addresses for each restaurant, but Filth Finder needed the geolocation of those restaurants. Fortunately, NYC has a nifty [GeoClient API](https://developer.cityofnewyork.us/api/geoclient-api) that allowed for geocoding the restaurant locations.

This post will unpack how Filth Finder's API works with a focus on geocoding. For future reference, the term "establishment" refers to a restaurant in this post.

**You can view Filth Finder's code, including the backend, [here](https://github.com/hcarnes/filth_finder).**

## What does the Filth Finder Rails API do?

1. Runs a one-time rake task that seeds the database with all of the NYC establishments and their geocoded locations. 
2. Provides an endpoint for fetching the top 50 restaurants closest to the user.

## What libraries were used for geocoding?

* **[PostGIS](https://postgis.net/)** is a library for Postgres, and it's used in this case to add database support for geocoding locations. 
* **[Activerecord PostGIS Adapter](https://github.com/rgeo/activerecord-postgis-adapter)** allows for accessing features of the PostGIS geospatial database from ActiveRecord.
* **[NYC GeoClient API](https://developer.cityofnewyork.us/api/geoclient-api)** provides coordinate and geographic attributes for supported input locations (establishment addresses).

## What is a rake task?

Rake tasks allow you to automate processes. In this case, we're automating the process of geocoding establishments and saving them to the database. Once they are saved to the database, we can use them on the frontend to determine which restaurants are closest to the user. The rake task was generated with `rails g task establishments`. This generates a file called `establishments.rake`. Below, you can see the "bare bones" of a rake task:

```ruby
require "nyc_geo_client"

namespace :establishments do
  desc "Load establishments from API, geocode, and store to db"
  task update_establishments: :environment do
    # the code that carries out a specific task goes here
  end
end
```

## Geocoding locations and saving to the DB

> Camis is a uniqe id number assigned to each establishment in the NYC Open Data API.

A new client is created using the NYC GeoClient API and set it equal to the `client` variable. Then, establishment data is fetched from the NYC Open Data API and parsed into JSON. The JSON is stored in a variable called `establishments`. Next, the code iterates through `establishments` and checks to make sure that only establishments with existing Camis ids will be geocoded. 

Next, the code builds establishment objects with the following attributes: `camis`, `dba`, and `address`. Notice that the NYC Geoclient calls the adress method (`client.address()`) and sets the `house_number`, `street`,`borough`, and `zipcode`. This line of code is where PostGIS geocoding happens: 

```ruby
e.location = "POINT(#{location["address"]["longitude"]} #{location["address"]["latitude"]})"
``` 

It takes the latitude and longitude we created using the NYC GeoClient API to create a single point. Finally, the establishment object is saved to the database.

```ruby
client = NYCGeoClient::Client.new(app_id: 'APP_ID', app_key: 'APP_KEY')
response = Faraday.get "https://data.cityofnewyork.us/resource/9w7m-hzhe.json?$group=camis,building,street,zipcode,dba&$select=camis,building,street,zipcode,dba&$limit=50000"
establishments = JSON.parse(response.body)

establishments.each do |e_obj|
  existing = Establishment.find_by_camis(e_obj["camis"])
  next if existing
  e = Establishment.new camis: e_obj["camis"], dba: e_obj["dba"], address: [e_obj["building"], e_obj["street"], e_obj["zipcode"]].join(" ")
  location = client.address(house_number: e_obj["building"], street: e_obj["street"], borough: e_obj["boro"], zip: e_obj["zipcode"])

  if location && location["address"] && location["address"]["latitude"] && location["address"]["longitude"]
    e.location = "POINT(#{location["address"]["longitude"]} #{location["address"]["latitude"]})"
    if e.save
      Rails.logger.info "Saved #{e.inspect} successfully"
    else
      Rails.logger.info "Failed to save #{e.inspect}"
    end

  else
    Rails.logger.info "Failed to geocode #{e_obj.inspect}"
    next
  end
end
```

## What would this look like in the database?

In Postico, you can see how the `location` column stores the geocoded location of the establishment.

<img src="https://i.imgur.com/M6sEYH7.png" alt="view of the database in Postico" title="data" />

## How does the API provide the 50 closest locations?

Now that we have all the establisment geolocations, we can query the database and return the restaurants closest to the user. With that said, the API has a route where users can find the locations near them.

```ruby
Rails.application.routes.draw do
  get "/near_me", to: "establishments#near_me"
end
```
The `EstablishmentsController` uses ActiveRecord to retrieve the top 50 establishments closest to the user. The `<->` function determines the distance between the user and the given establishment. Note, the user's location is passed in through params and converted to the datatype of geography. `SRID` stands for "spatial reference id" and tells Postgis which spatial reference system to use. In this case, the code stores the data using the WGS84 projected coordinate system (SRID 4326) that allows you to calculate things such as the distance between two points. I found [this](https://stackoverflow.com/questions/21935863/srid-meaning-in-postgis) Stack Overflow post helpful in explaining `SRID`.

```ruby
class EstablishmentsController < ApplicationController
  def near_me
    establishments = Establishment.order(["establishments.location <-> ?::geography", "SRID=4326;POINT(#{params[:lng].to_f} #{params[:lat].to_f})"]).limit(50)
  end
end
```

Here is the ActiveRecord query translated into SQL.

```SQL
SELECT  "establishments":
FROM "establishments" 
ORDER BY establishments.location <-> 'SRID=4326;POINT(#{params[:lng].to_f} #{params[:lat].to_f})'::geography
LIMIT 50
```

This API solved my problem: I was now able to compare the user's geolocation to each restaurant's location Filth Finder now had all the establishments and their geocoded locations waiting to be compared to the user location, which was determined on the client. 
