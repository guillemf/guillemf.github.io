---
layout: post
title: "iOS 7 and iOS 8 living together"
date: 2015-01-20 19:02:10 +0100
comments: true
categories: [Development, Tips'n'Tricks]
description: Responsiveness of a selector and NSFundationVersionNumber
---
This is the first post of 2015. I want to start it with a new category, Tips and Tricks. I've found that in my daily work I am fixing many different small pieces of code that, in some cases, will be interesting for you, so I decided to share it in my blog.

The firs one is how to include code that behaves differently when it's running on iOS 7 than when it's running on iOS 8, for example. I've found two main different ways to do it.
<!-- more -->

## Check the responsiveness to a selector.

Most of the times, the problem comes because of a change in the signature of an API or the deprecation of the methods. In this case the easiest way is to check if the object responds to the given selector. Two examples are NSOperation and UIApplication.

{% highlight objc %}

    NSOperation *myOperation = [[NSOperation alloc] init];

    if ([myOperation respondsToSelector:@selector(setName)]) {
        // iOS 8 specific code
    } else {
        // iOS 7 speficic code

    }

{% endhighlight  %}

{% highlight objc %}

    UIApplication *app = [UIApplication sharedApplication];

    if ([app respondsToSelector:@selector(registerForRemoteNotifications)]) {
        // iOS 8 specific code
    } else {
        // iOS 7 speficic code

    }

{% endhighlight  %}

## Check the value of _NSFundationVersionNumber_

The second option is using _NSFundationVersionNumber_. This option gives you the flexibility to behave differently not because of any restriction, but because functional requirements or just because you like it.

{% highlight objc %}

    if(floor(NSFoundationVersionNumber) <= NSFoundationVersionNumber_iOS_7_0){
        // iOS 7.0 specific code
    }else if(floor(NSFoundationVersionNumber) <= NSFoundationVersionNumber_iOS_7_1) {
        // iOS 7.1 specific code
    } else {
        // iOS 8.X specific code
    }

{% endhighlight  %}


You can use the defined constants:

* NSFoundationVersionNumber_iPhoneOS_2_0
* NSFoundationVersionNumber_iPhoneOS_2_1
* NSFoundationVersionNumber_iPhoneOS_2_2
* NSFoundationVersionNumber_iPhoneOS_3_0
* NSFoundationVersionNumber_iPhoneOS_3_1
* NSFoundationVersionNumber_iPhoneOS_3_2
* NSFoundationVersionNumber_iOS_4_0
* NSFoundationVersionNumber_iOS_4_1
* NSFoundationVersionNumber_iOS_4_2
* NSFoundationVersionNumber_iOS_4_3
* NSFoundationVersionNumber_iOS_5_0
* NSFoundationVersionNumber_iOS_5_1
* NSFoundationVersionNumber_iOS_6_0
* NSFoundationVersionNumber_iOS_6_1
* NSFoundationVersionNumber_iOS_7_0
* NSFoundationVersionNumber_iOS_7_1

Also OS X has its own ones.
