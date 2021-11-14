---
layout: post
title: A symbol layer for the Ergodox EZ
date: 2020-09-12
assets_url: https://assets.glyx.co.uk/blog
---

> :zap: TL;DR: I dig through a bunch of data on my local machine to determine a
> good layout for the code symbols layer on my fancy mechanical keyboard.

I recently treated myself to an [Ergodox EZ](https://ergodox-ez.com/), a...
somewhat over-the-top split mechanical keyboard with programmable inputs and an
unconventional layout.

![The Ergodox EZ]({{ page.assets_url }}/my-ergodox.jpg)

One of the challenges of the EZ is that it has fewer keys than a conventional
keyboard. The EZ has 76 keys total; by comparison, my other (numpad-less) keyboard has 88.
Thus, typically users make use of the [keyboard firmware's](https://qmk.fm/)
layer feature to access less commonly used keys like square brackets (`[]`).
This is what [my base
layer](https://configure.ergodox-ez.com/ergodox-ez/layouts/wNPrq/PDn3J/0) looks
like.

![My base layout as of September 2020]({{ page.assets_url }}/ergodox-base-layout-2020-09-13.png)

However, I have been struggling with the second layer (the tab labelled
**Code** in the image above). Until recently, my code layout looked like
this.

![My previous symbol layout]({{ page.assets_url }}/ergodox-old-symbol-layout-2020-09-13.png)

The left hand in this layout gives me access to a number of symbols commonly
used in various programming languages along with the various kinds of bracket.
On the right I have a numpad (quick numpad access has been a gamechanger).

I had a couple of problems with this layout though:
 <!--alex ignore easy harder-->
 - The keys corresponding to `TGB` on the base layer aren't in use on this
   layer at all. The `G` and `T` keys in particular are easy for me to reach,
   but unused, while I regularly use harder to reach keys like `ZX`.
 - The position of the `|` and `\` keys kept throwing me for a loop.

# Getting data

My main criteria for a good layout are:
 - **Ergonomics**, of which my main concern is that the keys I use most often
   should be comfortable to reach
 - **Mnemonics**, or in other words, I need to remember where the keys are :)

In order to address the first criterion, I created a subjective rating of key
ergonomics for the middle 15 keys of the left hand side of the ergodox.

![Sketch of keyboard ergonomics ratings]({{ page.assets_url }}/my-ergonomics-rating.jpg)

With this established, I now need to determine what symbols I use the most. For
this, I used the `grep`-alike [ripgrep](https://github.com/BurntSushi/ripgrep) to
trawl through my cloned repos and shell history, followed by some unix shell
data munging to count up their occurences. After some experimentation, I came up with the following regex:

```
[^a-zA-Z0-9:;\.,\-_"'\s]
```

which matches all characters excluding alphanumerics, spaces, and some common
symbols that are available on the base layer. With a bit of annoying shell
escaping, I can wrangle that into this command:

```shell
$ rg -o --no-filename '[^a-zA-Z0-9:;\.,\-_"'"'"'\s]' ~/code \
>     | sort | uniq -c \
>     | sort -nk1,1
```

which prints out each character matching the regex on its own line, then uses
the very handy `| sort | uniq -c` to get a line per unique symbol along with a
count of the number of occurrences of that symbol. Finally, I `sort`
numerically (`-n`) by the first space-separated field (`-k1,1`) in the output
of `uniq -c`, i.e. the counts. This produces output which looks something like
this:

    # ...
    # A bunch of low frequency non-english unicode
    # ...
      7863 的
      8878 ä
      9507 ü
      9853 `
     11191 é
     11372 ^
     13451 。
     19494 %
     25504 @
     27108 |
     31907 ?
     32693 !
     40264 #
     43464 +
     50780 &
     52662 $
     68608 *
    136801 \
    148287 ]
    148389 [
    201254 <
    221447 =
    238412 >
    268755 }
    268769 {
    290736 /
    495030 (
    495247 )

I also repeat the process for my shell history. This second dataset is if
anything more important, as I spend a lot more time on a command line than I do
writing code:

```shell
$ rg -o --no-filename '[^a-zA-Z0-9:;\.,\-_"'"'"'\s]' ~/.zsh_history \
    | sort | uniq -c \
    | sort -nk1,1
```

with the result:

      30 `
      52 ?
      90 #
      96 &
     102 <
     111 +
     112 %
     139 >
     145 }
     147 {
     168 !
     213 ^
     225 *
     240 @
     266 )
     267 $
     277 (
     352 ]
     365 [
     381 ~
     473 =
    1193 |
    1484 \
    5456 /

(Fun aside: I spent about 20 minutes hunting down some rogue raw CTRLs in
my `zsh_history` to get that output not to look weird. You're welcome!)

# Fixing the layout

With that data in hand, back to the layout. I decided early on in this process
to limit myself to the 15 centre keys of the right hand half of the Ergodox. Of
those, I quite liked my existing layout for the brackets. It's memorable,
allows me quickly type pairs of opening and closing brackets, and is already
roughly ordered by most used bracket types: parentheses on the homerow, curly
braces above, and the slightly less common square brackets below.

This leaves 9 other keys to play with. Many of the symbols in the data above
don't interest me, because they are either:
 - readily available on the base layer (like `/`, `'`, `"` or `@`)
 - arithmetic symbols provided by the numpad (`*`, `%`, `=`)

Ruling these out, here are the 8 symbols I need, in rough order from most to least prevalent:
 1. `|`: the pipe, which I use a _lot_ in shell (if you didn't already notice)
 2. `\`: backslashes, the string escape character of choice
 3. `#`: hash, commonly used as a comment opener in scripting languages
 4. `$`: dollar sign, a variable indicator in bash and the regex end-of-line marker
 8. `` ` ``: the backtick, essential in markdown, useless elsewhere
 5. `~`: tilde, the shell alias for `$HOME`
 6. `^`: caret, the regex start-of-line marker
 7. `&`: and finally, apmersand, which I mostly use to background shell processes

Combining this ranking with my layout sketch above, I came up with this layout:

![My new symbol layout sketch]({{ page.assets_url }}/new-layout-sketch.jpg)

The most used symbols, `|` and `\`, get a spot right on the home row. `#`, `$`
and `` ` `` get the next most coveted locations surrounding and above the home
row. Though based on its heavy usage, `$` could have been placed in the
slightly more convenient keys to the right of the home row, its placement above
and to the left allows us to put it next to `^`, forming a handy mnemonic: `^`
is the regex start-of-line marker, so it make sense that it would sit to the
teft of `$`, the end-of-line marker.

Tilde gets a slightly awkward placement in the bottom left, which allows us to
use our free 9th key for the `/`, making typing the common path prefix `~/`
nice and quick.

Finally, the little-used `&` key gets the annoying to reach bottom right key as
its home.

![My final symbol layout]({{ page.assets_url }}/ergodox-symbol-layer-2020-09-13.png)

So there you have it. I hope you found my process interesting. This is likely
to evolve in the future! I have spent quite a lot of time tweaking layouts
since I got this keyboard. If you have thoughts on how I can improve this, hit
me up on [twitter](https://twitter.com/ouroborosglyx). Happy hacking!
