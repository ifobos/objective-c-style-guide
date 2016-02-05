# Objective-C Style Guide

## Introduction

Here are some of the documents from Apple that informed the style guide. If something isn’t mentioned here, it’s probably covered in great detail in one of these:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Table of Contents

* [Code Organization](#code-organization)
* [Spacing](#spacing)
* [Naming](#naming)
* [Properties](#properties)
* [Methods](#methods-1)
* [Protocols](#protocols)
* [Variables](#variables)
* [Enum Types](#enumeration-types)
* [Bitmasks](#bitmasks)
* [Singletons](#singletons)
* [Comments](#comments)
* [Blocks](#blocks)
* [Class](#class)
* [Constants](#constants-1)
* [Basic Code Principles](#basic-code-principles)
* [CocoaPods](#cocoapods)
* [Xcode Project](#xcode-project)
* [Recommendations](#recommendations)
## Code Organization

* `init` and `dealloc` should always be placed at the top of the implementation
* Use `#pragma mark -` to categorize methods in functional groupings and protocol/delegate implementations following this general structure.

```objc
#pragma mark - Class Methods

+ (instancetype)sharedInstance {
    ...
}

#pragma mark - Lifecycle

- (instancetype)init {
    ...
}
- (void)dealloc {
    ...
}

// If this object conforms to NSCopying
#pragma mark - NSCopying

- (id)copyWithZone:(NSZone *)zone {
    ...
}

// If this overrides `NSObject`'s `description` method
#pragma mark - NSObject

- (NSString *)description {
    ...
}

// If this object is a UIViewController subclass
#pragma mark - View Lifecycle

- (void)viewDidLoad {
    ...
}

- (void)viewWillAppear:(BOOL)animated {
    ...
}

- (void)didReceiveMemoryWarning {
    ...
}

#pragma mark - Overriding Methods

- (void)setParentProperty:(id)value {
    ...
}

- (id)parentProperty {
    ...
}

// If this is a `UIView` subclass
#pragma mark - Views

- (UIView *)lazyLoadedView {
    ...
}

#pragma mark - Public

- (void)publicMethod {
    ...
}

#pragma mark - Private

- (void)privateMethod {
    ...
}

#pragma mark - Protocols conformance

#pragma mark - Pragma for each delegate
```

## Spacing

### Indentation

* **Indent using 4 spaces. Never indent with tabs.** Be sure to set this preference in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.
* One whitespace before the parenthesis, no space inside parenthesis

**Example:**

```objc
if (user.isHappy) {
    // Do something
}
else {
    // Do something else
}
```
**Not:**

```objc
if (user.isHappy)
{
    //Do something
}
else {
    //Do something else
}
```
### Line Break and Whitespace

* There should be exactly one blank line between methods to aid in visual clarity and organization.
* Whitespace within methods should be used to separate functionality (though often this can indicate an opportunity to split the method into several, smaller methods). 
* `@synthesize` and `@dynamic` should each be declared on new lines in the implementation.
* Separate imports from the rest of your file by 1 space. Optionally group imports if there are many (but try to have less dependencies). Generally strive to include frameworks first.

**Example**

``` obj-c
#import <AwesomeFramework/AwesomeFramework.h>
#import <AnotherFramework/AnotherFramework.h>

#import "SomeDependency.h"
#import "SomeOtherDependency.h"

@interface ABCMyClass
```
* Use one empty line between class extension and implementation in .m file.

**Example**

``` obj-c
@interface ABCMyClass ()

// Properties - empty line above and below

@end

@implementation ABCMyClass

// Body - empty line above and below

@end

```
* Always end a file with a newline.

**Example**

![](http://i.imgur.com/JBNo0SD.png)

* When using pragma marks leave 1 newline before and after.

**Example**

``` obj-c
- (CGSize)intrinsicContentSize {
    return CGSizeMake(12, 12);
}

#pragma mark - Private

- (void)setup {
    [self addGestureRecognizer:[[NSClickGestureRecognizer alloc] initWithTarget:self action:@selector(clicked:)]];
}
```

* When doing math use a single space between operators. Unless that operator is unary in which case don't use a space.

**Example**

```obj-c
NSInteger index = rand() % 50 + 25;
index++;
index += 1;
index--;
```

* When doing logic, a single space should follow the `if` and a single space should preceed the `{`

``` obj-c
if (alpha + beta <= 0) && (kappa + phi > 0) {
}
```
* Whitespace should in *all* cases be used to aid readability. Readability is highly subjective, so here are some rough guides:
  * Use new lines to delimit chunks of related code (approx 4-5 lines). If more than 4-5 lines are grouped, consider refactoring those lines into another method. 
    * By grouping related lines of code it naturally starts to show where the method can be refactored into smaller reusable units
  * One blank line is generally sufficient.
  * Avoid extraneous new lines between nested sets of parenthesis.
  * Avoid blank lines at the end of methods. (Consider delimiting the final return value with one though.)

**Example**

```objc
- (void)awakeFromNib {
    UIStoryboard *signatureStoryboard = [UIStoryboard storyboardWithName:@"ABCPopoverSignature" bundle:nil];
    self.signatureViewController = [signatureStoryboard instantiateViewControllerWithIdentifier:@"ABCPopoverSignature"];
    self.signatureViewController.modalPresentationStyle = UIModalPresentationPopover;
    self.signatureViewController.preferredContentSize = CGSizeMake(ABCPopoverSignatureWidth, ABCPopoverSignatureHeight);
    self.signatureViewController.signatureImageView = self;
    
    UITapGestureRecognizer *tapRecognizer = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(initiateSignatureCapture)];
    [self addGestureRecognizer:tapRecognizer];
}
```

## Naming

Apple naming conventions should be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Long, descriptive method and variable names are good.

**Example:**

```objc
UIButton *settingsButton;
```

**Not**

```objc
UIButton *setBut;
```

### Methods

Methods should not contain conjunction words, but tersely describe the parameters.

**Example:**

```objc
- (void)initWithUser:(User *)newUser firstname:(NSString *)firstname lastname:(NSString *)lastname;
```

**Not**

```objc
- (void)initWithUser:(User *)newUser forFirstname:(NSString *)firstname andLastname:(NSString *)lastname;
```


### Variables

Variables should be named descriptively, with the variable’s name clearly communicating what the variable _is_ and pertinent information a programmer needs to use that value properly.

**Example:**

* `NSString *title`: It is reasonable to assume a “title” is a string.
* `NSString *titleHTML`: This indicates a title that may contain HTML which needs parsing for display. _“HTML” is needed for a programmer to use this variable effectively._
* `NSAttributedString *titleAttributedString`: A title, already formatted for display. _`AttributedString` hints that this value is not just a vanilla title, and adding it could be a reasonable choice depending on context._
* `NSDate *now`: _No further clarification is needed._
* `NSDate *lastModifiedDate`: Simply `lastModified` can be ambiguous; depending on context, one could reasonably assume it is one of a few different types.
* `NSURL *URL` vs. `NSString *URLString`: In situations when a value can reasonably be represented by different classes, it is often useful to disambiguate in the variable’s name.
* `NSString *releaseDateString`: Another example where a value could be represented by another class, and the name can help disambiguate.

Single letter variable names should be avoided except as simple counter variables in loops.

Asterisks indicating a type is a pointer should be “attached to” the variable name. **For example,** `NSString *text` **not** `NSString* text` or `NSString * text`, except in the case of constants (`NSString * const NYTConstantString`).

Private properties should be used in place of instance variables whenever possible. Although using instance variables is a valid way of doing things, by agreeing to prefer properties our code will be more consistent.

**Example:**

```objc
@interface ABCTutorial : NSObject

@property (strong, nonatomic) NSString *tutorialName;

@end
```
**Not:**

```objc
@interface ABCTutorial : NSObject {
  NSString *tutorialName;
}
```

### Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.

**Example:**

```objc
static NSString * const ABCAboutViewControllerCompanyName = @"Kinetic Cafe Inc.";

static const CGFloat ABCImageThumbnailHeight = 50.0;
```

**Not:**

```objc
#define CompanyName @"Kinetic Cafe Inc."

#define thumbnailHeight 2
```

### Prefix

* A three letter prefix (e.g., `ABC`) should always be used for class names and constants. Constants should be camel-case with all words capitalized and prefixed by the related class name for clarity. A two letter prefix (e.g., `NS`) is [reserved for use by Apple](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/DefiningClasses/DefiningClasses.html#//apple_ref/doc/uid/TP40011210-CH3-SW12).
* Constant name should follow the format of `<filename>constantName`

**Example:**

```objc
static const NSTimeInterval ABCNavigationViewControllerNavigationFadeAnimationDuration = 0.3;
```

**Not:**

```objc
static const NSTimeInterval fadetime = 1.7;
```

* Properties and local variables should be camel-case with the leading word being lowercase.
* Instance variables should be camel-case with the leading word being lowercase, and should be prefixed with an underscore. This is consistent with instance variables synthesized automatically by LLVM. **If LLVM can synthesize the variable automatically, then let it.**

**Example:**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**Not:**

```objc
id varnm;
```

* Enum name should be a prefix of the variable names

**Example:**

```objc
typedef NS_ENUM(NSInteger, ABCStoryType) {
    ABCStoryTypeOld,
    ABCStoryTypeNew
};
```

### Categories

Categories may be used to concisely segment functionality and should be named to describe that functionality.

**Example:**

```objc
@interface UIViewController (ABCAutoHideNavBar)
@interface NSString (NSStringEncodingDetection)
```

**Not:**

```objc
@interface ABCModel (private)
@interface NSString (ABCRelative)
```

Methods and properties added in categories should be named with an app- or organization-specific prefix. This avoids unintentionally overriding an existing method, and it reduces the chance of two categories from different libraries adding a method of the same name. (The Objective-C runtime doesn’t specify which method will be called in the latter case, which can lead to unintended effects.)

**Example:**

```objc
@interface NSArray (ABCAccessors)
- (id)abc_objectOrNilAtIndex:(NSUInteger)index;
@end
```

**Not:**

```objc
@interface NSArray (ABCAccessors)
- (id)objectOrNilAtIndex:(NSUInteger)index;
@end
```

### Properties

- Don't use `id` is prefer `uid`, do not use any reserved words as property names on an object.
- Don't use `description` is prefer `about`, `description` is method of NSObject. All classes that inherit from NSObject inherit the method. It produces a textual description of the object for debugging purposes. 
- To name IBOutlets properties don't forget to sufix with same kind of class.

**Example:**

```objc
@interface ABCRepository ()

@property (nonatomic, strong) UIView *headerView;
@property (nonatomic, strong) UILabel *titleLabel;
@property (nonatomic, strong) UIWebView *mainWebView;

@end
```

**Not:**

```objc
@interface ABCRepository ()

@property (nonatomic, strong) UIView *header;
@property (nonatomic, strong) UILabel *title;
@property (nonatomic, strong) UIWebView *main;

@end
```

## Properties

Properties should be camel-case with the leading word being lowercase. Use auto-synthesis for properties rather than manual @synthesize statements unless you have good reason.

### Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class.

**Example:**

```objc
@interface ABCRepository ()

@property (nonatomic, strong) UIView *topView;
@property (nonatomic, strong) UIView *bannerView;
@property (nonatomic, strong) UIWebView *webView;

@end
```

### Property Attributes

Property attributes should be explicitly listed, and will help new programmers when reading the code.  The order of properties should be storage then atomicity, which is consistent with automatically generated code when connecting UI elements from Interface Builder.

**Example:**

```objc
@property (weak, nonatomic) IBOutlet UIView *containerView;
@property (strong, nonatomic) NSString *tutorialName;
```

**Not:**

```objc
@property (nonatomic, weak) IBOutlet UIView *containerView;
@property (nonatomic) NSString *tutorialName;
```

Properties with mutable counterparts (e.g. NSString) should prefer `copy` instead of `strong`. 
Why? Even if you declared a property as `NSString` somebody might pass in an instance of an `NSMutableString` and then change it without you noticing that.  

**Example:**

```objc
@property (copy, nonatomic) NSString *tutorialName;
```

**Not:**

```objc
@property (strong, nonatomic) NSString *tutorialName;
```

* Always declare memory-management semantics even on `readonly` properties.  List the management semantics first for consistency (and to match XCode default).

**Example:**

```objc
@property (assign, nonatomic) NSInteger statusCode;
```
    
**Not:**

```objc
@property (nonatomic) NSInteger statusCode;
```

### Dot Notation Syntax

Dot notation should **always** be used for accessing and mutating properties. Bracket notation is preferred in all other instances.

**Example:**

```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Not:**

```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

### Underscores

When using properties, instance variables should always be accessed and mutated using `self.`. This means that all properties will be visually distinct, as they will all be prefaced with `self.`. 

An exception to this: inside initializers, the backing instance variable (i.e. `_variableName`) should be used directly to avoid any potential side effects of the getters/setters.

Local variables should not contain underscores.

**Example:**

```objc
@interface SampleClass: NSObject

- (instancetype)initWithHeadline:(NSString *)headline;
@property (nonatomic) NSString *headline;

@end

@implementation SampleClass

- (instancetype)initWithHeadline:(NSString *)headline {
	self = [super init];
	if (self) {
		_headline = headline;
	}
	
	return self;
}

@end
```

**Not:**

```objc
@interface ABCSection : NSObject {
    NSString *headline;
}
```
### Lazy Instantiation

Use the lazy instantiation (or initialization) pattern in Objective-C with class members. You move the initialization code of the instance variable into the accessor method of the property. This a clean place to park your initialization code and the instance variable is only instantiated when it is first used. This can be especially valuable if there are several lines of code to initialize the instance variable.

**Example:**
```objc
#import "SampleClass.h"
 
@interface SampleClass()
 
@property (nonatomic) NSMutableArray *items;
 
@end
 
@implementation SampleClass
 
- (NSMutableArray *)items {
    if (!_items) {
        _items = [[NSMutableArray alloc] init];
        // add more lines of code for further initialization as needed
    }
 
    return _items;
}
 
- (void)addItem:(id)item {
    // self.items will be instantiated the first time addItem: is called
    [self.items addObject:item];
}
 
@end
```

## Methods

### Syntax

In method signatures, there should be a space after the method type (-/+ symbol). There should be a space between the method segments (matching Apple's style).  Always include a keyword and be descriptive with the word before the argument which describes the argument.

The usage of the word "and" is reserved.  It should not be used for multiple parameters as illustrated in the `initWithWidth:height:` example below.

**Example:**

```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
- (void)sendAction:(SEL)aSelector to:(id)anObject forAllCells:(BOOL)flag;
- (id)viewWithTag:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;
```

**Not:**

```objc
-(void)setT:(NSString *)text i:(UIImage *)image;
- (void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;
- (id)taggedView:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width andHeight:(CGFloat)height;
- (instancetype)initWith:(int)width and:(int)height;  // Never do this.
```

### Ternary Operator

The ternary operator, `?` , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an if statement, or refactored into named variables.

**Example:**

```objc
result = a > b ? x : y;
```

**Not:**

```objc
result = a > b ? x = c > d ? c : d : y;
```

### Golden Path

When coding with conditionals, the left hand margin of the code should be the "golden" or "happy" path.  That is, don't nest `if` statements.  Multiple return statements are OK.

**Example:**

```objc
- (void)someMethod {
  if (![someOther boolValue]) {
	return;
  }

  //Do something important
}
```

**Not:**

```objc
- (void)someMethod {
  if ([someOther boolValue]) {
    //Do something important
  }
}
```

### Conditionals

Conditional bodies should always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent [errors](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256). These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line “inside” the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**Example:**

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

### Error Handling

When methods return an error parameter by reference, switch on the returned value, not the error variable.

**Example:**

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


## Protocols

In a [delegate or data source protocol](https://developer.apple.com/library/ios/documentation/General/Conceptual/CocoaEncyclopedia/DelegatesandDataSources/DelegatesandDataSources.html), the first parameter to each method should be the object sending the message.

This helps disambiguate in cases when an object is the delegate for multiple similarly-typed objects, and it helps clarify intent to readers of a class implementing these delegate methods.

**Example:**

```objc
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath;
```

**Not:**

```objc
- (void)didSelectTableRowAtIndexPath:(NSIndexPath *)indexPath;
```


## Variables

### Literals

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**Example:**

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

### `CGRect` Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**Example:**

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

### Booleans

Objective-C uses `YES` and `NO`.  Therefore `true` and `false` should only be used for CoreFoundation, C or C++ code.  Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**Example:**

```objc
if (someObject) {}
if (![anotherObject boolValue]) {}
```

**Not:**

```objc
if (someObject == nil) {}
if ([anotherObject boolValue] == NO) {}
if (isAwesome == YES) {} // Never do this.
if (isAwesome == true) {} // Never do this.
```

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

```objc
@property (assign, getter=isEditable) BOOL editable;
```

## Enumeration Types

When using `enum`s, use the new fixed underlying type specification, which provides stronger type checking and code completion. The SDK includes a macro to facilitate and encourage use of fixed underlying types: `NS_ENUM()`.

**Example:**

```objc
typedef NS_ENUM(NSInteger, ABCUserState) {
    ABCUserStateNotSignedIn,
    ABCUserStateSignedIn
};

//You can also make explicit value assignments (showing older k-style constant definition):

typedef NS_ENUM(NSInteger, ABCGlobalConstants) {
  ABCPinSizeMin = 1,
  ABCPinSizeMax = 5,
  ABCPinCountMin = 100,
  ABCPinCountMax = 500,
};
```
**Not:**

```objc
enum GlobalConstants {
  kMaxPinSize = 5,
  kMaxPinCount = 500,
};
```
## Bitmasks

When working with bitmasks, use the `NS_OPTIONS` macro.

**Example:**

```objc
typedef NS_OPTIONS(NSUInteger, ABCCategory) {
    ABCCategoryFootwears = 1 << 0,
    ABCCategoryHandbags = 1 << 1,
    ABCCategoryAccessories = 1 << 2,
    ABCCategoryRandom = 1 << 3
};
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

## Comments

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted.

Commenting your code is fantastic; However, try not to be overdone or redundant.

**Example:**

```objc

  // display state selection for US users
  Country *country = [Country current];
  if ([country.code isEqualToString:@"US"]) {
    [self showStatesForm];
  }
```

**Not:**

```objc

  // get the current country
  Country *country = [Country current];
  
  // if country code is US
  if ([country.code isEqualToString:@"US"]) {
  
    // display the form input for state
    [self showStatesForm];
  }
```

Block comments should generally be avoided, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.


### Documentation

Create documentation of the different classes in its .h files

**A general review on the class:**

```objc

/**
 ABCClass is a subclass of NSObject created to 
 exemplify the use of comments for documentation.
*/
@interface ABCClass: NSObject
```
**Explanation for each method:**

```objc
/**
 A really simple way to calculate the sum of two numbers.
 
 @param firstNumber An NSInteger to be used in the summation of two numbers
 @param secondNumber The second half of the equation.
 
 @return The sum of the two numbers passed in.
 */
+ (NSInteger)addNumber:(NSInteger)firstNumber toNumber:(NSInteger)secondNumber;

```
>Check out the [HeaderDoc Tags](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/HeaderDoc/tags/tags.html#//apple_ref/doc/uid/TP40001215-CH346-CHDJFEGF)

**Description of the property:**

```objc

/// uid contains the model identifier returned by the API
@property (nonatomic) NSInteger uid;
```

**Others:**

Other relevant elements such as enum, external const, etc.

```objc

/// Direction is an enum representing the different cardinal directions.
typedef enum {
  north,
  south,
  west,
  east
} Direction;
```

## Blocks

* Blocks should have a space between their return type and name.
* Block definitions should omit their return type when possible.
* Block definitions should omit their arguments if they are void.
* Parameters in block types should be named unless the block is initialized immediately.

```objc
void (^blockName1)(void) = ^{
    // do some things
};

id (^blockName2)(id) = ^ id (id args) {
    // do some things
};
```

## Class

### Constructor Methods

Where class constructor methods are used, these should always return type of 'instancetype' and never 'id'. This ensures the compiler correctly infers the result type. 

```objc
@interface ABCAirplane
+ (instancetype)airplaneWithType:(ABCAirplaneType)type;
@end
```

## Constants

- Avoid declaring constants inside ProjectName-Prefix.pch file.
- Avoid magic numbers with no meaning, preferring instead a named variable or constant (see following examples).

### Integers

**Example:**

```objc
if ([pin length] > ABCPinSizeMax)
```

**Not:**

```objc
if ([pin length] < 5)
```
What is this checking for?
We can see that this is checking if the pin is longer than the max allowed.

### Floats

In a (top level) .h you can define the float:

```objc
extern const float ABCFooFloat;
```

and then in the relevant .m file assign it a value:

```objc
const float ABCFooFloat = 18.0;
```

### Strings

In a (top level) .h you can define the string:

```objc
extern NSString * const ABCLocationsDatabaseName;
```

and then in the relevant .m file assign it a value:

```objc
NSString * const ABCLocationsDatabaseName = @"locations.db";
```

Note: the above is a constant pointer to an `NSString` object while the following is a pointer to a constant `NSString` object.

**Not:**

```objc
const NSString * ABCConstantString = @""; // pointer to constant
// which is equivalent to
NSString const * ABCConstantString = @"";
```

## Basic Code Principles

* Each function/method should aim to perform one action/task that reflects it's name.
* Since each function/method performs one action/task each one should be relatively short. If the code does not fit on one screen for example, you know you have a problem!
* Declare local variables as close to the code they are used in as possible.
* Always aim to reduce code nesting (ie a statement that is nested in an if, an if, a for and an if is hard for the brain to evaluate.  Refactor!).
* DRY stands for Don't Repeat Yourself. Also known as DIE: Duplication is Evil. Every piece of knowledge must have a single, unambiguous, authoritative representation within a system.



## Xcode project

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. **Any Xcode groups created should be reflected by folders in the filesystem.** Code should be grouped not only by type, but also by feature for greater clarity.
To sort the folders of an existing project, the use of [synx](https://github.com/venmo/synx) is suggested

When possible, always turn on “Treat Warnings as Errors” in the target’s Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang’s pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).

## Cocoapods

When you need a library, first look for it in cocoapods. if you can not find it, or if you find yourself not meet your needs or expectations, create your own pod, and share. To know more about it, take a look at the [CocoaPods Guide](https://guides.cocoapods.org)

## Recommendations

* Read about [SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)).
* Read about [MVVM](https://www.objc.io/issues/13-architecture/mvvm/).
* Use [Dash](https://itunes.apple.com/us/app/dash-3-api-docs-snippets./id449589707?mt=12) for your snippets.
* Use [BBUncrustifyPlugin-Xcode](https://github.com/benoitsan/BBUncrustifyPlugin-Xcode) to help format your code with [uncrustify.cfg](/uncrustify.cfg) configuration file.

# Other Objective-C Style Guides

If ours doesn’t fit your tastes, have a look at some other style guides:

* [NY Time](https://github.com/NYTimes/objective-c-style-guide)
* [RayWenderlich](https://github.com/raywenderlich/objective-c-style-guide)
* [RobotsAndPencils](https://github.com/RobotsAndPencils/objective-c-style-guide)
* [GitHub](https://github.com/github/objective-c-style-guide)
* [Robot & Pencil](https://github.com/RobotsAndPencils/objective-c-style-guide)
