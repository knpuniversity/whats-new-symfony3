# AbstractVoter

Suppose we have a new page, `viewUserAction` which will be at `/users/{username}` with
a route name of `user_view`. In our example we don't want everyone to be able to view this 
page, we only want that user to view their own page unless their an admin then they'll have
extra page viewing permissions. This is a classic situation where you need security authorization
that's dependent on which user you're dealing with. 

This is the perfect case for voters. I've been talking about these for years and I still think
that they are underused. Repeat after me, "I do not need ACL, I need voters". Even better news,
voters are even easier to use in Symfony 3 (and 2.8). 

Instead of passing `$username` to this method I'll just typehint our user object. And thanks to
the `FrameworkExtraBundle` Symfony will just query for that for me with the username property.
Very cool! In here let's add `if (!$this->isGranted('USER_VIEW', $user)){}` and if this not granted
we'll `throw $this->createAccessDeniedException('No!')`. If access is granted let's just `dump('Access granted', $user);die;`.
The mystery here is the `USER_VIEW` because with `isGranted` you usually pass things like `ROLL_ADMIN`
or `ROLL_USER`. But you can invent whatever other string you want, I made up the `USER_VIEW`.
Whenever you call `isGranted` it goes into what is called a voter system. By default there's a
voter inside of Symfony that handles anything that starts with `ROLE_`. But, we can pass the string
`USER_VIEW` and add our own voter that will decide whether the currently logged in user has access to
view this user object. We could do this for anything, like say `BLOGPOST_EDIT, $post`, it's up to you
and your imagination.

In the security directory I'll create a new class called `UserVoter` and make this extend `AbstractVoter`
which is a class that was added back in Symfony 2.6, but we made it a lot better in Symfony 2.8. 

Use `comand+n` to open the generate menu, select implement methods. The two methods you need to implement are
`supports` and `voteOnAttribute`. This is a bit different than it used to be. 

Before we do anything else let's go into `services.yml` and register this as a service. How about
`user_voter`, class is `UserVoter`, we don't need any arguments but we do need a tag called `security.voter`.
As soon as we give it this tag, the `supports` method is going to be called on every single request
where we call `isGranted` asking our voter "Yo! Do you support this attribute, `ROLE_USER` or `USER_VIEW`?". 

Let's give this a try, fill in our form fields here check the box and login. Now that we're logged in as
weaverryan, we can go to `/users/weaverryan` and we get denied acccess. Right now all of the voters are
abstaining, they are saying that they don't support the attribute that we are using in here to set `USER_VIEW`.
By default, it's telling us that no voters know how to vote on it so access is going to be denied. 
Let's get the logic filled in here, `if (attribute != 'USER_VIEW'){ return false;}`, this basically says
don't ask us ask someone else. Also, `if (!$object)`, oh wait this should be `$object` up here. Ok back to our 
if statement, `if (!$object instanceof User){return false;}` because we only vote on user objects. Finally, at
the bottom `return true;`. You could make your voters support multiple attributes like `USER_EDIT` or even
multiple objects though I usually have one voter that makes all the decisions for all of the objects. 

If you return true from `supports`, then `voteOnAttribute` is called and you just need to return true for
access or false to deny access. The attribute and object here are the exact same attribute and object that
you pass to `supports` and the token gives you access to the currently logged in user if you need it. 

Let's do something really simple and ask our evil robot class whether or not we have access. To do that, of
course we'll need to inject it through the constructor `EvilSecurityRobot $robot` and then use initialize fields
to setup as a property. 

Down here instead of doing something intelligent I'll just say, `return $this->robot->doesRobotAllowAccess();`.
The last step to hook this up is back in `services.yml`. We now have arguments we need to pass, but I'll take
the lazy route and juse `autowire` this. Symfony will figure out what to pass into my user voter for me based
on this type hint, which we covered earlier. Let's head back to the browser and refresh. 

Hey, access granted! Refresh again, access not granted! Again! Not granted, again, granted! There's our evil 
security robot at work figuring out whether or not we have access. 

Use voters, they are easier than ever and you can do whatever crazy business logic you want inside of 
`voteOnAttribute` to figure out whether or not the current user has access to view, edit or delete whatever
object in the system.


