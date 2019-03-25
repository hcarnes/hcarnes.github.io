---
layout: post
title:      "NYC's Geoclient API: Filth Finder Edition"
date:       2019-3-27 00:010:15 -0500
permalink:  nyc_geo_client_api
published: false
---

Did you know NYC has a [geoclient API](https://developer.cityofnewyork.us/api/geoclient-api)? Filth Finder uses the NYC Geoclient API to load establishments from the NYC Open Data API, geocode the data, and store the data to the database. This post will cover the backend of Filth Finder, including how the API is setup, geocoding the establishment addresses, and creating a resource so that users can view nearby restaurants. Wow, that's a lot!

**You can view Filth Finder's code [here](https://github.com/hcarnes/filth_finder).**

> Geocoding is the computational process of transforming a physical address description to latitude and longitute coordinates.

## The goals

The first goal of the API used in Filth Finder is to run a one-time rake task that seeds the database with all of the NYC establishments and their geocoded locations. The second goal is to provide a resource for fetching the top 50 restaurants closest to the user.

## The libraries

* **[PostGIS](https://postgis.net/)** is a library for Postgres, and its use in this case to add database support for geocoding locations. 
* **[Activerecord PostGIS Adapter](https://github.com/rgeo/activerecord-postgis-adapter)** allows for accessing features of the PostGIS geospatial database from ActiveRecord.
* **[NYC Geoclient API](https://developer.cityofnewyork.us/api/geoclient-api)** provides coordinate and geographic attributes for supported input locations (establishment addresses).
* **[Faraday](https://github.com/lostisland/faraday)** is an HTTP client for fetching the data that will be parsed into JSON and geocoded.
* **[Rack-cors](https://github.com/cyu/rack-cors)** allows the API to make cross domain AJAX calls. You can configure to allow for anything.

## Creating the rake task

Rake tasks allow you to automate processes. In this case, we are automating the process of geocoding establishments and saving them to the database. Once they are saved to the database, we can use them on the frontend to determine which restaurants are closest to the user. The rake task was generated with `rails g establishments`. This file generated is called `establishments.rake`.

## How the rake task geocodes

> Camis is a uniqe id number assigned to each establishment in the NYC Open Data API.

The task includes a description and name. Next, a new client is created and set it equal to the `client` variable. Then, establishment data is fetched from the NYC Open Data API and parsed into JSON. The JSON is stored in a variable called `establishments`. Next, the code iterates through `establishments` and builds establishment objects with the following attributes: `camis`, `dba`, and `address`. Notice that the NYC Geoclient calls the adress method (`client.address()`) and sets the `house_number`, `street`,`borough`, and `zipcode` using the method.

This line of code is where PostGIS magic happens: 

`e.location = "POINT(#{location["address"]["longitude"]} #{location["address"]["latitude"]})"`. 

It takes the latitude and longitude we created using the NYC GeoClient API to create a single point. Finally, the establishment object is saved to the database.

```ruby
require "nyc_geo_client"

namespace :establishments do
  desc "Load establishments from API, geocode, and store to db"
  task update_establishments: :environment do
    client = NYCGeoClient::Client.new(app_id: APP_ID, app_key: APP_KEY)
    response = Faraday.get "https://data.cityofnewyork.us/resource/9w7m-hzhe.json?$group=camis,building,street,zipcode,dba&$select=camis,building,street,zipcode,dba&
    $limit=50000"
    establishments = JSON.parse(response.body)

    establishments.each do |e_obj|
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
end

```

Below, you can how in Postico, the `location` column stores the geocoded location of the establishment.

<img src="https://i.imgur.com/M6sEYH7.png" alt="view of the database in Postico" title="data" />

