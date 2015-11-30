# Micro Symfony via MicroKernelTrait

How big is Symfony, really? It's HUGE! I'm kidding. Dude, Symfony is just a bunch
of little libraries, so we can make it as small or as huge as we want. Most of us
start with the *Standard Edition*: it looks basically like this, 10 or so configuration
files, two front controllers, and some other skeleton files. Probably 20 or 30 files
to begin with.

But no more in Symfony 2.8! Ok, the Standard Edition of course still exists, but now
we have a brand new tool in the arsenal: the `MicroKernelTrait`. You can build a
Symfony app that's as small as one file, then let it grow from there.

Hey, should we try it?

## Basic "Micro" Kernel

Imagine that we don't have this big project: we *only* have a `composer.json` file
that requires `symfony/symfony`:

[[[ code('0494eb49a8') ]]]

In the app directory, create a new class called `LittleKernel`. The kernel is the
heart of your application: make it extend the base `Kernel` class:

[[[ code('13d96e4db4') ]]]

Normally, this class *must* implement two abstract methods: `registerBundles()` and
`registerContainerConfiguration()`. But let's *not* implement those. Instead, use
the new `MicroKernelTrait`:

[[[ code('7ad8bce4ae') ]]]

This implements `registerContainerConfiguration()` *for* us. All we need to do is
add `registerBundles()`, `configureRoutes()` and `configureContainer()`:

[[[ code('a0d4a736f7') ]]]

Ok, stop! Check this out, it's *really* cool. What *is* an application? Well, it's
a set of bundles, a collection of services, and a list of routes. And would you
look at that! We have exactly three methods that define the *three* things that make
up a framework.

## Single-File Symfony

Let's make a real-live app in just this one file. First, register some bundles:

[[[ code('4093c07675') ]]]

We only need *one* bundle: `FrameworkBundle`. To configure that bundle, head to
`configureContainer()`, call `$c->loadFromExtension()`, and pass it `framework` and
an array of configuration. The only key it *needs* is `secret`. Set it to the top
secret super cool-kids password: `micr0`:

[[[ code('7c68dac4da') ]]]

And guess what? We already know *exactly* how this `loadFromExtension()` works: it's
the PHP equivalent of having a `config.yml` file with a `framework` key at the root
and a `secret` key below that:

[[[ code('182a8c0efb') ]]]

Oh, and yea, you *can* still load YML and XML config files, and you probably will.
I'll mention that soon.

At this point, this app will *already* work. But let's make a page. In `configureRoutes()`,
we receive a shiny new `RouteCollectionBuilder` object - *another* new thing for
Symfony 2.8 that makes adding routes in PHP a lot more fun than it used to be.

Add a route with `$routes->add('/hello/symfony/{version}')`. The second argument
is the controller: pass it `kernel:helloSymfony`:

[[[ code('74a6b469a5') ]]]

This is the *service* format for configuring a controller: `kernel` is the name
of the service - that actually points to *this* object - and `helloSymfony` is
the name of the method.

Hey, let's build that:  `public function helloSymfony()`. Give it a `$version` argument
and say `return new Response('Hi Symfony version '.$version);`:

[[[ code('33ee99b742') ]]]

OMG. That's a fully-functional Symfony app in one file. The only thing we're missing
is a front controller that boots and runs this guy. To be *really* trendy, we could
put that code at the bottom of this file. But come on guys - I think we're letting
this micro-framework fad get to our heads. *Some* structure is a good thing.

## Adding the Front Controller

Instead, in the web directory, create a new file called `tiny.php`. This will be our
version of an `app.php` or `app_dev.php` file. 

Start by requiring Composer's autoloader and our `LittleKernel.php`, since it
isn't configured to be autoloaded:

[[[ code('6f236f2479') ]]]

The rest of this file is just the same boring stuff we see in `app_dev.php`. In fact, copy
the bottom of that file and paste it here. Change `AppKernel` to `LittleKernel` and make sure
the `Request` class has its `use` statement. Remove `loadClassCache()` - a small performance
line - to make this *even* smaller:

[[[ code('73bcfefbbe') ]]]

We're crazy!

Ready? Try it!

Over in the browser add `/tiny.php` to the URL:

> http://localhost:8000/tiny.php

Ah! Hide from the error! Wait, check it out, it says:

> No route found for "GET /" in...

Our app is working! We're using *such* a small bit of Symfony that we don't even have
the nice exception pages. You can get these back by adding `TwigBundle`, but it's not
*technically* necessary.

Try the real page now: `/hello/symfony/3`. There it is!

## Creating a Bigger (Micro) App

A real app won't be *just* one file. But the `MicroKernelTrait` allows you to grow
and opt into whatever features you want. Let's see if we can get our existing app
running. That means loading annotation routes from `DefaultController` and booting
Twig to render the templates.

In `LittleKernel`, add `TwigBundle` and `SensioFrameworkExtraBundle` to `registerBundles()`:

[[[ code('dccaf62dc0') ]]]

To *activate* Twig, we need more configuration under the `framework` key. Add a
`templating` key with an `engines` sub-key. In there, add `twig` in an array:

[[[ code('757aedc98f') ]]]

That configuration looks a little "deep", but it's *exactly* what you've always had in
your `config.yml` file:

[[[ code('85b07bf660') ]]]

Also activate the assets subsystem with an `assets` key set to an empty array. This
makes it possible to use the `asset()` function in Twig:

[[[ code('f2e54c5322') ]]]

### Loading External Routing Files

Next, we need to load the annotation routes. That's *super* easy.

Use `$routes->import()`. Pass it whatever routing resource you want, like a YAML
or XML file. In our case, import the annotations from the `Controller` directory with
`__DIR__.'/../src/AppBundle/Controller'`. Pass `/` as the second argument - that's
the prefix - and `annotation` - the "type" - as the last arg:

[[[ code('c84bea9fd6') ]]]

This should feel familiar too: it's equivalent to importing routes in a YAML file
with `resource` and `type` keys:

[[[ code('7c1008046b') ]]]

Really it's the exact same thing, just done in PHP instead.

We're done! But please, please don't get too excited, it's not going to work quite
yet. Refresh anyways to see what's happening.

Error!

> The annotation '@Sensio\Bundle\FrameworkExtraBundle\Configuration\Route' in method
  could not be auto-loaded.

If you work with annotations, then you need one little extra line of code. This line
already exists inside of Symfony's normal `app/autoload.php` file:

[[[ code('1188ef26b1') ]]]

In `tiny.php`, require *this* autoload file. That gives us the annotation line we
need *and* still initializes Composer's autoloader:

[[[ code('71cc6ac3db') ]]]

Refresh! It's alive!

Now check out the homepage. It's dead again!

> Unknown "is_granted" function in base.html.twig at line 19.

Open that file and find line 19. Our app is choking on `is_granted()`:

[[[ code('2e38529cc8') ]]]

This is one of the best and worst parts of the `MicroKernel`. If you're accustomed
to having twenty bundles, then you're accustomed to having *all* the features of
Symfony. But, with the `MicroKernel` you must opt into each feature. The `is_granted()`
comes from Symfony's `SecurityBundle`... and we're not using that! If we need it, then
you'll need to add it to `LittleKernel` and configure your `security.yml` file.

For now, remove `is_granted()`:

[[[ code('61e07f2718') ]]]

And try again.

It alive... again! That's the `MicroKernelTrait` in a nut shell: add as many bundles as
you want, configure them and add routing. You *can* still load external routing
and configuration files, and you should! The point isn't to put *everything* into
a single file: it's about starting with a single file and then choosing which bundles
and configurations make sense for your project. 

Right now, I have my `framework` configuration in PHP. But if the project grows,
I might want to use a YAML file instead. That's no problem.

[[[ code('d52a75f7d6') ]]]

See that `$loader` variable? It has an `import()` method on it - use that to import a
`config.yml` file and you're done. This is the PHP-equivalent to the `imports` line in YAML.

Go play with it: I hope you love it as much as I do!
