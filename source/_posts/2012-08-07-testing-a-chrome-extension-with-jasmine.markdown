---
layout: post
title: "Testing a Chrome Extension with Jasmine"
date: 2012-08-07 23:17
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
describe("A spy", function() {
  var foo;

  beforeEach(function() {
    foo = {
      setBar: function() {}
    };

    spyOn(foo, 'setBar'); // 1. setting up the spy
  });

  it("tracks all the arguments of its calls", function() {
    foo.setBar(456, 'another param'); // 2. call being tested
    expect(foo.setBar).toHaveBeenCalledWith(456, jasmine.any(String)); //3. checking the spy with a matcher
  });
});
```

The default behavior is just to track calls and arguments, accessible by calling `foo.setBar.mostRecentCall.args[0]` and `foo.setBar.calls[0].args[0]` for instance. There are some functions that may be used to change its behavior and to call the actual implementation, return a specific value or call a custom implementation.

## Some Recipes


### Executing JavaScript Files

I've added requireJS to mock the content script injection.

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

### Helpers

Sharing functions between spec files is easy. Define them in a JavaScript file and put its reference in the SpecRunner header. In adition, it's possible to declare beforeEach blocks.

``` javascript spec/SpecHelper.js
beforeEach(function(){
  bar = function(){};  
});
```

## Summary

Jasmine is powerful in its flexibility and simplicity.






