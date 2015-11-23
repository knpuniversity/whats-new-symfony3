# Form Updates

I know, I know, everyone *loves* it when the form system changes and breaks everything.
Well, get ready: there are more changes in Symfony 2.8. And at first, they're a little
shocking. But I think you'll like them: it's a debateable step towards simplification.

Let's create a registration form using this mysterious *new* stuff. Add a `Form`
directory and create a `RegistrationForm` class inside. PhpStorm gives me a nice
skeleton for the class.... *but* now it has too much. Remove the `getName()` method
at the bottom. This was always a useless, but required function. But no more!

For the most part, forms look the same. In `configureOptions` call `$resolver->setDefaults()`
and pass it `data_class` set to `AppBundle\Entity\User`. My super-simple user has
just *one* field on it: `username`, which makes this one of the most ridiculously 
useless registration forms in history.

## Form types as Class Names!

Add our one field with `$builder->add('username')`. But stop! Here is where things
are different. Before I would have passed `text` as the second argument. But now,
I'm going pass the full class name to the class that's *behind* the `text` field type.

To make things easier, use a shortcut: `TextType::class`. PhpStorm doesn't auto-complete
classes when you do this... yet. But if you focus on the class, a lightbulb will
allow you to import the class. Or hit `option+enter` on a Mac to bring it up immediately.
This adds the `use` statement. Using `::class` is new in PHP 5.5... so this change
won't be much fun unless you can use this. But, you can keep using the old syntax
until you switch to 3.0... which requires 5.5 anyways.

Phew! That's big thing number one: instead of weird strings, we have full class names.
This sucks because... well, this is a little bit more typing. But this is *saweet*
because a class name is more meaningful than a string like `text`. If this was your
first time using Symfony, you'd have a better chance of understanding how things work.
And I really like that.

## Create your Form: Still a Class Name

Time to use this! Open `DefaultController` and create a new `registerAction`. Set
the URL to `/register` call it `user_register`. Now, start just like normal with:
`$form = $this->createForm()`. But stop! I know you *want* to say `new RegistrationForm()`.
Instead, type `RegistrationForm::class`. Then, move your cursor to the class, hit
`option+enter` and import the class.  Oh and change `type` to `class`, duh! Importing
the class added the `use` statement on top.

So wow, not only do we *not* use magic strings anymore, we also *never* pass objects.
It's perfectly consistent: we *always* refer to forms and fields using the full class
name. There's an important consequence: the `RegistrationForm` will be created and
passed *no* constructor arguments.

## Form Type Constructor Args?

If you need to pass something to that object, you have two options - neither of which
are new. First, you can pass stuff through the `$options` array: the third argument
to `createForm()`. Second, you can still - like always - register your form type
as a service. If `RegistrationForm` has constructor args, that's the real answer:
register it as a service and tag it with `form.type`. You'll *still* refer to it
via the class name, but Symfony will use your service instead of creating a new object.

Add `$request` as an argument and then add the normal `$form->handleRequest($request)`.
`if ($form->isValid())`, for now `dump($form->getData());` and put a die statement
to celebrate.

Finally, at the bottom, `return $this->render('default/register.html.twig')`. Pass
it the form with `'form' => $form->createView(),`. Simple enough and the same as
always!

In `app/Resources/views/default`, create that `register.html.twig` template. I'll
copy in some boilerplate code for this form: none of this has changed. This opens
the form tag, dumps out all the fields, adds a submit button and closes the form.

Time to test it! Head to `/register`, throw in our username: `leannapelham` and hit
register. There's our dump!

Here's the moral of the story: forms work exactly like before. But now, form classes
are *always* referred to by their class name: never objects and never the short, but
weird magic string from the past.

This might be the biggest change in Symfony 3 that people will complain about, and
rightly so: if you have a lot of forms, this is a lot of changes. For that, check
out the [Symfony Upgrade Fixer](https://github.com/umpirsky/Symfony-Upgrade-Fixer):
a library that can automate some of the tasks of upgrading, including this one.
Oh, and it's made by my friend Saša, and he's a really cool dude.
