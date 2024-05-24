---
layout: post
title: "BDD - Mastermind : Adding calabash and creating our first tests"
date: 2014-11-20 20:06:16 +0100
comments: true
categories: [Development, BDD, TDD]
---

This is the sixth post to create an application using BDD and TDD in iOS. If you missed the previous post, my recommendation is that you read it first [here](/blog/2014/11/10/bdd-mastermind-view-preparation/ "BDD - Mastermind : View preparation"). If you just landed, better start on the [first](/blog/2014/08/20/bdd-from-the-idea-to-the-app-2/ "BDD: From the Idea to the App 2")

In this post is where we really start doing BDD, so get ready to enter to the new and exciting world of cucumber and behaviour driven development.

<!-- more -->
# Cucumber, What is it and How to use #

In previous post I've introduced the concept of cucumber, and in this post we are going to start with it. But first I'd like to give you a brief introduction about cucumber and what is it.

Cucumber is an set of tools that allows us to write tests in "_natural language_" that are readable by the computer and tell it what to test and how to test. Notice that I've written "_natural language_" between quotes and in italics. That is because we should make a couple precissions here. First, the language is natural because it can be read as a natural language written story, but is not so natural in the sense that we can't write it as it comes, but we have to follow some conventions that describe the gherkin syntax. Second, the test, as said before, are not going to work out of the box. Eventhough there are some steps that work with no additional effort, most of the steps will need a definition from your side.

You could say - "_what's the point in writting the tests if we have to do another development job behind?_" 

Well, the advantage you get from this is the ability to have clearer tests that can be mantained with lot less effort and that can be written, read and maintained by third party and non technical people.

# Back to school, let's learn some syntax #

A good definition of what is gerkhin is in [The Cucumber Book: Behaviour-Driven Development for Testes and Developers](https://pragprog.com/book/hwcuc/the-cucumber-book "The Pragmatic Bookshelf"), an excellent book that I highly recomend you to read if you want to be a "_Cucumber Master_".

> "_Gherkin gives us a lightweight structure for documenting examples of the behavior our stakeholders want, in a way that it can be easily understood both by the stakeholders and by Cucumber._"

Let's see an example:

{% highlight gherkin %}

Feature: Board behaviour
  As a Player
  I want to see how the cells react to my taps as expected
  So I can begin playing

Scenario: Initial situation
  Given I am on the Initial Screen
  Then a new game should be ready

{% endhighlight  %}

You can see this peace of code is written in a way anyone could understand, well, anyone who speaks english. What about a non english speaker stake holder? Well, gherkin syntax can be written in 40 different languages. You can see a list of available languages by running cucumber gem as '_cucumber --i18n help_' and you'll be listed all available languages. At this moment, this is what my system shows.

{% highlight vctreestatus %}
      | ar        | Arabic              | العربية           |
      | bg        | Bulgarian           | български         |
      | bm        | Malay               | Bahasa Melayu     |
      | ca        | Catalan             | català            |
      | cs        | Czech               | Česky             |
      | cy-GB     | Welsh               | Cymraeg           |
      | da        | Danish              | dansk             |
      | de        | German              | Deutsch           |
      | el        | Greek               | Ελληνικά          |
      | en        | English             | English           |
      | en-Scouse | Scouse              | Scouse            |
      | en-au     | Australian          | Australian        |
      | en-lol    | LOLCAT              | LOLCAT            |
      | en-old    | Old English         | Englisc           |
      | en-pirate | Pirate              | Pirate            |
      | en-tx     | Texan               | Texan             |
      | eo        | Esperanto           | Esperanto         |
      | es        | Spanish             | español           |
      | et        | Estonian            | eesti keel        |
      | fa        | Persian             | فارسی             |
      | fi        | Finnish             | suomi             |
      | fr        | French              | français          |
      | gl        | Galician            | galego            |
      | he        | Hebrew              | עברית             |
      | hi        | Hindi               | हिंदी               |
      | hr        | Croatian            | hrvatski          |
      | hu        | Hungarian           | magyar            |
      | id        | Indonesian          | Bahasa Indonesia  |
      | is        | Icelandic           | Íslenska          |
      | it        | Italian             | italiano          |
      | ja        | Japanese            | 日本語             |
      | kn        | Kannada             | ಕನ್ನಡ              |
      | ko        | Korean              | 한국어             |
      | lt        | Lithuanian          | lietuvių kalba    |
      | lu        | Luxemburgish        | Lëtzebuergesch    |
      | lv        | Latvian             | latviešu          |
      | nl        | Dutch               | Nederlands        |
      | no        | Norwegian           | norsk             |
      | pa        | Panjabi             | ਪੰਜਾਬੀ              |
      | pl        | Polish              | polski            |
      | pt        | Portuguese          | português         |
      | ro        | Romanian            | română            |
      | ru        | Russian             | русский           |
      | sk        | Slovak              | Slovensky         |
      | sr-Cyrl   | Serbian             | Српски            |
      | sr-Latn   | Serbian (Latin)     | Srpski (Latinica) |
      | sv        | Swedish             | Svenska           |
      | th        | Thai                | ไทย               |
      | tl        | Telugu              | తెలుగు             |
      | tr        | Turkish             | Türkçe            |
      | tt        | Tatar               | Татарча           |
      | uk        | Ukrainian           | Українська        |
      | uz        | Uzbek               | Узбекча           |
      | vi        | Vietnamese          | Tiếng Việt        |
      | zh-CN     | Chinese simplified  | 简体中文           |
      | zh-TW     | Chinese traditional | 繁體中文           |
	  
{% endhighlight  %}

So we could write the same peace of code we wrote before as.

{% highlight gherkin %}

Característica: Comportamiento del Tablero
  Como jugador
  Quiero saber como reaccionan las celdas a mis pulsaciones tal como esper
  y así poder empezar a jugar

Escenario: Situación inicial
  Dado que estoy en la pantalla inicial
  Entonces un nuevo juego debería estar listo

{% endhighlight  %}

And cucumber should be able to proceed the same way as when it was written in english. Of course the steps definitions won't be called the same or will do any translation, but we can map them.

Gherkin is basically a set of keywords organised in a certain way and followed by sentences that might or might not contain parameters and have to be defined as steps.

The keywords used by gherkin are:

* First level
	+ Feature
	+ Background
	+ Scenario
* Second level
	+ Given
	+ When
	+ Then
	+ And
	+ But
	+ *
	+ Scenario Outline
	+ Examples

First level keywords are used to separate different tests and group them, while the second level ones are to define each step or provide data to test on. I won't go deeper on this as it is not the purpose of this port serie. As I commented before, if you really want to get more information go and buy the book [The Cucumber Book: Behaviour-Driven Development for Testes and Developers](https://pragprog.com/book/hwcuc/the-cucumber-book "The Pragmatic Bookshelf"), it's at the high level Pragmatic Programmers always offer.

# Talk is cheap. Show me the code #

It all looks very nice, but, does it work? And how does that work?

Well, let's start by writting our first feature.

If you remember a previous post in this series called [BDD - Mastermind : Internal Design](/blog/2014/09/25/bdd-mastermind-internal-design/ "Previous post reference"), I defined there the acceptance criterias the way a stakeholder would define them ( more or less, that's my humble contribution ). Let's bring it back to write a couple of them in gherkin.

+ The screen should contain nine rows and five columns.
+ The left most column has to be gray.
+ The other five columns has to be white.
+ When I first open the app, a new game should be ready.
+ When a new game starts, the screen should contain no colors and no results.

As you can imagine there are many ways to write those acceptance criterias in gherkin. The one I choosed is the following one.

{% highlight gherkin %}

Scenario: Initial situation
  Given I am on the Initial Screen
  Then a new game should be ready

{% endhighlight  %}

As you might deduct, the step "_Given I am on the Initial Screen_" includes 

+ The screen should contain nine rows and five columns.
+ The left most column has to be gray.
+ The other five columns has to be white.

There are, at least, two ways we can do it. Creating one step for each condition and then putting them all together in a single step called "Given I am on the Initial Screen" or creating all steps into the "Given I am on the Initial Screen".

I decided to go this second way, but it would be a good practice if you try to do it the other way and see how it works the same way or even better.

So let's dive into the definition of the steps.

First of all, go to your project folder, be sure you are in the same folder as your .xcproject and then run 

{% highlight vctreestatus %}

$ gem install calabash-cucumber

$ calabash-ios setup
Checking if Xcode is running...

{% endhighlight  %}

When it is finished, open the new created .xcworkspace file and build the new target you have ending in -cal . You need to do it because cucumber needs a binary to run the tests on, otherwise you are going to start having complains from cucumber.

In the project folder you'll see a new folder called features, that's where all cucumber magic lives.

When you open the .feature file and replace the default feature for your new feature and you run cucumber, this is what you are going to see:

{% highlight vctreestatus %}

╰─$ DEVICE_TARGET='iPhone 6 (8.1 Simulator)' cucumber
Feature: Board behaviour
  As a Player
  I want to see how the cells react to my taps as expected
  So I can begin playing

  Scenario: Initial situation        # features/start.feature:6
    Given I am on the Initial Screen # features/start.feature:7
    Then a new game should be ready  # features/start.feature:8

1 scenario (1 undefined)
2 steps (2 undefined)
0m15.800s

You can implement step definitions for undefined steps with these snippets:

Given(/^I am on the Initial Screen$/) do
  pending # express the regexp above with the code you wish you had
end

Then(/^a new game should be ready$/) do
  pending # express the regexp above with the code you wish you had
end

{% endhighlight  %}

> Note that the command used to run the test '_DEVICE_TARGET='iPhone 6 (8.1 Simulator)' cucumber_'. Please take a look at [calabash documentation](https://github.com/calabash/calabash-ios/wiki "Documentation fo Calabash") for more options.

You can see how cucumber helps us.

> You can implement step definitions for undefined steps with these snippets:
>
> Given(/^I am on the Initial Screen$/) do
>   pending # express the regexp above with the code you wish you had
> end
> 
> Then(/^a new game should be ready$/) do
>   pending # express the regexp above with the code you wish you had
> end

Those are the steps you need to implement inside your features/step_definitions/mastermind_steps.rb file. Let's take a look on how I've defined them.

{% highlight ruby %}

Given /^I am on the Initial Screen$/ do
  # The screen should contain nine rows and five columns
  rows = query("view {accessibilityLabel BEGINSWITH 'Combination Row'}")
  fail("The screen should contain nine rows") if rows.count != 9
  
  # Following the mock, those rows have to be as wide as the screen
  window = query("window")
  rows.each do |row|
    if row["frame"]["width"] != window.first["frame"]["width"]
       fail("All rows have to be as wide as the screen") 
    end
  end
  # Following the mock, the rows have distributed along the screen  
  lastFloor = 0
  rows.each do |row|
    if row["frame"]["y"] != lastFloor
       fail("Each row has to be after the previous one, expecting #{lastFloor}, #{row["frame"]["y"]} found") 
    end
    fail("All rows should be higher than zero") if row["frame"]["height"] == 0
    lastFloor = row["frame"]["y"] + row["frame"]["height"]
  end
  
  # All rows are combination
  rows.each do |row|
    fail("All rows must be combination rows but #{row["class"]} was found") if row["class"] != "MMCombinationRow"
  end
  
  sleep(STEP_PAUSE)
end

Then(/^a new game should be ready$/) do
  rows = query("view {accessibilityLabel BEGINSWITH 'Combination Row'}")

  rows.each do |row|
    
    combination = row["label"][18..23]
    fail("All rows combinations must be empty but #{combination} was found") if combination != "'    '"
  end

end

{% endhighlight  %}

To match one buy one the acceptance criterias with the steps let's split it.

+ The screen should contain nine rows and five columns.

{% highlight ruby %}
  # The screen should contain nine rows and five columns
  rows = query("view {accessibilityLabel BEGINSWITH 'Combination Row'}")
  fail("The screen should contain nine rows") if rows.count != 9
{% endhighlight  %}

Previoulsy to this step, you can find a new class in the code called _MMCombinationRow_ that has been created with TDD and ensures containing five cells, one result cell and four color cells. So what's missing here is the number of rows.

Another way to implement this check is:

{% highlight ruby %}
  # The screen should contain nine rows and five columns
  rows = query("MMCombinationRow")
  fail("The screen should contain nine rows") if rows.count != 9
{% endhighlight  %}

Go and implement the rest and see how it fails and what have you got to change to make it pass and then go and run the unit tests to ensure you didn't break anything.

If all goes fine, just making a _git checkout_ of [Step 7](https://github.com/guillemf/Mastermind-BDD/tree/Step_7 "MasterMind-BDD Step7") and you should see something like this:

{% youtube RYihMe_5yws %}

Remember you can find the code of this post in [this commit, Step 8](https://github.com/guillemf/Mastermind-BDD/tree/Step_8 "MasterMind-BDD Step5").

If you like it, please share! Thank you for reading me.
