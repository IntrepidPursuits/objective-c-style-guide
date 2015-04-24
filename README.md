# Intrepid Pursuits Objective-C Style Guide

This style guide outlines the coding conventions of the iOS team at Intrepid Pursuits. It started as a fork of the coding convections of the New York Times iOS team (https://github.com/NYTimes/objective-c-style-guide).

#### Making/Requesting Changes

We welcome your feedback. The current maintainers of this style guide are @cprime, @rosslebeau, and @andrewdolce. If you have an issue with any of the existing rules and would like to see something changes, please open an issue or submit a documented PR with the desired change.


## Introduction

Here are some of the documents from Apple that informed the style guide. If something isn't mentioned here, it's probably covered in great detail in one of these:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Table of Contents

* [Dot-Notation Syntax](#dot-notation-syntax)
* [Spacing](#spacing)
* [Conditionals](#conditionals)
  * [Ternary Operator](#ternary-operator)
* [Error handling](#error-handling)
* [Methods](#methods)
* [Variables](#variables)
* [Naming](#naming)
* [Comments](#comments)
* [Init & Dealloc](#init-and-dealloc)
* [Literals](#literals)
* [CGRect Functions](#cgrect-functions)
* [Constants](#constants)
* [Enumerated Types](#enumerated-types)
* [Bitmasks](#bitmasks)
* [Private Properties](#private-properties)
* [Image Naming](#image-naming)
* [Booleans](#booleans)
* [Singletons](#singletons)
* [Xcode Project](#xcode-project)

## Dot-Notation Syntax

Dot-notation should **always** be used for accessing and mutating properties. Bracket notation is preferred in all other instances.

**For example:**
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Not:**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

Dot-notation should **not** be used to call methods.

**For example:**
```objc
array.firstObject
```

**Not:**
```objc
[array firstObject]
```

For Cocoa objects, pay attention to the headers headers from the latest available SDK.

**For example(as of iOS 8):**
```objc
array.firstObject
array.count
````
**Not:**
```objc
[array firstObject]
[array count]
```


## Spacing

* Indent using 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.

**For example:**
```objc
if (user.isHappy) {
//Do something
} else if (user.isDead) {
//Do something else
} else {
//Do yet another thing
}
```
* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.
* `@synthesize` and `@dynamic` should each be declared on new lines in the implementation.

## Line Character limits

Lines should be limitted to 120 characters long. It is useful to turn on Xcode's character limit indicator as a guide for when to break lines down.


## Conditionals

Conditional bodies should always use braces even when a conditional body could be written without braces. These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line "inside" the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**For example:**
```objc
if (!error) {
    return success;
}
```

**Not:**
```objc
if (!error)
    return success;
```

or

```objc
if (!error) return success;
```

### Ternary Operator

The Ternary operator, ? , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an if statement, or refactored into instance variables.

**For example:**
```objc
result = a > b ? x : y;
```

**Not:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## prama mark -

Use pragma marks to organize and break up the various features of your class files. They should also be used to group methods for specific protocols:

**For example:**

```objc
#pragma mark - UITableViewDataSource methods

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    //do stuff
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    //do more stuff
}

#pragma mark - UITableViewDelegate methods

- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
    //do even more stuff
}
```

## Error handling

When methods return a success bool and uses an error parameter by reference, switch on the returned value, not the error variable.

**For example:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

**Not:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

## Methods

In method signatures, there should be a space after the scope (-/+ symbol). There should be a space between the method segments.

**For Example**:
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```

More general methods should call more specific ones with default parameters

```objc
- (void)runBlock:(IPExecutionBlock)executionBlock {
    [self runBlock:executionBlock timeout:5];
}
- (void)runBlock:(IPExecutionBlock)executionBlock timeout:(NSTimeInterval)timeout {
    ...
}
```

## Property Specifier Ordering

The ordering of the property specifiers should be:
1. strong / weak / retain / unsafe_unretained / assign / copy
2. atomicity
3. writablity

## Variables

Variables should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

Asterisks indicating pointers belong with the variable, e.g., `NSString *text` not `NSString* text` or `NSString * text`, except in the case of constants.

Property definitions should be used in place of naked instance variables whenever possible. Direct instance variable access should be avoided except in initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information on using Accessor Methods in Initializer Methods and dealloc, see [here](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

**For example:**

```objc
@interface NYTSection: NSObject

@property (nonatomic) NSString *headline;

@end
```

**Not:**

```objc
@interface NYTSection : NSObject {
    NSString *headline;
}
```

#### Variable Qualifiers

When it comes to the variable qualifiers [introduced with ARC](https://developer.apple.com/library/ios/releasenotes/objectivec/rn-transitioningtoarc/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW4), the qualifer (`__strong`, `__weak`, `__unsafe_unretained`, `__autoreleasing`) should be placed between the asterisks and the variable name, e.g., `NSString * __weak text`. 

## Naming

Apple naming conventions should be adhered to wherever possible.

Long, descriptive method and variable names are good.

**For example:**

```objc
UIButton *shareButton;
```

**Not**

```objc
UIButton *shrBttn;
```

####Class Prefixes

A two to three letter prefix (e.g. `RM` or `NYT`) should always be used for class names and constants. Constants should be camel-case with all words capitalized. Publicly accessible  constants should be prefixed by the related class name for clarity.

**For example:**

```objc
static const NSTimeInterval NYTArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**Not:**

```objc
static const NSTimeInterval fadetime = 1.7;
```

####Properties

Properties and local variables should be camel-case with the leading word being lowercase.

Instance variables should be camel-case with the leading word being lowercase, and should be prefixed with an underscore. This is consistent with instance variables synthesized automatically by LLVM. **If LLVM can synthesize the variable automatically, then let it.**

**For example:**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**Not:**

```objc
id varnm;
```

For rules on naming properties we use the standard Apple rules ([Accessor Naming](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-1004202-BCIGGFCC)), with one expection. We recommend against changing the default setter/getter method names in order to minimize property declaration/use complexity.

**For example:**
```objc
@property (assign, nonatomic) BOOL editable;
```

**Not:**
```objc
@property (assign, nonatomic, getter=isEditable) BOOL editable;
```

###Categories

When creating categories on classes not owned by you, category methods should be prefixed with your project class prefix in order to avoid method signature collisions with the original class or a another category on the same class (or even a superclass):

```objc
@interface UIColor (INTAdditions)
+ (UIColor *)int_navigationBarColor;
@end
```

[Customizing Existing Classes](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/CustomizingExistingClasses/CustomizingExistingClasses.html#//apple_ref/doc/uid/TP40011210-CH6-SW4)


## Comments

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted.

Code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.

## init

`init` methods should be structured like this:

```objc
- (instancetype)init {
    self = [super init]; // or call the designated initalizer
    if (self) {
        // Custom initialization
    }

    return self;
}
```

## Literals

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**For example:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**Not:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**For example:**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**Not:**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.

**For example:**

```objc
static NSString * const NYTAboutViewControllerCompanyName = @"The New York Times Company";

static const CGFloat NYTImageThumbnailHeight = 50.0;
```

**Not:**

```objc 
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2
```

## Enumerated Types

Enum values should be prefixed with the enum type name. Furthermore, when using `enum`'s, you should use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types — `NS_ENUM()`

**Example:**

```objc
typedef NS_ENUM(NSInteger, NYTAdRequestState) {
    NYTAdRequestStateInactive,
    NYTAdRequestStateLoading
};
```

## Bitmasks

When working with bitmasks, use the `NS_OPTIONS` macro.

**Example:**

```objc
typedef NS_OPTIONS(NSUInteger, NYTAdCategory) {
  NYTAdCategoryAutos      = 1 << 0,
  NYTAdCategoryJobs       = 1 << 1,
  NYTAdCategoryRealState  = 1 << 2,
  NYTAdCategoryTechnology = 1 << 3
};
```

## Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. Named categories (such as `NYTPrivate` or `private`) should never be used unless extending another class.

**For example:**

```objc
@interface NYTAdvertisement ()

@property (strong, nonatomic) GADBannerView *googleAdView;
@property (strong, nonatomic) ADBannerView *iAdView;
@property (strong, nonatomic) UIWebView *adXWebView;

@end
```

## Image Naming

####Raw image naming conventions

Raw image filenames should:
* should not contain whitespace characters
* use the proper scale factors (eg, @2x @3x)
* have a the general form of location_what_adjectives

`homescreen_arrow_left_selected.png`
`homescreen_arrow_left_unselected.png`

####Images.xcassets

Image assets should be contained in the Images.xcassets file. Image names within the asset catelog should be named consistently to preserve organization and developer sanity. They should be named as one camel case string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

**For example:**

* `RefreshBarButtonItem` and `RefreshBarButtonItemSelected`
* `ArticleNavigationBarWhite` and `ArticleNavigationBarBlackSelected`

####[Prefix]ImageNames file

A constant should be declared for every image in the image asset categlog, with the same name. A script can be used to generate this file for a particular project so that the constants file is ensured to be match the asset catelog. (Ross is going to write this).


## Booleans

Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. This allows for more consistency across files and greater visual clarity. (__requires additional research__)

**For example:**

```objc
if (!someObject) {
}
```

**Not:**

```objc
if (someObject == nil) {
}
```
-----

Never implicily cast a non-BOOL expression to a BOOL because a BOOL is a char and if the thing that you are returning is larget than 8 bits, it can truncate to an unexpected value.

**For example:**

```objc
- (BOOL)hasObjects {
   return array.count > 0;
}
```

**Not:**

```objc
- (BOOL)hasObjects {
   return array.count;
}
```

-----

**For a `BOOL`, here are two examples:**

```objc
if (isAwesome)
if (![someObject boolValue])
```

**Not:**

```objc
if (isAwesome == YES) // Never do this.
if ([someObject boolValue] == NO)
```


## Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.
```objc
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```

This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## Importing .h files

You should prefer forward declaration for over importing .h files in your .h files to avoid circuluar reference dependency.

## Xcode project

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped not only by type, but also by feature for greater clarity. Files and groups should also be listed in alphabetical order.

## Project Sepcific Styles

Custom colors and fonts for the project should be defined in specific category files, particularly when used in multiple locations.

### UIColor+[Prefix]Colors file

```objc
+ (UIColor *)int_navigationBarBlueColor;
+ (UIColor *)int_submitButtonColor;
```

### UIFont+[Prefix]Fonts file

Custom fonts for the project sh

```objc
+ (UIFont *)int_navigationHeaderFontWithSize:(CGFloat)size;
+ (UIFont *)int_navigationHeaderFont;
```


