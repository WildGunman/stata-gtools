hashsort
--------

sort and gsort using hashes and C-plugins

_**Important:**_ Hashsort does not afford speed improvements over sort
when the resulting sort will be unique or when the user has access to
Stata/MP. Hence it is considered an experimental command, even though it is
generally faster than gsort even in Stata/MP.

_Note for Windows users:_ It may be necessary to run `gtools, dependencies` at
the start of your Stata session.

Syntax
------

<p><span class="codespan">hashsort [+|-] varname [[+|-] varname ...] [, <a href="#options">options</a> ] </p>

Description
-----------

hashsort uses C-plugins to implement a hash-based sort that is always
faster than sort for sorting groups and faster than gsort in general.
hashsort hashes the data and sorts the hash, and then it sorts one
observation per group. The fewer the number of gorups relative to the
number of observations, the larger the speed gain.

If the sort is expected to be unique or if the number of groups is large,
then this comes at a potentially large memory penalty and it may not be
faster than sort (the exception is when the sorting variables are all
integers).

Each varname can be numeric or a string. The observations are placed in
ascending order of varname if + or nothing is typed in front of the name
and are placed in descending order if - is typed. hashsort always
produces a stable sort.

Options
-------

- `generate(varname)` or `gen(varname)`  Store group ID in generate.

- `sortgen` Set data sortby variable to `generate`.

- `replace` If `generate` exits, it is replaced.

- `skipcheck` Skip internal is sorted check.

### Gtools options

(Note: These are common to every gtools command.)

- `verbose` prints some useful debugging info to the console.

- `benchmark` or `bench(level)` prints how long in seconds various parts of the
            program take to execute. Level 1 is the same as `benchmark`. Level 2
            additionally prints benchmarks for internal plugin steps.

- `hashlib(str)` On earlier versions of gtools Windows users had a problem
            because Stata was unable to find spookyhash.dll, which is bundled
            with gtools and required for the plugin to run correctly. The best
            thing a Windows user can do is run gtools, dependencies at the start
            of their Stata session, but if Stata cannot find the plugin the user
            can specify a path manually here.

Examples
--------

You can download the raw code for the examples below
[here  <img src="https://upload.wikimedia.org/wikipedia/commons/6/64/Icon_External_Link.png" width="13px"/>](https://raw.githubusercontent.com/mcaceresb/stata-gtools/master/docs/examples/hashsort.do)

```stata
. sysuse auto, clear
. hashsort price
. hashsort +price
. hashsort rep78 -price
. hashsort make
. hashsort foreign -make
```

One thing that is useful is that hashsort can encode a set of variables and
set the encoded variable as the sorting variable:

```stata
. sysuse auto, clear
(1978 Automobile Data)

. hashsort foreign -rep78, gen(id) sortgen
(note: missing values will be sorted last)

. disp "`: sortedby'"
id

. tab id

         id |      Freq.     Percent        Cum.
------------+-----------------------------------
          1 |          4        5.41        5.41
          2 |          2        2.70        8.11
          3 |          9       12.16       20.27
          4 |         27       36.49       56.76
          5 |          8       10.81       67.57
          6 |          2        2.70       70.27
          7 |          1        1.35       71.62
          8 |          9       12.16       83.78
          9 |          9       12.16       95.95
         10 |          3        4.05      100.00
------------+-----------------------------------
      Total |         74      100.00
```
