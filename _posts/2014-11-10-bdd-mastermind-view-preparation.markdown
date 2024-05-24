---
layout: post
title: "BDD - Mastermind : View preparation"
date: 2014-11-10 13:25:00 +0100
comments: true
categories: [Development, BDD, TDD]
---

This is the fifth post to create an application using BDD and TDD in iOS. If you missed the previous post, my recommendation is that you read it first [here](/blog/2014/10/21/bdd-mastermind-view-preparation-to-make-the-ui-testable/ "BDD - Mastermind : View preparation to make the UI testable").

In the previous post I went through a way to test the visible result of the custom view in a very intuitive way. It was just an exercise to test how, in a simple way, we could have a complete test of how we expect our view looks like. The truth is that this is a feeble method as it depends on an image that might or might not be 100% achievable by the tools we can make use of.

The purpose of the previous post was to introduce the technique we are going to use in this post. We are going to go through a technique that allows us to test the final behavior in a more secure and maintainable way.
 
You can find the code corresponding to this post in [this commit](https://github.com/guillemf/Mastermind-BDD/tree/Step_6 "MasterMind-BDD Step6"). Go and get it!
<!-- more -->
# The result cells

{% img left https://farm3.staticflickr.com/2941/15101651020_46fb32d967_o.jpg 190 490 'Mastermind Sketch With zones' 'Mastermind board Sketch with zones' %}

The second element we have on the screen is the results cell. If you remember from the first post of the series, we defined three areas to consider. Area number three is a special kind of view that provides different visual aspects depending on the result we are providing. The rules are described in the second post of the series [BDD - Mastermind : Internal design](/blog/2014/09/25/bdd-mastermind-internal-design/ "BDD - Mastermind : Internal design"), but we need add a few more "conditions" to have a really robust view that does what we want it to do.

**First** - We are only accepting two different symbols, no matter which symbols but just two different.<br>
**Second** - If the first symbol is a space, then it is considered there are no symbols of the first type and all the symbols are considered second kind.<br>
**Third** - As there is no specification, the circles will try to fill as much space as possible. One symbol will end in a single big circle.

With this in mind we have enough information to start creating the tests to implement the result cell.

# Test and implementation #

A very common practice is to create helper methods to simplify the test and provide more clarity. In this case we need to create a combination generator, so I implement this random combination generators.

{% highlight objc %}

// Helper Methods
- (NSString *)generateRandomCharString
{
    // Available symbols
    NSString *symbols = @"abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    
    // Pick a random index to extract two symbols
    int symbolIndex = arc4random_uniform((int)symbols.length-1);

    // Obtain the two random symbols
    NSString *symbol = [symbols substringWithRange:NSMakeRange(symbolIndex, 1)];
    
    // Obtain random number of each symbols
    int length = arc4random_uniform(10);
    
    NSMutableString *resultString = [NSMutableString stringWithString:@""];
    
    for (int n=0; n<length; n++)
        [resultString appendString:symbol];
    
    return [resultString copy];
    
}

- (NSString *)generateRandomResult
{
    NSMutableString *resultString = [[self generateRandomCharString] mutableCopy];
    return [[resultString stringByAppendingString:[self generateRandomCharString]] copy];
}

- (NSData *)dataForCellPNGRepresentationForView:(UIView *)view
{
    UIGraphicsBeginImageContext(view.bounds.size);
    [view.layer renderInContext:UIGraphicsGetCurrentContext()];
    UIImage* image1 = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();
    return UIImagePNGRepresentation(image1);
}

{% endhighlight  %}

+ **generateRandomCharString** - Is used to generate a string of random lenght of a random symbol. As you can see I previously have defined the array of valid symbols which contain no space. A good practice would be to add a test to check that any symbol placed is included in the set of valid symbols.
+ **generateRandomResult** - Is the method used to create a combination of two symbols.
+ **dataForCellPNGRepresentationForView** - Is the method used to obtain a png representation of the view. That is the way we can compare two views at visual level.

Now that we have all we need, let's write some tests. I won't go though each test as they are trivial, just list them.

- Test that on init the background is transparent.
- Test that on init the combination value is empty.
- Test that when the combination changes bezier path changes.
- Test that when the combination is empty bezier paths contains zero circles.
- Test that when the combination changes bezier paths contains the same number of circles as the length of the combination.
- Test that when the combination has more than two different symbols only first two are stored.
- Test that when the combination changes bezier paths contains only bezier paths;

# The final method for testing the visual result #

As I commented before, testing against images has some important limitations like depending on the device implementation, small changes makes the test fail, etc ... So, which is the best way to do it? Well nothing better than comparing a view with another view. That way we can be sure there is no difference and we can update easily the implementation.

To do it, you just need to create a view that looks like what you expect your view to look like. 

You might think, that is duplicating the effort. 

Well, create a dummy view that just does one thing is not duplicating the effort, is prototyping what you expect to obtain and also lets you define some rules that you might translate to your view.

Let's take a look at the dummy view I've implemented and you'll understand better what I mean.

{% highlight objc %}

// Dummy view to draw the image we expect

@interface DummyView : UIView

@property (nonatomic, strong) NSString *combination;
@property (nonatomic, strong) UIColor *color;

@end

@implementation DummyView

- (id)initWithFrame:(CGRect)frame combination:(NSString *)combination color:(UIColor *)color
{
    self = [super initWithFrame:frame];
    
    if (self) {
        self.combination = combination;
        self.color = color;
        self.backgroundColor = [UIColor clearColor];
    }
    
    return self;
}

- (void)drawRect:(CGRect)rect {
    // Obtain first char
    unichar leftchar = [self.combination characterAtIndex:0];
    NSString *combination = [self.combination stringByReplacingOccurrencesOfString:@" " withString:@""];
    // Obtain rectangle ration
    float K = rect.size.height/rect.size.width;
    // Obtain number of items
    long N = combination.length;
    
    // Obtain grid sizes
    float verItems = ceil(sqrtf(K*N));
    float horItems = ceil(sqrtf(N/K));
    
    // Items horizontally
    float gridHeigh = rect.size.height / verItems;
    float gridWidth = rect.size.width / horItems;
    
    // Draw circles
    [self.color setFill];
    [self.color setStroke];
    
    UIBezierPath *bezPath;
    int currentElement;
    
    for (int y = 0; y<verItems; y++) {
        for (int x = 0; x<horItems; x++) {
            currentElement = (horItems*y)+x ;
            if (currentElement >= N) {
                break;
            }
            bezPath = [UIBezierPath bezierPathWithOvalInRect:CGRectMake(x*gridWidth, y*gridHeigh, gridWidth, gridHeigh)];
            if ([combination characterAtIndex:currentElement] == leftchar) {
                [bezPath fill];
            } else {
                [bezPath stroke];
            }
        }
    }
}


@end

{% endhighlight  %}

This is the final implementation of the view, but usually it will grow with your development. Adding new features that you expect to find in your view. And more important, will protect you from changing what you expect to see if you make any change in your code in the future.

With that in mind, the tests become a lot more clear.

{% highlight objc %}

- (void)testIfNewResultHasOnlyOneSymbolByDefaultAreFirstSymbol
{
    resultCell.result = [self generateRandomCharString];
    resultCell.frame = CGRectMake(0, 0, arc4random_uniform(300), arc4random_uniform(300));
    
    DummyView *dummyV = [[DummyView alloc] initWithFrame:resultCell.frame
                                             combination:resultCell.result
                                                   color:resultCell.color];
    
    NSData *expectedImage = [self dataForCellPNGRepresentationForView:dummyV];
    
    NSData *obtainedImage = [self dataForCellPNGRepresentationForView:resultCell];
    
    
    XCTAssertEqualObjects(expectedImage, obtainedImage);
}

{% endhighlight  %}

1. Create our view.
2. Create a dummy view with the same parameters.
3. Extract the representing images.
4. Check both match.

Take a look at the rest of the test, I think they are pretty clear, but if you believe there is something you can't see, just send me an email or leave me a comment.

And with this post I finish with the TDD by now and we can start with the BDD.

Please, leave me comments, share and like if you can! See you in the next post.
