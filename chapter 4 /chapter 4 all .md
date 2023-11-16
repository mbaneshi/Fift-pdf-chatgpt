4.1. The state of the Fift interpreter
prints “safe/: Division by zero”, without the usual “ok”. The stack is
cleared in the process.
Incidentally, when the Fift interpreter encounters an unknown word that
cannot be parsed as an integer literal, an exception with the message “-?” is
thrown, with the effect indicated above, including the stack being cleared.
4 Dictionary, interpreter, and compiler
In this chapter we present several specific Fift words for dictionary manipulation and compiler control. The “compiler” is the part of the Fift interpreter
that builds lists of word references (represented by WordList stack values)
from word names; it is activated by the primitive “{” employed for defining
blocks as explained in 2.6 and 3.1.
Most of the information included in this chapter is rather sophisticated
and may be skipped during a first reading. However, the techniques described
here are heavily employed by the Fift assembler, used to compile TVM code.
Therefore, this section is indispensable if one wishes to understand the current implementation of the Fift assembler.
4.1 The state of the Fift interpreter
The state of the Fift interpreter is controlled by an internal integer variable
called state, currently unavailable from Fift itself. When state is zero,
all words parsed from the input (i.e., the Fift source file or the standard
input in the interactive mode) are looked up in the dictionary and immediately executed afterwards. When state is positive, the words found in the
dictionary are not executed. Instead, they (or rather the references to their
current definitions) are compiled, i.e., added to the end of the WordList being
constructed.
Typically, state equals the number of the currently open blocks. For
instance, after interpreting “{ 0= { ."zero"” the state variable will be
equal to two, because there are two nested blocks. The WordList being
constructed is kept at the top of the stack.
The primitive “{” simply pushes a new empty WordList into the stack,
and increases state by one. The primitive “}” throws an exception if state
is already zero; otherwise it decreases state by one, and leaves the resulting
36
4.3. Compiling literals
WordList in the stack, representing the block just constructed.7 After that,
if the resulting value of state is non-zero, the new block is compiled as a
literal (unnamed constant) into the encompassing block.
4.2 Active and ordinary words
All dictionary words have a special flag indicating whether they are active
words or ordinary words. By default, all words are ordinary. In particular,
all words defined with the aid of “:” and constant are ordinary.
When the Fift interpreter finds a word definition in the dictionary, it
checks whether it is an ordinary word. If it is, then the current word definition
is either executed (if state is zero) or “compiled” (if state is greater than
zero) as explained in 4.1.
On the other hand, if the word is active, then it is always executed, even
if state is positive. An active word is expected to leave some values x1 . . . xn
n e in the stack, where n ≥ 0 is an integer, x1 . . . xn are n values of arbitrary
types, and e is an execution token (a value of type WordDef ). After that,
the interpreter performs different actions depending on state: if state is
zero, then n is discarded and e is executed, as if a nip execute phrase were
found. If state is non-zero, then this collection is “compiled” in the current
WordList (located immediately below x1 in the stack) in the same way as if
the (compile) primitive were invoked. This compilation amounts to adding
some code to the end of the current WordList that would push x1, . . . , xn
into the stack when invoked, and then adding a reference to e (representing
a delayed execution of e). If e is equal to the special value ’nop, representing
an execution token that does nothing when executed, then this last step is
omitted.
4.3 Compiling literals
When the Fift interpreter encounters a word that is absent from the dictionary, it invokes the primitive (number) to attempt to parse it as an integer
or fractional literal. If this attempt succeeds, then the special value ’nop is
pushed, and the interpretation proceeds in the same way as if an active word
7The word } also transforms this WordList into a WordDef, which has a different type
tag and therefore is a different Fift value, even if the same underlying C++ object is used
by the C++ implementation.
37
4.5. Defining words and dictionary manipulation
were encountered. In other words, if state is zero, then the literal is simply left in the stack; otherwise, (compile) is invoked to modify the current
WordList so that it would push the literal when executed.
4.4 Defining new active words
New active words are defined similarly to new ordinary words, but using “::”
instead of “:”. For instance,
{ bl word 1 ’ type } :: say
defines the active word say, which scans the next blank-separated word after
itself and compiles it as a literal along with a reference to the current definition of type into the current WordList (if state is non-zero, i.e., if the Fift
interpreter is compiling a block). When invoked, this addition to the block
will push the stored string into the stack and execute type, thus printing the
next word after say. On the other hand, if state is zero, then these two
actions are performed by the Fift interpreter immediately. In this way,
1 2 say hello + .
will print “hello3 ok”, while
{ 2 say hello + . } : test
1 test 4 test
will print “hello3 hello6 ok”.
Of course, a block may be used to represent the required action instead
of ’ type. For instance, if we want a version of say that prints a space after
the stored word, we can write
{ bl word 1 { type space } } :: say
{ 2 say hello + . } : test
1 test 4 test
to obtain “hello 3 hello 6 ok”.
Incidentally, the words " (introducing a string literal) and ." (printing a
string literal) can be defined as follows:
{ char " word 1 ’nop } ::_ "
{ char " word 1 ’ type } ::_ ."
The new defining word “::_” defines an active prefix word, i.e., an active
word that does not require a space afterwards.
38
4.5. Defining words and dictionary manipulation
4.5 Defining words and dictionary manipulation
Defining words are words that define new words in the Fift dictionary. For
instance, “:”, “::_”, and constant are defining words. All of these defining
words might have been defined using the primitive (create); in fact, the user
can introduce custom defining words if so desired. Let us list some defining
words and dictionary manipulation words:
• create hword-namei (e – ), defines a new ordinary word with the name
equal to the next word scanned from the input, using WordDef e as its
definition. If the word already exists, it is tacitly redefined.
• (create) (e S x – ), creates a new word with the name equal to String
S and definition equal to WordDef e, using flags passed in Integer
0 ≤ x ≤ 3. If bit +1 is set in x, creates an active word; if bit +2 is set
in x, creates a prefix word.
• : hword-namei (e – ), defines a new ordinary word hword-namei in
the dictionary using WordDef e as its definition. If the specified word
is already present in the dictionary, it is tacitly redefined.
• forget hword-namei ( – ), forgets (removes from the dictionary) the
definition of the specified word.
• (forget) (S – ), forgets the word with the name specified in String S.
If the word is not found, throws an exception.
• :_ hword-namei (e – ), defines a new ordinary prefix word hword-namei,
meaning that a blank or an end-of-line character is not required by the
Fift input parser after the word name. In all other respects it is similar
to “:”.
• :: hword-namei (e – ), defines a new active word hword-namei in the
dictionary using WordDef e as its definition. If the specified word is
already present in the dictionary, it is tacitly redefined.
• ::_ hword-namei (e – ), defines a new active prefix word hword-namei,
meaning that a blank or an end-of-line character is not required by the
Fift input parser after the word name. In all other respects it is similar
to “::”.
39
4.6. Dictionary lookup
• constant hword-namei (x – ), defines a new ordinary word hword-namei
that would push the given value x when invoked.
• 2constant hword-namei (x y – ), defines a new ordinary word named
hword-namei that would push the given values x and y (in that order)
when invoked.
• =: hword-namei (x – ), defines a new ordinary word hword-namei that
would push the given value x when invoked, similarly to constant, but
works inside blocks and colon definitions.
• 2=: hword-namei (x y – ), defines a new ordinary word hword-namei
that would push the given values x and y (in that order) when invoked,
similarly to 2constant, but works inside blocks and colon definitions.
Notice that most of the above words might have been defined in terms of
(create):
{ bl word 1 2 ’ (create) } "::" 1 (create)
{ bl word 0 2 ’ (create) } :: :
{ bl word 2 2 ’ (create) } :: :_
{ bl word 3 2 ’ (create) } :: ::_
{ bl word 0 (create) } : create
{ bl word (forget) } : forget
4.6 Dictionary lookup
The following words can be used to look up words in the dictionary:
• ’ hword-namei ( – e), pushes the definition of the word hword-namei,
recovered at the compile time. If the indicated word is not found,
throws an exception. Notice that ’ hword-namei execute is always
equivalent to hword-namei for ordinary words, but not for active words.
• nop ( – ), does nothing.
• ’nop ( – e), pushes the default definition of nop—an execution token
that does nothing when executed.
• find (S – e −1 or e 1 or 0), looks up String S in the dictionary
and returns its definition as a WordDef e if found, followed by −1 for
ordinary words or 1 for active words. Otherwise pushes 0.
40
4.7. Creating and manipulating word lists
• (’) hword-namei ( – e), similar to ’, but returns the definition of
the specified word at execution time, performing a dictionary lookup
each time it is invoked. May be used to recover current values of constants inside word definitions and other blocks by using the phrase (’)
hword-namei execute.
• @’ hword-namei ( – e), similar to (’), but recovers the definition of
the specified word at execution time, performing a dictionary lookup
each time it is invoked, and then executes this definition. May be
used to recover current values of constants inside word definitions and
other blocks by using the phrase @’ hword-namei, equivalent to (’)
hword-namei execute, cf. 2.7.
• [compile] hword-namei ( – ), compiles hword-namei as if it were an ordinary word, even if it is active. Essentially equivalent to ’ hword-namei
execute.
• words ( – ), prints the names of all words currently defined in the
dictionary.
4.7 Creating and manipulating word lists
In the Fift stack, lists of references to word definitions and literals, to be
used as blocks or word definitions, are represented by the values of the type
WordList. Some words for manipulating WordLists include:
• { ( – l), an active word that increases internal variable state by one
and pushes a new empty WordList into the stack.
• } (l – e), an active word that transforms a WordList l into a WordDef (an execution token) e, thus making all further modifications of l
impossible, and decreases internal variable state by one and pushes
the integer 1, followed by a ’nop. The net effect is to transform the
constructed WordList into an execution token and push this execution
token into the stack, either immediately or during the execution of an
outer block.
• ({) ( – l), pushes an empty WordList into the stack.
• (}) (l – e), transforms a WordList into an execution token, making all
further modifications impossible.
41
4.8. Custom defining words
• (compile) (l x1 . . . xn n e – l
0
), extends WordList l so that it would
push 0 ≤ n ≤ 255 values x1, . . . , xn into the stack and execute the
execution token e when invoked, where 0 ≤ n ≤ 255 is an Integer. If e
is equal to the special value ’nop, the last step is omitted.
• does (x1 . . . xn n e – e
0
), creates a new execution token e
0
that would
push n values x1, . . . , xn into the stack and then execute e. It is roughly
equivalent to a combination of ({), (compile), and (}).
4.8 Custom defining words
The word does is actually defined in terms of simpler words:
{ swap ({) over 2+ -roll swap (compile) (}) } : does
It is especially useful for defining custom defining words. For instance,
constant and 2constant may be defined with the aid of does and create:
{ 1 ’nop does create } : constant
{ 2 ’nop does create } : 2constant
Of course, non-trivial actions may be performed by the words defined by
means of such custom defining words. For instance,
{ 1 { type space } does create } : says
"hello" says hello
"unknown error" says error
{ hello error } : test
test
will print “hello unknown error ok”, because hello is defined by means
of a custom defining word says to print “hello” whenever invoked, and
similarly error prints “unknown error” when invoked. The above definitions
are essentially equivalent to
{ ."hello" } : hello
{ ."unknown error" } : error
However, custom defining words may perform more sophisticated actions
when invoked, and preprocess their arguments at compile time. For instance,
the message can be computed in a non-trivial fashion:
42
5.1. Slice literals
"Hello, " "world!" $+ says hw
defines word hw, which prints “Hello, world!” when invoked. The string with
this message is computed once at compile time (when says is invoked), not
at execution time (when hw is invoked).
5 Cell manipulation
We have discussed the basic Fift primitives not related to TVM or the TON
Blockchain so far. Now we will turn to TON-specific words, used to manipulate Cells.
***
**Expert(s)**: Fift Language and Compiler Experts
**Possible Keywords**: Fift interpreter, dictionary manipulation, compiler control, WordList, active and ordinary words, literals, defining words, dictionary lookup, custom defining words, cell manipulation
**Question**: Can you provide an overview of the key concepts related to the Fift interpreter, including the state control mechanism, active and ordinary words, dictionary manipulation, and the creation and manipulation of WordLists?
**Plan**: I'll break down the information into key sections, explaining each concept in detail. I'll cover the state of the Fift interpreter, active and ordinary words, dictionary manipulation, compiling literals, defining words, and custom defining words.

***

### Fift Interpreter Overview

#### 4.1 The State of the Fift Interpreter
The Fift interpreter's behavior is influenced by an internal variable called `state`. When `state` is zero, parsed words are immediately executed; when positive, they are compiled into a WordList. The `state` typically represents the number of open blocks.

- The primitive `{` increases `state` and pushes a new empty WordList.
- The primitive `}` decreases `state` and transforms the current WordList into a WordDef if `state` is not zero.

#### 4.2 Active and Ordinary Words
All dictionary words have a flag indicating if they are active or ordinary. Active words are always executed, even if `state` is positive. Ordinary words are executed or compiled based on the value of `state`.

#### 4.3 Compiling Literals
When encountering a word absent from the dictionary, the interpreter attempts to parse it as a literal. If successful, it pushes the literal, and if `state` is positive, it invokes the `(compile)` primitive to modify the WordList.

#### 4.4 Defining New Active Words
Active words are defined using `::`. Examples include defining a word `say` that compiles the next word as a literal along with a reference to the current definition of `type`.

### Dictionary Manipulation

#### 4.5 Defining Words and Dictionary Manipulation
Defining words like `create`, `(create)`, `:`, `forget`, `::`, etc., manipulate the Fift dictionary. Custom defining words like `does` enable sophisticated actions during definition.

#### 4.6 Dictionary Lookup
Words like `'`, `nop`, `find`, `(')`, `@'`, `[compile]`, and `words` are used for dictionary lookup and manipulation.

#### 4.7 Creating and Manipulating Word Lists
WordLists, represented by `WordList` values, are used for blocks or word definitions. Words like `{`, `}`, `({)`, `(})`, `(compile)`, and `does` manipulate WordLists.

#### 4.8 Custom Defining Words
`does` and `create` can be used to define custom defining words. Examples include defining `constant` and `2constant`.

### Additional Concepts

#### 5.1 Slice Literals and Cell Manipulation
For completeness, there's a mention of slice literals and a transition to TON-specific words for cell manipulation.

This overview provides a comprehensive understanding of the key concepts related to the Fift interpreter.

### See also
- 📘 [Fift Documentation](https://fift-language.readthedocs.io/en/latest/)
- 📖 [TON Blockchain](https://en.wikipedia.org/wiki/Telegram_Open_Network) and [TVM](https://en.wikipedia.org/wiki/Telegram_Virtual_Machine) for TON-specific concepts.

### You may also enjoy
- 🚀 [TON Development Tools](https://docs.ton.dev/86757ecb2/p/54401d) for exploring TON development resources.
