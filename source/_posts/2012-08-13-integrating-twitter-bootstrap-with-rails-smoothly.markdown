---
layout: post
title: "Integrating Twitter Bootstrap with Rails smoothly"
date: 2012-08-13 10:23
comments: true
categories: Rails
---

[Twitter Bootstrap]:http://twitter.github.com/bootstrap/
[hn-bootstrap]:http://news.ycombinator.com/item?id=3536291
[builtwith]:http://builtwithbootstrap.com/
[LESS]:http://lesscss.org/
[Sass]:http://sass-lang.com/
[bootstrap-sass]:https://github.com/thomas-mcdonald/bootstrap-sass/
[bootstrap-sass-variables]:https://github.com/thomas-mcdonald/bootstrap-sass/blob/master/vendor/assets/stylesheets/bootstrap/_variables.scss
[bootstrap-sass-mixins]:https://github.com/thomas-mcdonald/bootstrap-sass/blob/master/vendor/assets/stylesheets/bootstrap/_mixins.scss
[bootstrap-js]:https://github.com/thomas-mcdonald/bootstrap-sass#javascripts
[bootstrap-less]:https://github.com/metaskills/less-rails-bootstrap/
[simple_form]:https://github.com/plataformatec/simple_form
[formtastic-bootstrap]:https://github.com/mjbellantoni/formtastic-bootstrap
[formtastic]:https://github.com/justinfrench/formtastic
[bootstrap_form]:https://github.com/potenza/bootstrap_form
[Rails-Bootstrap-Navbar]:https://github.com/julescopeland/Rails-Bootstrap-Navbar
[Bootswatch]:http://bootswatch.com/
[bootswatch-rails]:https://github.com/maxim/bootswatch-rails
[Blueprint CSS]:http://www.blueprintcss.org/
[variables]:http://twitter.github.com/bootstrap/less.html#variables
[alerts]:http://twitter.github.com/bootstrap/components.html#alerts
[stop_embedding]:http://ruby.bvision.com/blog/please-stop-embedding-bootstrap-classes-in-your-html


[Twitter Bootstrap] is a HTML, JS, CSS toolkit [loved, hated][hn-bootstrap] and [used by many developers][built-with]. As any tool, it can be useful for you in certain cases and, in this post, I will show how to smoothly integrate it with Ruby on Rails:

* Initial Setup
* Bootstrap Themes
* Form Builders
* Navigation Bar DSL
* Flash Messages
* Avoiding dependency

<!-- more -->

## Initial Setup

Instead of downloading [Twitter Bootstrap] from its project page and adapting it to Rails, there are several gems to get you going fast and as bonus, CSS extensions with [LESS] or [Sass]. As [Sass] is default in Rails, I prefer to use [bootstra-sass]. [less-rails-bootstrap] is an option If you want to use [LESS].

Don't forget to [include the javascript files][bootstrap-js].

## Bootstrap Themes

[Twitter Bootstrap] is easly customizable through its [variables and mixins using LESS][variables]. It can also be done with [bootstrap-sass] using the same names for [variables][bootstrap-sass-variables] and [mixins][bootstrap-sass-mixins].

If there isn't time or the need to customizing colors, fonts, etc, you can use a free theme from [Bootswatch]. There some good options and [bootswatch-rails] which makes it even easier if you are using [Sass].

## Form Builders

[formtastic-bootstrap] for [formtastic], a builtin generator for [simple_form] and a specified form builder called [bootstrap_form]. I use [simple_form], installing it with bootstrap option:

```
rails generate simple_form:install --bootstrap
```

## Navigation Bar DSL

The reason why I'm using [Rails-Bootstrap-Navbar]:

``` diff
+<%= nav_bar fixed: :top, brand: "Lentlist", responsive: true %>
-<div class="navbar navbar-fixed-top">
-  <div class="navbar-inner">
-    <div class="container">
-      <a class="btn btn-navbar" data-toggle="collapse" data-target=".nav-collapse">
-        <span class="icon-bar"></span>
-        <span class="icon-bar"></span>
-        <span class="icon-bar"></span>
-      </a>
-      <%= link_to "Lentlist", root_path, class: "brand" %>
-      <div class="nav-collapse">
-        <ul class="nav">
-        </ul>
-      </div>
-    </div>
-  </div>
-</div>
```

## Flash Messages

There are [4 kind of alerts][alerts] in [Twitter Bootstrap]: alert-success, alert-error, alert-block and alert-info. So you'll need to set the right CSS class for each flash message type. A solution is to create a helper like this one:

``` ruby
module ApplicationHelper

  def bootstrap_class_for flash_type
    case flash_type
      when :success
        "alert-success"
      when :error
        "alert-error"
      when :alert
        "alert-block"
      when :notice
        "alert-info"
      else
        flash_type.to_s
    end
  end
end
```

For the rest of this solution: [Rails flash messages using Twitter Bootstrap](https://gist.github.com/3344628)

## Avoiding dependency

Independently of the way you choose to use [Twitter Bootstrap], [avoid embedding Bootstrap classes in your HTML][stop_embedding], use inheritance and mixins to create your own classes using bootstrap classes. For instance:

``` erb
<body class="container">
  <%= render partial: 'shared/nav' %>
  <div class="row">
    <div class="span7">
      <%= render :partial => "shared/flash_messages", :locals => {:flash => flash} %> 
      <%= yield %>
    </div>
    <div class="span3 offset2">
      <%= render partial: 'shared/sidebar' %>
    </div>
  </div>
  <hr/>
  <footer>Footer</footer>
</body>
```

Instead of use `span*`, `row`, `offset*` and `container`, use semantic classes:

```
<body>
  <%= render partial: 'shared/nav' %>
  <div class="content">
    <div class="main">
      <%= render :partial => "shared/flash_messages", :locals => {:flash => flash} %> 
      <%= yield %>
    </div>
    <div class="sidebar">
      <%= render partial: 'shared/sidebar' %>
    </div>
  </div>
  <hr/>
  <footer>Footer</footer>
```

And use [Sass] or [LESS] to define your classes. A [Sass] example:

``` sass
body
  @extend .container
  padding-top: 60px

.content
  @include makeRow()

.content .main
  @include makeColumn(8)

.content .sidebar
  @include makeColumn(4, 2)
```

This way the CSS classes are easier to understand and it will be easier to change the CSS Framework or upgrade [Twitter Bootstrap] when needed.


## For more information

* [RailsApps Project: Twitter Bootstrap and Rails](http://railsapps.github.com/twitter-bootstrap-rails.html)
* [RailsCasts: Twitter Bootstrap Basics](http://railscasts.com/episodes/328-twitter-bootstrap-basics)
* [Please stop embedding Bootstrap classes in your HTML!][stop_embedding]
* [Lentlist: application used to create the examples](https://github.com/roberto/lentlist)
