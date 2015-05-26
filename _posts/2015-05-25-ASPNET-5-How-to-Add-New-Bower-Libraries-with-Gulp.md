---
published: true
layout: post
title: ASP.NET 5 How to Add New Bower Libraries with Gulp
tags: asp.net bower gulp
---

![gulp-bower](https://cloud.githubusercontent.com/assets/9366487/7791780/a9262d0a-0274-11e5-8939-e676fc0b0bd1.png)
In Part 1 of this 2 part post we looked at [how to use Bower to bring down a new client side library](http://mjsmithdev.com/2015/05/24/ASPNET-5-How-to-Easily-Add-New-Bower-Dependencies/). In Part 2 we will focus on how to include the important bits of that library in your ASP.NET 5 web project using [Gulp](http://gulpjs.com/).

Gulp is like build rules for client side libraries. In your ASP.NET 5 web project you'll see 'gulpfile.js'. This defines the "build rules" used by Gulp. If you open it up, you'll see a piece like...
{% highlight JavaScript %}
gulp.task("copy", ["clean"], function () {
  var bower = {
    "bootstrap": "bootstrap/dist/**/*.{js,map,css,ttf,svg,woff,eot}",
    "bootstrap-touch-carousel": "bootstrap-touch-carousel/dist/**/*.{js,css}",
    "hammer.js": "hammer.js/hammer*.{js,map}",
    "jquery": "jquery/jquery*.{js,map}",
    "jquery-validation": "jquery-validation/jquery.validate.js",
    "jquery-validation-unobtrusive": "jquery-validation-unobtrusive/jquery.validate.unobtrusive.js"
  }
...
{% endhighlight %}
This tells Gulp to perform a function when you call 'copy' or 'clean'. So, this is the second piece to the puzzle of adding a client side library to your project. Bower let us bring down the package and Gulp will copy over the appropriate bits into your project.

We could simply add a line to the bower variable in the gulp copy/clean function defined above that will copy over the 'dist' folder contents from our materialize library. That will work just fine. Problem though is that for every library you want to add, you'll need to remember to add it to 'bower.json' and 'gulpfile.json'. I hate having to do similar things in two different places every time. To get around this enter a nice Node.js package called "[Main-Bower-Files](https://github.com/ck86/main-bower-files)".

Main-Bower-Files will look in your Bower cache directory and pull out the main pieces of each library. This will save you from having to update that bower javascript variable with every client side package you want to use. First step is to add Main-Bower-Files. You can do that by opening another file in your project, 'package.json'. Add in "main-bower-files" like so...
{% highlight JavaScript %}
{
  "name": "ASP.NET",
  "version": "0.0.0",
  "devDependencies": {
    "gulp": "3.8.11",
    "rimraf": "2.2.8",
    "main-bower-files": "2.8.0"
  }
}
{% endhighlight %}
The 2.8.0 just happened to be the latest version as of this write up. This will tell Node.js to pull down that Node package which provides us a nice new tool to use inside of 'gulpfile.js'. After Visual Studio pulls down the Node.js package, open up 'gulpfile.js' again and in the top add a reference to the 'main-bower-files'...
{% highlight JavaScript %}
var gulp = require("gulp"),
  rimraf = require("rimraf"),
  fs = require("fs"),
  mainBowerFiles = require('main-bower-files');
{% endhighlight %}
Then in the copy/clean function change it to have Main-Bower-Files find the Bower main files for you...
{% highlight JavaScript %}
gulp.task("copy", ["clean"], function () {
    
  var files = mainBowerFiles({
      paths: {
          base: paths.bower
      }
  });

   gulp.src(files, { base: paths.bower })
    .pipe(gulp.dest(paths.lib));
});
{% endhighlight %}
Finally, right-click on 'gulpfile.js' in Solution Explorer and select "Task Runner Explorer". In the Task Runner Explorer window, right click on Copy and select Run...
![taskrunnerexplorer](https://cloud.githubusercontent.com/assets/9366487/7804546/94a1a31e-0332-11e5-8bd0-9cc4516359a1.png)
Your 'lib' directory under your 'wwwroot' should now have your new library...
![lib](https://cloud.githubusercontent.com/assets/9366487/7804578/fc5af032-0332-11e5-859c-89712072f11f.png)