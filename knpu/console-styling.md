# Console Styling

We all love ice cream and Symfony's console. We love the ability to add colors,
[beer shaped progress bars](http://knpuniversity.com/blog/fun-with-symfonys-console), 
tables and a bunch of other cool stuff.

A little feature snuck into Symfony 2.7 and was improved in Symfony 2.8. It's called
the `SymfonyStyle`. It's more than just a cool way to dress, it's the Twitter Bootstrap
for styling console commands. Ok ok, it's not a huge deal, but let's face it, that
blog post with the beer icon in the console? It's pretty much our most popular blog
post... so clearly you guys love this stuff.

Create a new `Command` directory. Inside, I'll take the lazy way road and have PhpStorm
make me a new command called `StylesPlayCommand`. Give it a name! `styles:play`. 

Start like we always do, with lame, worn-out `$output->writeln('boring')`. Zip over
to the terminal and try that out:

```bash
bin/console styles:play
```

And there it is! In all its white text on a black background glory.

## Introducing SymfonyStyle

Enough of that! That still works, it will always work. But now, create a new `$style`
variable set to `new SymfonyStyle()`. Pass it the `$input` and `$output`. This class
comes from a few friends of mine, [Kevin Bond](https://twitter.com/zenstruck) &
[Javier Eguiluz](https://twitter.com/javiereguiluz). This dynamic Canadian-Spaniard
team sat down and designed a good-looking style guide that can be used for all commands
in the Symfony ecosystem. As Javier put it, this is basically the stylesheet for
your commands. We use simple methods, and Javier makes sure it looks good. Thanks
Javier!

Ok, let's take this for a test drive. First, we need a big title:
`$style->title('Welcome to SymfonyStyle!')` and then a sub-header with
`$style->section('Wow, look at this text section');`. To print out normal text, use
`$style->text()`. I'll quote all demo pages on the Internet by saying "Lorem Ipsum Dolor"...
a bunch of times.

Because we're worried about all these lorem ipsums, use `$style->note('')` to remind
people to "write some real text eventually". Let's try it! Run:

```bash
bin/console styles:play
```

OooOOooooOOO. Without doing anything, Javier gives us the console equivalent of
`h1` and `h2` tags, with colors and separation. Below, it's subtle, but all the text
lines are indented with one space to make them stand out. The note starts with an
exclamation point and uses a hip color.

Get the idea?

Back on the command, add one more text line:
`$style->comment('Lorem ipsum is just latin garbage');`. Follow that with
`$style->comment('So don\'t overuse it')`. Make sure the method name is correct.
