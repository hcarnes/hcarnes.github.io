---
layout: post
title:      "Multi-Tenancy in Rails"
date:       2018-7-28 11:00:00 -0500
permalink:  multi-tenancy
published: false
---

An application that runs multiple clients on one system.

* multiple accounts
* data separation
* customization
* optimize resources
  * you can run deploy once and fix bugs one, and you don't have to manage one server for each customer

When not to use it:

  * simple website
  * social networks - if main idea is sharing data, we don't want to separate it

Multi-tenant Controllers need to:

* Recognize account
  * domain based: org.theodora.com
  * url based:  theodora.com/org
  * Login based - could be cookies based/session id based
    * uses the current user as the anchor
    * user is set in the session
    * could be account and user

* Set context: current_user
* Access data: current_user.xxx

before filter in application controller

Models

* Separate DB
  * pros
    * easy customization
    * data totally separated
    * failure local to individual db
    * against the grain in Rails
    * not very common
  * cons
    * resource overhead (db for each client!)
    * hard to cache?

* Schema separation
  * pros
    * strong data separation
    * independent account migration bc of data separation, less data to migrate at one time
  * cons
    * some resource overhead

* Scoped access - separated by specific organization_id
  * pros
    * low resource overhead
    * easy to create new accounts, just create new account in the table
  * cons
    * weak data separation (vulnerable to bugs)
    * hard to migrate large data sets
    * requires sharding/partitioning to scale

