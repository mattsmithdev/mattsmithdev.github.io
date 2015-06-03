---
published: true
layout: post
title: ASP.NET MVC 6 and the Infinite Scroll
tags: asp.net mvc jquery
---



![infinity](https://cloud.githubusercontent.com/assets/9366487/7947574/f022d450-094b-11e5-829c-5882444e6445.gif)
When we have long lists of items, it's nice to present the users with the option to page them, instead of showing them all at once. You can use your standard pager, which works great with something like a table list, or you might be able to try something called "infinite scrolling". Infinite scrolling is that feature where you start scrolling to the bottom of the page and it automagically loads more data for you. So, it's like paging but using the browser scroll bar. I've been doing a redesign of a book listing site, and instead of just providing the standard pager, I thought it would be nice to let them just keep on going.

So, where do we start? This post is written using ASP.NET 5 (MVC 6), but the code is basically the same in older versions of ASP.NET MVC. To do the infinite scrolling, we'll be using a JQuery plugin called "[jScroll](http://jscroll.com/)" written by Philip Klauzinski. To pull down a client side library in ASP.NET 5, we open up our bower.json file and just add in the library name and version, as such...
{% highlight JavaScript %}
{
  "name": "ASP.NET",
  "private": true,
  "dependencies": {
    "jquery": "*",
    "jquery-validation": "1.11.1",
    "jquery-validation-unobtrusive": "3.2.2",
    "materialize": "*",
    "jscroll": "2.3.4"
  }
}
{% endhighlight %}
...You can see jscroll there at the bottom. Now, if you happen to be using the ["Main-Bower-Files" NodeJS package I wrote about before](http://mjsmithdev.com/2015/05/25/ASPNET-5-How-to-Add-New-Bower-Libraries-with-Gulp/), you'll just need to open up Task Runner Explorer and run the Copy command to add it to your local 'wwwroot/lib' directory. If not, then you'll need to modify your gulp.js file to tell it to copy over the new file to your 'wwwroot/lib' directory.

Now we have the client side library available, we can add it to our razor view where you want the scrolling. (You can setup Gulp to combine and minify your javscript, which you'll want to do for production. I'll write about that later.) Since I will just be using this on one page, I'll just add it to the scripts section on that page...
{% highlight JavaScript %}
  @section scripts{
    <script src="~/lib/jscroll/jquery.jscroll.js"></script>
    <script>
      $(function () {
        $('#booklist').jscroll({
          debug: true,
          loadingHtml: '<div class="row"><div class="col s4 center"><div class="progress"><div class="indeterminate"></div></div></div></div>'
        });
      });
    </script>
  }
{% endhighlight %}
...This scripts section adds the JScroll library and initializes it. In this example, it will just look for a tag with the id of booklist. I'll show you that in a minute. The other properties turn on its debugging and changes it's loadingHtml to show a loader image (from the MaterialCSS library).

Now, let's create a partial view. So, create a new view under your Views and "ControllerName" directory (Views/Books, for example) and name it "_BookList". The underscore is not necessary, it's just convention.

Inside of "_BookList.cshtml", we'll put in the html that will have the infinite scroll. In this example, I'm just going to use a simple UL list...
{% highlight HTML %}
@model IEnumerable<DFE.Models.Book>
<div id="booklist" class="scroll">
  <ul id="ullist">
    @foreach (var item in Model) {
      <li>@Html.DisplayFor(modelItem => item.Title) by @Html.DisplayFor(modelItem => item.Author)</li>
    }
    <li class="next"><a href="/booklist?page=@(ViewBag.Page+1)">Loading...</a></li>
  </ul>
</div>
{% endhighlight %}
...So, it will simply add another UL list on each call. The link is what JScroll calls to show the next set. It will keep calling until we do a return of EmptyResult from our controller, which will then not display any HTML and JScroll will see that as a call to end. The ViewBag.Page just keeps track of what page we're on and what to fetch next.

Inside our controller, we now need to add a call for the 'booklist?page=' route you see in the link above. Here's our controller method...
{% highlight C# %}
[Route("/booklist")]
public IActionResult BookList(int page) {
  var books = bc.Books.ToList();

  int rangeStart = 0;
  int rangeLength = 0;

  int pageSize = 12;
  int pageNumber = page;

  if ((pageNumber * pageSize) > books.Count)
    return new EmptyResult();

  if (((pageNumber * pageSize) + pageSize) > books.Count) {
    rangeLength = books.Count % pageSize;
    rangeStart = books.Count - rangeLength;
  } else {
    rangeStart = (pageNumber * pageSize);
    rangeLength = pageSize;
  }

  books = books.GetRange(rangeStart, rangeLength);

  ViewBag.Page = pageNumber + 1;
  return PartialView("_BookList",books);
}
{% endhighlight %}
...You can see this method is called by the 'booklist' route and accepts one parameter called 'page'. The first thing we do is call our list of books from our EF context. After doing some initializing, we next do some checks on what range to display. Our first check is to see if given the pagenumber * the pageSize will go beyond the number of books available. If so, then we've reached the end and return an EmptyResult. The next if-else check will see if we have a full set of pageSize to display or just whatever is left. We then grab that range from the list. Finally we update the ViewBag.Page count and display the partial view.

If all goes well, you'll now get another UL list displayed in the brower, just by scrolling down, something like this table example...
![infinite-scrolling-example](https://cloud.githubusercontent.com/assets/9366487/7949407/8d4fa744-095b-11e5-820d-e18bb16ba3b8.gif)
