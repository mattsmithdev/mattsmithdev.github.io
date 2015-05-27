---
published: true
title: Solving 404.8 IIS Errors when Loading Your Client Side Libraries
---


Just ran into a problem trying to get the [MaterializeCSS](http://materializecss.com/) library working in Visual Studio 2015. It would refuse to load the CSS/JavaScript. I'm pulling in Materialize via Bower/Gulp. The main files are under 'materialize/bin'.

If you look at your browser's console, you'll see it's giving an error that it can't load them. When you go directly to the CSS file, IIS throws up a 404.8 Hidden Namespace error. It's not a 404.0 Not Found error, because it sees the files, it just can't load them.

If you look at the name of the directory that the Materialize team chose to use - 'bin' that might give you the clue to why this is happening. To protect us from possible attacks, by default certain key directories are blocked, including any directory named 'bin'. Now, you could add code to remove it from blocking bin, but I just found it safer to move the files outside of the 'bin' directory.

If you use the handy "main-bower-files" in your gulpfile.js, you might be interested in this piece...The only problem with manually moving around files is that MaterializeCSS has its 'main' defined in its bower.json to use the 'bin' directory. "main-bower-files" will just keep copying over the 'bin' directory. You can tell "main-bower-files" to override that, like such...
{% highlight JavaScript %}
  var files = mainBowerFiles({
      paths: {
          base: paths.bower
      },
      "overrides": {
          "materialize": {
              "main": "dist/**"
          }
      }
  });
{% endhighlight %}
That will copy the files from the 'dist' folder instead of what is defined under 'main'.

You can read up on [404.8 IIS errors](http://www.iis.net/configreference/system.webserver/security/requestfiltering/hiddensegments) at your leisure.
