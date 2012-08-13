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
[bootstrap-js]:https://github.com/thomas-mcdonald/bootstrap-sass#javascripts
[bootstrap-less]:https://github.com/metaskills/less-rails-bootstrap/
[simple_form]:https://github.com/plataformatec/simple_form
[formtastic-bootstrap]:https://github.com/mjbellantoni/formtastic-bootstrap
[formtastic]:https://github.com/justinfrench/formtastic
[bootstrap_form]:https://github.com/potenza/bootstrap_form
[rails_bootstrap_navbar]:https://github.com/julescopeland/Rails-Bootstrap-Navbar
[Bootswatch]:http://bootswatch.com/
[bootswatch-rails]:https://github.com/maxim/bootswatch-rails
[Blueprint CSS]:http://www.blueprintcss.org/


[Twitter Bootstrap] is a HTML, JS, CSS toolkit [loved, hated][hn-bootstrap] and [used by many developers][builtwith]. As any tool, it can be useful for you in some scenario and, in this post...TODO

* Initial Setup
* Bootstrap Themes
* Forms
* DSL for navigation bar
* Customization


## Initial Setup

Instead of downloading [Twitter Bootstrap] from its project page and adapt it to Rails, there are some gems to get it done fast and as bonus CSS extensions with [LESS] or [Sass]. As [Sass] is default in Rails, I prefer to use [bootstra-sass]. If you want to use [LESS], [less-rails-bootstrap](https://github.com/metaskills/less-rails-bootstrap/).

Don't forget to [include the javascript files][bootstrap-js].

## Bootstrap Themes

If there isn't time or need to spend customizing colors, fonts, etc, you can use a free theme from [Bootswatch]. [bootswatch-rails] makes this even easier if you are using [Sass].

## Forms

There are good options: [formtastic-bootstrap] for [formtastic], a builtin generator for [simple_form] and a specified form builder called [bootstrap_form]. I use [simple_form], installing it with bootstrap option:

```
rails generate simple_form:install --bootstrap
```

