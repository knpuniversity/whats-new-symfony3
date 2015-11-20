# Console Styling

We all love ice cream and Symfony's console. We love the ability to add colors,
[beer shaped progress bars](http://knpuniversity.com/blog/fun-with-symfonys-console), 
tables and a bunch of other cool stuff.

A little feature snuck into Symfony 2.7 and was improved in Symfony 2.8. It's called
the `SymfonyStyle`. It's more than just a cool way to dress, it's the Twitter Bootstrap
for styling console commands. Ok ok, it's not a huge deal, but let's face it, that
blog post with the beer icon in the console? It's pretty much our most popular blog
post... so clearly you guys love this stuff.

Create a new `Command` directory. Inside, I'll take the lazy road and have PhpStorm
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

Run it again!

```bash
bin/console styles:play
```

More indented text, this time with a little bit different styling.

## Success and Error Messages

Time to take the fancy up a notch! Add another section for some BIG messages.
SymfonyStyle has built-in methods to emphasize that "things are great!", "things are terrible!
or "OMG things are *really* terrible". Start with `$style->success('I <3 lorem ipsum');`.
Try it!

```bash
bin/console styles:play
```

A big ol' nice green message that just screams celebration.

Ok, maybe we don't love lorem ipsum. Send a warning with
`$style->warning('You should maybe not use lorem ipsum');` Try it!

```bash
bin/console styles:play
```

Now we have a menacing red message: you've been warned...

Next, try `$style->error('You should stop using lorem ipsum');`. And throw in one
last word of caution, `$style->caution('Stop using it seriously!');`. When we run this, 
we've got four blocks: all styled for their meaning with nice spacing, coloration
and margin. Thanks Javier!

## Tables and Lists

The `SymfonyStyle` has helpers for a few other things, like progress bars and tables.
Create a new section: `$style->section('Some tables and lists?');`. Creating tables
isn't new, but `$style` has a shortcut where Javier styles them for us. Thanks Javier!

Use `$style->table()`. The first argument holds the headers: `['User', 'Birthday'],`
and the second argument holds the rows. Plug in an important birthday to remember:
`['weaverryan','June 5th']` and an even *more* important one to remember,
`['leannapelham','All of February']`. That's right, the celebration for Leanna never
ends.

So let's see how this renders!

```bash
bin/console styles:play
```

Wow, look at that table: nice spacing, nice styled headers. Of course you could do
this all yourself, but why?

Ok, time for just *one* more: a list of my favorite things:
`$style->text('Ryan\'s my favorite things')` with a nicely-styled list. Use
`$style->listing([])`. Pass this an array, which you can think of as an unordered
list. Let's see, I like `['Running', 'Pizza', 'Watching Leanna tease Jordi Boggiano',]`.
Ok last run in the terminal!

```bash
bin/console styles:play
```

There you have it, the `SymfonyStyle`. Thanks Javier! Seriously, Javier is a cool dude.
