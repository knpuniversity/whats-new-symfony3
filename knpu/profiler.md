# New Profiler

Switch away from our `MicroKernel` to our real app where we have the `webProfilerBundle`
initialized. Immediately we've got the same page but with this sweet black bar down here.
This is the same web debug toolbar that you've always known and loved but redesigned and 
rethought. Which means of course means that it is now a nice sleek black with flat UI elements.

You should also notice that there's a lot less stuff on here by default. It's still collecting
the same amount of information as before, but it only starts out showing you the most important
stuff. I don't know about you but in the past mine was starting to wrap onto two lines. Here
we've got time to render templates, who's logged in and how long your total page took. This is
good stuff!

Just like always if we click into this we are brought to the web profiler. Which was also completely
redesigned to be much simpler to use and easier on the eyes. 

One feature of the profiler that has always existed but isn't widely known about is that you have
the ability to view all of the previous requests that were made by the profiler. So for example, 
head to `localhost:8000/login` in the browser and then go back and refresh this page in the profiler
the /login page shows up in the list. This is super handy when you're making API calls or Ajax requests.
If those explode you can go to any page, open any tab add `/_profiler` and you'll get this list. From
there you can find the page you're investigating and click it to get the details. 

Head over to the editor, open up the `DefaultController` and go down to our `sillyLoginAction`. Let's
pretend that something went wrong in here and we can't figure out what's going on. First thing we'll
do is dump this `security.authentication_utils` object to figure out what it is and below throw a new
exception just to simulate something going wrong with the helpful message of "Something went wrong!". 

Load this in the browser, there's our error and down here in the toolbar you can see the dumped
authentication utils. What if this wasn't a web page but something you were hitting with your API?
Copy the URL, head to the terminal and `curl` the URL. And now it's not so much fun, we've got
this giant ridiculous page and it's impossible to see what errors unless you love reading HTML inside
of the terminal and you can't even see the dumped variable. 

This is where you go back, head to `/_profiler` and there is our 500 error for the login page and you
can click in to see the details. This is awesome for two reasons. First, down in the debug tab we can
see what variable we want to debug. If you want to be able to print out a variable in an API context
you can do that, just use the awesome little dump function to make that happen. The second thing is 
that you can see the big beautiful exception page as if you had looked at it in a browser and figure
out what's going on. 

Use dump and the profiler when you're working with an API, it's one of the most useful tools that gets
overlooked way too often.

This feature isn't new in 2.8 but it's fancy new look is.
