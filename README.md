# Septima library documentation

## Introduction

### Overview
This is a C++ library for investigating tonal relations between seventh chords. It can
* generate elementary transitions between seventh chords,
* create chord networks,
* find optimal voicings for sequences of seventh chord symbols.

### Dependencies
1. GNU Linear Programming Kit ([GLPK](https://www.gnu.org/software/glpk/))
2. GNU Scientific Library ([GSL](https://www.gnu.org/software/gsl/))

### Installation
To compile the library, type `make`.

## Command-line interface

After a successful compilation, executable `septima` will appear in the installation directory.

### Usage

On Linux terminal, the executable is called like this:

`./septima <task> [<option(s)>] CHORDS or FILE`

#### Tasks
- `-h`, `--help` &mdash; Show this help message.
- `-t`, `--transition` &mdash; Generate transitions between two seventh chords.
- `-tc`, `--transition-classes` &mdash; Generate all structural classes of transitions between seventh chords.
- `-cg`, `--chord-graph` &mdash; Create chord graph from a set of chords.
- `-v`, `--voicing` &mdash; Find an optimal voicing for the given chord sequence.
- `-av`, `--all-voicings` &mdash; Find all optimal voicings for the given chord sequence.

#### Options
- `-c`, `--class` &mdash; Specify upper bound for voice-leading infinity norm. Default: 7.
- `-dg`, `--degree` &mdash; Specify degree of elementary transitions. Default: unset.
- `-aa`, `--allow-augmented` &mdash; Allow augmented realizations. By default, German sixths and Tristan chords are disabled.
- `-d`, `--domain` &mdash; Specify domain on the line of fifths. Default: {−15,−14,…,15}, which corresponds to notes from G&#119083; to A&#119082;.
- `-z`, `--tonal-center` &mdash; Specify tonal center on the line of fifths. Default: 0, which corresponds to the note D.
- `-lf`, `--label-format` &mdash; Specify format for chord graph labels. Choices are *symbol*, *number*, and *latex*. Default: *symbol*.
- `-p`, `--preparation` &mdash; Specify preparation scheme for elementary transitions. Choices are *none*, *generic*, and *acoustic*. Default: *none*.
- `-w`, `--weights` &mdash; Specify weight parameters for voicing algorithm. Three nonnegative floating-point values are required: tonal-center proximity weight *w*&#8321;, voice-leading complexity weight *w*&#8322;, and penalty *w*&#8323; for augmented sixths. By default, *w*&#8321; = 1.0, *w*&#8322; = 1.75, and *w*&#8323; = 0.25,
- `-ly`, `--lilypond` &mdash; Output transitions and voicings in Lilypond code.
- `-q`, `--quiet` &mdash; Suppress messages.
