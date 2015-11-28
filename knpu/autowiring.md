# Service Autowiring

The next new feature is called `autowiring`. And yes, it *is* magic. And yes, it's
super controversial - it's like the celebrity gossip of the Symfony world, right
along side annotations.

Autowiring makes registering services easier. Normally, a service needs a name, a
class *and* its constructor arguments:

[[[ code('aaa35b265c') ]]]

What autowiring says is, "maybe we don't need to give it the arguments?". So remove
the `arguments` key and instead type `autowire: true`:

[[[ code('6a74406008') ]]]

Head back to the browser, refresh the login page and press the login button. It doesn't
explode! How was it able to create the `WeirdFormAuthenticator`!!!??? That's autowiring.

## How does it Work?

It works via type-hints. In `WeirdFormAuthenticator` we're type-hinting the first argument 
with `EntityManager` and the second argument with `RouterInterface`:

[[[ code('36e46f3030') ]]]

Behind the scenes, well, in a "compiler pass" if you're super geeky and curious, autowiring
looks in the container and says "could you please show me all the services type-hinted with
`EntityManager`?". Since there is only one, it auto-wires that service as the first
argument.

The same is true for `RouterInterface`: it sees that there is only one service whose
class implements `RouterInterface`, so it injects the `router` service.

## What if there are Multiple Services with the Class?

But what if there are *multiple* services that implement an interface or match a class?
Well, frankly, your computer will catch on fire....

I'm kidding! Autowiring won't work, but it *will* throw a clear exception. Autowiring
is magical... but not completely *magical*: it won't try to guess *which* entity manager
you want. It's the Symfony-spin on auto-wiring.

If you do hit this problem, there is a way for you to tell the container *which*
service you want to inject for the type-hinted class. 

But the real beauty of autowiring is when it just works. The *whole* point is to
save time so you can rapidly develop.

## Moar Magic: Injecting Non-Existent Services

Autowiring has one more interesting trick. In the `Security` directory, create a new
super important class called `EvilSecurityRobot`. Give it one method:
`public function doesRobotAllowAccess()`:

[[[ code('d10a8a6ff7') ]]]

Basically, the evil robot will decide, randomly, whether or not we can login. So even if
I enter *all* the login fields correctly, the evil security robot could still say "NOPE!
You're not getting in and I'm not sorry. <3 The Evil Robot".

The `EvilSecurityRobot` is ready. To use this in `WeirdFormAuthenticator`, pass it
as the third argument to the constructor: `EvilSecurityRobot $robot`. Now create
a `$robot` property and set it:

[[[ code('ef5d9ce5c9') ]]]

In `checkCredentials()`, `if (!$this->robot->doesRobotAllowAccess())` then throw a
really clear new `CustomUserMessageAuthenticationException()` that says "RANDOM 
SECURITY ROBOT SAYS NO!":

[[[ code('6c1bfc61bf') ]]]

And I'll even put quotes around that. 

This is when we would *normally* go to `services.yml` and update the `arguments`
key to inject the `EvilSecurityRobot`. But wait! Before we do that, we need to register
the `EvilSecurityRobot` as a service first.

Resist the urge! Instead, do nothing: absolutely nothing. Refresh the page. Fill in
`weaverryan` and try to login until you se the error. Bam!

> RANDOM SECURITY ROBOT SAYS NO!

It works! What the heck is going on?

Remember,  `weird_authenticator` is autowired. Because of that, Symfony sees that
the third argument is type-hinted with `EvilSecurityRobot`. Next, it looks in the
container, but finds *no* services with this class. But instead of failing, it
creates a *private* service in the container automatically and injects that. This
actually works pretty well: `EvilSecurityRobot` itself is created with autowiring.
So if *it* had a couple of constructor arguments, it would try to autowire those
automatically.

Oh, and if you have multiple autowired services that need an `EvilSecurityRobot`,
the container will create just *one* private service and re-use it.

Some people will *love* this new feature and some people will *hate* it and complain
on Twitter. But it's cool: like most things, you're not forced to use it. But, if
you're doing some rapid application development, try it! It won't work in all cases,
and isn't able to inject configuration yet, but when it works, it can save time,
just like it did here for us.

Wield this new weapon wisely.

