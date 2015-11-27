# Erik

[![Join the chat at https://gitter.im/phimage/Erik](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/phimage/Erik?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat
            )](http://mit-license.org) [![Platform](http://img.shields.io/badge/platform-ios_osx-lightgrey.svg?style=flat
             )](https://developer.apple.com/resources/) [![Language](http://img.shields.io/badge/language-swift-orange.svg?style=flat
             )](https://developer.apple.com/swift) [![Issues](https://img.shields.io/github/issues/phimage/Erik.svg?style=flat
           )](https://github.com/phimage/Erik/issues) [![Cocoapod](http://img.shields.io/cocoapods/v/Erik.svg?style=flat)](http://cocoadocs.org/docsets/Erik/)


[<img align="left" src="logo.png" hspace="20">](#logo) Erik is an [headless browser](https://en.wikipedia.org/wiki/Headless_browser) based on [WebKit](https://fr.wikipedia.org/wiki/WebKit) and HTML parser [Kanna](https://github.com/tid-kijyun/Kanna).

An headless browser allow to run functional tests, to access and manipulate webpages using javascript.

```swift
let browser = Erik.visitURL(url]) { document, error in
    // browse HTML element, click, submit form and more
}
```

## Navigation
Go to an url
```swift
Erik.visitURL(url]) { object, error in
   if let e = error {

   } else if let doc = object as? Document {
     // HTML Inspection
   }
}
```
How to get current url?
```swift
if let url = Erik.currentURL {..}
```

For multiple browsing you can create an instance of headless browser and use same functions
```swift
let browser = Erik()
browser.visitURL...
```

## HTML Inspection
### Search for nodes by [CSS selector](http://www.w3schools.com/cssref/css_selectors.asp)
```swift
for link in doc.querySelectorAll("a, link") {
    print(link.text)
    print(link["href"])
 }
```
### Edit first input field with name "user"
```swift
if let input = doc.querySelectorAll("input[name=\"user\"]").first {
    input["value"] = "Eric"
 }
```

### Submit a form
```swift
if let form = doc.querySelector("form[id='search']") as? Form {
    form.submit()
 }
```

### Evaluate some JavaScript
```swift
let javaScriptSource = "console.log("test");"
Erik.evaluateJavaScript(javaScriptSource) { (obj, err) -> Void in
  if let error = err {
    switch error {
      case ErikError.JavaScriptError(let message):
        print(message)
      default :
        print("\(error)")
    }
  }
  else if let capturedValue = obj {
    // do something according to result
  }
}
```
`capturedValue` is the content of JavaScript variable `resultErik`
Affect this variable in your JavaScript code.
```swift
let javaScriptSource = "console.log("test"); var resultErik = 1 + 1;"
```

### Warning about DOM change
:warning: All action on Dom use JavaScript and do not modify the actual
`Document` object and its children `Element`.

You must use `currentContent` to get a refreshed `Document` object

### Get current content
```swift
Erik.currentContent { (obj, err) -> Void in
    if let error = err {
    }
    else if let document = obj {
       // HTML Inspection
    }
}
```
### Using Future
As an optional feature, you can use [Future/Promise](https://en.wikipedia.org/wiki/Futures_and_promises) ( Erik use frameworks [BrightFutures](https://github.com/Thomvis/BrightFutures) & [Result](https://github.com/antitypical/Result))

Example to submit a google search
```swift
let url = NSURL(string:"http://www.google.com")!
let value = "Erik The Phantom of Opera"
// visit
var future: Future<Document, NSError> = Erik.visitURLFuture(url)
// fill input field
future = future.flatMap { document -> Future<Document, NSError> in
  if let input = document.querySelector("input[name='q']") {
    input["value"] = value
  }
  return Erik.currentContentFuture()
}
// optionally check value & submit form
future = future.flatMap { document -> Future<Document, NSError> in
  if let input = document.querySelector("input[name='q']") {
      // value ==  input["value"]
  }
  if let form = document.querySelector("form[name=\"f\"]") as? Form {
    form.submit()
  }
  return Erik.currentContentFuture()
}
// finally get final result as success or error
future.onSuccess { document in
  // parse result
}
future.onFailure { error in
  print("\(error)")
}
```

## Links
- [A list of (almost) all headless web browsers in existence](https://github.com/dhamaniasad/HeadlessBrowsers)
- [Wikipedia Headless browser](https://en.wikipedia.org/wiki/Headless_browser)

## Setup

### Using [cocoapods](http://cocoapods.org/) ##

Add `pod 'Erik'` to your `Podfile` and run `pod install`.

*Add `use_frameworks!` to the end of the `Podfile`.*

#### Optional Future
Add `pod 'Erik/Future'` to your `Podfile` and run `pod install`.

## Roadmap
- [ ] (WIP) WebView screenshot (webkit view privates api)

## Why Erik?

A well known headless browser is named [PhantomJS](http://phantomjs.org/) and a very well known browser is [Opera](www.opera.com).

As a tribute I use [Erik](https://en.wikipedia.org/wiki/Erik_(The_Phantom_of_the_Opera)), firstname of the title character from Gaston Leroux's novel *Le Fantôme de l'Opéra* best known to English speakers as [The Phantom of Opera](https://en.wikipedia.org/wiki/The_Phantom_of_the_Opera). 

My name is also Erik. So egotistical to call a project using its firstname isn't it.

My only justification is that I was playing Metal Gear Solid *V* and the creator Hideo Kojima name appears over 100 times in the game. Coincidentally the full name of the game is Metal Gear Solid *V* : The  **Phantom** Pain.

## Lisense
The MIT License. See the LICENSE file for more information.
