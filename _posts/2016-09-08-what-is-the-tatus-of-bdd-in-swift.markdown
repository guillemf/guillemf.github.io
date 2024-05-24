---
layout: post
title: "What Is the Status of BDD in Swift"
date: 2016.09.18 18:43:09 +0200
comments: false
categories: Opinion
---

Again woking on a project with a heavy load on BDD.

In this case we had a greenfield project in front of us, as nothing done regarding automation was usable here. Obviously my advice was to opt for a Calabash based solution as I believe it’s a great, ‘standardised’ and long term solution.

Calabash is a cucumber based solution that works pretty well with iOS. Of corse you find some problems, the main one is that is an intrusive solution based on the inclusion of an http server. That server allows the communication between the cucumber Gem and the iOS app, to access to the UI elements and perform the different actions on it. That brings the problem of being dependent on the system.

With Swift calabash still works, but with the new UI Testing framework a new solution appears.

Up to now I didn’t dive into the nitty-gritties of the tool and to do it, there is no better way than creating an app.

It’s going to be a simple app that will only allow you to make some voice records on your iPhone, I could call it YAMVR, but probably I will go for something more glamourous.

So stay tunned and send any comment you have if you want me to check something special or add any feature to the app.

Thanks for reading me and don’t forget to follow me on twitter and check my repos in github.