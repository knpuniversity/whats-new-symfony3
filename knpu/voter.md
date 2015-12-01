# The new Voter Class

Create a new controller - `viewUserAction` - that will live at `/users/{username}`
with a route named `user_view`. Here's the challenge: we need to restrict who is allowed
to view this page based on some complex business logic. Maybe I can view only *my*
page... unless I'm an admin... who can view anyone's page. This is a classic situation
where security isn't global, it's dependent on the object being accessed. I can see
*my* user page but not *your* user page.

This is the perfect case for voters. I've been talking about these for years... and
they are *still* underused. Repeat after me, "I do not need ACL, I need voters".
And good news, voters are even easier to use in Symfony 3... I mean 2.8.

Instead of passing `$username` to the action, type-hint the `User` object. Thanks
to the FrameworkExtraBundle, Symfony will [query for the User automatically](http://knpuniversity.com/screencast/symfony-best-practices#the-in-famous-paramconverter-trick)
based on the `username` property.

## Using a Voter

Next, add `if (!$this->isGranted('USER_VIEW', $user)){}`. If this is not granted,
`throw $this->createAccessDeniedException('No!')`. If access *is* granted just
`dump('Access granted', $user);die;`.

The mysterious thing is `USER_VIEW`. We *usually* pass things like `ROLE_USER` or
`ROLE_ADMIN` to `isGranted()`. But you can invent whatever string you want: I made
up `USER_VIEW`.

### The Voter System

Whenever you call `isGranted()`, Symfony asks a set of "voters" whether or not the
current user should be granted access. One of the default voters handles anything
that starts with `ROLE_`. And guess what! You can *also* pass an object as a second
argument to `isGranted()`. That's also passed to the voters.

Here's the plan: create a new voter that decides access whenever we pass `USER_VIEW`
to `isGranted()`.

## Create the Voter

In the `Security` directory, create a new class called `UserVoter` and make this
extend `Voter`. This is a new class in Symfony 2.8 that's easier than the old `AbstractVoter`.

Use `command+n` to open the generate menu and select "Implement methods". The two methods
you need are `supports` and `voteOnAttribute`. This is a little different than before.

Now stop! And go register this as a service. Call it `user_voter` and add its class:
`UserVoter`. There aren't any arguments, but you *do* need a tag called `security.voter`.
As soon as we give it this tag, the `supports` method will be called *every* time
we call `isGranted()`, asking our voter "Yo! Do you support this attribute, like `ROLE_USER`
or `USER_VIEW`?".

I'm already logged in - so I'll head to `/users/weaverryan`. Access denied! Right
now, *none* of the voters are voting on this: they are *all* saying that they don't
support the `USER_VIEW` attribute. If nobody votes, access is denied.

## Adding Voter Logic

So let's code: In `supports()`, `if (attribute != 'USER_VIEW')`, then return
false. This says: "I don't know, go bother some other voter!".

Add another `if` statement. Wait! Change the argument to `$object` - this *is* the
object - if any - that's passed to `isGranted()`. Some now-fixed bad PHP-Doc in Symfony
caused that issue.

Anyways, `if (!$object instanceof User)`, then also return false: we only vote on
`User` objects.

Finally, at the bottom, `return true`. You can of course make your voter support
multiple attributes like `USER_EDIT` or even multiple objects. I usually have one
voter per object.

If you return true from `supports()`, then `voteOnAttribute()` is called. This is
where you shine: do whatever crazy business logic you need to and ultimately return
true for access or false to deny access. The `$attribute` and `$object` are the same
as before and `$token` gives you access to the currently-logged-in user.

Instead of adding real logic, let's let `EvilSecurityRobot` decide our fate. Add
a `__construct()` method with `EvilSecurityRobot` as the only argument. Create a
property and set it.

In `voteOnAttribute()`, `return $this->robot->doesRobotAllowAccess();`. Finally,
update the service in `services.yml` for the new argument. But take the lazy way
out: set `autowire: true`. Head to the brower and try it!

Hey, access granted! Refresh again, access not granted! Again! Not granted, again,
granted! The `EvilSecurityRobot` is hard at work causing us problems with its evil
access rules.

Ok, tl;dr: use voters, they're easier than ever and you can do whatever crazy business
logic you need.
