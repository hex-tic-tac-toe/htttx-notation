# HTTTX Notation

This repo is a standard definition for HeXo notation. The standard extends the notation defined in 
[hex-tic-tac-toe/hexagonal-tic-tac-toe-notation](https://github.com/hex-tic-tac-toe/hexagonal-tic-tac-toe-notation) 
(referred to as **notation v1** or just **v1** in this document) as a natural extension, a version 2. Notation in this 
standard is named **htttx notation v2** (referred to as **notation v2** or just **v2** in this document).

## The goals of the project

The main challenge **v2** is designed to solve, is storage and transfer of notated games and their analyses. As opposed
to **v2**, **v1** was designed with simplicity and basic game notation only in mind. **v1** does not support lines,
engine evaluations (apart from optional threat count), and visuals. This makes it difficult to use **v1** to share game
and opening studies.

In order to cater to those use-cases, **v2** includes a notion of per-stone and per-move optional data additions.
In the next section, the terms used to describe the standard are defined.

## Terminology

A **cell** - is a single hexagon which may have an X or an O *stone* placed, or nothing at all.

A **board** - is a complete infinite set of *cells* on which the game is played.

A **setup** - is an initial state of the *board*, which defines placed *stones*, and which player should make the first
*move*.

**Coordinates** - are two integers, **q** and **r** defining a position of a *cell* on the *board*. In this 
standard's text, coordinates are written as [*q*, *r*]. The coordinate system used in this standard is the same axial
coordinate system as the one used in **v1**, with the same orientation.

A **stone** - is a single X or an O symbol placed inside a *cell*.

A **turn** - is a pair of *stones* placed by the same player. In the context of this standard the *stones* may, but 
don't have to be ordered.

A **move** - is a set of: a *stone*, its *info*, and *visuals*. A *turn* consists of two *moves*.

A **line** - is an ordered set of alternating X and O *turns*. A *line* can have arbitrary starting and ending 
conditions.

A **main-line** - is a *line* consisting of *turns* that were either actually played, or are considered the most
important. A *main-line* starts with an implicit X *stone* placed at *coordinates* [0, 0].

A **variation** - is a *line* consisting of *turns* diverging from the *main-line* or from another *variation*.

A **position** - is a set of all the *stones* on the *board* at a single moment of time.

A **game notation** - is a set consisting of the *main-line* and all the *variations* diverging from that *line*, as 
well as all the nested *variations* of those, and all the additional *visuals* and other information regarding game's 
*turns*.

A **visual** - is a set of additional instructions for displaying a single *cell* in a specific *position*.

An **open evaluation** - is a signed integer representing the calculated advantage of one of the players relative to the
other in a specific *position*. Positive *open evaluation* is an advantage for X, negative - for O.

A **closed evaluation** - is a signed integer representing the number of additional *turns* it would take for one of 
the players to win the game from the current *position*, assuming perfect play by both players. Positive 
*closed evaluation* is the number of *turns* required for X to win. Negative is the number of *turns* required for O to
win, but negated.

A player's **clock** - is an amount of time that a player has in a *position* to complete a *turn* before the player
loses the game on time. It may be arbitrarily increased at any point based on the agreed upon rules of each particular
game.

A *stone* **info** - is a combination of the *evaluation* of the *position*, and the *clock* of the player placing the
*stone*, **after** the *stone* is placed on the board.

A **meta tag** or just a **tag** - is a single key-value pair of information related to the game and the 
*game notation*.

The game **meta** - is a set of all the **tags** of a game.

## Notation v2

The **notation** consists of 2 blocks: 
1. The *meta* block.
2. The *game notation* block.

All the whitespaces except inside the values of the *tags* are ignored and can be placed freely to assist readability.

### Meta

The *meta* block consists of *tags*, all of which, except `version`, are optional. The set of recommended *tags* can be
found further below.

Tags are formatted as `key[value]` pairs, unchanged from *v1* of the standard.

### Formal notation v2 format definition

```
<game>              ::= <metadata> <setup> [<line>]
                      | <metadata> <line>

<metadata>          ::= <version> {<tag>} ";"
<version>           ::= "version[2]"

<tag>               ::= <key> "[" <value> "]"

<setup>             ::= "x" {<stone>} ":" "o" {<stone>} {<visual>} ";" 
                      | "o" {<stone>} ":" "x" {<stone>} {<visual>} ";"

<line>              ::= <turn> {<turn>} [<final_turn>]
<variation>         ::= "(" <line> ")"

<turn>              ::= <turn_number> <move> <move> {<variation>} ";"
<final_turn>        ::= <turn_number> <move> <final_move> {<variation>} ";"

<turn_number>       ::= <integer> "."

<move>              ::= <stone> [<info>] {<visual>}
<final_move>        ::= <terminator> [<info>] {<visual>}

<terminator>        ::= "[/]"

<stone>             ::= "[" <coordinate> "]"


<info>              ::= ("{" <time_remaining> [<separator> <evaluation>] "}")
                      | ("{" <evaluation> "}")

<time_remaining>    ::= "@" <integer>

<evaluation>        ::= <open_evaluation> | <closed_evaluation>
<open_evaluation>   ::= "%" <signed_integer>
<closed_evaluation> ::= "#" <signed_integer>


<visual>            ::= "<" <coordinate> [<separator> <highlight>] [<separator> <label>] ">"
<highlight>         ::= "#" [<letter>]
<label>             ::= "$" <characters>


<coordinate>        ::= <signed_integer> "," <signed_integer>

<signed_integer>    ::= "0"
                      | ["-"] <nonzero> {<digit>}

<integer>           ::= "0"
                      | <nonzero> {<digit>}

<separator>         ::= ":"

<characters>        ::= <character> {<character>}
<character>         ::= <letter> | <digit>

<letter>            ::= [A-Z]
<nonzero>           ::= [1-9]
<digit>             ::= [0-9]
```

### Game

The *game notation* is structured as a set of turns. Turns are numbered with integers starting with 1. In a standard 
game with a default *setup* of a single X at [0, 0], odd numbered *turns* are O player's, and even numbered ones are X
player's *turns*. 

The first single X *stone* placed at [0, 0] is implicit, shouldn't be notated, and can be considered *turn* 0. However,
a different *setup* can be provided, overriding the implicit [0, 0].

Each *turn* notation consists of a *turn* number, *stone* definitions, a list of optional *variants*, and a `;`. For
example:
```
1. [1,0] [0,1];
```

### Stones

A *stone* definition includes the *stone* *coordinates* in square brackets `[q, r]`, optional *info* definition for a
*position* after the *stone* is placed in curly brackets `{%-10}`, and an optional set of *visuals* for that *position*, 
each defined in their own pair of angle brackets `<2,0> <3,0>`.

The recommended range for a *position's* *open evaluation* is from -100 to 100, with values derived from the expected
probability of a win by X, via `100 * (2 * P(x_wins) - 1)`, Which is a trivial mapping function from the range of 0 - 1
to the recommended range of -100 - 100.

Player's *clock* in the *stone's* *info* is written as an integer number of milliseconds until a loss on time.

### Visuals

A *visual* block has *cell* *coordinates* for the *visual*, as well as optional highlight and label definitions.

A label is a string of letters and/or numbers (usually one letter, or a single number up to a couple digits) that should
be rendered on a *cell*. If only a label definition is present, only a label should be rendered, without highlights.

A highlight is an ideally obvious visual change in the way a *cell* is rendered, be it a color change or an outline.
The letter inside the highlight definition should be interpreted as a type of highlight. If highlights are at all 
supported, at the very least a "neutral" highlight should be supported. Any type of highlight unknown to the parser
should be rendered as a "neutral" highlight. Letter `N` must also be treated as a "neutral" highlight. Other letters
can be used, but are up to the implementation to decide on. It is recommended to use `X` and `O` letters for highlights
of a color and/or style similar to the X and O stones respectively. `N` can be omitted from the highlight definition, 
i.e. `<1,0:#N>` and `<1,0:#>` must be rendered identically.

If neither definition is present (coordinates only, i.e. `<1,0>`), the visual must be treated as if it had a neutral
highlight definition (i.e. `<1,0:#N>`).

Further visual types might be amended to the standard in the future, so a parser should ignore any *visual* block that
contains sections it doesn't explicitly understand (however, unsupported but known sections can be treated in any way).

When deciding on when to render different *visuals*, it's important to understand that the *visuals* apply to the single
*position* they are written in. If the whole *game notation* is rendered, the user only sees the **final** *position*, 
so only the *visuals* defined on the last *stone* should be rendered. If, however, the game viewer shows the user the
game *stone* by *stone*, or *turn* by *turn*, only the *visuals* on the last *stone* shown would be rendered.

### Variations

A variation must start with the same *move* number as the *move* it's written as a part of. For example:
```
3. [1,0][0,1] (3. [1,1][0,-1];) ;
```
This works this way, because a *variation* replaces the move it's written in.

A single *move* can have multiple *variations* written one after another before the *move's* `;` terminator.

A *variation* contains a whole *line*, which means any number of full moves can be put into it, including nested 
*variations* of those moves as well. For example:
```
1. [-1,0] [0,-1]                 ;
2. [1,0]  [2,0]  
  ( 2. [1,-2]  [2,-2]        ;
    3. [-1,-1] [-1,-2]       ;
    4. [-1,1]  [1,-1] 
      ( 4. [-1,-3] [1,-1]; ) ; )

  ( 2. [-2,1]  [2,-3]        ;
    3. [-1,-1] [1,-1]        ; ) ;

3. [1,-2] [2,-3]                 ;
4. [3,0]  [4,0]                  ;
```

### "Final move" terminator

To support software that doesn't force the player to make both *moves*, if placing just one *stone* wins them the game,
**v2** introduces a `<terminator>` token which looks like this: `[/]`. This token allows to notate games finished in
this manner, without having to invent a second *move*. It should be clear that a second *move* in such situations is
irrelevant.

From the technical standpoint, having this token allows the format to consistently have 2 *moves* per *turn* defined.
Any *info* or *visuals* applied to this token, should be assumed to apply to the *position* after the first *stone* of
such *turn* is placed. For example this notation:
```
1. [1,1] [/] { @1000 } <1,1> <1,0> ;
```
is completely equivalent to this:
```
1. [1,1] { @1000 } <1,1> <1,0> [/] ;
```

It's discouraged to have *info* and *visuals* applied to both the first *stone* and the terminator. How those are 
treated in such situations is left up to implementation.

A terminator must only be used on the final *turn* of a *line*, but it doesn't have to result in a 6 in a row.

### Setup

To support arbitrary *position* and puzzle studies and notation, an optional *setup* section can be provided. This
section can be used to both define the initial *stones* placed on the *board* before any *turn* takes place, as well as
which player is making the first *turn* of the *main-line*.

A *setup* section is structured as a pair of arbitrarily long lists of *stones*, one list prefixed with `x`, and the 
other one with `o` in any order (the order is important). Lists are separated with a `:`. And the whole setup section
ends with a `;`. For example:
```
o : x [0,0] ;
```
is a standard game *setup*. This *setup* is the implicit default for all *game notations* without a *setup* section.
Making this notation:
```
version[2];
o : x [0,0];
1. [1,0][0,1];
```
fully equivalent to this one:
```
version[2];
1. [1,0][0,1];
```

The order in which `x` section and `o` sections appear defines which player makes the first *turn*. If the first section
is `o`, then the first *turn* after the *setup* is made by `o`, and vice-versa. (In this sense, the setup section could 
be thought of as 2 extra unlimited *turns* with explicitly defined player sides). For example:
```
x [1,0] [0,1] : o [0,0] ;
```
would setup the *board* with Xs on [1, 0] and [0, 1], an O on [0, 0], and X is to make the first *turn*.

*Setup* section can also include *visuals* right before the `;`. *Visuals* specified this way should be rendered on the
initial *position*, i. e. the *position* before any of the *stones* from the *main-line* are placed.

If a *game notation* provides a *setup* section, *main-line* can be omitted. This way of using *the notation* allows
notating puzzles without providing a solution, as well as notating arbitrary *positions* in general.

## Recommended meta tags

All tags are optional. Fully equivalent to ones found in **notation v1**, except for `version`.

| Key            | Description                              | Value                                                           |
| -------------- | ---------------------------------------- | --------------------------------------------------------------- |
| `name`         | Display name of the match                | string                                                          |
| `platform`     | Website or app where the game was played | string                                                          |
| `utcdatetime`  | Game start time in UTC                   | ISO 8601: `YYYY-MM-DD HH:MM:SS`                                 |
| `playercross`  | Name of the cross (X) player             | string                                                          |
| `playercircle` | Name of the circle (O) player            | string                                                          |
| `timecontrol`  | Time control format                      | string                                                          |
| `endreason`    | How the game ended                       | string - `win` `time` `resign` `draw`                           |
| `winner`       | Who won                                  | string - `cross` `circle`                                       |

### Time control format

Equivalent to **notation v1**.

> Currently supported time controls are:
> - Absolute
> - Fischer
>
> `<timecontrol> ::= <integer> ("+" <integer>)?` represents `basetime+increment`

## Examples

```
version[2]
name[GameName 0]
platform[WebsiteXY 0]
playercross[BlueWhale 0]
playercircle[GreenSnake 0]
timecontrol[Fischer 60+5]
endreason[resign]
winner[cross]
datetime[2026-10-06 12:00:00];
1. [1,-1] [/];
```

```
version[2];
1. [-1,0] [0,-1] ;
2. [1,0]  [2,0]  ;
3. [1,-2] [2,-3] ;
4. [3,0]  [4,0]  ;
5. [3,-4] [-2,1] ;
```

```
version[2];
1. [-1,0] [0,-1] ;
2. [1,0]  [2,0]  ;
3. [1,-2] [2,-3] ;
4. [3,0]  [4,0]  ;
5. [1,-1] [2,-2] ;
6. [5,0]  [/]    ;
```

```
version[2];
1. [-1,0] [0,-1] {@4500} ;
2. [1,0]  [2,0]  {@4050} ;
3. [1,-2] [2,-3] {@4200} ;
4. [3,0]  [4,0]  {@3550} ;
5. [1,-1] [2,-2] {@3100} ;
6. [5,0]  [/]    {@1050} ;
```

```
version[2];
1. [-1,0] [0,-1] ;
2. [1,0]  [2,0]  ;
3. [1,-2] [2,-3] ;
4. [3,0]  [4,0]  <3,-4> <-2,1> ;
5. [1,-1] [2,-2] <5,0:#X>      ;
6. [5,0]  [/]    ;
```

```
version[2];
1. [-1,0] {@4505} [0,-1] { @4500 : %-1 } ;
2. [1,0]  {@4055} [2,0]  { @4050 : %2  } ;
3. [1,-2] {@4205} [2,-3] { @4200 : %-5 } ;
4. [3,0]  {@3555} [4,0]  { @3550 : #-1 } ;
5. [1,-1] {@3105} [2,-2] { @3100 : #1  } ;
6. [5,0]  {@1050} [/] ;
```

```
version[2];
1. [-1,0] {@4505} [0,-1] { @4500 : %-1 } ;
2. [1,0]  {@4055} [2,0]  { @4050 : %2  } ;
3. [1,-2] {@4205} [2,-3] { @4200 : %-5 } ;
4. [3,0]  {@3555} [4,0]  { @3550 : #-1 } ;
5. [1,-1] {@3105} [2,-2] { @3100 : #1  } ;
6. [5,0]  {@1050}  
     <-1,0:$1 > <0,-1:$1> <1,0:$1> <2,0:$1>
     <1,-2:$2 > <2,-3:$2> <3,0:$2> <4,0:$2>
     <1,-1:$3 > <2,-2:$3> <5,0:$3>
   [/];
```

```
version[2];
1. [-1,0] [0,-1]              ;
2. [1,0]  [2,0]  
  ( 2. [1,-2]  [2,-2]     ;
    3. [-1,-1] [-1,-2]    ;
    4. [-1,1]  [1,-1] 
      ( 4. [-1,-3] [/]; ) ; )

  ( 2. [-2,1]  [2,-3]     ;
    3. [-1,-1] [/]        ; ) ;

3. [1,-2] [2,-3]              ;
4. [3,0]  [4,0]               ;
5. [1,-1] [2,-2]              ;
6. [5,0]  [/]                 ;
```

```
version[2];
1. [-1,0][0,-1]; 2. [1,0][2,0] (2. [1,-2][2,-2]; 3. [-1,-1][-1,-2]; 
4. [-1,1][1,-1] (4. [-1,-3][1,-1];);) (2. [-2,1][2,-3]; 3. [-1,-1][1,-1];);
3. [1,-2][2,-3]; 4. [3,0][4,0]<1,-1><5,0>; 5. [1,-1][2,-2]<1,-1:$1>; 
6. [5,0][/]<0,0:#X:$1> ;
```