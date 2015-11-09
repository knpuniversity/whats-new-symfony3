# Directory Structure

Hi guys!  Symfony 3 is around the corner and it's time to talk about all the new cool shiny stuff we're getting!  
But first, I have to be honest with you, Symfony 3 actually gives us nothing new, and this is a really good thing. And I'll talk about it more when we talk about upgrading Symfony 3.  But technically Symfony 2.8 and Symfony 3 have the exact same features.  So if you upgraded Symfony 2.8 you're gonna have everything.  And they also come out on the exact same day.

So what we're really talking about on this series is what's gonna be Symfony 2.8 and 3.0.  But there is one thing that's new specifically in just Symfony 3 and it's the new Symfony 3 directory structure which you see over here.  And if you're used to Symfony, it's not gonna be a big change.  You just have to be aware that it's happening.

The big thing is this new bar directory here which holds the cache and logs, which of course used to be an app directory.  Same thing with the [inaudible] [00:00:55] cache file.  It used to be an app, now it's inside a bar.

The other big thing is remember app slash console?  That's now bin slash console.  The cool thing about this is the app director is actually just configuration code at this point, stuff that we can actually modify.  So gone are things that we didn't touch and now we can focus on only files that we do want to touch.

Now if you're using page restore using the Symfony plug-in, at least for now this means that when you're unable to plug in you need to change a couple of settings here so that page restore knows to look in the directory.  I know one of the questions I'm gonna get is, do I have to change over to this new structure or how do I change over to this new structure.

And the thing is this new structure has always been available.  In fact, open up app kernel, you see that the only reason this new structure exists is because the get cache dir and get log dir have been overridden.  These default to whatever directory this file's in slash cache and slash logs.  So in your project, if you override these then boom; you're using the new director structure.  If you don't wanna use the new directory structure, don't use it.  Just be aware that the documentation's gonna change and we're gonna start referencing things like the bar, cache directory and the bin console script.

And those are the biggest changes.  Two other small things I notice is that the test directory had been moved actually to the root of the project and [inaudible] now just contains your actual page P code and the test directory contain tests for that code.  Now, if you were actually making a reasonable bundle then you could still keep your test directory in app bundle.  But [inaudible] we have all our tests in one spot right here.

The other one is [inaudible] has moved from the app directory up into the root of the project.  So when you run page unit, you'll just run page unit.  So when you run page unit you don't have to do the dash C app thing anymore.  You can just run page unit.  It's gonna find that configuration file all by itself.  So the first change in Symfony 3 is actually optional but it's a really cool structure so – it's a cool structure and it's gonna be really easy to switch over to.
