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

Another result of this
