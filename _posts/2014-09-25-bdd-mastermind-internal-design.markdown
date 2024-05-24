---
layout: post
title: "BDD - Mastermind : Internal design"
date: 2014-09-25 19:14:18 +0200
comments: true
categories: [Development, BDD]
---

This is the second post to create an application using BDD and TDD in iOS. If you missed the first post, my recommendation is that you read it first [here](/blog/2014/08/20/bdd-from-the-idea-to-the-app-2/ "BDD: From the Idea to the App 2").

In this post we are going to walk through the process of designing how we are going to architecture it internally and start writing down the first specifications that later on will become tests. I decided to structure it the way a "non technical" person would describe what she wants and then move it to the world of Unit Testing and Acceptance Tests.
<!-- more -->
## First TDD

{% img right https://farm4.staticflickr.com/3896/15171494279_9dfcea90f6_n.jpg 320 297 MVC #1 %}
Let's begin by revisiting the most important design pattern used in iOS Development, _Model - View - Controller_. The internal design we are going to implement follows the classic schema. The first decision to make here is where to put the logic. I've seen many designs that include the logic in the controller and leave the model just as a mere repository. My understanding is that the controller should act as the "where to take this" and "where to put that", not as the container of "what happens when y put this" or "what should I obtain when I deliver this".

So, here is the first decision, the logic of the game will live in the model. Let's write some requirements the model has to follow.

+ Every time we start a new game, the model has to create a new secret combination.
+ The secret combination is four numbers long.
+ The numbers in the combination are between 1 and 4.
+ The numbers in the combination can be repeated.
+ Every time we start a new game, the number of attempts has to be zero.
+ The maximum number of attempts is nine.
+ Only combinations of four numbers are accepted.
+ The combinations can contain duplicated numbers.
+ The numbers accepted in the combinations are between 1 and 4.
+ Every time a new combination is inserted a new result will be generated.
+ The result of a new combination inserted is a one letter "A" for each number that is in the secret combination in the right position and a letter "B" for each number that is in the combination but is not in the right position.
+ If the result is "AAAA" no new combination will be accepted.
+ If the number of attempts is nine and the last result is not "AAAA" no new combination will be accepted.
+ Its has to be possible to obtain the history of the game being played at any moment, providing the ordered sequence of pairs combination-result.

These are the requirements to follow. As you can see I've simplified the mastermind rules in order to make this exercise more understandable. We are not trying to create the final app but to understand what are we doing.

## Now BDD

Now that we have the model specified, let's go for the behavior we want for the app. These requirements are written in natural language and then we'll rewrite later on in [gherkin sintax](https://github.com/cucumber/cucumber/wiki/Gherkin "Gherkin Syntaxt Github"), which is the syntax used to describe the behavior that cucumber understands. As you will see, it is a very understandable syntax. Try to show the final result of the scenarios to a non technical person and see how easy she can understand and validate them, that's the great advantage I find in this, it's a bridge between devs and PO that both can understand and agree to a behavior with no interpretations and confusions.

Those could be the requirements. Note that we have to go to the very basic behavior to be sure nothing is left to no specification. If something is not explicitly defined, that means than nothing happens in that case.

+ The screen should contain nine rows and five columns.
+ The left most column has to be gray.
+ The other five columns has to be white.
+ When I first open the app, a new game should be ready.
+ When a new game starts, the screen should contain no colors and no results.

Up to this point we have initial conditions. Let's define the behavior to play.

+ When I tap on a white cell a red circle should appear.
+ When I tap on a red circle cell, a yellow circle should appear.
+ When I tap on a yellow circle, a green circle should appear.
+ When I tap on a green circle, a blue circle should appear.
+ When I tap on a blue circle, a red circle should appear.
+ When I swipe my finger from left to right the combination should be validated.
+ When a combination has been validated, no tap on that row will react.
+ When a row has no validated combination above on tap on its cells should react.
+ When the number of validated combinations is zero the first row will behave as if there was a validated row above it.

The third group of specifications will be the behavior of the results and the reset of the game.

+ If a combination is not validated the left most cell of the row will show no content.
+ When a cell is validated the left most cell of the row will show the result of the combination.
+ For each color of the combination that is in the right position the result cell will show a black circle.
+ For each color of the combination that is not in the right position the result cell will show a white circle.
+ If there is a result cell showing four black colors the board shouldn't react to any gesture except for the game reset.
+ If I swipe the board from left to right the current game should finish and a new game should start.

And this is all. We have created the specifications for all the rules and behaviors of the game. Of course we can define a lot more and be more exhaustive. But this is a good beginning.

In the next post [BDD - Mastermind : TDD in the Model](/blog/2014/09/29/bdd-mastermind-tdd-in-the-model/ "BDD - Mastermind : TDD in the Model") we'll start implementing using TDD for the Model.

If you like it, please share! And leave me a comment if you have some time, I'd love to know this is helping someone ;)


