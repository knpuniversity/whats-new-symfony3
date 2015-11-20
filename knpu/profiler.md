# New Profiler

Change the URL back to our *real*, non-micro app:

> http://localhost:8000

We see the same page, but with the sweet black web debug toolbar. This is the
same toolbar that you've always known and loved, but redesigned and rethought. That
means it now has a sleek black interface with flat UI elements, because they're 
super hip!

There's also a lot less stuff on it. Don't worry, it *is* still collecting all the
same stuff as before, but only the important details go here, like the template rendering
time, who's logged in, and how long the page took to load. This is *much* better than
2.7, where my toolbar was breaking onto multiple lines.

Just like always, you can click on any icon to arrive at the web profiler. This is
also completely redesigned to be simpler to use and easier on the eyes. 

## Profiling API/AJAX Requests

Click that `Last 10` link. This is *not* a new feature, but it's totally underused!
This lists all the previously profiled URLs. So for example, head to
`http://localhost:8000/login` and refresh this page. Hello `/login`!  If we clicked
the `477...` token URL, we'd see all the profiler info from that request.

Why do I love this? It's super handy when you're making API calls or Ajax requests.
If something goes wrong and you want to look into, open a tab, go to `/_profiler` and
find that request at the top of this list.

Check it out: go to your editor and open up `DefaultController`. Go down to
`sillyLoginAction`. Pretend that something went wrong and we can't figure it out.
If this `security.authentication_utils` is the problem, we might want to use `dump()`
to print it out. Below that, throw a `new Exception` to simulate something going
wrong with the helpful message of "Something went wrong!". 

Reload `/login`. There's the error! And there's the dumped variable down in the toolbar.
But what if this wasn't a big beautiful HTML web page but some API endpoint you're
testing!? Copy the URL, head to the terminal and `curl` the URL. Ah, gross, disgusting,
horrible - HTML in the terminal! Unless you *love* reading raw HTML, it's tough to
see what went wrong. And seeing the dumped variable... well... that's impossible!

Go back to `/_profiler`. There's our shiny 500 error. Click to get the details. This
is awesome for two reasons. First, down in the Debug tab, you can see the dumped
variable. Woot! You *can* dump a variable in your API and then see it here.

Second, you can see the big beautiful HTML exception page as if you had looked at
it in a browser. Hello sweet debugging.

So don't be crazy! Use `dump()` and the profiler for your API: it's one of the useful
tools that everyone loves to forget about.

This feature isn't new in 2.8, but this fancy new look is.
