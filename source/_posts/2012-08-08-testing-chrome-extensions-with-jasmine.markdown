---
layout: post
title: "Testing Chrome Extensions with Jasmine"
date: 2012-08-08 16:31
comments: true
categories: Testing
---

[extension]:http://github.com/roberto/gist-io-chrome
[jasmine-docs]:http://pivotal.github.com/jasmine/
[gistio]:http://gist.io/
[Jasmine]:http://github.com/pivotal/jasmine
[rspec]:http://rspec.info/
[requirejs]:http://requirejs.org/
[content_script]:http://code.google.com/chrome/extensions/content_scripts.html
[specrunner]:https://github.com/pivotal/jasmine/wiki/A-simple-project
[async]:http://pivotal.github.com/jasmine/#section-Asynchronous_Support
[extension-specs]:https://github.com/roberto/gist-io-chrome/tree/master/spec

This weekend I wrote [a chrome extension][extension] for [Gist.IO][gistio], a nice blog writing for hackers, to try out [Jasmine], a BDD framework for testing JavaScript code. This post contains some notes about it.

<!-- more -->

## Specs, Expectations, Matchers, Setup and Teardown

After I read a few paragraphs of the [well styled documentation][jasmine-docs], I got everything that I needed to start writing some specs. As I'm used to [RSpec][rspec] DSL, it was even easier.

``` javascript Code example
describe("foo", function() {
  var foo;

  beforeEach(function() {
    foo = 0;
    foo += 1;
  });

  afterEach(function() {
    foo = 0;
  });

  it("should be equal 1", function() {
    expect(foo).toEqual(1);
  });
});
```

## Spies

But the first thing that I really liked were the spies. They can stub functions and track calls and their arguments.

``` javascript Spy example
describe("foo.setBar", function() {
  var foo;

  beforeEach(function() {
    foo = { setBar: function() {} };

    // 1. setting up the spy
    spyOn(foo, 'setBar'); 
  });

  it("should call setBar passing 456 and any String", function() {
    // 2. call being tested
    foo.setBar(456, 'another param');

    //3. checking the spy with a matcher
    expect(foo.setBar).toHaveBeenCalledWith(456, jasmine.any(String)); 
  });
});
```

The default behavior is to just track calls and arguments, accessible by calling `foo.setBar.mostRecentCall.args[0]` and `foo.setBar.calls[0].args[0]` for instance. In addition, there are some functions that may be used to change its behavior and to call the actual implementation, return a specific value or call a custom implementation.

## Some Recipes

### Executing JavaScript Files

I've used [requireJS][requirejs] to mock the [content script injection][content_script], JavaScript files triggered by Chrome in specified pages according to manifest.json file.

To set it up using [SpecRunner.html][specrunner] add require.js to the header and put its config just before Jasmine setup in the script tag.

``` html SpecRunner.html
<script type="text/javascript">
  (function() {
    require = require.config({
      baseUrl: "../"
    });

    var jasmineEnv = jasmine.getEnv();
    //(...)
</script>
```

Next step, require the JavaScript file to be executed using [Jasmine Async Support][async].

``` javascript
runs(function(){
  require(['content_script']);
});

waits(100);

runs(function(){
  expect(chrome.extension.sendMessage).toHaveBeenCalledWith('io', jasmine.any(Function));
});

```

I've tried other solutions, but this one was the cleanest and simplest I found.

### Mocking Chrome Extension API

A hash and few spies are enough to get it done.

``` javascript
// chrome.pageAction.show
// chrome.pageAction.onClicked.addListener
chrome = {
  pageAction: {
    show: function(){},
    onClicked: {
      addListener: function(){}
    }
  }
} 

spyOn(chrome.pageAction.onClicked, 'addListener');
spyOn(chrome.pageAction, 'show');
```

### Testing inline functions passed as parameters

Let's say I want to test if the following inline function is really calling `alert("response")`.

``` javascript
chrome.extension.sendMessage("message", function(response) {
  alert("response");  
});
```

I can get it using `mostRecentCall` as shown below:

``` javascript
// 1. setting up the spies
spyOn(window, 'alert');
spyOn(chrome.extension, 'sendMessage');

// 2. the real deal
chrome.extension.sendMessage("message", function(response) {
  alert("response");  
});

// 3. catching and calling the inline function
chrome.extension.sendMessage.mostRecentCall.args[1].call();

// 4. checking behavior 
expect(alert).toHaveBeenCalledWith("response");
```

That is useful to test callbacks (e.g.: listeners/events in Chrome Extension API), but in most cases inline functions make code harder to read, test and reuse.

So, refactoring last example:

```
alertResponse = function(){
  alert("response");
}

spyOn(window, 'alert');
alertResponse();
expect(window.alert).toHaveBeenCalledWith("response");
```

```
spyOn(chrome.extension, 'sendMessage');
chrome.extension.sendMessage("message", alertResponse);
expect(chrome.extension.sendMessage).toHaveBeenCalledWith("message", alertResponse);
```

## Summary

Jasmine is powerful in its flexibility and simplicity. It's easy to use, extend and read. The only thing that I miss is: beforeAll, but I didn't need it to test my Gist.IO extension. Checkout its tests within Jasmine: [gist-io-chrome/spec][extension-specs].






