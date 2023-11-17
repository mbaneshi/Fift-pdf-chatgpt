8
7.2. Fift assembler basics
Notice that TVM is executed with internal logging enabled, and its log is
displayed in the standard output.
Simple TVM programs may be represented by Slice literals with the aid
of the x{...} construct similarly to the above examples. More sophisticated
programs are usually created with the aid of the Fift assembler as explained
in the next chapter.
7 Using the Fift assembler
The Fift assembler is a short program (currently less than 30KiB) written
completely in Fift that transforms human-readable mnemonics of TVM instructions into their binary representation. For instance, one could write <{
s1 s2 XCHG OVER }>s instead of x{1221} in the example discussed in 6.4,
provided the Fift assembler has been loaded beforehand (usually by the
phrase "Asm.fif" include).
7.1 Loading the Fift assembler
The Fift assembler is usually located in file Asm.fif in the Fift library directory (which usually contains standard Fift library files such as Fift.fif).
It is typically loaded by putting the phrase "Asm.fif" include at the very
beginning of a program that needs to use Fift assembler:
• include (S – ), loads and interprets a Fift source file from the path
given by String S. If the filename S does not begin with a slash, the Fift
include search path, typically taken from the FIFTPATH environment
variable or the -I command-line argument of the Fift interpreter (and
equal to /usr/lib/fift if both are absent), is used to locate S.
The current implementation of the Fift assembler makes heavy use of custom
defining words (cf. 4.8); its source can be studied as a good example of how
defining words might be used to write very compact Fift programs (cf. also
the original edition of [1], where a simple 8080 Forth assembler is discussed).
In the future, almost all of the words defined by the Fift assembler will
be moved to a separate vocabulary (namespace). Currently they are defined
in the global namespace, because Fift does not support namespaces yet.
59
7.2. Fift assembler basics
7.2 Fift assembler basics
The Fift assembler inherits from Fift its postfix operation notation, i.e., the
arguments or parameters are written before the corresponding instructions.
For instance, the TVM assembler instruction represented as XCHG s1,s2 in
[4] is represented in the Fift assembler as s1 s2 XCHG.
Fift assembler code is usually opened by a special opening word, such as
<{, and terminated by a closing word, such as }> or }>s. For instance,
"Asm.fif" include
<{ s1 s2 XCHG OVER }>s
csr.
compiles two TVM instructions XCHG s1,s2 and OVER, and returns the result
as a Slice (because }>s is used). The resulting Slice is displayed by csr.,
yielding
x{1221}
One can use Appendix A of [4] and verify that x{12} is indeed the (codepage
zero) code of the TVM instruction XCHG s1,s2, and that x{21} is the code
of the TVM instruction OVER (not to be confused with Fift primitive over).
In the future, we will assume that the Fift assember is already loaded and
omit the phrase "Asm.fif" include from our examples.
The Fift assembler uses the Fift stack in a straightforward fashion, using
the top several stack entries to hold a Builder with the code being assembled,
and the arguments to TVM instructions. For example:
• <{ ( – b), begins a portion of Fift assembler code by pushing an empty
Builder into the Fift stack (and potentially switching the namespace
to the one containing all Fift assembler-specific words). Approximately
equivalent to <b.
• }> (b – b
0
), terminates a portion of Fift assembler code and returns the
assembled portion as a Builder (and potentially recovers the original
namespace). Approximately equivalent to nop in most situations.
• }>c (b – c), terminates a portion of Fift assembler code and returns
the assembled portion as a Cell (and potentially recovers the original
namespace). Approximately equivalent to b>.
60
7.3. Pushing integer constants
• }>s (b – s), terminates a portion of Fift assembler code similarly to }>,
but returns the assembled portion as a Slice. Equivalent to }>c <s.
• OVER (b – b
0
), assembles the code of the TVM instruction OVER by
appending it to the Builder at the top of the stack. Approximately
equivalent to x{21} s,.
• s1 ( – s), pushes a special Slice used by the Fift assembler to represent
the “stack register” s1 of TVM.
• s0. . . s15 ( – s), words similar to s1, but pushing the Slice representing other “stack registers” of TVM. Notice that s16. . . s255 must be
accessed using the word s().
• s() (x – s), takes an Integer argument 0 ≤ x ≤ 255 and returns a
special Slice used by the Fift assembler to represent “stack register”
s(x).
• XCHG (b s s0 – b
0
), takes two special Slices representing two “stack registers” s(i) and s(j) from the stack, and appends to Builder b the code
for the TVM instruction XCHG s(i),s(j).
In particular, note that the word OVER defined by the Fift assembler has a
completely different effect from Fift primitive over.
The actual action of OVER and other Fift assembler words is somewhat
more complicated than that of x{21} s,. If the new instruction code does
not fit into the Builder b (i.e., if b would contain more than 1023 data bits after
adding the new instruction code), then this and all subsequent instructions
are assembled into a new Builder ˜b, and the old Builder b is augmented by
a reference to the Cell obtained from ˜b once the generation of ˜b is finished.
In this way long stretches of TVM code are automatically split into chains
of valid Cells containing at most 1023 bits each. Because TVM interprets a
lonely cell reference at the end of a continuation as an implicit JMPREF, this
partitioning of TVM code into cells has almost no effect on the execution.
7.3 Pushing integer constants
The TVM instruction PUSHINT x, pushing an Integer constant x when invoked, can be assembled with the aid of Fift assembler words INT or PUSHINT:
61
7.4. Immediate arguments
• PUSHINT (b x – b
0
), assembles TVM instruction PUSHINT x into a Builder.
• INT (b x – b
0
), equivalent to PUSHINT.
Notice that the argument to PUSHINT is an Integer value taken from the Fift
stack and is not necessarily a literal. For instance, <{ 239 17 * INT }>s is
a valid way to assemble a PUSHINT 4063 instruction, because 239·17 = 4063.
Notice that the multiplication is performed by Fift during assemble time, not
during the TVM runtime. The latter computation might be performed by
means of <{ 239 INT 17 INT MUL }>s:
<{ 239 17 * INT }>s dup csr. runvmcode .s 2drop
<{ 239 INT 17 INT MUL }>s dup csr. runvmcode .s 2drop
produces
x{810FDF}
execute PUSHINT 4063
execute implicit RET
4063 0
ok
x{8100EF8011A8}
execute PUSHINT 239
execute PUSHINT 17
execute MUL
execute implicit RET
4063 0
ok
Notice that the Fift assembler chooses the shortest encoding of the PUSHINT x
instruction depending on its argument x.
7.4 Immediate arguments
Some TVM instructions (such as PUSHINT) accept immediate arguments.
These arguments are usually passed to the Fift word assembling the corresponding instruction in the Fift stack. Integer immediate arguments are
usually represented by Integer s, cells by Cells, continuations by Builder s and
Cells, and cell slices by Slices. For instance, 17 ADDCONST assembles TVM
instruction ADDCONST 17, and x{ABCD_} PUSHSLICE assembles PUSHSLICE
xABCD_:
62
7.5. Immediate continuations
239 <{ 17 ADDCONST x{ABCD_} PUSHSLICE }>s dup csr.
runvmcode . swap . csr.
produces
x{A6118B2ABCD0}
execute ADDINT 17
execute PUSHSLICE xABCD_
execute implicit RET
0 256 x{ABCD_}
On some occasions, the Fift assembler pretends to be able to accept immediate arguments that are out of range for the corresponding TVM instruction.
For instance, ADDCONST x is defined only for −128 ≤ x < 128, but the Fift
assembler accepts 239 ADDCONST:
17 <{ 239 ADDCONST }>s dup csr. runvmcode .s
produces
x{8100EFA0}
execute PUSHINT 239
execute ADD
execute implicit RET
256 0
We can see that “ADDCONST 239” has been tacitly replaced by PUSHINT
239 and ADD. This feature is convenient when the immediate argument to
ADDCONST is itself a result of a Fift computation, and it is difficult to estimate whether it will always fit into the required range.
In some cases, there are several versions of the same TVM instructions,
one accepting an immediate argument and another without any arguments.
For instance, there are both LSHIFT n and LSHIFT instructions. In the
Fift assembler, such variants are assigned distinct mnemonics. In particular, LSHIFT n is represented by n LSHIFT#, and LSHIFT is represented by
itself.
7.5 Immediate continuations
When an immediate argument is a continuation, it is convenient to create
the corresponding Builder in the Fift stack by means of a nested <{ . . . }>
construct. For instance, TVM assembler instructions
63
7.5. Immediate continuations
PUSHINT 1
SWAP
PUSHCONT {
MULCONST 10
}
REPEAT
can be assembled and executed by
7
<{ 1 INT SWAP <{ 10 MULCONST }> PUSHCONT REPEAT }>s dup csr.
runvmcode drop .
producing
x{710192A70AE4}
execute PUSHINT 1
execute SWAP
execute PUSHCONT xA70A
execute REPEAT
repeat 7 more times
execute MULINT 10
execute implicit RET
repeat 6 more times
...
repeat 1 more times
execute MULINT 10
execute implicit RET
repeat 0 more times
execute implicit RET
10000000
More convenient ways to use literal continuations created by means of the
Fift assembler exist. For instance, the above example can be also assembled
by
<{ 1 INT SWAP CONT:<{ 10 MULCONST }> REPEAT }>s csr.
or even
<{ 1 INT SWAP REPEAT:<{ 10 MULCONST }> }>s csr.
64
7.6. Control flow: loops and conditionals
both producing “x{710192A70AE4} ok”.
Incidentally, a better way of implementing the above loop is by means of
REPEATEND:
7 <{ 1 INT SWAP REPEATEND 10 MULCONST }>s dup csr.
runvmcode drop .
or
7 <{ 1 INT SWAP REPEAT: 10 MULCONST }>s dup csr.
runvmcode drop .
both produce “x{7101E7A70A}” and output “10000000” after seven iterations
of the loop.
Notice that several TVM instructions that store a continuation in a separate cell reference (such as JMPREF) accept their argument in a Cell, not
in a Builder. In such situations, the <{ ... }>c construct can be used to
produce this immediate argument.
7.6 Control flow: loops and conditionals
Almost all TVM control flow instructions—such as IF, IFNOT, IFRET, IFNOTRET,
IFELSE, WHILE, WHILEEND, REPEAT, REPEATEND, UNTIL, and UNTILEND—can
be assembled similarly to REPEAT and REPEATEND in the examples of 7.5
when applied to literal continuations. For instance, TVM assembler code
DUP
PUSHINT 1
AND
PUSHCONT {
MULCONST 3
INC
}
PUSHCONT {
RSHIFT 1
}
IFELSE
which computes 3n + 1 or n/2 depending on whether its argument n is odd
or even, can be assembled and applied to n = 7 by
65
7.6. Control flow: loops and conditionals
<{ DUP 1 INT AND
IF:<{ 3 MULCONST INC }>ELSE<{ 1 RSHIFT# }>
}>s dup csr.
7 swap runvmcode drop .
producing
x{2071B093A703A492AB00E2}
ok
execute DUP
execute PUSHINT 1
execute AND
execute PUSHCONT xA703A4
execute PUSHCONT xAB00
execute IFELSE
execute MULINT 3
execute INC
execute implicit RET
execute implicit RET
22 ok
Of course, a more compact and efficient way to implement this conditional
expression would be
<{ DUP 1 INT AND
IF:<{ 3 MULCONST INC }>ELSE: 1 RSHIFT#
}>s dup csr.
or
<{ DUP 1 INT AND
CONT:<{ 3 MULCONST INC }> IFJMP
1 RSHIFT#
}>s dup csr.
both producing the same code “x{2071B093A703A4DCAB00}”.
Fift assembler words that can be used to produce such “high-level” conditionals and loops include IF:<{, IFNOT:<{, IFJMP:<{, }>ELSE<{, }>ELSE:,
}>IF, REPEAT:<{, UNTIL:<{, WHILE:<{, }>DO<{, }>DO:, AGAIN:<{, }>AGAIN,
}>REPEAT, and }>UNTIL. Their complete list can be found in the source file
66
7.7. Macro definitions
Asm.fif. For instance, an UNTIL loop can be created by UNTIL:<{ ... }>
or <{ ... }>UNTIL, and a WHILE loop by WHILE:<{ ... }>DO<{ ... }>.
If we choose to keep a conditional branch in a separate cell, we can use
the <{ ... }>c construct along with instructions such as IFJMPREF:
<{ DUP 1 INT AND
<{ 3 MULCONST INC }>c IFJMPREF
1 RSHIFT#
}>s dup csr.
3 swap runvmcode .s
has the same effect as the code from the previous example when executed,
but it is contained in two separate cells:
x{2071B0E302AB00}
x{A703A4}
execute DUP
execute PUSHINT 1
execute AND
execute IFJMPREF (2946....A1DD)
execute MULINT 3
execute INC
execute implicit RET
10 0
7.7 Macro definitions
Because TVM instructions are implemented in the Fift assembler using Fift
words that have a predictable effect on the Fift stack, the Fift assembler
is automatically a macro assembler, supporting macro definitions. For instance, suppose that we wish to define a macro definition RANGE x y, which
checks whether the TVM top-of-stack value is between integer literals x and
y (inclusive). This macro definition can be implemented as follows:
{ 2dup > ’ swap if
rot DUP rot GEQINT SWAP swap LEQINT AND
} : RANGE
<{ DUP 17 239 RANGE IFNOT: DROP ZERO }>s dup csr.
66 swap runvmcode drop .
67
7.8. Larger programs and subroutines
which produces
x{2020C210018100F0B9B0DC3070}
execute DUP
execute DUP
execute GTINT 16
execute SWAP
execute PUSHINT 240
execute LESS
execute AND
execute IFRET
66
Notice that GEQINT and LEQINT are themselves macro definitions defined in
Asm.fif, because they do not correspond directly to TVM instructions. For
instance, x GEQINT corresponds to the TVM instruction GTINT x − 1.
Incidentally, the above code can be shortened by two bytes by replacing
IFNOT: DROP ZERO with AND.
7.8 Larger programs and subroutines
Larger TVM programs, such as TON Blockchain smart contracts, typically
consist of several mutually recursive subroutines, with one or several of them
selected as top-level subroutines (called main() or recv_internal() for
smart contracts). The execution starts from one of the top-level subroutines, which is free to call any of the other defined subroutines, which in turn
can call whatever other subroutines they need.
Such TVM programs are implemented by means of a selector function,
which accepts an extra integer argument in the TVM stack; this integer
selects the actual subroutine to be invoked (cf. [4, 4.6]). Before execution, the
code of this selector function is loaded both into special register c3 and into
the current continuation cc. The selector of the main function (usually zero)
is pushed into the initial stack, and the TVM execution is started. Afterwards
a subroutine can be invoked by means of a suitable TVM instruction, such as
CALLDICT n, where n is the (integer) selector of the subroutine to be called.
The Fift assembler offers several words facilitating the implementation of
such large TVM programs. In particular, subroutines can be defined separately and assigned symbolic names (instead of numeric selectors), which
68
7.8. Larger programs and subroutines
can be used to call them afterwards. The Fift assembler automatically creates a selector function from these separate subroutines and returns it as the
top-level assembly result.
Here is a simple example of such a program consisting of several subroutines. This program computes the complex number (5 + i)
4
· (239 − i):
"Asm.fif" include
PROGRAM{
NEWPROC add
NEWPROC sub
NEWPROC mul
sub <{ s3 s3 XCHG2 SUB s2 XCHG0 SUB }>s PROC
// compute (5+i)^4 * (239-i)
main PROC:<{
5 INT 1 INT // 5+i
2DUP
mul CALL
2DUP
mul CALL
239 INT -1 INT
mul JMP
}>
add PROC:<{
s1 s2 XCHG
ADD -ROT ADD SWAP
}>
// a b c d -- ac-bd ad+bc : complex number multiplication
mul PROC:<{
s3 s1 PUSH2 // a b c d a c
MUL // a b c d ac
s3 s1 PUSH2 // a b c d ac b d
MUL // a b c d ac bd
69
7.8. Larger programs and subroutines
SUB // a b c d ac-bd
s4 s4 XCHG2 // ac-bd b c a d
MUL // ac-bd b c ad
-ROT MUL ADD
}>
}END>s
dup csr.
runvmdict .s
This program produces:
x{FF00F4A40EF4A0F20B}
x{D9_}
x{2_}
x{1D5C573C00D73C00E0403BDFFC5000E_}
x{04A81668006_}
x{2_}
x{140CE840A86_}
x{14CC6A14CC6A2854112A166A282_}
implicit PUSH 0 at start
execute SETCP 0
execute DICTPUSHCONST 14 (xC_,1)
execute DICTIGETJMP
execute PUSHINT 5
execute PUSHINT 1
execute 2DUP
execute CALLDICT 3
execute SETCP 0
execute DICTPUSHCONST 14 (xC_,1)
execute DICTIGETJMP
execute PUSH2 s3,s1
execute MUL
...
execute ROTREV
execute MUL
execute ADD
execute implicit RET
114244 114244 0
70
7.8. Larger programs and subroutines
Some observations and comments based on the previous example follow:
• A TVM program is opened by PROGRAM{ and closed by either }END>c
(which returns the assembled program as a Cell) or }END>s (which
returns a Slice).
• A new subroutine is declared by means of the phrase NEWPROC hnamei.
This declaration assigns the next positive integer as a selector for the
newly-declared subroutine, and stores this integer into the constant
hnamei. For instance, the above declarations define add, sub, and mul
as integer constants equal to 1, 2, and 3, respectively.
• Some subroutines are predeclared and do not need to be declared again
by NEWPROC. For instance, main is a subroutine identifier bound to the
integer constant (selector) 0.
• Other predefined subroutine selectors such as recv_internal (equal
to 0) or recv_external (equal to −1), useful for implementing TON
Blockchain smart contracts (cf. [5, 4.4]), can be declared by means of
constant (e.g., -1 constant recv_external).
• A subroutine can be defined either with the aid of the word PROC, which
accepts the integer selector of the subroutine and the Slice containing
the code for this subroutine, or with the aid of the construct hselectori
PROC:<{ ... }>, convenient for defining larger subroutines.
• CALLDICT and JMPDICT instructions may be assembled with the aid
of the words CALL and JMP, which accept the integer selector of the
subroutine to be called as an immediate argument passed in the Fift
stack.
• The current implementation of the Fift assembler collects all subroutines into a dictionary with 14-bit signed integer keys. Therefore, all
subroutine selectors must be in the range −2
13
. . . 2
13 − 1.
• If a subroutine with an unknown selector is called during runtime, an
exception with code 11 is thrown by the code automatically inserted
by the Fift assembler. This code also automatically selects codepage
zero for instruction encoding by means of a SETCP0 instruction.
71
7.8. Larger programs and subroutines
• The Fift assembler checks that all subroutines declared by NEWPROC are
actually defined by PROC or PROC:<{ before the end of the program. It
also checks that a subroutine is not redefined.
One should bear in mind that very simple programs (including the simplest smart contracts) may be made more compact by eliminating this general
subroutine selection machinery in favor of custom subroutine selection code
and removing unused subroutines. For instance, the above example can be
transformed into
<{ 11 THROWIF
CONT:<{ s3 s1 PUSH2 MUL s3 s1 PUSH2 MUL SUB
s4 s4 XCHG2 MUL -ROT MUL ADD }>
5 INT 1 INT 2DUP s4 PUSH CALLX
2DUP s4 PUSH CALLX
ROT 239 INT -1 INT ROT JMPX
}>s
dup csr.
runvmdict .s
which produces
x{F24B9D5331A85331A8A15044A859A8A075715C24D85C24D8588100EF7F58D9}
implicit PUSH 0 at start
execute THROWIF 11
execute PUSHCONT x5331A85331A8A15044A859A8A0
execute PUSHINT 5
execute PUSHINT 1
execute 2DUP
execute PUSH s4
execute EXECUTE
execute PUSH2 s3,s1
execute MUL
...
execute XCHG2 s4,s4
execute MUL
execute ROTREV
execute MUL
execute ADD
72
7.8. Larger programs and subroutines
execute implicit RET
114244 114244 0
73
References
References
[1] L. Brodie, Starting Forth: Introduction to the FORTH Language and
Operating System for Beginners and Professionals, 2nd edition, Prentice
Hall, 1987. Available at https://www.forth.com/starting-forth/.
[2] L. Brodie, Thinking Forth: A language and philosophy for solving
problems, Prentice Hall, 1984. Available at http://thinking-forth.
sourceforge.net/.
[3] N. Durov, Telegram Open Network, 2017.
[4] N. Durov, Telegram Open Network Virtual Machine, 2018.
[5] N. Durov, Telegram Open Network Blockchain, 2018.
74
***
**Expert(s)**: Telegram Open Network (TON) Virtual Machine Expert, Forth Language Expert
**Possible Keywords**: TVM, Fift assembler, Forth language, assembler basics, macro assembler, subroutine, control flow, immediate arguments
**Question**: What are the basics of using the Fift assembler in the context of the Telegram Open Network (TON) Virtual Machine (TVM)? How does it handle control flow, immediate arguments, and subroutine definitions?
**Plan**: I will provide a detailed explanation of the Fift assembler basics, focusing on control flow, immediate arguments, and subroutine definitions in the context of the TVM. I'll break down the information into manageable sections to ensure clarity and understanding.

***

### Fift Assembler Basics in the TON Virtual Machine (TVM)

#### 1. **Introduction to Fift Assembler**
The Fift assembler is a critical component of the Telegram Open Network (TON) Virtual Machine (TVM). It translates human-readable mnemonics of TVM instructions into their binary representation.

#### 2. **Loading the Fift Assembler**
To use the Fift assembler, it needs to be loaded with the phrase "Asm.fif include." Typically located in the Fift library directory, it's loaded using the "include" word.

```forth
include "Asm.fif"
```

#### 3. **Fift Assembler Basics**
- **Postfix Notation**: The Fift assembler inherits postfix operation notation from Forth. Arguments precede corresponding instructions.
- **Code Representation**: Fift assembler code is represented between `<{` and `}>` or `}>s`, where `s` denotes Slice.
  
```forth
<{ s1 s2 XCHG OVER }>s
```

#### 4. **Fift Assembler Instructions**
- **Loading and Terminating Code**: Instructions like `<{`, `}>`, `}>c`, and `}>s` facilitate code loading and termination.
- **Instructions like `OVER` and `XCHG`**: Used to represent TVM instructions. The Fift assembler supports a variety of TVM instructions.

```forth
<{ s1 s2 XCHG OVER }>s
```

### Control Flow in Fift Assembler

#### 5. **Loops and Conditionals**
- Fift assembler supports control flow instructions like `IF`, `ELSE`, `REPEAT`, etc.
- Examples demonstrate the assembly of TVM control flow instructions using Fift.

#### 6. **Immediate Arguments**
- TVM instructions like `PUSHINT` with immediate arguments are assembled using Fift assembler words like `INT` or `PUSHINT`.
- Immediate arguments can be out of the expected range in some cases, and the Fift assembler handles it gracefully.

#### 7. **Subroutines and Macro Definitions**
- Fift assembler simplifies the implementation of subroutines, supporting macro definitions.
- Subroutines are declared with `NEWPROC`, defined with `PROC`, and invoked using `CALLDICT`.

#### 8. **Large Programs and Subroutines**
- Larger TVM programs involve multiple mutually recursive subroutines.
- Fift assembler provides convenient words like `PROGRAM`, `NEWPROC`, and `PROC` for defining and organizing larger programs.

### See also
- 📜 [Fift Documentation](https://test.ton.org/ton.pdf) for comprehensive details on Fift and TVM.
- 📘 [Telegram Open Network](https://en.wikipedia.org/wiki/Telegram_Open_Network) for a broader overview of the TON ecosystem.

### You may also enjoy
- 🚀 [Building Smart Contracts on TON](https://www.google.com/search?q=building+smart+contracts+on+TON) for practical insights into TON smart contract development.
