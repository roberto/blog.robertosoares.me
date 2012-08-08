---
layout: post
title: "Testing a Chrome Extension with Jasmine"
date: 2012-08-08 10:51
comments: true
categories: Testing
---

This weekend I wrote a chrome extension for Gist.IO, a nice blog writing for hackers, to try out Jasmine, a BDD framework for testing JavaScript code. This post contains my impressions about Jasmine.

## Specs, Expectations, Matchers, Setup and Teardown

After I read a few paragraphs of its well styled documentation, I got everything that I need to start to write some specs. As I'm used to RSpec DSL, it was even easier.

``` javascript Code example
describe("A spec", function() {
  var foo;

  beforeEach(function() {
    foo = 0;
    foo += 1;
  });

  afterEach(function() {
    foo = 0;
  });

  it("is just a function, so it can contain any code", function() {
    expect(foo).toEqual(1);
  });
});
```

## Spy

But that first thing that I really liked were the spies. They can stub functions and track calls and their arguments. For instance:

```
describe("foot.setBar", function() {
  var foo;

  beforeEach(function() {
    foo = {
      setBar: function() {}
    };

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

The default behavior is just to track calls and arguments, accessible by calling `foo.setBar.mostRecentCall.args[0]` and `foo.setBar.calls[0].args[0]` for instance. There are some functions that may be used to change its behavior and to call the actual implementation, return a specific value or call a custom implementation.

## Some Recipes

### Executing JavaScript Files

I've used requireJS to mock the content script injection, JavaScript files triggered by Chrome in specified pages according to manifest.json file.

To setup it using SpecRunner.html it's just to add require.js to the header and put its config just before Jasmine setup in the script tag.

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

Next step, require the JavaScript file using [Jasmine Async Support][async].

``` javascript
runs(function(){
  require(['io_content_script']);
});

waits(100);

runs(function(){
  expect(chrome.extension.sendMessage).toHaveBeenCalledWith('io', jasmine.any(Function));
});

```

I've tried other solutions, but that one was the cleanest and simplest.

### Mocking Chrome Extension API


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

Let's say I want to test if the following function is really calling `alert("response")`.

```
chrome.extension.sendMessage("message", function(response) {
  alert("response");  
});
```

```
// setting up the spies
spyOn(window, 'alert');
spyOn(chrome.extension, 'sendMessage');

// the real deal
chrome.extension.sendMessage("message", function(response) {
  alert("response");  
});

// catching and calling the inline function
chrome.extension.sendMessage.mostRecentCall.args[1].call();

// checking behavior 
expect(alert).toHaveBeenCalledWith("response");
```

That is useful to test listeners, callbacks.


## Summary

Jasmine is powerful in its flexibility and simplicity.






