---
layout: post
title: "BDD - Mastermind : TDD in the model"
date: 2014-09-29 17:06:51 +0200
comments: true
categories: [Development, BDD, TDD]
---

This is the third post to create an application using BDD and TDD in iOS. If you missed the previous post, my recommendation is that you read it first [here](/blog/2014/08/20/bdd-from-the-idea-to-the-app-2/ "BDD: From the Idea to the App 2").

It's time to get your hands dirty. To make it really easy I've decided to tag each relevant commit with step numbers so you can follow step by step the process. I recommend you to code your own version, but if at some point you are lost, just checkout the step where you got lost and we'll be synchronized again.

First thing to do is create your project and have it ready to run. You can create a new project or checkout [this commit](https://github.com/guillemf/Mastermind-BDD/tree/Step_1 "MasterMind-BDD Step1") or just checkout from terminal by :

{% highlight sh %}
git clone git@github.com:guillemf/Mastermind-BDD.git 
git tag -l
git checkout tags/Step_1.
{% endhighlight  %}

<!-- more -->
As you might now the process of TDD is basically writing a test, seeing how it fails, implement the solution and run again the test until it passes. I'm not going to go deep in what is TDD as there are many webs that explain very well how to do TDD like [Quality Coding](http://qualitycoding.org) specially the screencast series starting with [Intro to Objectve-C TDD](http://qualitycoding.org/objective-c-tdd/).

So, assuming you know what is TDD and how to do it, let's start our process.

What I do is take each requirement and try to dissect it in all the possible unitary tests I can do covering all possibilities. So let's start on the first one.

* Every time we start a new game, the model has to create a new secret combination.

The first test to write is to be sure that right from the beginning there is something in combination. I write my first test and, following the method, I get my first error.

![First Failing Test](https://farm3.staticflickr.com/2941/15217066667_a2f04c500c_o.png "Test 1 Fail")

So I need to fix that situation, first by implementing MMModel class.

With this implementation

{% highlight objc %}
@interface MMModel : NSObject

@property (nonatomic, strong) NSString *combination;

@end

@implementation MMModel


- (id)init{
    
    self = [super init];
    
    if (self) {
        self.combination = @"";
    }
    
    return self;
}

@end
{% endhighlight  %}

The tests passes, things seem to go in the right way. Let's face the second test. As it says every time we **start** a new game, a **new combination**, the next test seems obvious that it has to be something like:


{% highlight objc %}

- (void)testModelOnStartShouldGenerateANewCombination {
    
    MMModel *model = [[MMModel alloc] init];
    
    NSString *originalCombination = model.combination;
    
    [model start];
    
    XCTAssertNotEqual(originalCombination, model.combination, @"After start the combination has to be different from the previous one");
}
{% endhighlight  %}

But that only covers one iteration, and unit tests has to be _independent_ on the contexts, that mean a random number of times, so let's introduce a random number to repeat starts.

{% highlight objc %}
- (void)testModelOnStartShouldGenerateANewCombination {
    
    MMModel *model = [[MMModel alloc] init];
    
    int lowerBound = 1;
    int upperBound = 5;
    
    int rndValue = lowerBound + arc4random() % (upperBound - lowerBound);
    
    for (int n= 1; n<=rndValue; n++) {
        
        NSString *originalCombination = model.combination;
        [model start];
        XCTAssertNotEqual(originalCombination, model.combination, @"After start the combination has to be different from the previous one");

    }
}
{% endhighlight  %}

And this implementation

{% highlight objc %}
- (void)start {
    self.combination = [NSString stringWithFormat:@"%d", arc4random()];
}
{% endhighlight  %}

Makes the test pass. Yes, you might think "_Hey, that's a trick. This is not a valid combination!!_" and you'd be right, but don't forget TDD says "**Write the minimum amount of code that makes the test pass**", and that's what we are doing. Later on we'll find that we need to change this implementation to pass other tests, but we'll be sure we are not breaking the rules with the following and more complex tests we need to pass.

It's time for some refactoring and we'll move the declaration of model to the setup method. As we need a new combination from the start, we'll also move a call to start in the model inside the creation. We run the tests by Cmd+U and see all still works.

Next requirement is :

* The secret combination is four numbers long.

So the tests are :

{% highlight objc %}
- (void)testModelSecretCombinationShouldContainFourNumbers
{
    XCTAssertEqual(model.combination.length, 4ul, @"Secrect combination length should be four");
}

- (void)testModelSecretCombinationIsNumeric
{
    NSCharacterSet* nonNumbers = [[NSCharacterSet decimalDigitCharacterSet] invertedSet];
    NSRange r = [model.combination rangeOfCharacterFromSet: nonNumbers];
    
    XCTAssertEqual(r.location, NSNotFound, @"All digits should be numeric");
}
{% endhighlight  %}

And here we have our first need of re-coding a part of the source to be able to pass those two tests. Try to do it by yourself and if you can't see how to do it, you can go and checkout [this commit](https://github.com/guillemf/Mastermind-BDD/tree/Step_2 "MasterMind-BDD Step2").

With the next requirement, things start becoming complicated and won't be so easy in terms of coding.

* The numbers in the combination are between 1 and 4.

Let's write the test to fail.

{% highlight objc %}
- (void)testModelSecretCombinationAllDigitsAreBetween1and4
{
    NSCharacterSet* non1234 = [[NSCharacterSet characterSetWithCharactersInString:@"1234"] invertedSet];
    NSRange r = [model.combination rangeOfCharacterFromSet: non1234];
    
    XCTAssertEqual(r.location, NSNotFound, @"All digits should be between 1 and 4");

}
{% endhighlight  %}

Again all the challenge is inside _start_ method in _MMModel.m_. It is a good practice to try to implement it by yourself. Once you are done, checkout [Step 3](https://github.com/guillemf/Mastermind-BDD/tree/Step_3 "MasterMind-BDD Step3") and you can compare your solution to mine and see how beautiful it is to see there are many ways to find a solution for the same problem. And remember to write **the less possible code** to pass the tests.

To not make this post eternal, I will just comment next requirement:

* The numbers in the combination can be repeated.

In this case we can't write a test, at least in an easy way, about how to allow something. If opposite to this requirement it would be "_The numbers in the combination can be repeated_" it would be easy to traverse the digits and ensure there is no repetition, but as long as this is not a restriction, I won't write a test for it. As usual, you can have a different opinion and write a test to verify it, I encourage you to do it and paste a comment explaining why you took that decision. 

You'll find the full code for this post in [Step 4](https://github.com/guillemf/Mastermind-BDD/tree/Step_4 "MasterMind-BDD Step4").

In the next post [BDD - Mastermind : Preparation to make the UI Testable](/blog/2014/10/21/bdd-mastermind-view-preparation-to-make-the-ui-testable/ "BDD - Mastermind : Preparation to make the UI Testable") we'll start preparing the views to implement it with BDD.

If you like it, please share! And leave me a comment if you have some time, I'd love to know this is helping someone ;)

