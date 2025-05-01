# Mida
## Mida Music Programming Language
One of the main challenges in teaching a language model to make music is that no system affords an LLM the proper tools to express musical ideas in text form. Today im happy to present a notation language I've designed called "Mida" which is aimed at alleviating these stress points and moving the field towards models which are more capable at generating music without leaving text generation. Mida offers a more expressive, readable, and flexible alternative to ABC notation and RAW MIDI for text-based music creation.

While it's possible to generate musical patches using LLMs with programming languages like Faust, ChucK, Humdrum, and others, none of these solutions provide what LLMs really need: A fully text based system for representing a complete song. It's more of a markdown language that a programming language.

My hope is that my sharing this language, more people start to get interested in teaching LLMs how to generate music purely from text, please check out the github repository if you're interested in learning more. Thank you!
## What The ^*~> Is Mida?
Mida is a musical language designed to be fully text based drop in replacement for midi. Unlike ABC notation, mida supports drums, lyrics, and multitracked composition. Let's begin learning how to write Mida.

## Mida Basics: The Audicle

```mida
*C4 E4 G4*
```  


This is an **Audicle.** It's the basic building block of everything in mida. In mida, an audicle is delimited by asterisks, and the information inside will be played on a quantized 16th note grid, no matter what, so this audicle you see here is three sixteenth notes. To add sustains and rests, we **simply add a period or a hyphen for each 16th sustain or 16th note rest** we want to add, and to make notes trigger polyphonically, we connect them with a tilde. To keep things well organized, we can use pipes to look like measure lines, but these will be ignored by our parser. Comments are done with double pipes outside audicles, and multi line comments are endcapped with a pipe and a greater-than symbol. Notes must have an octave and a pitch, and optionally an accidental with `#` or `b`


## Mida Basics: Sustains, Rests, Polyphony, Accidentals, and Decoration.
```mida
*C#4~E4~G#4 - - - | . . . .* || Cmaj 4th Octave Quarter Note + Quarter Note Rest |>
```
Next we will cover multitracking, lyrics, drums, loops, and more; but first, I feel as though it would be worth mentioning how the above Audicle would be printed. Mida perscribes a specific way that audicles be printed known as the Audicle Log. Essentially, the composition is played where each 16th note event gets its own line. this makes it possible to debug music and align tracks without playing them aloud.


## Mida Basics: Chaining Audicles + The Audicle Log


As an example, ill connect two Audicles with a tilde, which means they will start at the same time, then I'll show what the log output would look like.

```mida
*C#4~E4~G#4 - - - | . . . .*~*F#1~F#2 - - - | - - - -*
```

### output:


```mida
C#4~E4~G4 F#1~F#2

- -

- -

- -

. -

. -

. -

. -
```

In essense, the Audicle Log is your primary debugging tool, aside from your ears, for validating multitrack compositions. 

## Mida Basics: The Blawc

Using chained Audicles is useful for simpler compositions, but sometimes you need more power and complexity. The second way to do multitrack compositions in Mida is called a block. However, to avoid confusion with the common term, we refer to it as a *blawc.* To start a blawc, type this operator.

```
`~# 
```
The contents of the block are delimited by single quotes, like so: Each audicle will start at the same time, just like chaining an Audicle, but without needing to use the tilde operator to link them manually. 

```
`~#
'
*C#4~E4~G#4 - - - | . . . .*
*F#1~F#2 - - - | - - - -*
'
```
Since they don't need to be connected by tildes, Mida blawcs open the door for non-local Audicles to have synchronous start times. This makes it much easier to manage layered compositions without having to keep everything in one chain. 
The output would be exactly the same as the chained Audicle example.

## Mida Basics: The Lyricle

Lyricles are lyrical Audicles. They get delimited by double quotes instead of asterisks, and show lyric information instead of note information, but the same rules apply. 

```
"Hello - - | World . . Of~Mida . ." ||  "Hello" plays for 3 16th notes. "World" plays for 1 16th note then rests for 2. "Of~Music" is connected by a tilde which means both words are to be performed in that single 16th note. |>
```
## Mida Basics: Loops
It's often desirable while writing Mida to want to loop things. Mida makes this easy to do by simply wrapping you audicle in question in a pair of curly braces, and putting a number before the brace opens to denote the number of times it loops. 
```
4 {*Bb3 - . | D3~F3 . . | D3~F3 . . *} || Loops the audicle four times |>
```
Loops work for any kind of Audicle, and can also be nested.
```
4 {"Hi~ - . | You . . | There . . "
4 {"I'm - - Here! - ."} } || Inner Loop Plays 4 Times For Each Outer Loop, Making the inner loop play for a total of 16 times  |>
```
## Intermediate Mida: Type Set Bunkers

**Type Set** is Mida's system for drum notation. Type Set notation uses special symbols to denote rhythmic events, and uses parentheses as delimiters; and is on an 8th note grid instead of a 16th note grid.
**The direction that the note is facing determines the swing**

```
Standard Hit      *|   or   |*
Accented Hit      ^|   or   |^
Ghost Note        v|   or   |v
Flam              /|   or   |/
Drag              *|,  or   ,|*
Double Stroke     **|  or   |**
Rest              _
Roll              {}
```

For Rolls, triplet, quintuplets, etc; Curly braces indicate rhythmic events that will happen in a single 8th note
```
{*| *| *|} || Triplet 8th Notes |>
```
### Full Patterns
To actually delimit type set, we use parentheses and call it a **Bunker.** Here is an example of a simple four on the floor beat. Instruments are inferred, not explicitly denoted. 
```
(*| _ ^| _ *| _ ^| _)
```
A Simple swung "High-Hat" pattern that uses a **Roll.** Inside type set's parenthesis "Bunkers," curly braces act as rolls, not loops, squeezing multiple hits into one 8th note event.
```
(*| {|* *|})
```


## Intermediate Mida: Polymorphism
The third way to do multitrack compositions in Mida is with the following operators: 

The Assignment Operator
```
^*~> 
```
The Call Operator

```
<~*^
```
The **Assignment** and **Call** operators are two frequently used operators, and can act as a swiss army knife depending on the context. In this section we'll learn how to use them for basic multitrack compositions without a Blawc or a Tilde.
```
myAssignment ^*~> *C4 - - - Bb3 - - .*
myAssignment <~*^
```
Simply name things with ```^*~> and call them with <~*^``` overloaded functions can be done easily easily sync Audicles. 
```
myOverloadedCHORD ^*~> *C#4~E4~G#4 - - - | . . . .*
myOverloadedCHORD ^*~> *F#1~F#2 - - - | - - - -*
myOverloadedCHORD <~*^
```
Assigning both Audicles overloads the name with both instead of overwriting. We can even achieve true polymorphism and overload the same name with different types of audicles. 
```
myPolymorphic ^*~> *C#4~E4~G#4 - - - | . . . .*
myPolymorphic ^*~> "Hello! - - - | - - - -"
myPolymorphic ^*~> (|* _ ^| _)
myPolymorphic <~*^
```

