---
title: "German for Programmers"
date: 2019-02-03T21:37:26+01:00
draft: false
---

After 2 years of learning German I've noticed that, for the most part, you can
go a long way by mapping foreign concepts to ones that you already know. In
particular, I've had success mapping aspects of German grammar to programming
concepts I use every day. After all, programmers deal with weird grammars all
the time, why not take advantage of that skill?


### Gendered nouns: forward error correction

Probably the first difficulty people run into when leaning German is memorizing
the gender of each noun and [properly declining][graffiti] that noun's articles
when used in a sentence. While the grammar is mostly regular here, two things
make this challenging: the nonsensical assignment of genders to nouns[^genders]
and the sheer amount of memorization required to select the correct declension
once the gender of the noun is known.

While no amount of explanation can fully console someone going through this
process, I've made peace with gendered nouns by treating them as a form of
[forward error correction][fec]. Forward error correction is an encoding
technique that adds extra "redundant" information to a message such that if
parts of the message are lost or garbled, the extra information can be used to
reconstruct the original message _without_ having to ask the sender to
retransmit.

If you've ever had to attach words to letters to spell something complicated
over the phone (S, T as in Tom, I, P as in Peter, I, C as in Charlie, ...) you
understand forward error correction. Underlying these methods
are an agreed-upon set of rules that both sender and receiver know ("I'm going
to say a word that we both know, and the letter I'm trying to communicate to you
will be the first letter in that word"). A more formal "code" would involve
agreeing to always use the [NATO phonetic alphabet][nato] when spelling
things out over the phone. When using this scheme, if you heard
"{garbled}-ELTA," you can guess that
the sender said "Delta" because the rules were such that the sender couldn't
have said anything else (no other letter's pronunciation ends in "elta").

Computers use a more complicated version of this to
protect bitstreams against different kinds of errors.
The "rules that both sender and receiver know" can be
[Hamming Matrices][hamming] [^hamming-explain],
[Reed-Solomon polynomials][reed-solomon] [^reed-solomon-explain],
[Markov Chains][convolutional] [^convolutional-explain], or modifications and
combinations of all of these.

The genders of individual nouns and the grammatical rules to decline articles
and adjectives using those genders constitute _a shared set of rules that the
sender and receiver both know_. It may not approach the
[Shannon limit][shannon], but German gendered nouns form a weird, organically
developed forward error correcting code. This might be a convoluted
example, but imagine trying to hear the difference between _Rat_ and _Rad_ over
a bad phone line. Your brain has to do this kind of reconstruction all the time,
you're just not usually conscious of it! "Gib mir das Ra{d,t}" makes the
distinction clear -- a German speaker would decode it as _das Rad_ immediately,
because that "just sounds right." The declinations are just parity bits in the
sentence, and whatever "just sounds right" is actually an error-free solution
to a parity calculation.


### Many German verb constructs are just context managers

The earliest "weird" form of German grammar you usually encounter is the
_trennbares Verb_. Much like "turning on a switch" is different than
"turning a switch,"
"Ich schalte den Schalter ein" is different than "Ich schalte den Schalter." The
main difference is that in English the rest of the sentence continues after the
"on," while in German the "on" always has to come at the end of the clause. So
while in English we would say "I'm calling up a friend with my new phone," in
German you would say "Ich rufe einen Freund mit meinem neuen Telefon an."
Loosely translated, this would be "I'm calling a friend with my new phone up."

The fact that the _an_ comes at the end of a conjugated clause using _anrufen_
works the same way Python's [context managers][context-manager]
work[^other-lang-context]. Context managers take a bit of
explaining if you're not used to them, but they are a powerful way to harness
the [`finally`][finally] clause in Python. `finally` lets you execute code when
a code execution leaves a certain scope. The most-often used context manager is
in the `file` object; it ensures that the file is closed when the object goes
out of scope so you can't forget to close it.

```python
with open('workfile') as f:
    read_data = f.read()
```

replaces

```python
f = open('workfile')
try:
    read_data = f.read()
finally:
    f.close()
```

In addition to cleverly eliminating errors regarding forgotten cleanup and
further consolidating code, context
managers help ensure the correct ordering of cleanup code. This is because
as context managers are combined, they maintain a LIFO ordering -- the innermost
context manager's exit code is executed first, followed by the next innermost,
until the entire stack has been executed.

This LIFO ordering can be seen with more complicated "context-manager-like"
German verb constructs. Take a _Modalverb_ such as _können_ ("is able to")
combined with the dependent clause created by _ob_ ("if"). Both _ob_ and
_können_
require that the verb comes at the end of the clause involving it -- "Ich weiß
nicht, ob der Zug kommt" ("I don't know if the train is coming"). When using
both _ob_ and _können_ together, the LIFO ordering reveals itself: "Ich weiß
nicht, ob sie tanzen kann."
Here, _können_ ends its clause with _tanzen_, and _ob_ ends its clause with
_können_. Since they're chained, this means that _können_ itself goes at the
end, and its clause comes _before_ it (this is determined by _ob_, take a look
at the _Zug_ example again).
Visually, it becomes "ob [(sie tanzen) kann]," where () signifies the
scope for _können_ and [] signifies the scope for _ob_. In both scopes, the
relevant verb comes at the end, and the ordering follows the inverse of how
the scopes are introduced.

Theoretically this can go even further, although it becomes quite complicated to
parse. Generously contributed by [Matthias Görner][matthias], the sentence
"Ich stimme dem
Maler, der die Meinung, dass Rot keine Farbe ist, vertritt, zu" layers a
_trennvares Verb_ (_zustimmen_), a relative clause (_der ... vertritt_), and a
dependent clause (_dass ... ist_) to demonstrate the LIFO ordering. The
translation is "I agree with the painter, who represents the opinion that red is
not a color."


### Prepositions claim different areas of 'cognitive space' in English and German

In 2010, the xkcd author ran a color survey looking for similarities in how
people described colors. Out of that came a chart mapping a continuous color
space to discrete English words[^colors]:

{{< figure src="/static/xkcd_colors.png"
    title="clusters of English names for colors"
    width="70%"
    link="https://blog.xkcd.com/2010/05/03/color-survey-results/"
>}}

More detailed analyses have been done by [Lindsey and Brown][lindsey-and-brown]
and [Kuriki et al.][kuriki] for American English and Japanese respectively
(thanks to [Alice Boxhall][alice] for helping me find these!). The most
striking part of this work is that Japanese speakers are far more likely to
consider "light blue" to be a basic color term than English speakers would:

{{< figure src="/static/raw_colors.png"
    title="colors participants were asked to name"
>}}

{{< figure src="/static/english_colors.png"
    title="colors that American English speakers were most likely to assign"
>}}

{{< figure src="/static/japanese_colors.png"
    title="colors that japanese speakers were most likely to assign"
>}}

Just as our word "blue" covers both "blue" and "light-blue" in Japanese[^teal],
so do English prepositions cover a different "semantic space" than German
preopsitions. Take _bei_: according to [Pons][pons-bei], depending on the
situation _bei_ can function like the English prepositions with, for, in, to,
near, by, among, and during. Talk about multi-purpose!
[_auf_][pons-auf] has the same problem, and it doesn't help that _auf_ is one
of the preopsitions that [change meaning][two-cases] depending on which case
it's used with.

This became infuriating as I would memorize the translation given by the
dictionary, then stare in disbelief at _auf_ being used for both "The paper is
on the table" and "We speak in English" ("Das Papier liegt auf dem Tisch" and
"Wir sprechen auf Englisch").
The best advice I have is to ignore
the English translations and senses entirely. _um_ simply isn't "around," it
covers a different shape over the semantic space and should be treated on its
own. Don't pay attention to translations, don't try to "guess" how it might work
based on prepositions you know, just learn them afresh (usually by memorizing
which prepositions often go with which verbs, or memorizing common phrases like
"_auf der Hand_").


### Declined 'the's are just how German does kwargs

Most of the toil around early German involves learning how to properly decline
articles. While the [genderedness](#gendered-nouns-forward-error-correction)
causes its own problems, even the declination rules themselves can take time to
learn. The advantage of having declined articles is that subject/object order
isn't fixed.

For example, take the English sentence "The man throws the ball to the dog."
There's no way to rearrange the sentence to have the same meaning without adding
extra punctuation or words -- "the ball throws the dog to the man" has a
completely different meaning. The best you can do is maybe "to the dog, the man
throws the ball" but that extra comma changes the structure a bit. In German,
all three nouns are masculine and would nominally be _der Mann_, _der Ball_, and
_der Hund_. However, each _der_ changes whether the word is a subject
(_Nominativ_), direct object (_Akkusativ_), or indirect object (_Dativ_).
This means that the above sentence in German could be "Der Mann schickt dem Hund
den Ball" (I'm using _schicken_, "to send," here because it makes the grammar
simpler). Because each "the" is declined (_der_, _den_, and _dem_), German
allows you to be more flexible with where you put your subjects and objects
around the verb.

There are [additional rules around object placement][word-order] which restrict
some permutations, so ultimately it's possible to write the same sentence three
ways:

1. "Der Mann schickt dem Hund den Ball."
1. "Den Ball schickt der Mann dem Hund."
1. "Dem Hund schickt der Mann den Ball."

Each way can be used to emphasize a different object, but they all mean the same
grammatically.

In Python, a function can be called with positional and keyword arguments.
Usually positional arguments are specified in the signature, but unlimited
'extra' positional arguments and keyword arguments can be collected by convention
in an "`*args`" array and a "`**kwargs`" dictionary respectively[^kwargs].
`kwargs` have the nice property that they can be given in any
order[^kwarg-ordering], but the price is that you have to refer to them by
keyword:

```python
def ball_thrower_pos(subject, direct_object, indirect_object):
    """This uses English-style positional ordering."""
    print(f'The {subject} throws the {direct_object} to the {indirect_object}.')


def ball_thrower_kwargs(**kwargs):
    """This uses Germanish free-form keyword ordering."""
    subject = kwargs['subject']
    direct_object = kwargs['direct_object']
    indirect_object = kwargs['indirect_object']
    print(f'The {subject} throws the {direct_object} to the {indirect_object}.')


# These print the same thing
ball_thrower_pos('man', 'ball', 'dog')
ball_thrower_kwargs(subject='man', direct_object='ball', indirect_object='dog')
ball_thrower_kwargs(direct_object='ball', indirect_object='dog', subject='man')
ball_thrower_kwargs(indirect_object='dog', subject='man', direct_object='ball')
ball_thrower_kwargs(subject='man', indirect_object='dog', direct_object='ball')

# This does *not* print the same thing
ball_thrower_pos('ball', 'man', 'dog')
```

Note how the positional one loses functionality but requires less typing. You
need *some* kind of information to differentiate in which sense a word is to
be used, and each way has a tradeoff. English chooses to use position for
simplicity and loses flexibility, German chooses to use declination for
flexibility but loses simplicity.


### Position 2 is just a delay slot

In normal sentences, German verbs stay in "Position 2," meaning right after the
first clause. Directly translated, this can sound quite weird! "The dog goes to
the vet tomorrow" could become "tomorrow goes the dog to the vet." There are
still [kernels of this in modern English][v2] but in general it can take a long
time to get used to the fixed aspect of German word order.

Thinking of [delay slots][delay-slots] in older computer architectures helped me
conceptualize Position 2 a little better. Processors need to determine where
to read instructions after a branch (say, which side of an if statement to
execute). In the case of pipelined processors, this calculation can be blocked
while waiting for a previous value to become computed[^branches]. Delay slots
were used by pipelined CPUs to solve this problem before modern
[out-of-order execution][oooe] methods rendered them unnecessary. The older
processors were unable to look ahead to compute possible branch targets, so
they forced the developer to give a one or two cycle "heads up" that
a branch was coming. The processor used this time to compute the jump targets
with the
caveat that the programmer couldn't insert [hazards][hazards] in the delay slots
-- that would defeat the entire purpose of the delay.

I see verbs staying in Position 2 the same way: they guarantee your brain gets a
heads up on what action is about to happen, even if you have a long flourishing
adverbial or subject clause. "On Fridays I eat pizza" becomes "On Fridays eat
[hey the subject's coming later but just fyi the subject is eating] I pizza."
It's possible to get lost in
[complicated German sentences](#many-german-verb-constructs-are-just-context-managers),
so forcing the important bits up front just seems helpful.

[^branches]: If a processor needs to know which side of `if (x + 4) > 5` to
    execute, it can't do anything until it knows what `x + 4` is. This can be a
    problem in pipelined processors where most of the speed gains come from
    starting an instruction before the instruction before it has finished.
    Immense performance gains can be had if you use tricks to "fill up" the
    downtime while you're waiting for a critical instruction to finish. For
    example, if you have
    ```
    y = 5 + 6
    if (x + 4) > 5:
        print('hello!')
    else:
        print('goodbye!')
    ``` you can start computing `(x + 4)` _before_ `y = 5 + 6`, so that the
    answer is ready when the if statement needs to determine which `print`
    statement to execute. You're just rearranging when things execute to make
    sure there is as little downtime as possible.

[^colors]: Remember, what we call "red" [maps][hyperphysics] to a range spanning
    625-740nm, and that precise range might change person to person.

[^convolutional-explain]: Convolutional codes repeatedly mix new data with
    previously emitted data. There are a few ways to decode this, but the
    [Viterbi algorithm][viterbi] is pretty cool -- you use dynamic programming
    to reconstruct the input (represented as a path through a Markov chain)
    that was most likely to have generated the observed output (the convolved
    and emitted data). This works even if that output was emitted unreliably.

[^genders]: _Der Junge_, boy, is masculine. _Das Mädchen_, girl, is neutral.
    _Die Männlichkeit_, masculinity, is female.

[^hamming-explain]: Mix the bits into overlapping checksums so if one checksum
    fails, you can reconstruct the original bit from the other checksums.

[^kwarg-ordering]: [PEP 468](https://www.python.org/dev/peps/pep-0468/),
    introduced in Python 3.6, preserves the keyword argument order in the
    dictionary given to the called function. This enables cases where that might
    be useful.

[^kwargs]: `kwargs` is pronounced like the fictional word "quargs," and any
    other pronunciation is incorrect.

[^other-lang-context]: Other langauges have constructs like this. Go has
    [defer][go-defer], Rust has [scopeguard][scopeguard], C++ has
    [RAII][raii], and Scheme has [call-with-input-file][call-with-input-file].

[^reed-solomon-explain]: Encode the data as a polynomial, then send points that
    sit on that polynomial such that you can reconstruct the polynomial from the
    points received. By sending way more points than necessary you can correctly
    reconstruct the original polynomial even if some points are missing or
    altered.

[^teal]: It should be noted that in the study of American English, about 1/3 of
    respondents used "teal" instead of "blue" when describing the lighter,
    greener shades of "blue."


[alice]: https://twitter.com/sundress
[call-with-input-file]: https://www.scheme.com/tspl3/io.html
[context-manager]: https://www.python.org/dev/peps/pep-0343/#specification-the-with-statement
[convolutional]: https://en.wikipedia.org/wiki/Convolutional_code
[delay-slots]: https://en.wikipedia.org/wiki/Delay_slot
[finally]: https://docs.python.org/3/tutorial/errors.html#defining-clean-up-actions
[fec]: https://en.wikipedia.org/wiki/Forward_error_correction
[go-defer]: https://tour.golang.org/flowcontrol/12
[graffiti]: https://i.redd.it/hotye1wdbg511.jpg
[hamming]: https://en.wikipedia.org/wiki/Hamming(7,4)#Hamming_matrices
[hazards]: https://en.wikipedia.org/wiki/Hazard_(computer_architecture)
[hyperphysics]: http://hyperphysics.phy-astr.gsu.edu/hbase/vision/specol.html
[kuriki]: https://jov.arvojournals.org/article.aspx?articleid=2608579
[lindsey-and-brown]: https://jov.arvojournals.org/article.aspx?articleid=2121523
[matthias]: http://www.unhyperbolic.org/
[nato]: https://en.wikipedia.org/wiki/NATO_phonetic_alphabet
[oooe]: https://en.wikipedia.org/wiki/Out-of-order_execution
[pons-auf]: https://de.pons.com/%C3%BCbersetzung?q=auf&l=deen&in=&lf=en
[pons-bei]: https://de.pons.com/%C3%BCbersetzung?q=bei&l=deen&in=&lf=en
[raii]: https://en.wikipedia.org/wiki/Resource_acquisition_is_initialization
[reed-solomon]: http://pfister.ee.duke.edu/courses/ecen604/rspoly.pdf
[scopeguard]: https://github.com/bluss/scopeguard
[shannon]: https://en.wikipedia.org/wiki/Noisy-channel_coding_theorem
[two-cases]: http://www.canoo.net/services/OnlineGrammar/Wort/Praeposition/Kasus/2Kasus.html?lang=en
[v2]: https://en.wikipedia.org/wiki/V2_word_order#Vestiges_of_V2_in_Modern_English
[viterbi]: https://en.wikipedia.org/wiki/Viterbi_algorithm
[word-order]: http://www.dartmouth.edu/~deutsch/Grammatik/WordOrder/WordOrder.html
