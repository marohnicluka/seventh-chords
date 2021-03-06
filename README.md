# Septima library

## Introduction

### Overview
Septima is a C++ library for investigating parsimonious diatonic/chromatic relations between seventh chords as well as seventh-chord networks and sequences based on these relations.

#### Main features
* Generating elementary transitions between seventh chords
* Creating chord networks
* finding optimal voicings for sequences of seventh chord symbols

A command-line interface is provided for using the above features from a terminal.

### Installation

#### Dependencies
1. GCC
2. Standard Template Library (STL)
2. GNU Linear Programming Kit ([GLPK](https://www.gnu.org/software/glpk/))
3. GNU Scientific Library ([GSL](https://www.gnu.org/software/gsl/))

It is recommended (but not required) that [Lilypond](https://www.lilypond.org) and [Graphviz](https://graphviz.org/) are installed on your system.

In Ubuntu, mandatory dependencies can be installed by typing:

```
sudo apt-get install -y build-essential libglpk-dev libgsl-dev
```

Optional dependencies are installed by typing:

```
sudo apt-get install -y lilypond graphviz texlive-latex-recommended dot2tex
```

#### Build & install Septima

Septima is packaged as a library and command-line tool (CLT). To build and install these components using a Linux terminal, enter:

```
./configure
make
sudo make install
```

This installs the library and CLT in `/usr/local` by default. To change the installation directory, use the option `--prefix=<path>` when running the configure script, for example:

```
./configure --prefix=/home/luka/.local
```

In the above case, it is enough to type `make install` instead of calling `sudo`.

#### Creating offline documentation

To convert this file to PDF, use [Grip](https://pypi.org/project/grip/):

```
pip install grip
grip README.md
```

Then click `http://localhost:xxxx/` which opens the rendering in browser. Print to file when ready.

## Command-line interface

After a successful installation, command-line application `septima` will appear in `<prefix>/bin`, by default `/usr/local/bin`.

### Usage

The command-line tool is called from terminal like this:

```
septima <task> [<option(s)>] CHORDS or FILE
```

#### Tasks
- `-h`, `--help` &mdash; Show this help message.
- `-t`, `--transition` &mdash; Generate transitions from the first seventh chord to the rest.
- `-tc`, `--transition-classes` &mdash; Generate all structural classes of transitions between seventh chords.
- `-ts`, `--transition-stats` &mdash; Output voice-leading statistics for the given chords.
- `-cg`, `--chord-graph` &mdash; Create chord graph from a set of chords.
- `-v`, `--voicing` &mdash; Find an optimal voicing for the given chord sequence.
- `-av`, `--all-voicings` &mdash; Find all optimal voicings for the given chord sequence.
- `-mn`, `--Pmn-relations` &mdash; Output all pairs (*m*,*n*) such that the given two chords are *Pₘₙ*-related.

#### Options
- `-c`, `--class` &mdash; Specify upper bound for voice-leading infinity norm. Default: 7.
- `-dg`, `--degree` &mdash; Specify degree of elementary transitions. Default: unset.
- `-aa`, `--allow-augmented` &mdash; Allow augmented realizations. By default, German sixths and Tristan chords are disabled.
- `-fa`, `--force-augmented` &mdash; Spell first realization in a transition as augmented sixth.
- `-nr`, `--no-respell` &mdash; Do not respell augmented sixths. By default, they are respelled whenever possible.
- `-ns`, `--no-simplification` &mdash; Do not discard enharmonic equivalents with larger voice-leading L1 norm. By default, only transitions with minimal L1 norm are retained.
- `-d`, `--domain` &mdash; Specify domain on the line of fifths. It is entered as a comma-separated list of integers. Blocks of several consecutive integers, such as e.g. 1,2,3,4,5, can be entered as 1:5. The default domain is {−15,−14,…,15}, which corresponds to notes from G&#119083; to A&#119082;.
- `-z`, `--tonal-center` &mdash; Specify tonal center on the line of fifths. Default: 0, which corresponds to the note D.
- `-lf`, `--label-format` &mdash; Specify format for chord graph labels. Choices are **symbol**, **number**, and **latex**. Default: **symbol**.
- `-p`, `--preparation` &mdash; Specify preparation scheme for elementary transitions. Choices are **none**, **generic** (for preparation of generic sevenths), **acoustic** (for preparation of acoustic sevenths), and **classical** (for preparation of only non-dominant seventh chords). Default: **none**.
- `-w`, `--weights` &mdash; Specify weight parameters for the voicing algorithm. Three nonnegative floating-point values are required: tonal-center proximity weight *w*&#8321;, voice-leading complexity weight *w*&#8322;, and penalty *w*&#8323; for augmented sixths. By default, *w*&#8321; = 1.0, *w*&#8322; = 1.75, and *w*&#8323; = 1.4.
- `-vc`, `--vertex-centrality` &mdash; Show centrality measure with each vertex of the chord graph. Choices are **none**, **label**, and **color**. Default: **none**.
- `-ly`, `--lilypond` &mdash; Output transitions and voicings in Lilypond code.
- `-cs`, `--chord-symbols` &mdash; Print chord symbols above realizations in Lilypond output.
- `-q`, `--quiet` &mdash; Suppress messages.

#### Entering chords

Chords are entered using symbols in form *root*:*quality*. *root* is one of the integers 0,1,…,11, which represent pitch classes. *quality* (following the notation proposed by [Harte (2005)](http://ismir2005.ismir.net/proceedings/1080.pdf)) is either

- **d7** (dominant seventh),
- **hdim7** (half-diminished seventh),
- **m7** (minor seventh),
- **maj7** (major seventh), or
- **dim7** (diminished seventh).

Optionally, *root* can be left unspecified (in that case the colon is also not entered), in which case all seventh chords of the given *quality* are generated. For example, `dim7` is equivalent to `0:dim7 1:dim7 2:dim7` (there are 3 diminished seventh chords in total).

Septima can also read chords from a file. Chords are entered using the same syntax as above and they are separated by either spaces, tabs, newlines, commas, or semicolons. These delimiters may be combined. Lines starting with the character # are ignored, thereby providing a way to write comments in the file.

## Examples

### Transitions

#### Generating elementary transitions between two seventh chords

In this example, elementary transitions between the dominant seventh chord on C (C⁷) and the diminished seventh chord on B (B⁰⁷) are generated. Enter:

```
septima -t -aa -ly 0:d7 11:dim7 >out.ly
```

This will output all transitions (including those with augmented sixths, since option `-aa` is used) in Lilypond format to the file `out.ly`. 

To generate a PNG image (Lilypond has to be installed), enter:

```
lilypond -dbackend=eps -dresolution=600 --png out.ly
rm out-*
```

The following output (`out.png`) is obtained:

<p align="center"><img src="images/trans_C7-Bdim7.png" width="62%"></p>

In the next example, all connections of Tristan chord to another dominant/half-diminished seventh chord are generated. Enter:

```
septima -t -aa -fa -cs -ly 5:hdim7 d7 hdim7 >out.ly
```

Note that a list of seventh chords is given; the first chord is called *home chord* while others are *destination chords*. Any duplicates are automatically removed. The option `-fa` forces the home chord to be spelled as an augmented sixth, while `-cs` enables printing chord symbols in the output. Processing `out.ly` with Lilypond, similarly to above, produces the following image:

<p align="center"><img src="images/trans_TC.png" width="70%"></p>

#### Generating all structural classes of transitions between chords in the given set

For the given set of seventh chords, all possible elementary transitions are generated and split into structural-equivalence classes.
Each class is represented by a transition closest to the tonal center *z*. For example, all classes of transitions between dominant seventh chords are generated by typing:

```
septima -tc -aa -ly d7 >out.ly
```

After converting `out.ly` to PNG using Lilypond, one obtains:

<p align="center"><img src="images/trans_d7-d7.png" width="70%"></p>

Hence there exist exactly seven structurally different ways to connect two dominant seventh chords using a diatonic/chromatic voice leading.

To generate all classes of diatonic transitions between dominant, half-diminished, minor, and major seventh chords, enter:

```
septima -tc -c 5 -ly -cs d7 hdim7 m7 maj7 >out.ly
```

Diatonic voice leading is obtained by setting `-c` option to 5. The following 25 transition types are obtained:

<p align="center"><img src="images/trans_diatonic.png" width="70%"></p>

#### Voice-leading statistics

Septima can perform automatic statistical analysis for the given set of seventh chords. It computes all allowed transitions up to congruence and outputs the following data.

 * total number of transitions
 * total number of efficient transitions
 * average voice-leading displacement
 * average voice-leading excess relative to the efficient voice leading
 * number of transitions in which common tones are fixed
 * number of transitions which feature contrary motion
 * distribution of transitions by voice-leading shift
 * distribution of transitions over *mn*-types (*mn*-type of a transition is a pair of integers indicating amounts of voices that move by semitone resp. by whole tone)
 
For example, to obtain statistics for the complete set of 51 seventh chords (using augmented realizations only when necessary and ignoring the preparation rule), enter:

```
septima -ts -aa d7 hdim7 m7 maj7 dim7
```

Output:

```
Total transitions: 1392
Efficient transitions: 1320 (94.8276%)
Average voice-leading shift: 3.72414 semitones
Average relative excess: 2.06897%
Common tones are fixed in 1320 transitions
Contrary motion occurs in 336 transitions
Distribution by voice-leading shift:
1: 96
2: 252
3: 312
4: 300
5: 216
6: 144
7: 72
Distribution over mn-pair types:
(0,1): 36
(0,2): 24
(0,3): 36
(1,0): 96
(1,1): 120
(1,2): 96
(1,3): 72
(2,0): 216
(2,1): 156
(2,2): 108
(3,0): 192
(3,1): 120
(4,0): 120
```

### Chord graphs

Chord graphs are output in [DOT format](https://graphviz.org/doc/info/lang.html). It may be worth noting that the free computer algebra system [Giac/Xcas](https://www-fourier.ujf-grenoble.fr/~parisse/giac.html) supports importing from DOT and features an extensive package for graph theory, which can be useful for investigating properties of chord graphs.

For example, to create the chord graph on minor seventh chords with edges corresponding to diatonic relations, enter:

```
septima -cg -c 5 m7 >cg.dot
```

Graphviz can be used to visualize the graph. In this case it is best to use circular layout, obtained by typing:

```
circo -Tpng -o cg1.png cg.dot
```

The result is shown below. 

<p align="center"><img src="images/cg1.png" width="50%"></p>

The following example shows how to create the chord graph on all dominant and half-diminished seventh chords which can be realized in the domain from F&flat; to B&sharp; on the line of fifths, excluding the tones C, G, D, and A. Those chords which cannot be realized in the domain are automatically excluded from the graph. Note that the resulting graph is directed because the option `-p` is set to `generic` and hence only transitions in which the generic seventh of second chord is prepared in the first chord are considered. 

```
septima -cg -d -10:-3,2:10 -p generic d7 hdim7 >cg.dot
dot -Tpng -Grankdir=LR -o cg2.png cg.dot
```

The result is shown below.

<p align="center"><img src="images/cg2.png" width="50%"></p>

For creating chord graphs with nicely typeset chord symbols, consider setting the option `-lf` to `latex`. In that case, `./septima` outputs a file to be processed with [dot2tex](https://dot2tex.readthedocs.io/en/latest/). For example, a chord graph on the set {C⁷, E&flat;⁷, F&sharp;⁷, A&#9651;, B&flat;&#216;} is created. After that, Graphviz is called to create `xdot` format, which is passed to dot2tex (note that `--crop` option is used). The resulting TeX file is compiled with PDFLatex and converted to PNG using pdftoppm (from [Poppler](https://poppler.freedesktop.org/)), as follows.

```
septima -cg -p generic -lf latex 0:d7 3:m7 6:d7 9:maj7 10:hdim7 >cg.dot
circo -Txdot -o cg.xdot cg.dot
dot2tex --crop -tmath --figpreamble="\Large" -o cg.tex cg.xdot
pdflatex cg.tex
pdftoppm -png cg.pdf >cg.png
```

The result is shown below.

<p align="center"><img src="images/cg3.png" width="400"></p>

By setting the option `-vc` one can obtain more informative graphs. In that case, communnicability betweenness centrality (CBC) is computed for each vertex (see [this paper](https://arxiv.org/abs/0905.4102) by E. Estrada *et al.*). The original definition is adjusted for graphs which are not (strongly) connected. If `-vc` is set to `label` resp. `color`, then CBC measure *cₖ* is written in the **xlabel** resp. **fillcolor** attribute of the *k*-th vertex. When processed by Graphviz, *cₖ* is either shown near the respective vertex or it represents a particular color in the gray scale&mdash;represented by the segment [0, 1] where 0 and 1 correspond to white black color, respectively. For example, a chord graph on the set {C⁷, C&sharp;&#216;, Dm⁷, E&flat;&#9651;, G⁷, A&flat;&#216; Am⁷, B&flat;&#9651;} is visualized by typing:

```
septima -cg -p generic -vc color 0:d7 7:d7 1:hdim7 8:hdim7 2:m7 9:m7 3:maj7 10:maj7 >cg.dot
circo -Tpng -o cg.png cg.dot
```

The obtained image is shown below. Darker vertices are more important as stepping-stones in chord progressions.

<p align="center"><img src="images/cg4.png" width="50%"></p>

### Voicings

For example, to generate a voicing for the progression F&#216; &#8594; E⁷ &#8594; A&#9837;&#216; &#8594; G⁷ &#8594; D&#216; &#8594; B⁷, which appears at the beginning of Wagner's *Tristan*, enter:

```
septima -v -aa sequences/Wagner1.seq
```

This command uses default weight parameters for the voicing algorithm. The optimal voicing is output as a sequence of chord realizations.

```
Using GLPK 4.65
Finding optimal voicing for the sequence [5:hdim7,4:d7,8:hdim7,7:d7,2:hdim7,11:d7]
D#-F-G#-B
D-E-G#-B
D-F#-G#-B
D-F-G-B
D-F-Ab-C
D#-F#-A-B
Recommended key signature: 2 sharps
```

Note that the program also prints the recommended key signature at the end.

To find all optimal voicings, enter:

```
septima -av -aa sequences/Wagner1.seq
```

Output:

```
Using GLPK 4.65
Finding optimal voicing for the sequence [5:hdim7,4:d7,8:hdim7,7:d7,2:hdim7,11:d7]
Found 1 voicing(s)

Voicing #1:
D#-F-G#-B
D-E-G#-B
D-F#-G#-B
D-F-G-B
D-F-Ab-C
D#-F#-A-B
```

The result shows that there is a unique optimal voicing.

## Using Septima in C++ projects

After a successful installation, the shared library `libseptima.so` will be available in `<prefix>/lib` and the corresponding header files in `<prefix>/include/septima`. This allows linking the library with other C++ projects. The headers contain brief descriptions of the implemented routines.

An example application **genprog**, which uses Septima, is provided in the `example` directory. It is a command-line application which accepts two chords *c* and *d* as input arguments, optionally with a positive integer *n* which defaults to 10. The program creates chord graph on all seventh chords which are connected if there exists a prepared elementary transition between them (augmented sixths are not allowed). The weight of arc (*c*&#8321;,*c*&#8322;) is equal to the smallest value (1+VLD(*T*))/VL(*T*) among all elementary transitions *T* from *c*&#8321; to *c*&#8322;. Here, VLD(*T*) and VL(*T*) are the directional and absolute voice-leading shift of *T*, respectively (as defined by [Kochavi, 2008](https://works.swarthmore.edu/fac-music/58/)). The program then finds first *n* cheapest paths from *c* to *d* and prints them on the standard output.

The application is compiled by typing:

```
g++ -o genprog genprog.cpp -lseptima
```
