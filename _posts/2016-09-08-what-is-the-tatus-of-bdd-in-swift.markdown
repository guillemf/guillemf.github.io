---
layout: post
title: "What Is the Status of BDD in Swift"
date: 2016.09.18 18:43:09 +0200
comments: false
categories: Opinion
---

I am again working on a project that has a heavy load on BDD.

In this case, we had a greenfield project in front of us, as nothing done regarding automation was usable here. My advice was to opt for a Calabash-based solution as I believe it’s a great, ‘standardized’ and long-term solution.

Calabash is a cucumber-based solution that works pretty well with iOS. Of course, you find some problems; the main one is that it is an intrusive solution based on including an HTTP server. That server allows the communication between the cucumber Gem and the iOS app to access the UI elements and perform different actions. That brings the problem of being dependent on the system.

With Swift, calabash still works, but with the new UI Testing framework a new solution appears.

Up to now I didn’t dive into the tool's nitty-gritty; to do it, there is no better way than creating an app.

It will be a simple app that allows you to make voice records on your iPhone. I could call it YAMVR, but I will probably go for something more glamorous.

So stay tuned and send any comments you have if you want me to check something unique or add any feature to the app.

Thanks for reading me, and don’t forget to follow me on Twitter and check my repos in GitHub.