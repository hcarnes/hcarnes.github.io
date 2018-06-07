---
layout: post
title:      "HTTP"
date:       2018-06-06 15:12:15 -0500
permalink:  http_protocol
---

The client needs data from the server the and the server needs a way to present the data to the user on the client. They both need each other. And, if it weren't for HTTP, they wouldn't know how to communicate. Communication is a major key in any relationship, so it comes to no surprise that in 1991 Tim Berner's Lee created the HyperText Transfer Protocol(HTTP) for communication between the client and server.

So...
><img src="https://i.imgur.com/UOyjnqw.png" title="http_blob" height="250" width="250" class="img-responsive"> According to MDN, HTTP is a protocol which allows the fetching of resources, such as HTML documents. It is the foundation of any data exchange on the Web and a client-server protocol, which means requests are initiated by the recipient, usually the Web browser. A complete document is reconstructed from the different sub-documents fetched, for instance text, layout description, images, videos, scripts, and more.

A protocol is a set of rules, and HTTP is a set of rules for formatting data when making requests. HTTP takes place through a TCP/IP connection.

> <img src="https://i.imgur.com/ATv1MVL.png" title="tcp_blob" height="250" width="250" class="img-responsive"> TCP/IP stands for Transmission Control Protocol/Internet Protocol. TCP/IP is used as a standard for transmitting data over networks. When you make a request, that information is broken up into packets. Each packet is tagged with a TCP header, which include the source and destination port numbers, and an IP header which includes the source and destination IP addresses to give it its identity. Once the packets reach the destination, they are reassembled again and delivered as one piece.

HTTP enforces rules about: 

* **Request methods** - types of methods available to manipulate [resources](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Identifying_resources_on_the_Web)
* **Request header fields** - formatting requests to a server
* **Response header fields** - formatting reponses that are returned to the client

## Request Methods

HTTP verbs AKA request methods let you manipulate data or retreive data.

* **Get** - only gets/retrieves data; ex) `get '/questions'`
* **Head** - this is the same as GET, but the response does not have a body; ex) `HEAD /index.html`
* **Post** - sends data to server that causes a change in state (usually through an HTML form) ex) signing up a new user; ex)`post '/users'`
* **Patch** - modifies part of a resource; ex) `patch '/questions/:id'`
* **Delete** - deletes the specified resource; ex) `delete '/questions/:id'`
* **Put** - creates a resource or replaces representation; ex) `PUT /questions/<new_question>`
* **Options** - used to describe the communication options for the target resource; ex) `OPTIONS /users/me`
* **Trace** - performs a message loop-back test along the path to the target resource; ex) `TRACE /index.html`

## General Header

The general header shows the request url, request method, status code, the IP address, and the [referrer policy header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy).

<img src="https://i.imgur.com/cHJWskQ.png" title="general_header" class="img-responsive">

The status codes are key in understanding what happens if you get an error and they are organized into general categories. You can always google them but here is a quick cheatsheet.

<img src="https://i.imgur.com/3tvIkNI.jpg" title="status_codes" height="450" width="450" class="img-responsive">


## Request Header Fields

This is information that a user's browser sends to a web server containing details of whwat the browser wants and will accept from a server. This includes type, version, and capabilities of the browser making the request so that the server return compatible data. Here is what it looks like in the console under the network tab once you type a url in the browser and hit enter:

<img src="https://i.imgur.com/2JjOg1a.png" title="request_header" class="img-responsive">

## Response Header 

This is info that the web server sends back to the browser in response to receiving an HTTP request. Data, size, type of file, and data about the server are includes in this header. Here is what it looks like in the console under the network tab:

<img src="https://i.imgur.com/RX4n4Sr.png" title="response_header" class="img-responsive">

## Last Words

HTTP is **stateless**, meaning each request between the client and server is being executed independently. The most popular way to link a series of requests together is by using cookies 🍪 to create a [session](https://developer.mozilla.org/en-US/docs/Web/HTTP/Session).


 
 


