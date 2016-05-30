![Pretty Banner](https://github.com/KrakenDev/PrediKit/wiki/Sources/Banner.jpg)
[![Travis Build Status](http://img.shields.io/travis/KrakenDev/PrediKit.svg?style=flat-square)](https://travis-ci.org/KrakenDev/PrediKit)
[![Coveralls](https://img.shields.io/coveralls/KrakenDev/PrediKit/master.svg?style=flat-square)](https://coveralls.io/github/KrakenDev/PrediKit?branch=master)
[![Supported Platforms](https://img.shields.io/cocoapods/p/PrediKit.svg)]()
[![Swift Version Compatibility](https://img.shields.io/badge/swift2-compatible-4BC51D.svg?style=flat-square)](https://developer.apple.com/swift)
[![Cocoapods Version](https://img.shields.io/badge/pod-3.0-blue.svg?style=flat-square)](https://cocoapods.org/pods/PrediKit)
[![Carthage Compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat-square)](https://github.com/Carthage/Carthage)
[![LICENSE](http://img.shields.io/badge/license-MIT-blue.svg?style=flat-square)](https://raw.githubusercontent.com/KrakenDev/PrediKit/master/LICENSE)

#PrediKit
A Swift `NSPredicate` DSL for iOS &amp; OS X inspired by [SnapKit](https://github.com/SnapKit/SnapKit), lovingly written in Swift, and created by that weird dude at [KrakenDev](https://krakendev.io).

If you're familiar with the intuitive feel of the [SnapKit](https://github.com/SnapKit/SnapKit) API, then you'll feel right at home with PrediKit! 💖

#Documentation
Documentation is generated by [Jazzy](https://github.com/realm/jazzy) and can be found [here for your convenience](http://krakendev.github.io/PrediKit)!

#Why create PrediKit?
Because I wanted to! Also, because `NSPredicate` creation is hard. When working with `CoreData` you use `NSPredicates` to fetch persisted objects. `CoreData` is hard enough, so why have the additional complexity of using a complex string-based predicate system?

The language you need to use to create predicate formats are completely string-based which can lead to a host of issues:
* We have a tendency to misspell words and with the current system, you can build and run without ever knowing you misspelled a property name...until it's too late.
* For many an iOS/OSX developer (myself included), they may be very unfamiliar with the `SQL`-like language that comes with the creation of predicate formats. In fact, an entire [cheatsheet](https://realm.io/news/nspredicate-cheatsheet/) by the awesome guys at [Realm](https://realm.io/) was created because of this!
* With complex predicate creation, it's easy to get string-blindness. Go ahead...try creating a complex predicate and reading it after a couple of hours. I dare you. 😎
* If you misspell a property key name in a predicate format string but the string is parseable by the `NSPredicate` system, then nothing happens. It just fails silently. At least, I think it does. I'm currently writing this on 2 hours of sleep. Don't quote me on that.

#What does it fix?
Well, hopefully it fixes all of the above and more. Currently, it:
* Gives the developer a closure based way to create NSPredicates.
* It also, through the magic of Xcode, gives you a way to autocomplete your queries. No more referencing a cheatsheet. Just hit the dot button and enjoy the autocomplete.
* I also carefully constructed the API to read as much like a book as possible. Matching strings even have a redundant API just to be grammatically correct when specifying if a string `matches` or `doesNot.match` another value.
* Through a little runtime-magic/reflection, PrediKit will crash at runtime if you misspell a property key or supply a property key that does not exist in a specific class' property list.
* All predicate `builder` closures do not need capture semantics as each closure is a `@noescape` closure. [Read here if you don't know what that means](http://krakendev.io/blog/hipster-swift#noescape) 🤓.

#Installation
PrediKit can be included in your project through any of these methods:

## CocoaPods
[CocoaPods](http://cocoapods.org) is a dependency manager for Cocoa projects. You can install it with the following command:

```bash
$ gem install cocoapods
```

> CocoaPods 0.39.0+ is required to build PrediKit

To integrate PrediKit through CocoaPods, make sure the `use_frameworks!` line is included in your Podfile (PrediKit is written in Swift so it needs to be brought in as a framework). Make sure these lines are somewhere in your `Podfile`:

```ruby
use_frameworks!
pod 'PrediKit'
```

Then, run the following command:

```bash
$ pod install
```

Afterwards, whenever you need PrediKit, add this line to the top of the file it's being used in:

```swift
import PrediKit
```

## Carthage
[Carthage](https://github.com/Carthage/Carthage) is a decentralized dependency manager that builds your dependencies and provides you with binary frameworks.

You can install Carthage with [Homebrew](http://brew.sh/) using the following command:

```bash
$ brew update
$ brew install carthage
```

To integrate PrediKit into your Xcode project using Carthage, specify it in your `Cartfile`:

```ogdl
github "KrakenDev/PrediKit"
```

Run `carthage update` to build the framework and drag the built `PrediKit.framework` into your Xcode project.

Afterwards, whenever you need PrediKit, add this line to the top of the file it's being used in:

```swift
import PrediKit
```

##Manually

If you prefer not to use either of the aforementioned dependency managers, you can integrate PrediKit into your project manually.

First, copy and paste these commands into Terminal:
```bash
git clone https://github.com/KrakenDev/PrediKit.git
open PrediKit/Sources/
```

This should open a Finder window with the important files needed for PrediKit located in the Sources folder of the repo. Drag these folders into your project (preferable in a folder named "PrediKit") and code away! Since you would be copying these files into your project directly, there is no need for the `import PrediKit` line in any of the files that you need it.

The downside to this is that you can not update PrediKit easily. You would need to repeat these steps each time you wanna grab the latest and greatest! 😱

#Usage
PrediKit tries to make `NSPredicate` creation easy. **Heavily** inspired by [SnapKit's](https://github.com/SnapKit/SnapKit) API, the API for PrediKit is extremely similar for people who love it as much as I do. Check it out. This example creates a predicate used to fetch a `ManagedObject` from `CoreData`:

```swift
let predicate = NSPredicate(ManagedLegend.self) { includeIf in
    includeIf.string("title").equals("The Almighty Kraken")
}
```

To check if a property is nil:

```swift
let predicate = NSPredicate(ManagedLegend.self) { includeIf in
    includeIf.string("title").equalsNil
}
```

PrediKit can also query member properties. Say you have a class structure like this:

```swift
class Captain: NSObject {
    var name: String
}
class Ship: NSObject {
    var captain: Captain
}
```

And you want to create these predicates:

```swift
let someCaptain = Captain()
NSPredicate(format: "captain == %@ && captain.name == 'Chief Supreme'", someCaptain)
```

Creating the above with PrediKit is easy and expressive:

```swift
let someCaptain = Captain()
let predicate = NSPredicate(Ship.self) { includeIf
    let includeIfShipCaptain = includeIf.member("captain", ofType: Captain.self)
    includeIfShipCaptain.equals(someCaptain) &&
    includeIfShipCaptain.string("name").equals("Chief Supreme")
}
```

PrediKit also overloads the `&&`, `||`, and `!` operators. This allows you compound and specify whether or `not` to include your `includers` (Crappy name, I know. Feel free to give me suggestions).

```swift
let predicate = NSPredicate(ManagedLegend.self) { includeIf in
    //Include any ManagedLegend instance if the property named "string" is NOT nil and does NOT equal "The Almighty Kraken"
    !includeIf.string("title").equalsNil &&
    !includeIf.string("title").equals("The Almighty Kraken") &&
    
    //Also include any ManagedLegend instance if the date property named "birthdate" is in the past or if the bool property "isAwesome" is true.
    includeIf.date("birthdate").isEarlierThan(NSDate()) ||
    includeIf.bool("isAwesome").isTrue
}
```

You can even create `includers` conditionally!

```swift
let predicate = NSPredicate(ManagedLegend.self) { includeIf in
    let isKrakenQuery = includeIf.string("title").equals("The Almighty Kraken")
    let birthdateQuery = includeIf.date("birthdate").isEarlierThan(NSDate())
    let isAwesomeQuery = includeIf.bool("isAwesome").isTrue
    
    if shouldCheckBirthdate {
        (isKrakenQuery && birthdateQuery) || isAwesomeQuery
    } else {
        isKrakenQuery || isAwesomeQuery
    }
}
```
I don't know about y'all, but the `SQL`-like `IN` operator was hard to wrap my head around. PrediKit makes this a little more human-readable:

```swift
let awesomePeeps = ["Kraken", "Cthulhu", "Voldemort", "Ember", "Umber", "Voldemort"]
let predicate = NSPredicate(ManagedLegend.self) { includeIf in
    includeIf.string("title").matchesAnyValueIn(awesomePeeps)
}
```

PrediKit also has built-in support for subquery predicates:

```swift
let predicate = NSPredicate(ManagedLegend.self) { includeIf in
    includeIf.string("title").equals("The Almighty Kraken") &&
    
    //Only include Krakens that have more than three hungry cerberus friends
    includeIf.collection("cerberusFriends").subquery(ManagedCerberus.self) {
        $0.bool("isHungry").isTrue
        return .IncludeIfMatched(.Amount(.IsGreaterThan(3)))
    }
}
```

#Selector Extension Pattern
Personally, I love using a variation of the [Selector Extension Pattern](https://medium.com/swift-programming/swift-selector-syntax-sugar-81c8a8b10df3#.bypt7blba) when using PrediKit. It allows you to avoid misspelling your property names when using the API. It also allows you to rename your selector properties at will. By renaming, every instance of that selector used by PrediKit should give you a compiler error so you don't miss a beat and can feel safe knowing you haven't missed any property names in a name change refactor. By creating a Selector extension like so:

```swift
import Foundation

extension Selector {
    private enum Names: String {
        case title
        case birthdate
        case age
        case friends
        case isAwesome
        case isHungry
    }

    private init(_ name: Names) {
        self.init(name.rawValue)
    }

    static let title = Selector(.title)
    static let birthdate = Selector(.birthdate)
    static let age = Selector(.age)
    static let friends = Selector(.friends)
    static let isAwesome = Selector(.isAwesome)
    static let isHungry = Selector(.isHungry)
}
```

PrediKit becomes a lot more expressive now:

```swift
//BEFORE
let predicate = NSPredicate(ManagedLegend.self) { includeIf in
    includeIf.string("title").equals("The Almighty Kraken")
}
//AFTER
let predicate = NSPredicate(ManagedLegend.self) { includeIf in
    includeIf.string(.title).equals("The Almighty Kraken")
}
```

#LICENSE
PrediKit is licensed under the `MIT` license. Check out the `LICENSE` file to learn more.
