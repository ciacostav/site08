# site08
site08v2
NYTimes Objective-C Style Guide

This style guide outlines the coding conventions of the iOS teams at The New York Times. We welcome your feedback in issues and pull requests. Also, we’re hiring.

Thanks to all of our contributors.
Introduction

Here are some of the documents from Apple that informed the style guide. If something isn’t mentioned here, it’s probably covered in great detail in one of these:

    The Objective-C Programming Language
    Cocoa Fundamentals Guide
    Coding Guidelines for Cocoa
    iOS App Programming Guide

This style guide conforms to IETF's RFC 2119. In particular, code which goes against the RECOMMENDED/SHOULD style is allowed, but should be carefully considered.
Table of Contents

    Dot Notation Syntax
    Spacing
    Conditionals
        Ternary Operator
    Error handling
    Methods
    Variables
    Naming
        Categories
    Comments
    Init & Dealloc
    Literals
    CGRect Functions
    Constants
    Enumerated Types
    Bitmasks
    Private Properties
    Image Naming
    Booleans
    Singletons
    Imports
    Protocols
    Xcode Project

Dot Notation Syntax

Dot notation is RECOMMENDED over bracket notation for getting and setting properties.

For example:

view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;

Not:

[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;

Spacing

    Indentation MUST use 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode.
    Method braces and other braces (if/else/switch/while etc.) MUST open on the same line as the statement. Braces MUST close on a new line.

For example:

if (user.isHappy) {
    // Do something
}
else {
    // Do something else
}

    There SHOULD be exactly one blank line between methods to aid in visual clarity and organization.
    Whitespace within methods MAY separate functionality, though this inclination often indicates an opportunity to split the method into several, smaller methods. In methods with long or verbose names, a single line of whitespace MAY be used to provide visual separation before the method’s body.
    @synthesize and @dynamic MUST each be declared on new lines in the implementation.

Conditionals

Conditional bodies MUST use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent errors. These errors include adding a second line and expecting it to be part of the if-statement. Another, even more dangerous defect can happen where the line “inside” the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

For example:

if (!error) {
    return success;
}

Not:

if (!error)
    return success;

or

if (!error) return success;

Ternary Operator

The intent of the ternary operator, ? , is to increase clarity or code neatness. The ternary SHOULD only evaluate a single condition per expression. Evaluating multiple conditions is usually more understandable as an if statement or refactored into named variables.

For example:

result = a > b ? x : y;

Not:

result = a > b ? x = c > d ? c : d : y;

Error Handling

When methods return an error parameter by reference, code MUST switch on the returned value and MUST NOT switch on the error variable.

For example:

NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}

Not:

NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).
Methods

In method signatures, there SHOULD be a space after the scope (- or + symbol). There SHOULD be a space between the method segments.

For example:

- (void)setExampleText:(NSString *)text image:(UIImage *)image;

Variables

Variables SHOULD be named descriptively, with the variable’s name clearly communicating what the variable is and pertinent information a programmer needs to use that value properly.

For example:

    NSString *title: It is reasonable to assume a “title” is a string.
    NSString *titleHTML: This indicates a title that may contain HTML which needs parsing for display. “HTML” is needed for a programmer to use this variable effectively.
    NSAttributedString *titleAttributedString: A title, already formatted for display. AttributedString hints that this value is not just a vanilla title, and adding it could be a reasonable choice depending on context.
    NSDate *now: No further clarification is needed.
    NSDate *lastModifiedDate: Simply lastModified can be ambiguous; depending on context, one could reasonably assume it is one of a few different types.
    NSURL *URL vs. NSString *URLString: In situations when a value can reasonably be represented by different classes, it is often useful to disambiguate in the variable’s name.
    NSString *releaseDateString: Another example where a value could be represented by another class, and the name can help disambiguate.

Single letter variable names are NOT RECOMMENDED, except as simple counter variables in loops.

Asterisks indicating a type is a pointer MUST be “attached to” the variable name. For example, NSString *text not NSString* text or NSString * text, except in the case of constants (NSString * const NYTConstantString).

Property definitions SHOULD be used in place of naked instance variables whenever possible. Direct instance variable access SHOULD be avoided except in initializer methods (init, initWithCoder:, etc…), dealloc methods and within custom setters and getters. For more information, see Apple’s docs on using accessor methods in initializer methods and dealloc.

For example:

@interface NYTSection: NSObject

@property (nonatomic) NSString *headline;

@end

Not:

@interface NYTSection : NSObject {
    NSString *headline;
}

Variable Qualifiers

When it comes to the variable qualifiers introduced with ARC, the qualifier (__strong, __weak, __unsafe_unretained, __autoreleasing) SHOULD be placed between the asterisks and the variable name, e.g., NSString * __weak text.
Naming

Apple naming conventions SHOULD be adhered to wherever possible, especially those related to memory management rules (NARC).

Long, descriptive method and variable names are good.

For example:

UIButton *settingsButton;

Not

UIButton *setBut;

A three letter prefix (e.g., NYT) MUST be used for class names and constants, however MAY be omitted for Core Data entity names. Constants MUST be camel-case with all words capitalized and prefixed by the related class name for clarity. A two letter prefix (e.g., NS) is reserved for use by Apple.

For example:

static const NSTimeInterval NYTArticleViewControllerNavigationFadeAnimationDuration = 0.3;

Not:

static const NSTimeInterval fadetime = 1.7;

Properties and local variables MUST be camel-case with the leading word being lowercase.

Instance variables MUST be camel-case with the leading word being lowercase, and MUST be prefixed with an underscore. This is consistent with instance variables synthesized automatically by LLVM. If LLVM can synthesize the variable automatically, then let it.

For example:

@synthesize descriptiveVariableName = _descriptiveVariableName;

Not:

id varnm;

Categories

Categories are RECOMMENDED to concisely segment functionality and should be named to describe that functionality.

For example:

@interface UIViewController (NYTMediaPlaying)
@interface NSString (NSStringEncodingDetection)

Not:

@interface NYTAdvertisement (private)
@interface NSString (NYTAdditions)

Methods and properties added in categories MUST be named with an app- or organization-specific prefix. This avoids unintentionally overriding an existing method, and it reduces the chance of two categories from different libraries adding a method of the same name. (The Objective-C runtime doesn’t specify which method will be called in the latter case, which can lead to unintended effects.)

For example:

@interface NSArray (NYTAccessors)
- (id)nyt_objectOrNilAtIndex:(NSUInteger)index;
@end

Not:

@interface NSArray (NYTAccessors)
- (id)objectOrNilAtIndex:(NSUInteger)index;
@end

Comments

When they are needed, comments SHOULD be used to explain why a particular piece of code does something. Any comments that are used MUST be kept up-to-date or deleted.

Block comments are NOT RECOMMENDED, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.
init and dealloc

dealloc methods SHOULD be placed at the top of the implementation, directly after the @synthesize and @dynamic statements. init methods SHOULD be placed directly below the dealloc methods of any class.

init methods should be structured like this:

- (instancetype)init {
    self = [super init]; // or call the designated initializer
    if (self) {
        // Custom initialization
    }

    return self;
}

Literals

NSString, NSDictionary, NSArray, and NSNumber literals SHOULD be used whenever creating immutable instances of those objects. Pay special care that nil values not be passed into NSArray and NSDictionary literals, as this will cause a crash.

For example:

NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;

Not:

NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];

CGRect Functions

When accessing the x, y, width, or height of a CGRect, code MUST use the CGGeometry functions instead of direct struct member access. From Apple's CGGeometry reference:

    All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

For example:

CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);

Not:

CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;

Constants

Constants are RECOMMENDED over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants MUST be declared as static constants. Constants MAY be declared as #define when explicitly being used as a macro.

For example:

static NSString * const NYTAboutViewControllerCompanyName = @"The New York Times Company";

static const CGFloat NYTImageThumbnailHeight = 50.0;

Not:

#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2

Enumerated Types

When using enums, the new fixed underlying type specification MUST be used; it provides stronger type checking and code completion. The SDK includes a macro to facilitate and encourage use of fixed underlying types: NS_ENUM().

Example:

typedef NS_ENUM(NSInteger, NYTAdRequestState) {
    NYTAdRequestStateInactive,
    NYTAdRequestStateLoading
};

Bitmasks

When working with bitmasks, the NS_OPTIONS macro MUST be used.

Example:

typedef NS_OPTIONS(NSUInteger, NYTAdCategory) {
    NYTAdCategoryAutos      = 1 << 0,
    NYTAdCategoryJobs       = 1 << 1,
    NYTAdCategoryRealState  = 1 << 2,
    NYTAdCategoryTechnology = 1 << 3
};

Private Properties

Private properties SHALL be declared in class extensions (anonymous categories) in the implementation file of a class.

For example:

@interface NYTAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end

Image Naming

Image names should be named consistently to preserve organization and developer sanity. Images SHOULD be named as one camel case string with a description of their purpose, followed by the un-prefixed name of the class or property they are customizing (if there is one), followed by a further description of color and/or placement, and finally their state.

For example:

    RefreshBarButtonItem / RefreshBarButtonItem@2x and RefreshBarButtonItemSelected / RefreshBarButtonItemSelected@2x
    ArticleNavigationBarWhite / ArticleNavigationBarWhite@2x and ArticleNavigationBarBlackSelected / ArticleNavigationBarBlackSelected@2x.

Images that are used for a similar purpose SHOULD be grouped in respective groups in an Images folder or Asset Catalog.
Booleans

Values MUST NOT be compared directly to YES, because YES is defined as 1, and a BOOL in Objective-C is a CHAR type that is 8 bits long (so a value of 11111110 will return NO if compared to YES).

For an object pointer:

if (!someObject) {
}

if (someObject == nil) {
}

For a BOOL value:

if (isAwesome)
if (!someNumber.boolValue)
if (someNumber.boolValue == NO)

Not:

if (isAwesome == YES) // Never do this.

If the name of a BOOL property is expressed as an adjective, the property’s name MAY omit the is prefix but should specify the conventional name for the getter.

For example:

@property (assign, getter=isEditable) BOOL editable;

Text and example taken from the Cocoa Naming Guidelines.
Singletons

Singleton objects SHOULD use a thread-safe pattern for creating their shared instance.

+ (instancetype)sharedInstance {
    static id sharedInstance = nil;

    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        sharedInstance = [[[self class] alloc] init];
    });

    return sharedInstance;
}

This will prevent possible and sometimes frequent crashes.
Imports

If there is more than one import statement, statements MUST be grouped together. Groups MAY be commented.

Note: For modules use the @import syntax.

// Frameworks
@import QuartzCore;

// Models
#import "NYTUser.h"

// Views
#import "NYTButton.h"
#import "NYTUserView.h"

Protocols

In a delegate or data source protocol, the first parameter to each method SHOULD be the object sending the message.

This helps disambiguate in cases when an object is the delegate for multiple similarly-typed objects, and it helps clarify intent to readers of a class implementing these delegate methods.

For example:

- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath;

Not:

- (void)didSelectTableRowAtIndexPath:(NSIndexPath *)indexPath;

Xcode project

The physical files SHOULD be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created SHOULD be reflected by folders in the filesystem. Code SHOULD be grouped not only by type, but also by feature for greater clarity.

Target Build Setting “Treat Warnings as Errors” SHOULD be enabled. Enable as many additional warnings as possible. If you need to ignore a specific warning, use Clang’s pragma feature.
Other Objective-C Style Guides

If ours doesn’t fit your tastes, have a look at some other style guides:

    Google
    GitHub
    Adium
    Sam Soffes
    CocoaDevCentral
    Luke Redpath
    Marcus Zarra
    Wikimedia
