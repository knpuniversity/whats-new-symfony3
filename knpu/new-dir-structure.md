# Shiny new Directory Structure

Well hey guys! Symfony 3 is around the corner, ah, or maybe even released if you're
watching this later! Hello from the past!

Anyways, it's time to talk about all the new cool shiny stuff. But first, I have
a confession: Symfony 3 doesn't have *any* new features. Not even 1... and this is
amazing. Seriously, Symfony is paving the way towards a new paradigm of upgrading.
I'll talk about upgrading later. But in a nut shell, it means that you'll be able
to upgrade to Symfony 3 without rewriting your code and breaking everything.

## Introducing, Symfony 2.8!

When Symfony 3 is released, Symfony 2.8 will be released at the same time and both
versions will have the same features. That means that if you upgrade to 2.8, you'll
get all the good new stuff. So what's new in Symfony 3? Nothing! But there *are*
a lot of new great features in 2.8, and I'll tell you about them.

## The New Directory Structure

But wait! There *is* one thing that's new *specifically* in Symfony 3: the new Symfony 3
directory structure. You can see it in my editor. If you're an experienced user,
it's not a big change.

### var/

First, the `app/cache` and `app/logs` directories have been moved to a new `var/`
directory. The `bootstrap.php.cache` file also got moved here from `app/`.

### bin/console

Second, we all know and love `app/console`. Well, that's now `bin/console`:

```bash
bin/console
```

As a result, the `app/` directory is now *just* configuration code: stuff that we
can actually modify. All the things we don't normally need to worry about editing -
like the cache and `console` file - are out of the way. This gives `app/` a new focus.

## PhpStorm Symfony Plugin

If you're using PhpStorm with the Symfony plugin, you'll need to change a couple
of settings so that it knows to look in the `var/` directory for a few cache files.
You may not need to do this in the future - but double-check it. 

## How can I Upgrade to the New Structure

So, how can we upgrade *our* project from the old structure to this one? And do we
*need* to change our project?

Actually, no: this new structure has *always* been possible. In fact, open up `AppKernel`.
The *only* reason the new structure works is because the `getCacheDir()` and `getLogDir()`
methods have been overridden:

[[[ code('25b2b4b27f') ]]]

If you don't override these, they default to using `cache/` and `logs/` in *this*
directory: `app/`.

So in your project, if you override these then... boom! You're using the new directory
structure. Well, you'll also want to move the `console` file and a few other things.
I'll talk about upgrading later.

And if you *don't* want to use the new directory structure... then boom! Don't use
it. You're free to organize things however you want. Just be aware that the Symfony
documentation will be reference things like the `bin/console` and `var/cache` and
you'll need to translate to what that means in your app.

## New Tests Paths

There are two other tiny changes: the `tests` directory has been moved out of `AppBundle`
into the root of the project:

[[[ code('6783628b29') ]]]

And `phpunit.xml.dist` also now lives in the root directory. That means no more
`phpunit -c app`: just run `phpunit`:

```bash
phpunit
```

and it'll find the configuration file automatically.

That's it for the new directory structure, you can start using it right now, or leave
your project alone.
