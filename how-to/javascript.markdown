---
layout: howto
title:  "JavaScript"
date:   2014-08-26 9:42:00am
categories: [howto]
---

# JavaScript Guide

Making the move from a REST-based approach to a real-time publish-subscribe architecture can seem intimidating at first. Once the core concepts are understood, your options as a developer really start to open up. This guide will get you started with using the Kaazing JavaScript.

## Introduction

Traditionally, when you load a web page into the browser, there are a lot of things going on behind the scenes.  The most notable detail from an architetectural perspective is that many one-off connections are made to a server, a resource is requested, delivered, and then the connection is terminated.  This happens repeatedly for images, CSS file, JavaScript files, etc.  Even AJAX requests happen in this manner.

### Connecting

In a publish-subscribe architecture, a connection is made to the server, and remains open until it is either closed by your application, or at the server for any myriad of reasons.  It is this long-lived connection that allows data to flow freely in both directions - to the server and from the server.

Initially this may sound counter-intuitive.  Will not long-lived connections create an unmanageable load on your infrastructure?  Kaazing Gateway leverages modern networking advances to deliver web-scale performance.  In many cases, performing data operations using Kaazing Gateway will enable you to deliver the same scale you need today, with less infrastructure.

> Kaazing Gateway will even allow you to configure load balancing and clustering schemes.

You may be concerned that not all the client technologies you need to support will support WebSocket (the underlying technology that makes all of this possible).  Kaazing Gateway will fallback gracefully to deliver nearly the same performance using a variety of methods.  When using Kaazing Gateway, you do not have to worry about whether or not your clients will be able to connect.

> If you are concerned that you need specific ports open, Kaazing Gateway can be installed in your DMZ, where you can actually close all ports, and still keep real-time communication features.

### Publish

The first part of the publish-subscribe architecture is "publish".  Publishing data means that you are sending data across the open connection.  That is it.  Think of this as roughly analogous to the XMLHttpRequest send method.

When publishing data, you do not point at a specific script on the server.  Rather you publish to a "topic".  Any number of systems may publish to a given topic.  In this manner the various parts of your architecture can be completely decoupled.

Where you might historically have kept the endpoint of an XHR exchange the same, you can now change that endpoint as you see fit.  In fact, you can even change the underlying technology stack entirely, without impacting the other clients.

### Subscribe

To receive data from some other part of the system, you "subscribe" to a given topic.  Whenever data is published to that topic, it will be distributed to any listening clients.  Those clients might be web browsers, but they might also be other servers, or even native applications.

It is in this fashion that publish-subscribe allows you to decouple all the parts of your application.  This approach becomes very powerful, allowing you to change subsystems without impacting one another.

### Topics

Topics are effectively the destinations for your data communication.  You can publish to any number of topics, and can also subscribe to any number of topics.  To make an analogy to XHR requests, the topic would be the URL endpoint.

Topic names follow a specific naming format.  Topics always start with "/topic/" and then you append whatever additional naming format that makes sense for your application.  If you are building a chat application, a topic name might be "/topic/chat".

You can use a period to further isolate topics.  For example, in a chat application, you may want to have a topic that gives you a list of the names of people in the room.  This might take the form of "/topic/chat.list".  This notation is common throughout most publish-subscribe systems.

## Let's Do This

The first step to take when using Kaazing Gateway, is to include the necessary SCRIPT tags into your page.  There are three tags that need to be included.

    <script src="WebSocket.js"></script>
    <script src="JMS.js"></script>
    <script src="Kaazing.js"></script>

The first is a WebSocket library that provides the aforementioned capability to fall back to other means of connectivity should WebSocket not be supported.

The second is a protocol library called "JMS".  JMS stands for Java Messaging Service, which is really an implementation detail we do not need to concern ourselves with at this point.  Just know that it is this library that layers on the robust publish-subscribe mechanisms needed to provide decoupling of your systems.

The last script to include is the Kaazing Gateway library that you will work with as the developer.  This library presents a straightforward API for leveraging publish-subscribe in your application.  We will deal with this API in the remainder of this guide.

### Connecting

With the necessary libraries available, the next step is to actually connect to Kaazing Gateway.  Keep in mind that once this connection is opened, it stays that way.  You will want to instantiate a single instance of the library in a global scope, and reuse it throughout the life of your application.

    var kzng = new Kaazing();

    kzng.connect( KAAZING_ID, null, {
	    success: function() {
		    console.log( "You are connected." );
	    },
	    error: function() {
		    console.log( "Error connecting." );
	    }
    } );

The connect method identifies who you are by using your Kaazing Developer ID.  A default destination of the free Developer Edition of Kaazing Gateway, is assumed.  You can pass an option object with an alternative endpoint, and other parameters.

The connect method also takes an object with success and error properties.  These properties are pointers to functions that will be called once the connection has been made (or failed).  You can choose to use anonymous functions inline, or point to other functions in your application.

Knowing when you are connected is important.  You cannot subscribe to a topic for data, or publish data, until the connection is made.  Doing so before the connection is made will result in an error.

### Subscribe

To listen for incoming data we will use the subscribe method on the Kaazing Gateway library.  This method takes a topic name as the argument.  After you subscribe to a topic, and data sent to that topic will be distributed to any connected clients.

    kzng.subscribe( "/topic/chat" );

Topic names are effectively an arbitrary naming scheme that you find makes the most sense for your application.  You do not need to configure topics on the server - they are logical separations.

> **Pro Tip:** Extract your topic names from your code to global variables.  This will allow you to change endpoints without having to crawl your code in the future.  It will also help you to see the logical organization of your real-time communications in one glance.

### Publish

Publishing data can take place at any point in your application, after you have connected to Kaazing Gateway.  You do not need to be subscribed to a topic in order to publish content to it.  You can also publish data in a "fire and forget" fashion.

    kzng.publish( "/topic/chat", "Hello world!" );

The publish method needs to know the name of the topic to which you want to send data.  The second argument is the content you want to send to the topic.  Most of the times you will want to send text content, but Kaazing Gateway is capable of handling binary data as well.

If you want to publish a complex structure such as a JavaScript object or Array, you will want to serialize that data into a JSON text string before publishing it to the topic.  Conversely, when the data arrives, if you are using content other than a text string, you will want to deserialize it using JSON.parse().

## Summary

You can now connect to Kaazing Gateway, subscribe to a topic, and publish data to a topic.  That is it!  You are on your way to building your first real-time application.
