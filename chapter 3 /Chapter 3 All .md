Chapter 3. Blocks, loops, and conditionals
2.18 Command line arguments in script mode
The Fift interpreter can be invoked in script mode by passing -s as a command line option. In this mode, all further command line arguments are
not scanned for Fift startup command line options. Rather, the next argument after -s is used as the filename of the Fift source file, and all further
command line arguments are passed to the Fift program by means of special
words $n and $#:
• $# ( – x), pushes the total number of command-line arguments passed
to the Fift program.
• $n ( – S), pushes the n-th command-line argument as a String S. For
instance, $0 pushes the name of the script being executed, $1 the first
command line argument, and so on.
• $() (x – S), pushes the x-th command-line argument similarly to $n,
but with Integer x taken from the stack.
Additionally, if the very first line of a Fift source file begins with the two
characters “#!”, this line is ignored. In this way simple Fift scripts can be
written in a *ix system. For instance, if
#!/usr/bin/fift -s
{ ."usage: " $0 type ." <num1> <num2>" cr
."Computes the product of two integers." cr 1 halt } : usage
{ ’ usage if } : ?usage
$# 2 <> ?usage
$1 (number) 1- ?usage
$2 (number) 1- ?usage
* . cr
is saved into a file cmdline.fif in the current directory, and its execution bit is set (e.g., by chmod 755 cmdline.fif), then it can be invoked
from the shell or any other program, provided the Fift interpreter is installed as /usr/bin/fift, and its standard library Fift.fif is installed as
/usr/lib/fift/Fift.fif:
$ ./cmdline.fif 12 -5
prints
-60
when invoked from a *ix shell such as the Bourne–again shell (Bash).
28
3.2. Conditional execution of blocks
3 Blocks, loops, and conditionals
Similarly to the arithmetic operations, the execution flow in Fift is controlled
by stack-based primitives. This leads to an inversion typical of reverse Polish
notation and stack-based arithmetic: one first pushes a block representing
a conditional branch or the body of a loop into the stack, and then invokes
a conditional or iterated execution primitive. In this respect, Fift is more
similar to PostScript than to Forth.
3.1 Defining and executing blocks
A block is normally defined using the special words “{” and “}”. Roughly
speaking, all words listed between { and } constitute the body of a new block,
which is pushed into the stack as a value of type WordDef. A block may be
stored as a definition of a new Fift word by means of the defining word “:”
as explained in 2.6, or executed by means of the word execute:
17 { 2 * } execute .
prints “34 ok”, being essentially equivalent to “17 2 * .”. A slightly more
convoluted example:
{ 2 * } 17 over execute swap execute .
applies “anonymous function” x 7→ 2x twice to 17, and prints the result
2 · (2 · 17) = 68. In this way a block is an execution token, which can be
duplicated, stored into a constant, used to define a new word, or executed.
The word ’ recovers the current definition of a word. Namely, the construct ’ hword-namei pushes the execution token equivalent to the current
definition of the word hword-namei. For instance,
’ dup execute
is equivalent to dup, and
’ dup : duplicate
defines duplicate as a synonym for (the current definition of) dup.
Alternatively, we can duplicate a block to define two new words with the
same definition:
{ dup * }
dup : square : **2
defines both square and **2 to be equivalent to dup *.
29
3.3. Simple loops
3.2 Conditional execution of blocks
Conditional execution of blocks is achieved using the words if, ifnot, and
cond:
• if (x e – ), executes e (which must be an execution token, i.e., a
WordDef ),5 but only if Integer x is non-zero.
• ifnot (x e – ), executes execution token e, but only if Integer x is zero.
• cond (x e e0 – ), if Integer x is non-zero, executes e, otherwise executes
e
0
.
For instance, the last example in 2.12 can be more conveniently rewritten
as
{ { ."true " } { ."false " } cond } : ?.
2 3 < ?. 2 3 = ?. 2 3 > ?.
still resulting in “true false false ok”.
Notice that blocks can be arbitrarily nested, as already shown in the
previous example. One can write, for example,
{ ?dup
{ 0<
{ ."negative " }
{ ."positive " }
cond
}
{ ."zero " }
cond
} : chksign
-17 chksign
to obtain “negative ok”, because −17 is negative.
5A WordDef is more general than a WordList. For instance, the definition of the
primitive + is a WordDef, but not a WordList, because + is not defined in terms of other
Fift words.
30
3.4. Loops with an exit condition
3.3 Simple loops
The simplest loops are implemented by times:
• times (e n – ), executes e exactly n times, if n ≥ 0. If n is negative,
throws an exception.
For instance,
1 { 10 * } 70 times .
computes and prints 1070
.
We can use this kind of loop to implement a simple factorial function:
{ 0 1 rot { swap 1+ tuck * } swap times nip } : fact
5 fact .
prints “120 ok”, because 5! = 1 · 2 · 3 · 4 · 5 = 120.
This loop can be modified to compute Fibonacci numbers instead:
{ 0 1 rot { tuck + } swap times nip } : fibo
6 fibo .
computes the sixth Fibonacci number F6 = 13.
3.4 Loops with an exit condition
More sophisticated loops can be created with the aid of until and while:
• until (e – ), executes e, then removes the top-of-stack integer and
checks whether it is zero. If it is, then begins a new iteration of the
loop by executing e. Otherwise exits the loop.
• while (e e0 – ), executes e, then removes and checks the top-of-stack
integer. If it is zero, exits the loop. Otherwise executes e
0
, then begins
a new loop iteration by executing e and checking the exit condition
afterwards.
For instance, we can compute the first two Fibonacci numbers greater than
1000:
{ 1 0 rot { -rot over + swap rot 2dup >= } until drop
} : fib-gtr
1000 fib-gtr . .
31
3.5. Recursion
prints “1597 2584 ok”.
We can use this word to compute the first 70 decimal digits of the golden
ratio φ = (1 + √
5)/2 ≈ 1.61803:
1 { 10 * } 70 times dup fib-gtr */ .
prints “161803 . . . 2604 ok”.
3.5 Recursion
Notice that, whenever a word is mentioned inside a { ...} block, the current
(compile-time) definition is included in the WordList being created. In this
way we can refer to the previous definition of a word while defining a new
version of it:
{ + . } : print-sum
{ ."number " . } : .
{ 1+ . } : print-next
2 . 3 . 2 3 print-sum 7 print-next
produces “number 2 number 3 5 number 8 ok”. Notice that print-sum
continues to use the original definition of “.”, but print-next already uses
the modified “.”.
This feature may be convenient on some occasions, but it prevents us
from introducing recursive definitions in the most straightforward fashion.
For instance, the classical recursive definition of the factorial
{ ?dup { dup 1- fact * } { 1 } cond } : fact
will fail to compile, because fact happens to be an undefined word when the
definition is compiled.
A simple way around this obstacle is to use the word @’ (cf. 4.6) that
looks up the current definition of the next word during the execution time
and then executes it, similarly to what we already did in 2.7:
{ ?dup { dup 1- @’ fact * } { 1 } cond } : fact
5 fact .
produces “120 ok”, as expected.
However, this solution is rather inefficient, because it uses a dictionary
lookup each time fact is recursively executed. We can avoid this dictionary
lookup by using variables (cf. 2.14 and 2.7):
32
3.5. Recursion
variable ’fact
{ ’fact @ execute } : fact
{ ?dup { dup 1- fact * } { 1 } cond } ’fact !
5 fact .
This somewhat longer definition of the factorial avoids dictionary lookups at
execution time by introducing a special variable ’fact to hold the final definition of the factorial.6 Then fact is defined to execute whatever WordDef
is currently stored in ’fact, and once the body of the recursive definition
of the factorial is constructed, it is stored into this variable by means of the
phrase ’fact !, which replaces the more customary phrase : fact.
We could rewrite the above definition by using special “getter” and “setter”
words for vector variable ’fact as we did for variables in 2.14:
variable ’fact
{ ’fact @ execute } : fact
{ ’fact ! } : :fact
forget ’fact
{ ?dup { dup 1- fact * } { 1 } cond } :fact
5 fact .
If we need to introduce a lot of recursive and mutually-recursive definitions,
we might first introduce a custom defining word (cf. 4.8) for simultaneously
defining both the “getter” and the “setter” words for anonymous vector variables, similarly to what we did in 2.14:
{ hole dup 1 { @ execute } does create 1 ’ ! does create
} : vector-set
vector-set fact :fact
{ ?dup { dup 1- fact * } { 1 } cond } :fact
5 fact .
The first three lines of this fragment define fact and :fact essentially in
the same way they had been defined in the first four lines of the previous
fragment.
If we wish to make fact unchangeable in the future, we can add a forget
:fact line once the definition of the factorial is complete:
6Variables that hold a WordDef to be executed later are called vector variables. The
process of replacing fact with ’fact @ execute, where ’fact is a vector variable, is
called vectorization.
33
3.5. Recursion
{ hole dup 1 { @ execute } does create 1 ’ ! does create
} : vector-set
vector-set fact :fact
{ ?dup { dup 1- fact * } { 1 } cond } :fact
forget :fact
5 fact .
Alternatively, we can modify the definition of vector-set in such a way that
:fact would forget itself once it is invoked:
{ hole dup 1 { @ execute } does create
bl word tuck 2 { (forget) ! } does swap 0 (create)
} : vector-set-once
vector-set-once fact :fact
{ ?dup { dup 1- fact * } { 1 } cond } :fact
5 fact .
However, some vector variables must be modified more than once, for
instance, to modify the behavior of the comparison word less in a merge
sort algorithm:
{ hole dup 1 { @ execute } does create 1 ’ ! does create
} : vector-set
vector-set sort :sort
vector-set merge :merge
vector-set less :less
{ null null rot
{ dup null? not }
{ uncons swap rot cons -rot } while drop
} : split
{ dup null? { drop } {
over null? { nip } {
over car over car less ’ swap if
uncons rot merge cons
} cond
} cond
} :merge
{ dup null? {
dup cdr null? {
34
3.6. Throwing exceptions
split sort swap sort merge
} ifnot
} ifnot
} :sort
forget :merge
forget :sort
// set ‘less‘ to compare numbers, sort a list of numbers
’ < :less
3 1 4 1 5 9 2 6 5 9 list
dup .l cr sort .l cr
// set ‘less‘ to compare strings, sort a list of strings
{ $cmp 0< } :less
"once" "upon" "a" "time" "there" "lived" "a" "kitten" 8 list
dup .l cr sort .l cr
producing the following output:
(3 1 4 1 5 9 2 6 5)
(1 1 2 3 4 5 5 6 9)
("once" "upon" "a" "time" "there" "lived" "a" "kitten")
("a" "a" "kitten" "lived" "once" "there" "time" "upon")
3.6 Throwing exceptions
Two built-in words are used to throw exceptions:
• abort (S – ), throws an exception with an error message taken from
String S.
• abort"hmessagei" (x – ), throws an exception with the error message
hmessagei if x is a non-zero integer.
The exception thrown by these words is represented by the C++ exception
fift::IntError with its value equal to the specified string. It is normally
handled within the Fift interpreter itself by aborting all execution up to
the top level and printing a message with the name of the source file being
interpreted, the line number, the currently interpreted word, and the specified
error message. For instance:
{ dup 0= abort"Division by zero" / } : safe/
5 0 safe/ .
35
***
**Expert(s)**: Fift Interpreter Expert
**Possible Keywords**: Fift interpreter, script mode, command line arguments, conditional execution, blocks, loops, recursion
**Question**: How can one use command line arguments in script mode with the Fift interpreter, and what are the key features related to blocks, loops, and conditionals in Fift?
**Plan**: I will provide a detailed explanation of using command line arguments in script mode with the Fift interpreter. Then, I'll delve into the features of blocks, loops, and conditionals in Fift, addressing the user's query step by step.

To use command line arguments in script mode with the Fift interpreter, the `-s` option is employed. The interpreter reads the next argument after `-s` as the filename of the Fift source file, and subsequent command line arguments are passed to the Fift program using special words like `$#` and `$n`. These words allow accessing the total number of command-line arguments and retrieving specific arguments by index.

For instance, a script named `cmdline.fif` could be invoked as follows:

```bash
$ ./cmdline.fif 12 -5
```

Where the Fift script might look like:

```Fift
#!/usr/bin/fift -s
{ ."usage: " $0 type ." <num1> <num2>" cr
."Computes the product of two integers." cr 1 halt } : usage
{ ’ usage if } : ?usage
$# 2 <> ?usage
$1 (number) 1- ?usage
$2 (number) 1- ?usage
* . cr
```

This script takes two numbers as command line arguments and prints their product.

Moving on to blocks, loops, and conditionals in Fift, the language operates using stack-based primitives. Blocks are defined using `{` and `}` and can be executed or stored as WordDefs. Conditional execution is achieved with `if`, `ifnot`, and `cond`, while loops include `times`, `until`, and `while`. Additionally, recursion is supported in Fift, allowing for the creation of recursive definitions using `@’` or variables.

I'll provide more detailed explanations and examples as we progress. Let's start with command line arguments in script mode.

### Command Line Arguments in Script Mode with Fift

When using the Fift interpreter in script mode, command line arguments can be processed by following a specific pattern. The `-s` option is used to invoke script mode, and subsequent command line arguments are accessed using special words in the Fift program.

#### Invoking Script Mode
The Fift interpreter is invoked in script mode as follows:

```bash
$ /path/to/fift -s script.fif arg1 arg2 ...
```

Here, `script.fif` is the Fift source file, and `arg1`, `arg2`, etc., are the command line arguments.

#### Accessing Command Line Arguments
Within the Fift script, special words are utilized to access command line arguments:

- `$# ( – x)`: Pushes the total number of command-line arguments.
- `$n ( – S)`: Pushes the n-th command-line argument as a String S.

For example, the script snippet below demonstrates a simple Fift script that takes two numbers as command line arguments and computes their product:

```Fift
#!/usr/bin/fift -s
{ ."usage: " $0 type ." <num1> <num2>" cr
 ."Computes the product of two integers." cr 1 halt } : usage
{ ’ usage if } : ?usage
$# 2 <> ?usage
$1 (number) 1- ?usage
$2 (number) 1- ?usage
* . cr
```

In this script, `$#` checks if there are exactly two command line arguments, and `$1` and `$2` are used to retrieve and process the numbers.

#### Execution Example
Assuming the script is saved as `cmdline.fif` and given execution permissions:

```bash
$ ./cmdline.fif 12 -5
```

This command prints the result of multiplying 12 and -5, which is -60.

### See also
- [Fift Interpreter Documentation](https://test.ton.org/DOCSDRAFT/ton-fift.html) for more details on command line arguments and script mode.
- [Fift Scripting Tutorial](https://test.ton.org/DOCSDRAFT/ton-fift.html#tutorial) for additional examples and in-depth explanations.
