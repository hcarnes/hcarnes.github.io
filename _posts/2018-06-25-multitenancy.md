---
layout: post
title:      "Multitenancy"
date:       2018-07-06 15:12:15 -0500
permalink:  multitenancy
---

Multitenancy

> The term "software multitenancy" refers to a software architecture in which a single instance of software runs on a server and serves multiple tenants. Source: [Wikipedia](https://en.wikipedia.org/wiki/Multitenancy)

A way to separate records based on the account that is currently logged in. This can come in different ways. 1) go to subdomain that you set up when you register

Apartment lets you create tenants, which are the subdomains you want. You grab `tenant_name` from user when they register. Apartment also creates a separate Postgres SQL schema. So, it separates the data into separate databases for you.

In development, you can't use `localhost` to reference subdomains and you can't use IP address either. So somewhere registered the `lvh.me` points to `127001`, so it will work like `locahost` but because it is a real domain it will allow you to test locally with subdomains.

Apartment knows what tenant you are are in and scopes things for you. Migrations can run across tenants. Apartments uses middleware to query databases separately. So, you can build things independently for each subdomain. The main domain 

In production use www as the main subdomain

So you and exclude users from user the `www` subdomain.

Every Saas (software as a service) app needs multitenancy and user management.







 
 


