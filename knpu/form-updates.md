# Form Changes

The form system also got some changes in the newest version of Symfony.
These weren't fundamental changes, but they are definitely interesting.
Create a registration form so I can show these updates off.

Go straight to creating a form type with a form directory and inside of
that use the PhpStorm shortcut to create a `RegistrationForm` which gives
us this nice skeleton. Interestingly, I want you to get rid of the `getName`
function at the bottom. This hasn't been needed since Symfony 2.7, so it's
something you can remove from your projects as you upgrade. This leaves us 
with `buildForm` and `configureOptions`.

For the most part though, things look the same. In `configureOptions` call
`$resolver->setDefaults` and pass it the `data_class` since this is a registration
form. Set that to `AppBundle\Entity\User` to create a new user class. My user
only has one field on it, username, which makes this one of the most ridiculously 
useless registration forms in history. We'll need to add our one field with
`$builder->add('username')` and here is where things are different. Before I would
have passed `text` as the second argument here. Now, I'm going to instead type the
full class name to the class behind the text field type. 

To make things easier I'll use a shortcut to do that. `TextType::class`. Of course
when we do that we'll need a use statement for the `TextType` and unfortunatley 
PhpStorm doesn't autocomplete it yet as I type it because there is nothing else
static on that class. But if you focus here you'll see a little lightbulb that
allows you to import the class. Or if you're lazy, you can hit `option+enter`
on a mac and import class will show up and you can grab the one from the Symfony
form compontent. 

That's big thing number one, no more weird strings, instead we have full class names.
The downside to this is that this will be a little bit longer, but if you use the
autocomplete functionality it's really not a big deal. The benefit is that the strings
like text were really abstract. It wasn't clear to users what that actually referred to.

With this we can see that the functionality is coming from this class. We could hold the
command key and click into it if we wanted to figure out how this particular text type works.

With this done head into the `DefaultController` to create a new registration page for our form.
Create a new `registerAction`, set the URL to `/register` and we'll call the route `user_register`.
Start just like normal with `$form = $this->createForm()` but instead of saying `new registrationForm`
type `RegistrationForm::type`. Move your cursor back over here, hit `option+enter` and eventually that
will give you the import class. Oh and change that from `type` to `class` duh!

The important thing is that when I imported it that added the use statement up top for our registration form.
Not only do we not use strings anymore you also don't pass it objects, you only ever pass it the class name.
A result of this is that the registration form is going to be created and passed no constructor arguments. So,
you can't have a construct funtion to your registration form anymore. 

If you need to pass something to your registration form to configure it or some objects that it actually needs
you can pass it in through the `$options` which would be the third argument when you actually create a
form class. Or you still can of course register your form type as a service. That's the real answer if you had
a form like `registrationForm` and it needed some outside dependencies you can still have a construct funtion.
But then you would need to register it as a service and tag it with `form.type`. 

Add the `$request` as an argument and then add the normal `$form->handleRequest($request)`. `if ($form->isValid())`
for now just `dump($form->getData());` and put a die statement at the end. 

Finally, at the bottom `return $this->render('default/register.html.twig')` to render the twig template.
And we'll pass it our form with `'form' => $form->createView(),`. Simple enough! 

In `app/resources/views/default` create the `register.html.twig` file to go with our return. Make this new file
extend `base.html.twig`, add in the block body tags, an h1 of "You should signup!" and now I'll render the form
in as lazy of a way as I can think of with `{{form_start(form)}}`, `{{form_end(form)}}`, to render all the fields
at once plug in `{{form_widget(form)}}` and manually add a submit button with a few bootstrap classes to make it
look nice. 

Let's try out our form handywork! Back in the browser head to the /register page, throw in our username of `leannapelham`
and hit register. There's our dump!

Moral of the story, forms work exactly as they did before. But now, form classes are always the string class name. 
Never objects, never the weird little formatted text type thing. 

A corallary to this if you think about it enough is that you can no longer replace custom form field types. For example,
before there was a text type and if you registered a new service and aliased your type with text type it would replace
the old one. 

Intentionally that is not possible anymore. If you want to replace something in a core type, you need to use a form
type extension. That's the proper way to plug into the form type system and change whatever you want inside of the core.

This will be one of the biggest changes for people, but fortunately it's mostly superficial. 
