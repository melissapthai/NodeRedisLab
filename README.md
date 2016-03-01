# A Gentle Introduction to Scaling Node.js Apps with Redis

![Node is the only real dev language](https://lodejs.org/images/logos/nodejs-green.png) 

![Redis](http://download.redis.io/logocontest/82.png) 

## Introduction

In this tutorial, we’ll go through the basics of how to scale a simple Node app. Why is this an important topic? Scalable software makes it possible to expand the number of users that you’d normally not be able to support than if you were running your app on a single server. It allows your system to be enlarged to accommodate growth.

In software, scalability normally comes in two forms: vertical and horizontal scaling. Vertical scaling is the growing of a single server, with more CPUs, faster CPUs, more memory, or faster disk I/O. Horizontal scaling involves adding additional servers to support the overall applications computing requirements.

Today we’ll be talking about horizontal scaling. Although we won’t touch on load balancing across resources, we will be talking about facilitating message passing among multiple servers. So let’s get started!

## Prerequisites

Before we start learning how to scale Node apps, it’s extremely important to get the basics down. This tutorial assumes that you have basic JavaScript knowledge. If you are new to Node, you can download it here: https://nodejs.org/en/download/.

Here’s just one of many tutorials available online for getting started with Node: http://www.tutorialspoint.com/nodejs/. At the very least go through the first several sections to get up and running and semi-comfortable, because the rest of this tutorial will assume that you know basic Node concepts and NPM commands.

In this tutorial, we’ll be using several Node modules, the most important being:

- **Express.js** – This is a small yet powerful Node web application server framework designed for a variety of web applications. It helps you organize your web application into an MVC architecture on the server side. Given you already have Node installed, just follow these steps to install Express: http://expressjs.com/en/starter/installing.html. If you’re new to Express, I highly suggest doing their tutorial to get a feel for what it’s like: http://expressjs.com/en/starter/hello-world.html .
- **Socket.io** – This is a library for real-time web applications, enabling bidirectional event-based communication. There are lots of tutorials on the internet on how to use Socket.io, but let’s use this one from their official site: http://socket.io/get-started/chat/ . We’ll be building off from this tutorial, so be sure to do it!

*IMPORTANT NOTE*: As of the time of this writing, I noticed a few bugs in Socket.io’s official tutorial that arose from recent library updates. Please refer to their GitHub repo for the corrected code: https://github.com/rauchg/chat-example. 

- **Redis** – This is an in-memory data structure store, used as a database, cache, and message broker. You can find a short introduction here: http://redis.io/topics/introduction . For this tutorial, we’ll be using Redis’ pub/sub (Publisher/Subscribe) feature. Pub/sub is a messaging pattern where there are multiple publishers sending messages to multiple subscribers listening for messages on specific channels.

Before you get started with the rest of this tutorial, make sure you have Redis installed. The Redis project doesn’t officially support Windows, although the Microsoft Open Tech group develops and maintains this Windows port targeting Win64. You can download the Windows executable here: https://github.com/rgl/redis/downloads.

## Getting Started

At this point, you should now have a basic understanding of Node, Express, Socket.io, and Redis; and have a very simple chat app from Socket.io’s official website. 

Here's an architecture diagram of what we have so far:

![The beginnings of a beautiful thing](http://i.imgur.com/YkUuST4.png)

Great! What could possibly go wrong?

The main problem right now is that when we run this app locally, we have one server (localhost:3000). However, when you roll out a service, you want more than just one instance of the server running.

Let’s see if we can horizontally scale our app a bit by adding another Socket.io server and using Redis to facilitate message passing between the two servers.

## Using Redis' Pub/Sub

First, let’s make a duplicate copy of our current project and name it something like NodeRedisServer2. In the NodeRedisLab2 project, go to app.js (or index.js if you cloned right from the Socket.io chat tutorial repo) and change the port to listen on 3001.

![Changing the port to listen on 3001](http://i.imgur.com/1Y35FTo.png)

At this point, our system has the following architecture:

![New and improved architecture... kind of](http://i.imgur.com/xR0sdF2.png)

Now we have two servers, but how do we make it so that Client A can send a message to everyone else, including Client D on a different server?

Here’s where Redis comes to the rescue!

A great way to solve this problem is by using Redis’ pub/sub feature to mediate message-passing between our Socket.io servers.
Luckily, there’s an NPM package just for using Redis with Node apps; it’s essentially a Socket.io Redis adapter.
For each version of our app, install the Redis adapter:

```sh
$ npm install socket.io-redis --save
```

Now we need to tell Node to use Redis. In app.js in both applications, modify the first few require statements to the following:

![Some modifications](http://i.imgur.com/lOgSBXT.png)

Now run both applications with the following command:

```sh
$ node app.js
```

Open up localhost:3000 and localhost:3001 in separate windows in your browser, and type in a couple of chat messages to yourself.

![Cool!](http://i.imgur.com/Vg5OTGI.png)

Here’s what the architecture looks like now Redis is in place:

![Final architecture](http://i.imgur.com/YkDsm6o.png)

Notice how Redis allows us to horizontally scale our application so we can have multiple servers publishing and subscribing to message such that users connected to different instances of the app can communicate with each other.

## Conclusion

Congratulations! You now have a simple Node chat app that utilizes multiple Socket.io servers and is horizontally scalable with the help of Redis! We’ve only scratched the surface of what’s possible with scalability with Node, but I hope this tutorial has been a good introduction to the design and implementation decisions that arise when taking your app to the next level.
