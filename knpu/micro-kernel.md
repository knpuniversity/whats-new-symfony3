# MicroKernel

How big is Symfony really? And how little can we make it? Typically we start with the
Symfony Standard Edition, which looks a bit like this with 10 configuration files, 
a web directory with two front controllers and this general skeleton that has 20 or 30
files just to begin with. 

A new feature in Symfony 2.8, called the MicroKernel, you can do something different.
You can build an entire fully functioning framework with 1 file! And grow from there
when you want to. It's like starting with Silex, except you still have access to all
the normal Symfony stuff like bundles and creating services in the container. 

Imagine that instead of this project right now the only thing we have is a `composer.json`
file and it requires `symfony/symfony` it's the only thing we need. 

In the app directory create a new php class called `LittleKernel`. The kernel is the heart
of your application and it needs to extend the `BaseKernel` class or implement an interface.

Normally, when you extend this you need a `registerBundles` and `registerContainerConfiguration`.
Instead of doing that we'll use a new trait called `MicroKernelTrait`. As soon as we have this
the methods that we'll implement are `registerBundles`, `configureRoutes` and `configureContainer`.

This is cool, the kernel is the heart of your application and what is a Symfony application?
Well, it's a set of bundles, a set of services in the container and a set of routes and that's it.
Here we have the exact three methods that we need to define that and we can make it look however
we want.

Let's see if we can get an entire application running in just this one file. In `registerBundles`
we'll need at least the framework bundle. In `configureContainer` the minimum amount of configuration
we need is to call `loadFromExtension('framework')` and pass this a secret key, `micr0`. shhhh it's secret.

This `loadFromExtension` is the equivalent to having your `config.yml` file with the framework key
at the root. This is the yaml version of configuration and this `loadFromExtension` is just the same
thing being done in PHP. 

In `configureRoutes` pass the new route Collection builder object which is a new thing for Symfony 2.8.
And was built to support the MicroKernel. It allows you to build routes in PHP with a really nice interface.
Let's do that!

`$routes->add('/hello/symfony/{version}')`, the second argument is the controller `kernel:helloSymfony`.
This is the service format of configuring a controller, `kernel` is the name of the service, which will
point to this class and `helloSymfony` is the name of the method. Finish this off with `public function helloSymfony()`.
Give it a `$version` argument and say `return new Response('Hi Symfony version'.$version);`. 

Look at that we now have a fully functional Symfony application all in one file. The only thing we need now is a
front controller that boots and runs this and if you want you could put that right at the bottom of this file.

I'm a minimalist but let's make things a little bit bigger, in the web directory create a new file called
`tiny.php`. This will basically be our version of an `app.php` or `app_dev.php` file. 

Of course we need to start by requiring Composer's autoloader and our `LittleKernel` file which doesn't
have a namespace so it can't be autoloaded. Now this is just the exact same stuff that you're used to seeing
inside of `app_dev.php`. In fact, copy this code from `app_dev.php` and paste it into `tiny.php`. Change
`AppKernel` to `LittleKernel`. I'll autocomplete the request class so that PHPStorm will plug the use statement
in for us. 

Remove the `loadClassCache` to make this even smaller. Here we create the `LittleKernel`, create the `$request`,
pass the `$request` into the kernel and that's it. This front controller is going to look the same no matter
what you do. 

Ready? Let's give it a try!

Over in the browser add /tiny.php to our URL and when we do we get a terrible error! But wait, looking at this
error it says "No route found for 'GET'" our application is working! We're using such a small bit of Symfony
that we don't have the nice exception pages. We could get these if we used the Twig bundle, but even without
those we still have a functional app even if we don't have nice looking errors. 

Let's try a real page like `/hello/symfony/3` and wow, there it is! 

Okay, let's make this a little bit bigger and see if we can run our existing app through the `MicroKernel`.
Our existing app has a `DefaultController`, some annotation routes on it and we use Twig to render our
templates. 

In `LittleKernel` the first thing we'll need to do is instantiate a new `TwigBundle` and a new `SensioFrameworkExtraBundle`
so we can load annotation routes. To activate Twig we need just a little bit more configuration inside of
our framework key down here. This is the kind of stuff that you'd already see in your `config.yml`. Drop
a templating key in here with an engines subkey and then we'll add Twig as an engine. Activate the assets
subsystem which you can do by just adding `assets` and setting that to an empty array. This makes it possible
to use the asset function inside of Twig. 

In `configureRoutes` add the annotation route to the one we already have here, do that with an
`$annotationRoutes` variable and set it to `$routes->import('')` and inside of there import whatever resource
you want like a yaml or xml file. In our case we'll import the annotations from a controller. `__DIR__.'/../src/AppBundle/Controller'` the second argument is the type which is typically optional unless you're
using something crazy like annotations. 

This is equivalent to what you would normally see inside of a yaml file where there is a resource key and a type
of annotations. Really it's the exact same thing, just done inside of PHP instead. 

Back in our `LittleKernel` add `$routes->mount('/'.$annotationRoutes)` which says "Yo! Load those into my routes.
Don't get too excited, this isn't going to work quite yet. But let's refresh anyways to see what's happening.

The first error we get here is "The annotation '@Sensio\Bundle\FrameworkExtraBundle\Configuration\Route' could not
be loaded". If you work with annotations, then you need ot add one little extra line of code which already exists
inside of Symfony's normal autoload file `app/autoload` and it's this line here: `AnnotationRegistry::registerLoader`,
in `tiny.php` instead of using composer's autoloader directly I'm going to load the normal autoload file which
itself actually loads the composer autoloader then takes care of this annotations thing. 

Refresh and it works! Let's check out the homepage and now the site is failing with "Unknown 'is_granted' function in base.html.twig on line 19". Let's look and see what's happening on line 19 in that file. Our app is choking on
`is_granted`. This is one of the really cool but tricky things about using the `MicroKernel`. If you're used to having
ten or twenty bundles then you're used to having all the features of Symfony. But, with the `MicroKernel` you have
to opt into those. The `is_granted` comes from Symfony's security bundle and if you want to use that you'll have to
add it into `LittleKernel`. For now, just take that out and refresh again in the browser. 

It works! That's the `MicroKernel` in a nut shell. Add as many bundles as you want and import external files just like
you saw here with the annotation route. This isn't about putting everything into a single file, it's about starting
with a single file and then choosing which bundles and configurations make sense for your project. 

Same thing down here, right now I have my framework configuration inside of PHP, but as this project grows I'll eventually
want to use a yaml file which I could do really easily by using this `$loader` variable over here and telling it
to import one of the configuration files. 

Finally, for multi-kernel applications things get easier, because it's just really obvious and clean what configuration
files are loading. We'll cover this in even more detail in the future, but for now play with it and I hope you love it
as much as I do!




