---
layout: post
title: "MFMailComposeViewController Nightmare"
date: 2015-01-23 10:30:03 +0100
comments: true
description: MFMailComposeViewController and Appearance Proxy problems
categories: [Development, Tips'n'Tricks]
---
I've just gone through MFMailComposeViewController because I had a problem with the look&feel of the Cancel and Save buttons on the mail compose dialog toolbar. They just looked too, how to say, transparent. The problem was that in the app I am working on I use appearance proxy. That seemed not to work with MFMailComposeViewController.
<!-- more -->

My first approach was to change those behavior that didn't fit in my needs. And the result was ... nothing. It's like that doesn't apply to it if you are using a global setting. You can find many solutions thought the internet telling you to user setTintColor on the MFMailComposeViewController object you've just created, and they will work if you don't use the appearance proxy at some point of your app initialization like:

{% highlight objc %}

    [[UIBarButtonItem appearanceWhenContainedIn:[UINavigationBar class], nil] setTitleTextAttributes:buttonAttributes forState:UIControlStateNormal];

    [[UINavigationBar appearance] setTintColor:[UIColor redColor]];
	
{% endhighlight  %}

If you did it, forget about using:

{% highlight objc %}

	[mailViewController.navigationBar setTintColor:[UIColor redColor]];

	[self presentViewController:mailViewController animated:YES completion:nil]; 
	
{% endhighlight  %}

The result is going to be the same nothing I had.

But the good new is that there is a solution. I know it's not the most elegant one but it works and you can save a lot of time investigating that I already invested. The solution is to change the global setting in the appearance proxy to match what you need and set it back to the original behavior when the dialog is dismissed.

As you know there is not going to be any dialog with navigation bar while the mail composer is present, it is safe to change it. To revert the changes, you need to do it in _- (void)mailComposeController:(MFMailComposeViewController*)controller didFinishWithResult:(MFMailComposeResult)result error:(NSError*)error_. I use a singleton to contain the configuration colors I need to restore the original look&feel, if you don't have it, you need to implement a mechanism to do it or you'll have a coupled and dependent code.

Thanks for reading and don't forget to share and follow me in twitter @guillemfg.