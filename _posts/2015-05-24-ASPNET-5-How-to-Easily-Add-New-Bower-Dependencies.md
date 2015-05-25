---
layout: post
published: true
title: ASP.NET 5 How to Easily Add New Bower Dependencies
tags: asp.net bower
---

With ASP.NET 5 we have a new way to manage client side libraries, including JavaScript and CSS libraries. Prior to ASP.NET 5, out of the box for client side libraries you could use Nuget or just manually download the files. Problem with Nuget for client side is someone has to maintain that list and there aren't anywhere as many in Nuget as there are in already maintained client side packaging systems. You can always manually download them, but obviously that leads to maintenace issues of keeping updated.

Enter [Bower](http://bower.io/). It handles pulling down and keeping up to date with the latest version of many client side packages. So, lets look at the basics of how Bower works in ASP.NET 5 and how we can add a new client side package.

If you create a new web project, you'll have a 'bower.json' file that looks like...

{% highlight JavaScript %}
{
  "name": "ASP.NET",
  "private": true,
  "dependencies": {
    "jquery": "*",
    "jquery-validation": "1.11.1",
    "jquery-validation-unobtrusive": "3.2.2"
  }
}
{% endhighlight %}

Let's say in your web project you want to use a [materialize style library](http://materializecss.com/getting-started.html) in your project. You see on the project site it says...

{% highlight %}
bower install materialize
{% endhighlight %}

...or alternatively you could go to the bower site and [search for the bower package name](http://bower.io/search/) there. So, you have the package name 'materialize'. Let's add that to the 'bower.json' file...

{% highlight JavaScript %}
{
  "name": "ASP.NET",
  "private": true,
  "dependencies": {
    "jquery": "*",
    "jquery-validation": "1.11.1",
    "jquery-validation-unobtrusive": "3.2.2",
    "materialize": "*"
  }
}
{% endhighlight %}

...The * on the end just specifies to get any/latest version. Bower uses semantic versioning, so you can specify specic versions or ranges of versions that it should use.

So, that's it right! Nope. Well, sort of. That's enough to pull down the materialize library files into the local Bower cache, but not enough for you to actually be able to use it in your site yet. Next we need to have a process to move the necessary library files from the Bower cache over to your libs directory. That will be handled by another new piece to the ASP.NET world - Gulp. So this post short and sweet, I'll put the Gulp piece in Part 2 of ASP.NET 5 How to Easily Add New Bower Dependencies.
