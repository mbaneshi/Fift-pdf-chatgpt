5.1. Slice literals
"Hello, " "world!" $+ says hw
defines word hw, which prints “Hello, world!” when invoked. The string with
this message is computed once at compile time (when says is invoked), not
at execution time (when hw is invoked).
5 Cell manipulation
We have discussed the basic Fift primitives not related to TVM or the TON
Blockchain so far. Now we will turn to TON-specific words, used to manipulate Cells.
5.1 Slice literals
Recall that a (TVM) Cell consists of at most 1023 data bits and at most four
references to other Cells, a Slice is a read-only view of a portion of a Cell, and
a Builder is used to create new Cells. Fift has special provisions for defining
Slice literals (i.e., unnamed constants), which can also be transformed into
Cells if necessary.
Slice literals are introduced by means of active prefix words x{ and b{:
• b{hbinary-datai} ( – s), creates a Slice s that contains no references
and up to 1023 data bits specified in hbinary-datai, which must be a
string consisting only of the characters ‘0’ and ‘1’.
• x{hhex-datai} ( – s), creates a Slice s that contains no references and
up to 1023 data bits specified in hhex-datai. More precisely, each hex
digit from hhex-datai is transformed into four binary digits in the usual
fashion. After that, if the last character of hhex-datai is an underscore _,
then all trailing binary zeroes and the binary one immediately preceding
them are removed from the resulting binary string (cf. [4, 1.0] for more
details).
In this way, b{00011101} and x{1d} both push the same Slice consisting of
eight data bits and no references. Similarly, b{111010} and x{EA_} push the
same Slice consisting of six data bits. An empty Slice can be represented as
b{} or x{}.
If one wishes to define constant Slices with some Cell references, the
following words might be used:
43
5.2. Builder primitives
• |_ (s s0 – s
00), given two Slices s and s
0
, creates a new Slice s
00, which is
obtained from s by appending a new reference to a Cell containing s
0
.
• |+ (s s0 – s
00), concatenates two Slices s and s
0
. This means that the
data bits of the new Slice s
00 are obtained by concatenating the data
bits of s and s
0
, and the list of Cell references of s
00 is constructed
similarly by concatenating the corresponding lists for s and s
0
.
5.2 Builder primitives
The following words can be used to manipulate Builder s, which can later be
used to construct new Cells:
• <b ( – b), creates a new empty Builder.
• b> (b – c), transforms a Builder b into a new Cell c containing the same
data as b.
• i, (b x y – b
0
), appends the big-endian binary representation of a signed
y-bit integer x to Builder b, where 0 ≤ y ≤ 257. If there is not enough
room in b (i.e., if b already contains more than 1023 − y data bits), or
if Integer x does not fit into y bits, an exception is thrown.
• u, (b x y – b
0
), appends the big-endian binary representation of an
unsigned y-bit integer x to Builder b, where 0 ≤ y ≤ 256. If the
operation is impossible, an exception is thrown.
• ref, (b c – b
0
), appends to Builder b a reference to Cell c. If b already
contains four references, an exception is thrown.
• s, (b s – b
0
), appends data bits and references taken from Slice s to
Builder b.
• sr, (b s – b
0
), constructs a new Cell containing all data and references from Slice s, and appends a reference to this cell to Builder b.
Equivalent to <b swap s, b> ref,.
• $, (b S – b
0
), appends String S to Builder b. The string is interpreted
as a binary string of length 8n, where n is the number of bytes in the
UTF-8 representation of S.
44
5.2. Builder primitives
• B, (b B – b
0
), appends Bytes B to Builder b.
• b+ (b b0 – b
00), concatenates two Builders b and b
0
.
• bbits (b – x), returns the number of data bits already stored in Builder b.
The result x is an Integer in the range 0 . . . 1023.
• brefs (b – x), returns the number of references already stored in
Builder b. The result x is an Integer in the range 0 . . . 4.
• bbitrefs (b – x y), returns both the number of data bits x and the
number of references y already stored in Builder b.
• brembits (b – x), returns the maximum number of additional data bits
that can be stored in Builder b. Equivalent to bbits 1023 swap -.
• bremrefs (b – x), returns the maximum number of additional cell references that can be stored in Builder b.
• brembitrefs (b – x y), returns both the maximum number of additional
data bits 0 ≤ x ≤ 1023 and the maximum number of additional cell
references 0 ≤ y ≤ 4 that can be stored in Builder b.
The resulting Builder may be inspected by means of the non-destructive
stack dump primitive .s, or by the phrase b> <s csr.. For instance:
{ <b x{4A} s, rot 16 u, swap 32 i, .s b> } : mkTest
17239 -1000000001 mkTest
<s csr.
outputs
BC{000e4a4357c46535ff}
ok
x{4A4357C46535FF}
ok
One can observe that .s dumps the internal representation of a Builder, with
two tag bytes at the beginning (usually equal to the number of cell references
already stored in the Builder, and to twice the number of complete bytes
stored in the Builder, increased by one if an incomplete byte is present). On
45
5.3. Slice primitives
the other hand, csr. dumps a Slice (constructed from a Cell by <s, cf. 5.3)
in a form similar to that used by x{ to define Slice literals (cf. 5.1).
Incidentally, the word mkTest shown above (without the .s in its definition) corresponds to the TL-B constructor
test#4a first:uint16 second:int32 = Test;
and may be used to serialize values of this TL-B type.
5.3 Slice primitives
The following words can be used to manipulate values of the type Slice, which
represents a read-only view of a portion of a Cell. In this way data previously
stored into a Cell may be deserialized, by first transforming a Cell into a
Slice, and then extracting the required data from this Slice step-by-step.
• <s (c – s), transforms a Cell c into a Slice s containing the same data.
It usually marks the start of the deserialization of a cell.
• s> (s – ), throws an exception if Slice s is non-empty. It usually marks
the end of the deserialization of a cell, checking whether there are any
unprocessed data bits or references left.
• i@ (s x – y), fetches a signed big-endian x-bit integer from the first
x bits of Slice s. If s contains less than x data bits, an exception is
thrown.
• i@+ (s x – y s0
), fetches a signed big-endian x-bit integer from the first
x bits of Slice s similarly to i@, but returns the remainder of s as well.
• i@? (s x – y −1 or 0), fetches a signed integer from a Slice similarly to
i@, but pushes integer −1 afterwards on success. If there are less than
x bits left in s, pushes integer 0 to indicate failure.
• i@?+ (s x – y s0 −1 or s 0), fetches a signed integer from Slice s and
computes the remainder of this Slice similarly to i@+, but pushes −1
afterwards to indicate success. On failure, pushes the unchanged Slice s
and 0 to indicate failure.
• u@, u@+, u@?, u@?+, counterparts of i@, i@+, i@?, i@?+ for deserializing
unsigned integers.
46
5.3. Slice primitives
• B@ (s x – B), fetches first x bytes (i.e., 8x bits) from Slice s, and returns
them as a Bytes value B. If there are not enough data bits in s, throws
an exception.
• B@+ (s x – B s0
), similar to B@, but returns the remainder of Slice s as
well.
• B@? (s x – B −1 or 0), similar to B@, but uses a flag to indicate failure
instead of throwing an exception.
• B@?+ (s x – B s0 −1 or s 0), similar to B@+, but uses a flag to indicate
failure instead of throwing an exception.
• $@, $@+, $@?, $@?+, counterparts of B@, B@+, B@?, B@?+, returning the
result as a (UTF-8) String instead of a Bytes value. These primitives
do not check whether the byte sequence read is a valid UTF-8 string.
• ref@ (s – c), fetches the first reference from Slice s and returns the
Cell c referred to. If there are no references left, throws an exception.
• ref@+ (s – s
0
c), similar to ref@, but returns the remainder of s as well.
• ref@? (s – c −1 or 0), similar to ref@, but uses a flag to indicate
failure instead of throwing an exception.
• ref@?+ (s – s
0
c −1 or s 0), similar to ref@+, but uses a flag to indicate
failure instead of throwing an exception.
• empty? (s – ?), checks whether a Slice is empty (i.e., has no data bits
and no references left), and returns −1 or 0 accordingly.
• remaining (s – x y), returns both the number of data bits x and the
number of cell references y remaining in Slice s.
• sbits (s – x), returns the number of data bits x remaining in Slice s.
• srefs (s – x), returns the number of cell references x remaining in
Slice s.
• sbitrefs (s – x y), returns both the number of data bits x and
the number of cell references y remaining in Slice s. Equivalent to
remaining.
47
5.3. Slice primitives
• $>s (S – s), transforms String S into a Slice. Equivalent to <b swap
$, b> <s.
• s>c (s – c), creates a Cell c directly from a Slice s. Equivalent to <b
swap s, b>.
• csr. (s – ), recursively prints a Slice s. On the first line, the data
bits of s are displayed in hexadecimal form embedded into an x{...}
construct similar to the one used for Slice literals (cf. 5.1). On the
next lines, the cells referred to by s are printed with larger indentation.
For instance, values of the TL-B type Test discussed in 5.2
test#4a first:uint16 second:int32 = Test;
may be deserialized as follows:
{ <s 8 u@+ swap 0x4a <> abort"constructor tag mismatch"
16 u@+ 32 i@+ s> } : unpackTest
x{4A4357C46535FF} s>c unpackTest swap . .
prints “17239 -1000000001 ok” as expected.
Of course, if one needs to check constructor tags often, a helper word can
be defined for this purpose:
{ dup remaining abort"references in constructor tag"
tuck u@ -rot u@+ -rot <> abort"constructor tag mismatch"
} : tag?
{ <s x{4a} tag? 16 u@+ 32 i@+ s> } : unpackTest
x{4A4357C46535FF} s>c unpackTest swap . .
We can do even better with the aid of active prefix words (cf. 4.2 and 4.4):
{ dup remaining abort"references in constructor tag"
dup 256 > abort"constructor tag too long"
tuck u@ 2 { -rot u@+ -rot <> abort"constructor tag mismatch" }
} : (tagchk)
{ [compile] x{ 2drop (tagchk) } ::_ ?x{
{ [compile] b{ 2drop (tagchk) } ::_ ?b{
{ <s ?x{4a} 16 u@+ 32 i@+ s> } : unpackTest
x{4A4357C46535FF} s>c unpackTest swap . .
48
5.5. Bag-of-cells operations
A shorter but less efficient solution would be to reuse the previously defined
tag?:
{ [compile] x{ drop ’ tag? } ::_ ?x{
{ [compile] b{ drop ’ tag? } ::_ ?b{
x{11EF55AA} ?x{11E} dup csr.
?b{110} csr.
first outputs “x{F55AA}”, and then throws an exception with the message
“constructor tag mismatch”.
5.4 Cell hash operations
There are few words that operate on Cells directly. The most important of
them computes the (sha256-based) representation hash of a given cell (cf. [4,
3.1]), which can be roughly described as the sha256 hash of the cell’s data
bits concatenated with recursively computed hashes of the cells referred to
by this cell:
• hashB (c – B), computes the sha256-based representation hash of
Cell c (cf. [4, 3.1]), which unambiguously defines c and all its descendants (provided there are no collisions for sha256). The result
is returned as a Bytes value consisting of exactly 32 bytes.
• hashu (c – x), computes the sha256-based representation hash of c as
above, but returns the result as a big-endian unsigned 256-bit Integer.
• shash (s – B), computes the sha256-based representation hash of a
Slice by first transforming it into a cell. Equivalent to s>c hashB.
5.5 Bag-of-cells operations
A bag of cells is a collection of one or more cells along with all their descendants. It can usually be serialized into a sequence of bytes (represented by
a Bytes value in Fift) and then saved into a file or transferred by network.
Afterwards, it can be deserialized to recover the original cells. The TON
Blockchain systematically represents different data structures (including the
TON Blockchain blocks) as a tree of cells according to a certain TL-B scheme
(cf. [5], where this scheme is explained in detail), and then these trees of cells
are routinely imported into bags of cells and serialized into binary files.
Fift words for manipulating bags of cells include:
49
5.6. Binary file I/O and Bytes manipulation
• B>boc (B – c), deserializes a “standard” bag of cells (i.e., a bag of cells
with exactly one root cell) represented by Bytes B, and returns the
root Cell c.
• boc+>B (c x – B), creates and serializes a “standard” bag of cells, containing one root Cell c along with all its descendants. An Integer
parameter 0 ≤ x ≤ 31 is used to pass flags indicating the additional
options for bag-of-cells serialization, with individual bits having the
following effect:
– +1 enables bag-of-cells index creation (useful for lazy deserialization of large bags of cells).
– +2 includes the CRC32-C of all data into the serialization (useful
for checking data integrity).
– +4 explicitly stores the hash of the root cell into the serialization
(so that it can be quickly recovered afterwards without a complete
deserialization).
– +8 stores hashes of some intermediate (non-leaf) cells (useful for
lazy deserialization of large bags of cells).
– +16 stores cell cache bits to control caching of deserialized cells.
Typical values of x are x = 0 or x = 2 for very small bags of cells (e.g.,
TON Blockchain external messages) and x = 31 for large bags of cells
(e.g., TON Blockchain blocks).
• boc>B (c – B), serializes a small “standard” bag of cells with root Cell c
and all its descendants. Equivalent to 0 boc+>B.
For instance, the cell created in 5.2 with a value of TL-B Test type may be
serialized as follows:
{ <b x{4A} s, rot 16 u, swap 32 i, b> } : mkTest
17239 -1000000001 mkTest boc>B Bx.
outputs “B5EE9C7201040101000000000900000E4A4357C46535FF ok”. Here
Bx. is the word that prints the hexadecimal representation of a Bytes value.
50
5.6. Binary file I/O and Bytes manipulation
5.6 Binary file I/O and Bytes manipulation
The following words can be used to manipulate values of type Bytes (arbitrary byte sequences) and to read them from or write them into binary
files:
• B{hhex-digitsi} ( – B), pushes a Bytes literal containing data represented by an even number of hexadecimal digits.
• Bx. (B – ), prints the hexadecimal representation of a Bytes value.
Each byte is represented by exactly two uppercase hexadecimal digits.
• file>B (S – B), reads the (binary) file with the name specified in
String S and returns its contents as a Bytes value. If the file does not
exist, an exception is thrown.
• B>file (B S – ), creates a new (binary) file with the name specified in
String S and writes data from Bytes B into the new file. If the specified
file already exists, it is overwritten.
• file-exists? (S – ?), checks whether the file with the name specified
in String S exists.
For instance, the bag of cells created in the example in 5.5 can be saved to
disk as sample.boc as follows:
{ <b x{4A} s, rot 16 u, swap 32 i, b> } : mkTest
17239 -1000000001 mkTest boc>B "sample.boc" B>file
It can be loaded and deserialized afterwards (even in another Fift session)
by means of file>B and B>boc:
{ <s 8 u@+ swap 0x4a <> abort"constructor tag mismatch"
16 u@+ 32 i@+ s> } : unpackTest
"sample.boc" file>B B>boc unpackTest swap . .
prints “17239 -1000000001 ok”.
Additionally, there are several words for directly packing (serializing) data
into Bytes values, and unpacking (deserializing) them afterwards. They can
be combined with B>file and file>B to save data directly into binary files,
and load them afterwards.
***
  **Expert(s)**: Fift Language and TON Blockchain Experts
**Possible Keywords**: Fift, TON Blockchain, Slice literals, Builder primitives, Slice primitives, Cell manipulation, Bag-of-cells operations, Binary file I/O, Bytes manipulation
**Question**: Can you provide an improved explanation of the Fift language features related to Slice literals, Builder primitives, and Bag-of-cells operations, as well as how binary file I/O and Bytes manipulation are handled?
**Plan**: I will break down the explanation into sections, covering Slice literals, Builder primitives, Slice primitives, Bag-of-cells operations, and Binary file I/O with Bytes manipulation. I'll use Google Search Hyperlinks to provide additional resources for each section.

***

### Slice literals in Fift

Fift introduces Slice literals through active prefix words `b{}` and `x{}`. These literals enable the creation of Slices, read-only views of a portion of a Cell, and can be transformed into Cells if needed. For example, `b{00011101}` and `x{1d}` both push the same Slice with eight data bits and no references.

### Builder primitives

Fift provides Builder primitives for manipulating Builder objects, which are used to construct new Cells. These include operations like concatenation (`|+`), appending references (`ref`), and more.

### Slice primitives

Fift offers Slice primitives for manipulating values of the type Slice, representing a read-only view of a Cell. These include transformations like `<s` (transform Cell to Slice) and operations to fetch integers or Bytes from a Slice.

### Bag-of-cells operations

Fift supports operations on bags of cells, which are collections of cells and their descendants. This involves serialization (`B>boc`), deserialization (`boc>B`), and various options for serialization flags.

### Binary file I/O and Bytes manipulation

Fift includes functionality for handling binary file I/O and Bytes manipulation. This allows reading from (`file>B`) and writing to (`B>file`) binary files, checking file existence (`file-exists?`), and other operations.

For more details, refer to the [Fift documentation](https://ton.org/fift/fift.html).

### See also
- 📜 [Fift Documentation](https://ton.org/fift/fift.html): Official Fift documentation for in-depth understanding.

### You may also enjoy
- 🧩 [TON Blockchain Overview](https://www.google.com/search?q=TON+Blockchain+overview): Learn more about the TON Blockchain and its features.
- 📚 [Binary Serialization](https://www.google.com/search?q=binary+serialization+in+Fift): Explore additional resources on binary serialization in Fift.

***
