# Console Styling

We all love icecream and Symfony's console command ability. We even love
the ability to add colors, [beer shaped progress bars](http://knpuniversity.com/blog/fun-with-symfonys-console), 
tables and all kinds of cool stuff.

A little feature snuck into Symfony 2.7 and was improved for Symfony 2.8 and it's called the
Symfony Style. It's more than just a cool way to dress, it's the twitter bootstrap for styling
inside of console commands. Not a huge deal, but you need to know about it because it'll make your
console commands way more awesome with less work. 

Create a new command directory and inside of there I'll take the lazy way and use the `command+option`
shortcut and make a new command called `StylesPlayCommand`. Give it the name `styles:play`. 

Start like we always do, with `$output->writeln('boring')` what an exciting message. Zip over to the
terminal and try that out. `./bin/console styles:play`. And there it is in all of its glory. 

Enough with that! That still works, it will always work. Instead, create a new `$style` variable
set to `new SymfonyStyle()` and pass it `$input` and `$output`. `SymfonyStyle` is from a few friends
of mine, Kevin Bond & Javier Eguiluz. This dynamic Canadian-Spaniard team sat down and discussed how
to get a consistently good looking style with the console commands in the entire Symfony ecosphere and
make it really easy to do. As Javier put it, this is basically the stylesheet for your commands. We can
just write methods and it's just going to look good. 

Let's look at some of the basics, like `$style->title('');`. We'll want to start this off with something
big like "Welcome to SymfonyStyle!". Below that put a subtitle `$style->section('Wow, look at this text section');`.
Below we're going to use a bunch of different methods that just print out text in different ways. Of course,
the easiest one is going to be `$style->text('')` and I'll add the most famous of quotes "Lorem ipsum dolor"
and paste that a few times.

In addition to normal text there are a couple of other ones you can do as well, like, `$style->note('')` because
we're worried about all the lorem ipsums, so we'll remind people to "write some real text eventually". Before
we go any further let's try this out, because I want you to see how it's rendering these things. 

Over in the terminal run `./bin/console/styles:play` and there it is! Basically it looks like we have an h1 on top,
followed by an h2 with some nice coloration between the sections. And below our two headers the text is 
indented by one space which may seem subtle but the idea is that the header will be all the way to the left
and everything else will be indented by one space including our little note here which has an exclamation point
and hip color.

Back in the IDE let's look at one more basic style, `$style->comment('Lorem ipsum is just latin garbage');`.
Follow that with a little command `$style->comment('So don\'t overuse it')`. And make sure the method name
is correct. 

Let's run this again in the terminal, and we see that our comments show up with the one space and the //. 
So, it may not be that big of a deal, but it's cool that you can call these methods and have styling
happen. 

Time to take the fancy up a notch, enter down to another section and add `$style->section('How about some BIG messages?');`.
There are options in here to display messages that emphasize "things are great!", "things are terrible! or "caution".
Let's start with `$style->success('I <3 lorem ipsum');`. I can't wait let's go see how this looks in the 
terminal. Look at that nice big green message, it just feels like a celebration!

Ok, maybe we don't love lorem ipsum so we'll send a warning with `$style->warning('You should maybe not use lorem ipsum');`
Let's see how this one renders in the terminal. Wow, check out that sweet red message - you've been warned.

Seriously guys, enough with the latin. Let's try `$style->error('You should stop using lorem ipsum');`. And
let's throw in one last word of caution, `$style->caution('Stop using it seriously!');`. When we run this, 
we've got four blocks all styled for their meaning with nice spacing, coloration and margin. 

There are a few other things that we could do with styling, like a progress bar and a few items to help with
lists. Let's take a look at those with a new `$style->section('Some tables and lists?');`. Let's start with
the table. There's already ways to make tables inside of the console, but style has a shortcut for this. 
`$style->table()`, the first argument to this will be the headers, `['User', 'Birthday'],` and the second argument
is the rows. So we'll plug in an important birthday to remember, `['weaverryan','June 5th']` and an even
more important one to remember, `['leannapelham','All of February']`. Let's see how this renders in the terminal.

Wow, look at that nice table, nice spacing and styled headers. Of course you could do this all yourself, but why
when this does it for you without any effort.

Let's wrap this up by listing out a few of my favorite things, kind of like an Oprah list. 
`$style->text('Ryan\'s my favorite things')` with a nicely styled list. `$style->listing([])`, this is just an array 
which you can think of as an unordered list. Let's see, I like `['Running', 'Pizza', 'Watching Leanna tease Jordi Boggiano',]`. Ok last run in the terminal, check out that awesome list. 

There you have it, the SymfonyStyle - enjoy!





