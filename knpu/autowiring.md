# Service Autowiring

The next new feature in Symfony is called `autowiring`. It's a small way to make registering services
just a little bit easier. Normally when you need a service you do exactly this, give it a name, a class
and the arguments. What autowiring says is "maybe we don't need to give it the arguments". So remove the
arguments key and instead type `autowire: true`. 

Head back to the browser, refresh the login page, press the login button and we get the error "Username could
not be found". Normally hitting the login button in this instance should explode the page. In the background
it should be using our `weird_authenticator` and shouldn't be able to instantiate it and not work ... but it
does work! This is thanks to the new little key we added `autowire: true`. This is your way to opt into autowiring
for this service. Autowiring is done in the Symfony way where it's convenient but not magical. 

The way this works is via typehints. In `WeirdFormLoginAuthenticator` we're typehinting the first argument 
`EntityManager` and the second argument with `RouterInterface`. Behind the scenes autowiring goes and looks in
the container and says "could you please show me all the services typehinted with `EntityManager`" and since 
there is only one it knows to inject it. It's the same thing with `RouterInterface`, it sees that there is only
one service whose class name implements `RouterInterface` so it injects the Router object. If there are multiple
objects that implement a certain interface or have a class then you'll get an exception that says, "the autowiring
can't figure out which class to inject". In that case, there are ways for you to specify which objects you want
based on what is typehinted. 

The real beauty of autowiring is when it just works. The whole purpose of this is to save you time when you're 
rapidly developing something and getting it up and running. We can go even further than this. In the security
directory, create a new super important class called `EvilSecurityRobot`. And its job is going to be to have a
`public function doesRobotAllowAccess()` and what we're going to do is call this from within our `WeirdFormLoginAuthenticator`
and this evil robot is going to decide randomly whether or not the user should have access. Even if I put all my
fields in incorrectly The evil security robot could still say "NOPE! You're not getting in and I'm not sorry.
Get out of here! I'm evil." 

Ok this class is ready and now we know to use this we're going to inject it into our construct function. Type
`EvilSecurityRobot $robot` and I'll use my keyboard shortcut to add this as a property. Down here in `checkCredentials`
we'll say `if (!$this->robot->doesRobotAllowAccess())` then throw a really clear new `CustomUserMessageAuthenticationException()` that says "RANDOM SECURITY ROBOT SAYS NO!". And I'll even put quotes around
that. 

At this point our next step would be to go to `services.yml` and update the arguments key to inject the evil security robot.
But wait! The evil security robot itself is not even a service. So the real first step would be to make an evil security robot
service down here so that it can even be injected into the `weird_authenticator`. Surprise, we're not going to do either
of those things. Instead, we'll just refresh and it's going to work. For the username I'll put in weaverryan, and if you
try a couple of times there it is, "RANDOM SECURITY ROBOT SAYS NO!". This is really cool! 

Because the `weird_authenticator` is autowired it sees that the third argument is typehinted with `EvilSecurityRobot`. It
then sees that there are no services in the container for `EvilSecurityRobot` so it creates a private service in the
container automatically and injects that. This actually works pretty well, `EvilSecurityRobot` itself will be instantiated
with autowiring so if this has a couple constructor arguments it would try to autowire those. 

If you have multiple classes that need the `EvilSecurityRobot` it's only going to create that private service once. 
And then it will reuse that to inject it into all of the services that need `EvilSecurityRobot`. 

This is one of the more controversial new features in Symfony, but if you're doing rapid application development, try it!
It's not going to work in all cases, and we probably need to make it smoother for some edge cases in the future. But,
as you can see here this is the perfect example where it saved me some time, code and things ar working! 

Weild these new weapons wisely.





