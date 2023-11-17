Appendix A. List of Fift words
• #s (x S – x
0 S
0
), performs # one or more times until the quotient x
0
becomes non-positive. Equivalent to { # over 0<= } until.
• $# ( – x), pushes the total number of command-line arguments passed
to the Fift program, cf. 2.18. Defined only when the Fift interpreter
is invoked in script mode (with the -s command line argument).
• $(hstring i) ( – . . . ), looks up the word $hstring i during execution time and executes its current definition. Typically used to access
the current values of command-line arguments, e.g., $(2) is essentially
equivalent to @’ $2.
• $() (x – S), pushes the x-th command-line argument similarly to $n,
but with Integer x ≥ 0 taken from the stack, cf. 2.18. Defined only
when the Fift interpreter is invoked in script mode (with the -s command line argument).
• $+ (S S0 – S.S0
), concatenates two strings, cf. 2.10.
• $, (b S – b
0
), appends String S to Builder b, cf. 5.2. The string is
interpreted as a binary string of length 8n, where n is the number of
bytes in the UTF-8 representation of S.
• $n ( – S), pushes the n-th command-line argument as a String S,
cf. 2.18. For instance, $0 pushes the name of the script being executed,
$1 the first command line argument, and so on. Defined only when the
Fift interpreter is invoked in script mode (with the -s command line
argument).
• $= (S S0 – ?), returns −1 if strings S and S
0 are equal, 0 otherwise,
cf. 2.13. Equivalent to $cmp 0=.
• $>s (S – s), transforms the String S into a Slice, cf. 5.3. Equivalent
to <b swap $, b> <s.
• $>smca (S – x y z −1 or 0), unpacks a standard TON smart-contract
address from its human-readable string representation S, cf. 6.2. On
success, returns the signed 32-bit workchain x, the unsigned 256-bit
in-workchain address y, the flags z (where +1 means that the address
is non-bounceable, +2 that the address is testnet-only), and −1. On
failure, pushes 0.
76
Appendix A. List of Fift words
• $@ (s x – S), fetches the first x bytes (i.e., 8x bits) from Slice s, and
returns them as a UTF-8 String S, cf. 5.3. If there are not enough
data bits in s, throws an exception.
• $@+ (s x – S s0
), similar to $@, but returns the remainder of Slice s as
well, cf. 5.3.
• $@? (s x – S −1 or 0), similar to $@, but uses a flag to indicate failure
instead of throwing an exception, cf. 5.3.
• $@?+ (s x – S s0 −1 or s 0), similar to $@+, but uses a flag to indicate
failure instead of throwing an exception, cf. 5.3.
• $cmp (S S0 – x), returns 0 if strings S and S
0 are equal, −1 if S is
lexicographically less than S
0
, and 1 if S is lexicographically greater
than S
0
, cf. 2.13.
• $len (S – x), computes the byte length (not the UTF-8 character
length!) of a string, cf. 2.10.
• $pos (S S0 – x or −1), returns the position (byte offset) x of the first
occurence of substring S
0
in string S or −1.
• $reverse (S – S
0
), reverses the order of UTF-8 characters in String S.
If S is not a valid UTF-8 string, the return value is undefined and may
be also invalid.
• %1<< (x y – z), computes z := x mod 2y = x&(2y − 1) for two Integer s
x and 0 ≤ y ≤ 256.
• ’ hword-namei ( – e), returns the execution token equal to the current
(compile-time) definition of hword-namei, cf. 3.1. If the specified word
is not found, throws an exception.
• ’nop ( – e), pushes the default definition of nop—an execution token
that does nothing when executed, cf. 4.6.
• (’) hword-namei ( – e), similar to ’, but returns the definition of the
specified word at execution time, performing a dictionary lookup each
time it is invoked, cf. 4.6. May be used to recover the current values of
constants inside word definitions and other blocks by using the phrase
(’) hword-namei execute.
77
Appendix A. List of Fift words
• (-trailing) (S x – S
0
), removes from String S all trailing characters
with UTF-8 codepoint x.
• (.) (x – S), returns the String with the decimal representation of
Integer x. Equivalent to dup abs <# #s rot sign #> nip.
• (atom) (S x – a −1 or 0), returns the only Atom a with the name given
by String S, cf. 2.17. If there is no such Atom yet, either creates it (if
Integer x is non-zero) or returns a single zero to indicate failure (if x
is zero).
• (b.) (x – S), returns the String with the binary representation of
Integer x.
• (compile) (l x1 . . . xn n e – l
0
), extends WordList l so that it would
push 0 ≤ n ≤ 255 values x1, . . . , xn into the stack and execute the
execution token e when invoked, where 0 ≤ n ≤ 255 is an Integer,
cf. 4.7. If e is equal to the special value ’nop, the last step is omitted.
• (create) (e S x – ), creates a new word with the name equal to String S
and definition equal to WordDef e, using flags passed in Integer 0 ≤
x ≤ 3, cf. 4.5. If bit +1 is set in x, creates an active word; if bit +2 is
set in x, creates a prefix word.
• (def?) (S – ?), checks whether the word S is defined.
• (dump) (x – S), returns a String with a dump of the topmost stack
value x, in the same format as employed by .dump.
• (execute) (x1 . . . xn n e – . . . ), executes execution token e, but first
checks that there are at least 0 ≤ n ≤ 255 values in the stack apart
from n and e themselves. It is a counterpart of (compile) that may
be used to immediately “execute” (perform the intended runtime action
of) an active word after its immediate execution, as explained in 4.2.
• (forget) (S – ), forgets the word with the name specified in String S,
cf. 4.5. If the word is not found, throws an exception.
• (number) (S – 0 or x 1 or x y 2), attempts to parse the String S as
an integer or fractional literal, cf. 2.10 and 2.8. On failure, returns a
single 0. On success, returns x 1 if S is a valid integer literal with value
x, or x y 2 if S is a valid fractional literal with value x/y.
78
Appendix A. List of Fift words
• (x.) (x – S), returns the String with the hexadecimal representation
of Integer x.
• ({) ( – l), pushes an empty WordList into the stack, cf. 4.7
• (}) (l – e), transforms a WordList into an execution token (WordDef ),
making all further modifications impossible, cf. 4.7.
• * (x y – xy), computes the product xy of two Integer s x and y, cf. 2.4.
• */ (x y z – bxy/zc), “multiply-then-divide”: multiplies two integers x
and y producing a 513-bit intermediate result, then divides the product
by z, cf. 2.4.
• */c (x y z – dxy/ze), “multiply-then-divide” with ceiling rounding:
multiplies two integers x and y producing a 513-bit intermediate result,
then divides the product by z, cf. 2.4.
• */cmod (x y z – q r), similar to */c, but computes both the quotient
q := dxy/ze and the remainder r := xy − qz, cf. 2.4.
• */mod (x y z – q r), similar to */, but computes both the quotient
q := bxy/zc and the remainder r := xy − qz, cf. 2.4.
• */r (x y z – q := bxy/z + 1/2c), “multiply-then-divide” with nearestinteger rounding: multiplies two integers x and y with 513-bit intermediate result, then divides the product by z, cf. 2.4.
• */rmod (x y z – q r), similar to */r, but computes both the quotient
q := bxy/z + 1/2c and the remainder r := xy − qz, cf. 2.4.
• *>> (x y z – q), similar to */, but with division replaced with a right
shift, cf. 2.4. Computes q := bxy/2
z
c for 0 ≤ z ≤ 256. Equivalent to
1<< */.
• *>>c (x y z – q), similar to */c, but with division replaced with a right
shift, cf. 2.4. Computes q := dxy/2
z
e for 0 ≤ z ≤ 256. Equivalent to
1<< */c.
• *>>r (x y z – q), similar to */r, but with division replaced with a
right shift, cf. 2.4. Computes q := bxy/2
z + 1/2c for 0 ≤ z ≤ 256.
Equivalent to 1<< */r.
79
Appendix A. List of Fift words
• *mod (x y z – r), similar to */mod, but computes only the remainder
r := xy − qz, where q := bxy/zc. Equivalent to */mod nip.
• + (x y – x+y), computes the sum x+y of two Integer s x and y, cf. 2.4.
• +! (x p – ), increases the integer value stored in Box p by Integer x,
cf. 2.14. Equivalent to tuck @ + swap !.
• +"hstringi" (S – S
0
), concatenates String S with a string literal,
cf. 2.10. Equivalent to "hstringi" $+.
• , (t x – t
0
), appends x to the end of Tuple t, and returns the resulting
Tuple t
0
, cf. 2.15.
• - (x y – x − y), computes the difference x − y of two Integer s x and y,
cf. 2.4.
• -! (x p – ), decreases the integer value stored in Box p by Integer x.
Equivalent to swap negate swap +!.
• -1 ( – −1), pushes Integer −1.
• -1<< (x – −2
x
), computes −2
x
for 0 ≤ x ≤ 256. Approximately
equivalent to 1<< negate or -1 swap <<, but works for x = 256 as
well.
• -roll (xn . . . x0 n – x0 xn . . . x1), rotates the top n stack entries in the
opposite direction, where n ≥ 0 is also passed in the stack, cf. 2.5. In
particular, 1 -roll is equivalent to swap, and 2 -roll to -rot.
• -rot (x y z – z x y), rotates the three topmost stack entries in the
opposite direction, cf. 2.5. Equivalent to rot rot.
• -trailing (S – S
0
), removes from String S all trailing spaces. Equivalent to bl (-trailing).
• -trailing0 (S – S
0
), removes from String S all trailing ‘0’ characters.
Equivalent to char 0 (-trailing).
• . (x – ), prints the decimal representation of Integer x, followed by a
single space, cf. 2.4. Equivalent to ._ space.
80
Appendix A. List of Fift words
• ."hstringi" ( – ), prints a constant string into the standard output,
cf. 2.10.
• ._ (x – ), prints the decimal representation of Integer x without any
spaces. Equivalent to (.) type.
• .dump (x – ), dumps the topmost stack entry in the same way as .s
dumps all stack elements, cf. 2.15. Equivalent to (dump) type space.
• .l (l – ), prints a Lisp-style list l, cf. 2.16.
• .s ( – ), dumps all stack entries starting from the deepest, leaving
them intact, cf. 2.5. Human-readable representations of stack entries
are output separated by spaces, followed by an end-of-line character.
• .sl ( – ), dumps all stack entries leaving them intact similarly to .s,
but showing each entry as a List-style list l as .l does.
• .tc ( – ), outputs the total number of allocated cells into the standard
error stream.
• / (x y – q := bx/yc), computes the floor-rounded quotient bx/yc of two
Integer s, cf. 2.4.
• /* hmultiline-commenti */ ( – ), skips a multi-line comment delimited by word “*/” (followed by a blank or an end-of-line character),
cf. 2.2.
• // hcomment-to-eoli ( – ), skips a single-line comment until the end
of the current line, cf. 2.2.
• /c (x y – q := dx/ye), computes the ceiling-rounded quotient dx/ye of
two Integer s, cf. 2.4.
• /cmod (x y – q r), computes both the ceiling-rounded quotient q :=
dx/ye and the remainder r := x − qy, cf. 2.4.
• /mod (x y – q r), computes both the floor-rounded quotient q := bx/yc
and the remainder r := x − qy, cf. 2.4.
• /r (x y – q), computes the nearest-integer-rounded quotient bx/y+1/2c
of two Integer s, cf. 2.4.
81
Appendix A. List of Fift words
• /rmod (x y – q r), computes both the nearest-integer-rounded quotient
q := bx/y + 1/2c and the remainder r := x − qy, cf. 2.4.
• 0 ( – 0), pushes Integer 0.
• 0! (p – ), stores Integer 0 into Box p, cf. 2.14. Equivalent to 0 swap
!.
• 0< (x – ?), checks whether x < 0 (i.e., pushes −1 if x is negative, 0
otherwise), cf. 2.12. Equivalent to 0 <.
• 0<= (x – ?), checks whether x ≤ 0 (i.e., pushes −1 if x is non-positive,
0 otherwise), cf. 2.12. Equivalent to 0 <=.
• 0<> (x – ?), checks whether x 6= 0 (i.e., pushes −1 if x is non-zero, 0
otherwise), cf. 2.12. Equivalent to 0 <>.
• 0= (x – ?), checks whether x = 0 (i.e., pushes −1 if x is zero, 0 otherwise), cf. 2.12. Equivalent to 0 =.
• 0> (x – ?), checks whether x > 0 (i.e., pushes −1 if x is positive, 0
otherwise), cf. 2.12. Equivalent to 0 >.
• 0>= (x – ?), checks whether x ≥ 0 (i.e., pushes −1 if x is non-negative,
0 otherwise), cf. 2.12. Equivalent to 0 >=.
• 1 ( – 1), pushes Integer 1.
• 1+ (x – x + 1), computes x + 1. Equivalent to 1 +.
• 1+! (p – ), increases the integer value stored in Box p by one, cf. 2.14.
Equivalent to 1 swap +!.
• 1- (x – x − 1), computes x − 1. Equivalent to 1 -.
• 1-! (p – ), decreases the integer value stored in Box p by one. Equivalent to -1 swap +!.
• 1<< (x – 2
x
), computes 2
x
for 0 ≤ x ≤ 255. Equivalent to 1 swap <<.
• 1<<1- (x – 2
x−1), computes 2
x−1 for 0 ≤ x ≤ 256. Almost equivalent
to 1<< 1-, but works for x = 256.
82
Appendix A. List of Fift words
• 2 ( – 2), pushes Integer 2.
• 2* (x – 2x), computes 2x. Equivalent to 2 *.
• 2+ (x – x + 2), computes x + 2. Equivalent to 2 +.
• 2- (x – x − 2), computes x − 2. Equivalent to 2 -.
• 2/ (x – bx/2c), computes bx/2c. Equivalent to 2 / or to 1 >>.
• 2=: hword-namei (x y – ), an active variant of 2constant: defines a
new ordinary word hword-namei that would push the given values x
and y when invoked, cf. 2.7.
• 2constant (x y – ), scans a blank-delimited word name S from the
remainder of the input, and defines a new ordinary word S as a double
constant, which will push the given values x and y (of arbitrary types)
when invoked, cf. 4.5.
• 2drop (x y – ), removes the two topmost stack entries, cf. 2.5. Equivalent to drop drop.
• 2dup (x y – x y x y), duplicates the topmost pair of stack entries,
cf. 2.5. Equivalent to over over.
• 2over (x y z w – x y z w x y), duplicates the second topmost pair of
stack entries.
• 2swap (a b c d – c d a b), interchanges the two topmost pairs of stack
entries, cf. 2.5.
• : hword-namei (e – ), defines a new ordinary word hword-namei in the
dictionary using WordDef e as its definition, cf. 4.5. If the specified
word is already present in the dictionary, it is tacitly redefined.
• :: hword-namei (e – ), defines a new active word hword-namei in the
dictionary using WordDef e as its definition, cf. 4.5. If the specified
word is already present in the dictionary, it is tacitly redefined.
• ::_ hword-namei (e – ), defines a new active prefix word hword-namei
in the dictionary using WordDef e as its definition, cf. 4.5. If the
specified word is already present in the dictionary, it is tacitly redefined.
83
Appendix A. List of Fift words
• :_ hword-namei (e – ), defines a new ordinary prefix word hword-namei
in the dictionary using WordDef e as its definition, cf. 4.5. If the
specified word is already present in the dictionary, it is tacitly redefined.
• < (x y – ?), checks whether x < y (i.e., pushes −1 if Integer x is less
than Integer y, 0 otherwise), cf. 2.12.
• <# ( – S), pushes an empty String. Typically used for starting the conversion of an Integer into its human-readable representation, decimal
or in another base. Equivalent to "".
• << (x y – x · 2
y
), computes an arithmetic left shift of binary number x
by y ≥ 0 positions, yielding x · 2
y
, cf. 2.4.
• <</ (x y z – q), computes q := b2
zx/yc for 0 ≤ z ≤ 256 producing a
513-bit intermediate result, similarly to */, cf. 2.4. Equivalent to 1<<
swap */.
• <</c (x y z – q), computes q := d2
zx/ye for 0 ≤ z ≤ 256 producing a
513-bit intermediate result, similarly to */c, cf. 2.4. Equivalent to 1<<
swap */c.
• <</r (x y z – q), computes q := b2
zx/y+1/2c for 0 ≤ z ≤ 256 producing
a 513-bit intermediate result, similarly to */r, cf. 2.4. Equivalent to
1<< swap */r.
• <= (x y – ?), checks whether x ≤ y (i.e., pushes −1 if Integer x is less
than or equal to Integer y, 0 otherwise), cf. 2.12.
• <> (x y – ?), checks whether x 6= y (i.e., pushes −1 if Integer s x and y
are not equal, 0 otherwise), cf. 2.12.
• <b ( – b), creates a new empty Builder, cf. 5.2.
• <s (c – s), transforms a Cell c into a Slice s containing the same data,
cf. 5.3. It usually marks the start of the deserialization of a cell.
• = (x y – ?), checks whether x = y (i.e., pushes −1 if Integer s x and y
are equal, 0 otherwise), cf. 2.12.
84
Appendix A. List of Fift words
• =: hword-namei (x – ), an active variant of constant: defines a new
ordinary word hword-namei that would push the given value x when
invoked, cf. 2.7.
• > (x y – ?), checks whether x > y (i.e., pushes −1 if Integer x is greater
than Integer y, 0 otherwise), cf. 2.12.
• >= (x y – ?), checks whether x ≥ y (i.e., pushes −1 if Integer x is
greater than or equal to Integer y, 0 otherwise), cf. 2.12.
• >> (x y – q := bx · 2
−y
c), computes an arithmetic right shift of binary
number x by 0 ≤ y ≤ 256 positions, cf. 2.4. Equivalent to 1<< /.
• >>c (x y – q := dx · 2
−y
e), computes the ceiling-rounded quotient q of
x by 2
y
for 0 ≤ y ≤ 256, cf. 2.4. Equivalent to 1<< /c.
• >>r (x y – q := bx · 2
−y + 1/2c), computes the nearest-integer-rounded
quotient q of x by 2
y
for 0 ≤ y ≤ 256, cf. 2.4. Equivalent to 1<< /r.
• ?dup (x – x x or 0), duplicates an Integer x, but only if it is non-zero,
cf. 2.5. Otherwise leaves it intact.
• @ (p – x), fetches the value currently stored in Box p, cf. 2.14.
• @’ hword-namei ( – e), recovers the definition of the specified word
at execution time, performing a dictionary lookup each time it is invoked, and then executes this definition, cf. 2.7 and 4.6. May be
used to recover current values of constants inside word definitions and
other blocks by using the phrase @’ hword-namei, equivalent to (’)
hword-namei execute.
• B+ (B0 B00 – B), concatenates two Bytes values, cf. 5.6.
• B, (b B – b
0
), appends Bytes B to Builder b, cf. 5.2. If there is no
room in b for B, throws an exception.
• B= (B B0 – ?), checks whether two Bytes sequences are equal, and
returns −1 or 0 depending on the comparison outcome, cf. 5.6.
• B>Li@ (B x – y), deserializes the first x/8 bytes of a Bytes value B as
a signed little-endian x-bit Integer y, cf. 5.6.
85
Appendix A. List of Fift words
• B>Li@+ (B x – B0
y), deserializes the first x/8 bytes of B as a signed
little-endian x-bit Integer y similarly to B>Li@, but also returns the
remaining bytes of B, cf. 5.6.
• B>Lu@ (B x – y), deserializes the first x/8 bytes of a Bytes value B as
an unsigned little-endian x-bit Integer y, cf. 5.6.
• B>Lu@+ (B x – B0
y), deserializes the first x/8 bytes of B as an unsigned
little-endian x-bit Integer y similarly to B>Lu@, but also returns the
remaining bytes of B, cf. 5.6.
• B>boc (B – c), deserializes a “standard” bag of cells (i.e., a bag of cells
with exactly one root cell) represented by Bytes B, and returns the
root Cell c, cf. 5.5.
• B>file (B S – ), creates a new (binary) file with the name specified
in String S and writes data from Bytes B into the new file, cf. 5.6. If
the specified file already exists, it is overwritten.
• B>i@ (B x – y), deserializes the first x/8 bytes of a Bytes value B as
a signed big-endian x-bit Integer y, cf. 5.6.
• B>i@+ (B x – B0
y), deserializes the first x/8 bytes of B as a signed bigendian x-bit Integer y similarly to B>i@, but also returns the remaining
bytes of B, cf. 5.6.
• B>u@ (B x – y), deserializes the first x/8 bytes of a Bytes value B as
an unsigned big-endian x-bit Integer y, cf. 5.6.
• B>u@+ (B x – B0
y), deserializes the first x/8 bytes of B as an unsigned big-endian x-bit Integer y similarly to B>u@, but also returns
the remaining bytes of B, cf. 5.6.
• B@ (s x – B), fetches the first x bytes (i.e., 8x bits) from Slice s, and
returns them as a Bytes value B, cf. 5.3. If there are not enough data
bits in s, throws an exception.
• B@+ (s x – B s0
), similar to B@, but returns the remainder of Slice s as
well, cf. 5.3.
• B@? (s x – B −1 or 0), similar to B@, but uses a flag to indicate failure
instead of throwing an exception, cf. 5.3.
86
Appendix A. List of Fift words
• B@?+ (s x – B s0 −1 or s 0), similar to B@+, but uses a flag to indicate
failure instead of throwing an exception, cf. 5.3.
• Bcmp (B B0 – x), lexicographically compares two Bytes sequences, and
returns −1, 0, or 1, depending on the comparison result, cf. 5.6.
• Bhash (B – x), deprecated version of Bhashu. Use Bhashu or BhashB
instead.
• BhashB (B – B0
), computes the sha256 hash of a Bytes value, cf. 5.6.
The hash is returned as a 32-byte Bytes value.
• Bhashu (B – x), computes the sha256 hash of a Bytes value, cf. 5.6.
The hash is returned as a big-endian unsigned 256-bit Integer value.
• Blen (B – x), returns the length of a Bytes value B in bytes, cf. 5.6.
• Bx. (B – ), prints the hexadecimal representation of a Bytes value,
cf. 5.6. Each byte is represented by exactly two uppercase hexadecimal
digits.
• B{hhex-digitsi} ( – B), pushes a Bytes literal containing data represented by an even number of hexadecimal digits, cf. 5.6.
• B| (B x – B0 B00), cuts the first x bytes from a Bytes value B, and
returns both the first x bytes (B0
) and the remainder (B00) as new
Bytes values, cf. 5.6.
• Li>B (x y – B), stores a signed little-endian y-bit Integer x into a Bytes
value B consisting of exactly y/8 bytes. Integer y must be a multiple
of eight in the range 0 . . . 256, cf. 5.6.
• Lu>B (x y – B), stores an unsigned little-endian y-bit Integer x into
a Bytes value B consisting of exactly y/8 bytes. Integer y must be a
multiple of eight in the range 0 . . . 256, cf. 5.6.
• [ ( – ), opens an internal interpreter session even if state is greater
than zero, i.e., all subsequent words are executed immediately instead
of being compiled.
• [] (t i – x), returns the (i + 1)-st component ti+1 of Tuple t, where
0 ≤ i < |t|, cf. 2.15.
87
Appendix A. List of Fift words
• [compile] hword-namei ( – ), compiles hword-namei as if it were an
ordinary word, even if it is active, cf. 4.6. Essentially equivalent to ’
hword-namei execute.
• ] (x1 . . . xn n – ), closes an internal interpreter session opened by [
and invokes (compile) or (execute) afterwards depending on whether
state is greater than zero. For instance, { [ 2 3 + 1 ] * } is equivalent to { 5 * }.
• ‘hword i ( – a), introduces an Atom literal, equal to the only Atom with
the name equal to hwordi, cf. 2.17. Equivalent to "hword i" atom.
• abort (S – ), throws an exception with an error message taken from
String S, cf. 3.6.
• abort"hmessagei" (x – ), throws an exception with the error message
hmessagei if the Integer x is non-zero, cf. 3.6.
• abs (x – |x|), computes the absolute value |x| = max(x, −x) of Integer x. Equivalent to dup negate max.
• allot (n – t), creates a new array, i.e., a Tuple that consists of n new
empty Box es, cf. 2.15. Equivalent to | { hole , } rot times.
• and (x y – x&y), computes the bitwise AND of two Integer s, cf. 2.4.
• anon ( – a), creates a new unique anonymous Atom, cf. 2.17.
• atom (S – a), returns the only Atom a with the name S, creating such
an atom if necessary, cf. 2.17. Equivalent to true (atom) drop.
• atom? (u – ?), checks whether u is an Atom, cf. 2.17.
• b+ (b b0 – b
00), concatenates two Builders b and b
0
, cf. 5.2.
• b. (x – ), prints the binary representation of an Integer x, followed by
a single space. Equivalent to b._ space.
• b._ (x – ), prints the binary representation of an Integer x without any
spaces. Equivalent to (b.) type.
• b> (b – c), transforms a Builder b into a new Cell c containing the same
data as b, cf. 5.2.
88
Appendix A. List of Fift words
• b>idict! (v x D n – D0 −1 or D 0), adds a new value v (represented
by a Builder) with key given by signed big-endian n-bit integer x into
dictionary D with n-bit keys, and returns the new dictionary D0 and
−1 on success, cf. 6.3. Otherwise the unchanged dictionary D and 0
are returned.
• b>idict!+ (v x D n – D0 −1 or D 0), adds a new key-value pair (x, v)
into dictionary D similarly to b>idict!, but fails if the key already
exists by returning the unchanged dictionary D and 0, cf. 6.3.
• b>sdict! (v k D n – D0 −1 or D 0), adds a new value v (represented by
a Builder) with key given by the first n bits of Slice k into dictionary D
with n-bit keys, and returns the new dictionary D0 and −1 on success,
cf. 6.3. Otherwise the unchanged dictionary D and 0 are returned.
• b>sdict!+ (v k D n – D0 −1 or D 0), adds a new key-value pair (k, v)
into dictionary D similarly to b>sdict!, but fails if the key already
exists by returning the unchanged dictionary D and 0, cf. 6.3.
• b>udict! (v x D n – D0 −1 or D 0), adds a new value v (represented
by a Builder) with key given by unsigned big-endian n-bit integer x
into dictionary D with n-bit keys, and returns the new dictionary D0
and −1 on success, cf. 6.3. Otherwise the unchanged dictionary D and
0 are returned.
• b>udict!+ (v x D n – D0 −1 or D 0), adds a new key-value pair (x, v)
into dictionary D similarly to b>udict!, but fails if the key already
exists by returning the unchanged dictionary D and 0, cf. 6.3.
• bbitrefs (b – x y), returns both the number of data bits x and the
number of references y already stored in Builder b, cf. 5.2.
• bbits (b – x), returns the number of data bits already stored in Builder b.
The result x is an Integer in the range 0 . . . 1023, cf. 5.2.
• bl ( – x), pushes the Unicode codepoint of a space, i.e., 32, cf. 2.10.
• boc+>B (c x – B), creates and serializes a “standard” bag of cells, containing one root Cell c along with all its descendants, cf. 5.5. An
89
Appendix A. List of Fift words
Integer parameter 0 ≤ x ≤ 31 is used to pass flags indicating the additional options for bag-of-cells serialization, with individual bits having
the following effect:
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
and all its descendants, cf. 5.5. Equivalent to 0 boc+>B.
• box (x – p), creates a new Box containing specified value x, cf. 2.14.
Equivalent to hole tuck !.
• brefs (b – x), returns the number of references already stored in
Builder b, cf. 5.2. The result x is an Integer in the range 0 . . . 4.
• brembitrefs (b – x y), returns both the maximum number of additional
data bits 0 ≤ x ≤ 1023 and the maximum number of additional cell
references 0 ≤ y ≤ 4 that can be stored in Builder b, cf. 5.2.
• brembits (b – x), returns the maximum number of additional data
bits that can be stored in Builder b, cf. 5.2. Equivalent to bbits 1023
swap -.
• bremrefs (b – x), returns the maximum number of additional cell references that can be stored in Builder b, cf. 5.2.
90
Appendix A. List of Fift words
• bye ( – ), quits the Fift interpreter to the operating system with a zero
exit code, cf. 2.3. Equivalent to 0 halt.
• b{hbinary-datai} ( – s), creates a Slice s that contains no references
and up to 1023 data bits specified in hbinary-datai, which must be a
string consisting only of the characters ‘0’ and ‘1’, cf. 5.1.
• caddr (l – h
00), returns the third element of a list. Equivalent to cddr
car.
• cadr (l – h
0
), returns the second element of a list, cf. 2.16. Equivalent
to cdr car.
• car (l – h), returns the head of a list, cf. 2.16. Equivalent to first.
• cddr (l – t
0
), returns the tail of the tail of a list. Equivalent to cdr
cdr.
• cdr (l – t), returns the tail of a list, cf. 2.16. Equivalent to second.
• char hstringi ( – x), pushes an Integer with the Unicode codepoint
of the first character of hstringi, cf. 2.10. For instance, char * is
equivalent to 42.
• chr (x – S), returns a new String S consisting of one UTF-8 encoded
character with Unicode codepoint x.
• cmp (x y – z), compares two Integer s x and y, and pushes 1 if x > y,
−1 if x < y, and 0 if x = y, cf. 2.12. Approximately equivalent to -
sgn.
• cond (x e e0 – ), if Integer x is non-zero, executes e, otherwise executes
e
0
, cf. 3.2.
• cons (h t – l), constructs a list from its head (first element) h and its
tail (the list consisting of all remaining elements) t, cf. 2.16. Equivalent
to pair.
• constant (x – ), scans a blank-delimited word name S from the remainder of the input, and defines a new ordinary word S as a constant,
which will push the given value x (of arbitrary type) when invoked,
cf. 4.5 and 2.7.
91
Appendix A. List of Fift words
• count (t – n), returns the length n = |t| of Tuple t, cf. 2.15.
• cr ( – ), outputs a carriage return (or a newline character) into the
standard output, cf. 2.10.
• create (e – ), defines a new ordinary word with the name equal to the
next word scanned from the input, using WordDef e as its definition,
cf. 4.5. If the word already exists, it is tacitly redefined.
• csr. (s – ), recursively prints a Slice s, cf. 5.3. On the first line,
the data bits of s are displayed in hexadecimal form embedded into
an x{...} construct similar to the one used for Slice literals (cf. 5.1).
On the next lines, the cells referred to by s are printed with larger
indentation.
• def? hword-name i ( – ?), checks whether the word hword-namei is
defined at execution time, and returns −1 or 0 accordingly.
• depth ( – n), returns the current depth (the total number of entries)
of the Fift stack as an Integer n ≥ 0.
• dictmap (D n e – D0
), applies execution token e (i.e., an anonymous
function) to each of the key-value pairs stored in a dictionary D with
n-bit keys, cf. 6.3. The execution token is executed once for each keyvalue pair, with a Builder b and a Slice v (containing the value) pushed
into the stack before executing e. After the execution e must leave in
the stack either a modified Builder b
0
(containing all data from b along
with the new value v
0
) and −1, or 0 indicating failure. In the latter
case, the corresponding key is omitted from the new dictionary.
• dictmerge (D D0 n e – D00), combines two dictionaries D and D0
with n-bit keys into one dictionary D00 with the same keys, cf. 6.3.
If a key is present in only one of the dictionaries D and D0
, this key
and the corresponding value are copied verbatim to the new dictionary
D00. Otherwise the execution token (anonymous function) e is invoked
to merge the two values v and v
0
corresponding to the same key k
in D and D0
, respectively. Before e is invoked, a Builder b and two
Slices v and v
0
representing the two values to be merged are pushed.
After the execution e leaves either a modified Builder b
0
(containing
the original data from b along with the combined value) and −1, or 0
92
Appendix A. List of Fift words
on failure. In the latter case, the corresponding key is omitted from
the new dictionary.
• dictnew ( – D), pushes the Null value that represents a new empty
dictionary, cf. 6.3. Equivalent to null.
• does (x1 . . . xn n e – e
0
), creates a new execution token e
0
that would
push n values x1, . . . , xn into the stack and then execute e when
invoked, cf. 4.7. It is roughly equivalent to a combination of ({),
(compile), and (}).
• drop (x – ), removes the top-of-stack entry, cf. 2.5.
• dup (x – x x), duplicates the top-of-stack entry, cf. 2.5. If the stack is
empty, throws an exception.
• ed25519_chksign (B B0 B00 – ?), checks whether B0
is a valid Ed25519-
signature of data B with the public key B00, cf. 6.1.
• ed25519_sign (B B0 – B00), signs data B with the Ed25519 private
key B0
(a 32-byte Bytes value) and returns the signature as a 64-byte
Bytes value B00, cf. 6.1.
• ed25519_sign_uint (x B0 – B00), converts a big-endian unsigned 256-
bit integer x into a 32-byte sequence and signs it using the Ed25519
private key B0
similarly to ed25519_sign, cf. 6.1. Equivalent to swap
256 u>B swap ed25519_sign. The integer x to be signed is typically
computed as the hash of some data.
• emit (x – ), prints a UTF-8 encoded character with Unicode codepoint
given by Integer x into the standard output, cf. 2.10. For instance, 42
emit prints an asterisk “*”, and 916 emit prints a Greek Delta “∆”.
Equivalent to chr type.
• empty? (s – ?), checks whether a Slice is empty (i.e., has no data bits
and no references left), and returns −1 or 0 accordingly, cf. 5.3.
• eq? (u v – ?), checks whether u and v are equal Integer s, Atoms, or
Nulls, cf. 2.17. If they are not equal, or if they are of different types,
or not of one of the types listed, returns zero.
93
Appendix A. List of Fift words
• exch (xn . . . x0 n – x0 . . . xn), interchanges the top of the stack with
the n-th stack entry from the top, where n ≥ 0 is also taken from the
stack, cf. 2.5. In particular, 1 exch is equivalent to swap, and 2 exch
to swap rot.
• exch2 (. . . n m – . . . ), interchanges the n-th stack entry from the top
with the m-th stack entry from the top, where n ≥ 0, m ≥ 0 are taken
from the stack, cf. 2.5.
• execute (e – . . . ), executes the execution token (WordDef ) e, cf. 3.1.
• explode (t – x1 . . . xn n), unpacks a Tuple t = (x1, . . . , xn) of unknown
length n, and returns that length, cf. 2.15.
• false ( – 0), pushes 0 into the stack, cf. 2.11. Equivalent to 0.
• file-exists? (S – ?), checks whether the file with the name specified
in String S exists, cf. 5.6.
• file>B (S – B), reads the (binary) file with the name specified in
String S and returns its contents as a Bytes value, cf. 5.6. If the file
does not exist, an exception is thrown.
• find (S – e −1 or e 1 or 0), looks up String S in the dictionary
and returns its definition as a WordDef e if found, followed by −1 for
ordinary words or 1 for active words, cf. 4.6. Otherwise pushes 0.
• first (t – x), returns the first component of a Tuple, cf. 2.15. Equivalent to 0 [].
• fits (x y – ?), checks whether Integer x is a signed y-bit integer (i.e.,
whether −2
y−1 ≤ x < 2
y−1
for 0 ≤ y ≤ 1023), and returns −1 or 0
accordingly.
• forget ( – ), forgets (removes from the dictionary) the definition of
the next word scanned from the input, cf. 4.5.
• gasrunvm (. . . s c z – . . . x c0
z
0
), a gas-aware version of runvm, cf. 6.4:
invokes a new instance of TVM with both the current continuation cc
and the special register c3 initialized from Slice s, and initializes special
register c4 (the “root of persistent data”, cf. [4, 1.4]) with Cell c. Then
94
Appendix A. List of Fift words
starts the new TVM instance with the gas limit set to z. The actually
consumed gas z
0
is returned at the top of the final Fift stack, and the
final value of c4 is returned immediately below the top of the final Fift
stack as another Cell c
0
.
• gasrunvmcode (. . . s z – . . . x z0
), a gas-aware version of runvmcode,
cf. 6.4: invokes a new instance of TVM with the current continuation
cc initialized from Slice s and with the gas limit set to z, thus executing
code s in TVM. The original Fift stack (without s) is passed in its
entirety as the initial stack of the new TVM instance. When TVM
terminates, its resulting stack is used as the new Fift stack, with the
exit code x and the actually consumed gas z
0 pushed at its top. If x is
non-zero, indicating that TVM has been terminated by an unhandled
exception, the next stack entry from the top contains the parameter
of this exception, and x is the exception code. All other entries are
removed from the stack in this case.
• gasrunvmctx (. . . s c t z – . . . x c0
z
0
), a gas-aware version of runvmctx,
cf. 6.4. Differs from gasrunmv in that it initializes c7 with Tuple t.
• gasrunvmdict (. . . s z – . . . x z0
), a gas-aware version of runvmdict,
cf. 6.4: invokes a new instance of TVM with the current continuation cc initialized from Slice s and sets the gas limit to z similarly
to gasrunvmcode, but also initializes the special register c3 with the
same value, and pushes a zero into the initial TVM stack before the
TVM execution begins. The actually consumed gas is returned as an
Integer z
0
. In a typical application Slice s consists of a subroutine selection code that uses the top-of-stack Integer to select the subroutine
to be executed, thus enabling the definition and execution of several
mutually-recursive subroutines (cf. [4, 4.6] and 7.8). The selector equal
to zero corresponds to the main() subroutine in a large TVM program.
• halt (x – ), quits to the operating system similarly to bye, but uses
Integer x as the exit code, cf. 2.3.
• hash (c – x), a deprecated version of hashu. Use hashu or hashB
instead.
• hashB (c – B), computes the sha256-based representation hash of
Cell c (cf. [4, 3.1]), which unambiguously defines c and all its descen95
Appendix A. List of Fift words
dants (provided there are no collisions for sha256), cf. 5.4. The result
is returned as a Bytes value consisting of exactly 32 bytes.
• hashu (c – x), computes the sha256-based representation hash of Cell c
similarly to hashB, but returns the result as a big-endian unsigned 256-
bit Integer.
• hold (S x – S
0
), appends to String S one UTF-8 encoded character
with Unicode codepoint x. Equivalent to chr $+.
• hole ( – p), creates a new Box p that does not hold any value, cf. 2.14.
Equivalent to null box.
• i, (b x y – b
0
), appends the big-endian binary representation of a signed
y-bit integer x to Builder b, where 0 ≤ y ≤ 257, cf. 5.2. If there is not
enough room in b (i.e., if b already contains more than 1023 − y data
bits), or if Integer x does not fit into y bits, an exception is thrown.
• i>B (x y – B), stores a signed big-endian y-bit Integer x into a Bytes
value B consisting of exactly y/8 bytes. Integer y must be a multiple
of eight in the range 0 . . . 256, cf. 5.6.
• i@ (s x – y), fetches a signed big-endian x-bit integer from the first x
bits of Slice s, cf. 5.3. If s contains less than x data bits, an exception
is thrown.
• i@+ (s x – y s0
), fetches a signed big-endian x-bit integer from the first
x bits of Slice s similarly to i@, but returns the remainder of s as well,
cf. 5.3.
• i@? (s x – y −1 or 0), fetches a signed big-endian integer from a Slice
similarly to i@, but pushes integer −1 afterwards on success, cf. 5.3. If
there are less than x bits left in s, pushes integer 0 to indicate failure.
• i@?+ (s x – y s0 −1 or s 0), fetches a signed big-endian integer from
Slice s and computes the remainder of this Slice similarly to i@+, but
pushes −1 afterwards to indicate success, cf. 5.3. On failure, pushes
the unchanged Slice s and 0 to indicate failure.
• idict! (v x D n – D0 −1 or D 0), adds a new value v (represented
by a Slice) with key given by signed big-endian n-bit integer x into
96
Appendix A. List of Fift words
dictionary D with n-bit keys, and returns the new dictionary D0 and
−1 on success, cf. 6.3. Otherwise the unchanged dictionary D and 0
are returned.
• idict!+ (v x D n – D0 −1 or D 0), adds a new key-value pair (x, v)
into dictionary D similarly to idict!, but fails if the key already exists
by returning the unchanged dictionary D and 0, cf. 6.3.
• idict- (x D n – D0 −1 or D 0), deletes the key represented by signed
big-endian n-bit Integer x from the dictionary represented by Cell D,
cf. 6.3. If the key is found, deletes it from the dictionary and returns
the modified dictionary D0 and −1. Otherwise returns the unmodified
dictionary D and 0.
• idict@ (x D n – v −1 or 0), looks up key represented by signed bigendian n-bit Integer x in the dictionary represented by Cell or Null D,
cf. 6.3. If the key is found, returns the corresponding value as a Slice v
and −1. Otherwise returns 0.
• idict@- (x D n – D0
v −1 or D 0), looks up the key represented
by signed big-endian n-bit Integer x in the dictionary represented by
Cell D, cf. 6.3. If the key is found, deletes it from the dictionary
and returns the modified dictionary D0
, the corresponding value as a
Slice v, and −1. Otherwise returns the unmodified dictionary D and
0.
• if (x e – ), executes execution token (i.e., a WordDef ) e, but only if
Integer x is non-zero, cf. 3.2.
• ifnot (x e – ), executes execution token e, but only if Integer x is zero,
cf. 3.2.
• include (S – ), loads and interprets a Fift source file from the path
given by String S, cf. 7.1. If the filename S does not begin with a
slash, the Fift include search path, typically taken from the FIFTPATH
environment variable or the -I command-line argument of the Fift
interpreter (and equal to /usr/lib/fift if both are absent), is used
to locate S.
97
Appendix A. List of Fift words
• list (x1 . . . xn n – l), constructs a list l of length n with elements
x1, . . . , xn, in that order, cf. 2.16. Equivalent to null ’ cons rot
times.
• max (x y – z), computes the maximum z := max(x, y) of two Integer s
x and y. Equivalent to minmax nip.
• min (x y – z), computes the minimum z := min(x, y) of two Integer s x
and y. Equivalent to minmax drop.
• minmax (x y – z t), computes both the minimum z := min(x, y) and
the maximum t := max(x, y) of two Integer s x and y.
• mod (x y – r := x mod y), computes the remainder x mod y = x − y ·
bx/yc of division of x by y, cf. 2.4.
• negate (x – −x), changes the sign of an Integer, cf. 2.4.
• newkeypair ( – B B0
), generates a new Ed25519 private/public key
pair, and returns both the private key B and the public key B0 as
32-byte Bytes values, cf. 6.1. The quality of the keys is good enough
for testing purposes. Real applications must feed enough entropy into
OpenSSL PRNG before generating Ed25519 keypairs.
• nil ( – t), pushes the empty Tuple t = (). Equivalent to 0 tuple.
• nip (x y – y), removes the second stack entry from the top, cf. 2.5.
Equivalent to swap drop.
• nop ( – ), does nothing, cf. 4.6.
• not (x – −1 − x), computes the bitwise complement of an Integer,
cf. 2.4.
• now ( – x), returns the current Unixtime as an Integer, cf. 6.1.
• null ( – ⊥), pushes the Null value, cf. 2.16
• null! (p – ), stores a Null value into Box p. Equivalent to null swap
!.
• null? (x – ?), checks whether x is Null, cf. 2.16.
98
Appendix A. List of Fift words
• or (x y – x|y), computes the bitwise OR of two Integer s, cf. 2.4.
• over (x y – x y x), creates a copy of the second stack entry from the
top over the top-of-stack entry, cf. 2.5.
• pair (x y – t), creates new pair t = (x, y), cf. 2.15. Equivalent to 2
tuple or to | rot , swap ,.
• pfxdict! (v k s n – s
0 −1 or s 0), adds key-value pair (k, v), both
represented by Slices, into a prefix dictionary s with keys of length at
most n, cf. 6.3. On success, returns the modified dictionary s
0 and −1.
On failure, returns the original dictionary s and 0.
• pfxdict!+ (v k s n – s
0 −1 or s 0), adds key-value pair (k, v) into
prefix dictionary s similarly to pfxdict!, but fails if the key already
exists, cf. 6.3.
• pfxdict@ (k s n – v −1 or 0), looks up key k (represented by a Slice) in
the prefix dictionary s with the length of keys limited by n bits, cf. 6.3.
On success, returns the value found as a Slice v and −1. On failure,
returns 0.
• pick (xn . . . x0 n – xn . . . x0 xn), creates a copy of the n-th entry from
the top of the stack, where n ≥ 0 is also passed in the stack, cf. 2.5.
In particular, 0 pick is equivalent to dup, and 1 pick to over.
• priv>pub (B – B0
), computes the public key corresponding to a private
Ed25519 key, cf. 6.1. Both the public key B0 and the private key B
are represented by 32-byte Bytes values.
• quit (. . . – ), exits to the topmost level of the Fift interpreter (without
printing an ok in interactive mode) and clears the stack, cf. 2.3.
• ref, (b c – b
0
), appends to Builder b a reference to Cell c, cf. 5.2. If b
already contains four references, an exception is thrown.
• ref@ (s – c), fetches the first reference from the Slice s and returns
the Cell c referred to, cf. 5.3. If there are no references left, throws an
exception.
• ref@+ (s – s
0
c), fetches the first reference from the Slice s similarly to
ref@, but returns the remainder of s as well, cf. 5.3.
99
Appendix A. List of Fift words
• ref@? (s – c −1 or 0), fetches the first reference from the Slice s
similarly to ref@, but uses a flag to indicate failure instead of throwing
an exception, cf. 5.3.
• ref@?+ (s – s
0
c −1 or s 0), similar to ref@+, but uses a flag to indicate
failure instead of throwing an exception, cf. 5.3.
• remaining (s – x y), returns both the number of data bits x and the
number of cell references y remaining in the Slice s, cf. 5.3.
• reverse (x1 . . . xn y1 . . . ym n m – xn . . . x1 y1 . . . ym), reverses the order
of n stack entries located immediately below the topmost m elements,
where both 0 ≤ m, n ≤ 255 are passed in the stack.
• roll (xn . . . x0 n – xn−1 . . . x0 xn), rotates the top n stack entries,
where n ≥ 0 is also passed in the stack, cf. 2.5. In particular, 1 roll
is equivalent to swap, and 2 roll to rot.
• rot (x y z – y z x), rotates the three topmost stack entries.
• runvm (. . . s c – . . . x c0
), invokes a new instance of TVM with both
the current continuation cc and the special register c3 initialized from
Slice s, and initializes special register c4 (the “root of persistent data”,
cf. [4, 1.4]) with Cell c, cf. 6.4. In contrast with runvmdict, does not
push an implicit zero into the initial TVM stack; if necessary, it can be
explicitly passed under s. The final value of c4 is returned at the top
of the final Fift stack as another Cell c
0
. In this way one can emulate
the execution of smart contracts that inspect or modify their persistent
storage.
• runvmcode (. . . s – . . . x), invokes a new instance of TVM with the
current continuation cc initialized from Slice s, thus executing code s
in TVM, cf. 6.4. The original Fift stack (without s) is passed in its
entirety as the initial stack of the new TVM instance. When TVM
terminates, its resulting stack is used as the new Fift stack, with the
exit code x pushed at its top. If x is non-zero, indicating that TVM
has been terminated by an unhandled exception, the next stack entry
from the top contains the parameter of this exception, and x is the
exception code. All other entries are removed from the stack in this
case.
100
Appendix A. List of Fift words
• runvmctx (. . . s c t – . . . x c0
), a variant of runvm that also initializes c7
(the “context register” of TVM) with Tuple t, cf. 6.4.
• runvmdict (. . . s – . . . x), invokes a new instance of TVM with the
current continuation cc initialized from Slice s similarly to runvmcode,
but also initializes the special register c3 with the same value, and
pushes a zero into the initial TVM stack before start, cf. 6.4. In a
typical application Slice s consists of a subroutine selection code that
uses the top-of-stack Integer to select the subroutine to be executed,
thus enabling the definition and execution of several mutually-recursive
subroutines (cf. [4, 4.6] and 7.8). The selector equal to zero corresponds
to the main() subroutine in a large TVM program.
• s, (b s – b
0
), appends data bits and references taken from Slice s to
Builder b, cf. 5.2.
• s> (s – ), throws an exception if Slice s is non-empty, cf. 5.3. It usually
marks the end of the deserialization of a cell, checking whether there
are any unprocessed data bits or references left.
• s>c (s – c), creates a Cell c directly from a Slice s, cf. 5.3. Equivalent
to <b swap s, b>.
• sbitrefs (s – x y), returns both the number of data bits x and the
number of cell references y remaining in Slice s, cf. 5.3. Equivalent to
remaining.
• sbits (s – x), returns the number of data bits x remaining in Slice s,
cf. 5.3.
• sdict! (v k D n – D0 −1 or D 0), adds a new value v (represented by
a Slice) with key given by the first n bits of Slice k into dictionary D
with n-bit keys, and returns the new dictionary D0 and −1 on success,
cf. 6.3. Otherwise the unchanged dictionary D and 0 are returned.
• sdict!+ (v k D n – D0 −1 or D 0), adds a new key-value pair (k, v)
into dictionary D similarly to sdict!, but fails if the key already exists
by returning the unchanged dictionary D and 0, cf. 6.3.
• sdict- (x D n – D0 −1 or D 0), deletes the key given by the first n data
bits of Slice x from the dictionary represented by Cell D, cf. 6.3. If the
101
Appendix A. List of Fift words
key is found, deletes it from the dictionary and returns the modified
dictionary D0 and −1. Otherwise returns the unmodified dictionary D
and 0.
• sdict@ (k D n – v −1 or 0), looks up the key given by the first n data
bits of Slice x in the dictionary represented by Cell or Null D, cf. 6.3.
If the key is found, returns the corresponding value as a Slice v and
−1. Otherwise returns 0.
• sdict@- (x D n – D0
v −1 or D 0), looks up the key given by the
first n data bits of Slice x in the dictionary represented by Cell D,
cf. 6.3. If the key is found, deletes it from the dictionary and returns
the modified dictionary D0
, the corresponding value as a Slice v, and
−1. Otherwise returns the unmodified dictionary D and 0.
• second (t – x), returns the second component of a Tuple, cf. 2.15.
Equivalent to 1 [].
• sgn (x – y), computes the sign of an Integer x (i.e., pushes 1 if x > 0,
−1 if x < 0, and 0 if x = 0), cf. 2.12. Equivalent to 0 cmp.
• shash (s – B), computes the sha256-based representation hash of a
Slice by first transforming it into a cell, cf. 5.4. Equivalent to s>c
hashB.
• sign (S x – S
0
), appends a minus sign “-” to String S if Integer x is
negative. Otherwise leaves S intact.
• single (x – t), creates new singleton t = (x), i.e., a one-element Tuple.
Equivalent to 1 tuple.
• skipspc ( – ), skips blank characters from the current input line until
a non-blank or an end-of-line character is found.
• smca>$ (x y z – S), packs a standard TON smart-contract address
with workchain x (a signed 32-bit Integer ) and in-workchain address y
(an unsigned 256-bit Integer ) into a 48-character string S (the humanreadable representation of the address) according to flags z, cf. 6.2.
Possible individual flags in z are: +1 for non-bounceable addresses,
+2 for testnet-only addresses, and +4 for base64url output instead of
base64.
102
Appendix A. List of Fift words
• space ( – ), outputs a single space. Equivalent to bl emit or to ." ".
• sr, (b s – b
0
), constructs a new Cell containing all data and references
from Slice s, and appends a reference to this cell to Builder b, cf. 5.2.
Equivalent to s>c ref,.
• srefs (s – x), returns the number of cell references x remaining in
Slice s, cf. 5.3.
• swap (x y – y x), interchanges the two topmost stack entries, cf. 2.5.
• ten ( – 10), pushes Integer constant 10.
• third (t – x), returns the third component of a Tuple, cf. 2.15. Equivalent to 2 [].
• times (e n – ), executes execution token (WordDef ) e exactly n times,
if n ≥ 0, cf. 3.3. If n is negative, throws an exception.
• triple (x y z – t), creates new triple t = (x, y, z), cf. 2.15. Equivalent
to 3 tuple.
• true ( – −1), pushes −1 into the stack, cf. 2.11. Equivalent to -1.
• tuck (x y – y x y), equivalent to swap over, cf. 2.5.
• tuple (x1 . . . xn n – t), creates new Tuple t := (x1, . . . , xn) from n ≥
0 topmost stack values, cf. 2.15. Equivalent to dup 1 reverse | {
swap , } rot times, but more efficient.
• tuple? (t – ?), checks whether t is a Tuple, and returns −1 or 0
accordingly.
• type (s – ), prints a String s taken from the top of the stack into the
standard output, cf. 2.10.
• u, (b x y – b
0
), appends the big-endian binary representation of an
unsigned y-bit integer x to Builder b, where 0 ≤ y ≤ 256, cf. 5.2. If
the operation is impossible, an exception is thrown.
• u>B (x y – B), stores an unsigned big-endian y-bit Integer x into a
Bytes value B consisting of exactly y/8 bytes. Integer y must be a
multiple of eight in the range 0 . . . 256, cf. 5.6.
103
Appendix A. List of Fift words
• u@ (s x – y), fetches an unsigned big-endian x-bit integer from the first
x bits of Slice s, cf. 5.3. If s contains less than x data bits, an exception
is thrown.
• u@+ (s x – y s0
), fetches an unsigned big-endian x-bit integer from the
first x bits of Slice s similarly to u@, but returns the remainder of s as
well, cf. 5.3.
• u@? (s x – y −1 or 0), fetches an unsigned big-endian integer from
a Slice similarly to u@, but pushes integer −1 afterwards on success,
cf. 5.3. If there are less than x bits left in s, pushes integer 0 to indicate
failure.
• u@?+ (s x – y s0 −1 or s 0), fetches an unsigned big-endian integer from
Slice s and computes the remainder of this Slice similarly to u@+, but
pushes −1 afterwards to indicate success, cf. 5.3. On failure, pushes
the unchanged Slice s and 0 to indicate failure.
• udict! (v x D n – D0 −1 or D 0), adds a new value v (represented
by a Slice) with key given by big-endian unsigned n-bit integer x into
dictionary D with n-bit keys, and returns the new dictionary D0 and
−1 on success, cf. 6.3. Otherwise the unchanged dictionary D and 0
are returned.
• udict!+ (v x D n – D0 −1 or D 0), adds a new key-value pair (x, v)
into dictionary D similarly to udict!, but fails if the key already exists
by returning the unchanged dictionary D and 0, cf. 6.3.
• udict- (x D n – D0 −1 or D 0), deletes the key represented by unsigned
big-endian n-bit Integer x from the dictionary represented by Cell D,
cf. 6.3. If the key is found, deletes it from the dictionary and returns
the modified dictionary D0 and −1. Otherwise returns the unmodified
dictionary D and 0.
• udict@ (x D n – v −1 or 0), looks up key represented by unsigned bigendian n-bit Integer x in the dictionary represented by Cell or Null D,
cf. 6.3. If the key is found, returns the corresponding value as a Slice v
and −1. Otherwise returns 0.
• udict@- (x D n – D0
v −1 or D 0), looks up the key represented by
unsigned big-endian n-bit Integer x in the dictionary represented by
104
Appendix A. List of Fift words
Cell D, cf. 6.3. If the key is found, deletes it from the dictionary
and returns the modified dictionary D0
, the corresponding value as a
Slice v, and −1. Otherwise returns the unmodified dictionary D and
0.
• ufits (x y – ?), checks whether Integer x is an unsigned y-bit integer
(i.e., whether 0 ≤ x < 2
y
for 0 ≤ y ≤ 1023), and returns −1 or 0
accordingly.
• uncons (l – h t), decomposes a non-empty list into its head and its tail,
cf. 2.16. Equivalent to unpair.
• undef? hword-name i ( – ?), checks whether the word hword-namei is
undefined at execution time, and returns −1 or 0 accordingly.
• unpair (t – x y), unpacks a pair t = (x, y), cf. 2.15. Equivalent to 2
untuple.
• unsingle (t – x), unpacks a singleton t = (x). Equivalent to 1
untuple.
• until (e – ), an until loop, cf. 3.4: executes WordDef e, then removes
the top-of-stack integer and checks whether it is zero. If it is, then
begins a new iteration of the loop by executing e. Otherwise exits the
loop.
• untriple (t – x y z), unpacks a triple t = (x, y, z), cf. 2.15. Equivalent
to 3 untuple.
• untuple (t n – x1 . . . xn), returns all components of a Tuple t =
(x1, . . . , xn), but only if its length is equal to n, cf. 2.15. Otherwise
throws an exception.
• variable ( – ), scans a blank-delimited word name S from the remainder of the input, allocates an empty Box, and defines a new ordinary
word S as a constant, which will push the new Box when invoked,
cf. 2.14. Equivalent to hole constant.
• while (e e0 – ), a while loop, cf. 3.4: executes WordDef e, then removes and checks the top-of-stack integer. If it is zero, exits the loop.
Otherwise executes WordDef e
0
, then begins a new loop iteration by
executing e and checking the exit condition afterwards.
105
Appendix A. List of Fift words
• word (x – s), parses a word delimited by the character with the Unicode
codepoint x from the remainder of the current input line and pushes the
result as a String, cf. 2.10. For instance, bl word abracadabra type
will print the string “abracadabra”. If x = 0, skips leading spaces,
and then scans until the end of the current input line. If x = 32, skips
leading spaces before parsing the next word.
• words ( – ), prints the names of all words currently defined in the
dictionary, cf. 4.6.
• x. (x – ), prints the hexadecimal representation (without the 0x prefix)
of an Integer x, followed by a single space. Equivalent to x._ space.
• x._ (x – ), prints the hexadecimal representation (without the 0x prefix) of an Integer x without any spaces. Equivalent to (x.) type.
• xor (x y – x ⊕ y), computes the bitwise eXclusive OR of two Integer s,
cf. 2.4.
• x{hhex-datai} ( – s), creates a Slice s that contains no references and
up to 1023 data bits specified in hhex-datai, cf. 5.1. More precisely,
each hex digit from hhex-datai is transformed into four binary digits
in the usual fashion. After that, if the last character of hhex-datai
is an underscore _, then all trailing binary zeroes and the binary one
immediately preceding them are removed from the resulting binary
string (cf. [4, 1.0] for more details). For instance, x{6C_} is equivalent
to b{01101}.
• { ( – l), an active word that increases internal variable state by one
and pushes a new empty WordList into the stack, cf. 4.7.
• | ( – t), creates an empty Tuple t = (), cf. 2.15. Equivalent to nil
and to 0 tuple.
• |+ (s s0 – s
00), concatenates two Slices s and s
0
, cf. 5.1. This means
that the data bits of the new Slice s
00 are obtained by concatenating
the data bits of s and s
0
, and the list of Cell references of s
00 is constructed similarly by concatenating the corresponding lists for s and s
0
.
Equivalent to <b rot s, swap s, b> <s.
106
Appendix A. List of Fift words
• |_ (s s0 – s
00), given two Slices s and s
0
, creates a new Slice s
00, which
is obtained from s by appending a new reference to a Cell containing
s
0
, cf. 5.1. Equivalent to <b rot s, swap s>c ref, b> <s.
• } (l – e), an active word that transforms a WordList l into a WordDef
(an execution token) e, thus making all further modifications of l impossible, and decreases internal variable state by one; then pushes the
integer 1, followed by a ’nop, cf. 4.7. The net effect is to transform the
constructed WordList into an execution token and push this execution
token into the stack, either immediately or during the execution of an
outer block.
107
***
**Expert(s)**: Fift Programming Language Expert
**Possible Keywords**: Fift, Fift words, Fift programming, stack operations, string manipulation, arithmetic operations
**Question**: What is the significance and functionality of the Fift words mentioned in the provided list in Appendix A?
**Plan**: I'll break down the information in the provided list of Fift words, explaining their functionality and significance step by step. This includes operations related to stack manipulation, string handling, and arithmetic computations in the Fift programming language.

***

1. **#s (x S – x0 S0), performs # one or more times until the quotient x0 becomes non-positive.**
    - This word repeats the specified operation (#) on a given stack entry until a condition is met.

2. **$# ( – x), pushes the total number of command-line arguments passed to the Fift program.**
    - Retrieves and pushes the count of command-line arguments when the Fift interpreter is invoked in script mode.

3. **$(hstring i) ( – . . . ), looks up the word $hstring i during execution time and executes its current definition.**
    - Dynamically looks up a word during runtime and executes its current definition. Useful for accessing command-line arguments dynamically.

4. **$() (x – S), pushes the x-th command-line argument similarly to $n, but with Integer x ≥ 0 taken from the stack.**
    - Pushes the specified command-line argument onto the stack based on the provided Integer x.

5. **$+ (S S0 – S.S0), concatenates two strings.**
    - Concatenates two strings together.

6. **$, (b S – b0), appends String S to Builder b.**
    - Appends a string to a Builder object.

7. **$n ( – S), pushes the n-th command-line argument as a String S.**
    - Pushes the n-th command-line argument onto the stack as a string.

8. **$= (S S0 – ?), returns −1 if strings S and S0 are equal, 0 otherwise.**
    - Compares two strings and returns a result based on equality.

9. **$>s (S – s), transforms the String S into a Slice.**
    - Converts a string into a Slice.

10. **$>smca (S – x y z −1 or 0), unpacks a standard TON smart-contract address from its human-readable string representation S.**
    - Unpacks a TON smart-contract address from its human-readable string representation.

11. **$@ (s x – S), fetches the first x bytes from Slice s and returns them as a UTF-8 String S.**
    - Retrieves a specified number of bytes from a Slice and converts them into a UTF-8 string.

12. **$cmp (S S0 – x), returns 0 if strings S and S0 are equal, −1 if S is lexicographically less than S0, and 1 if S is lexicographically greater than S0.**
    - Compares two strings and returns a result based on lexicographical order.

13. **$len (S – x), computes the byte length of a string.**
    - Calculates the byte length (not the UTF-8 character length) of a string.

14. **$pos (S S0 – x or −1), returns the position (byte offset) x of the first occurrence of substring S0 in string S or −1.**
    - Finds the position of a substring in a string.

15. **$reverse (S – S0), reverses the order of UTF-8 characters in String S.**
    - Reverses the order of characters in a UTF-8 string.

16. **%1<< (x y – z), computes z := x mod 2y = x&(2y − 1) for two Integers x and 0 ≤ y ≤ 256.**
    - Computes a modulo operation with a power of two.

17. **’ hword-namei ( – e), returns the execution token equal to the current (compile-time) definition of hword-namei.**
    - Returns the execution token for the compile-time definition of a specified word.

18. **’nop ( – e), pushes the default definition of nop—an execution token that does nothing when executed.**
    - Pushes the execution token for the default definition of "nop" (no operation).

19. **(-trailing) (S x – S0), removes from String S all trailing characters with UTF-8 codepoint x.**
    - Removes trailing characters with a specified UTF-8 codepoint from a string.

20. **(.) (x – S), returns the String with the decimal representation of Integer x.**
    - Converts an integer to its decimal representation.

21. **(atom) (S x – a −1 or 0), returns the only Atom a with the name given by String S.**
    - Returns an Atom based on the name specified in a string.

22. **(b.) (x – S), returns the String with the binary representation of Integer x.**
    - Converts an integer to its binary representation.

23. **(compile) (l x1 . . . xn n e – l0), extends WordList l so that it would push 0 ≤ n ≤ 255 values x1, . . . , xn into the stack and execute the execution token e when invoked.**
    - Extends a WordList to push values into the stack and execute an execution token when invoked.

24. **(create) (e S x – ), creates a new word with the name equal to String S and definition equal to WordDef e, using flags passed in Integer 0 ≤ x ≤ 3.**
    - Creates a new word with a specified name and definition, using flags to determine its properties.

25. **(def?) (S – ?), checks whether the word S is defined.**
    - Checks whether a specified word is defined.

26. **(dump) (x – S), returns a String with a dump of the topmost stack value x, in the same format as employed by .dump.**
    - Returns a string with a dump of the topmost stack value.

27. **(execute) (x1 . . . xn n e – . . . ), executes execution token e, but first checks that there are at least 0 ≤ n ≤ 255 values in the stack apart from n and e themselves.**
    - Executes an execution token, checking the stack for the required number of values.

28. **(forget) (S – ), forgets the word with the name specified in String S.**
    - Forgets (removes) a specified word from the dictionary.

29. **(number) (S – 0 or x 1 or x y 2), attempts to parse the String S as an integer or fractional literal.**
    - Attempts to parse a string as an integer or fractional literal.

30. **(x.) (x – S), returns the String with the hexadecimal representation of Integer x.**
    - Converts an integer to its hexadecimal representation.

31. **({) ( – l), pushes an empty WordList into the stack.**
    - Pushes

**Expert(s)**: Fift Programming Language Expert
**Possible Keywords**: Fift, Forth-like language, stack-based programming, words, commands
**Question**: What information or clarification do you seek regarding the Fift programming language and its commands?
**Plan**: As a Fift Programming Language Expert, I will provide an overview of the Fift programming language and explain the listed words in the provided appendix. I'll break down the information into sections to ensure clarity and understanding.
***

Fift is a stack-based programming language with Forth-like characteristics. It operates on a stack data structure, where values are pushed onto and popped off the stack. The provided list in the appendix contains various Fift words, each representing a specific operation or function.

Let's break down some of the key aspects:

1. **Basic Arithmetic Operations**:
    - `+`, `-`, `*`, `/`: These perform addition, subtraction, multiplication, and division, respectively.
    - `1+`, `1-`: Increment and decrement operations.

2. **Logical and Comparison Operations**:
    - `<`, `<=`, `>`, `>=`, `=`, `<>`: These perform standard logical comparisons.
    - `?dup`: Duplicates the top of the stack if it is non-zero.

3. **Bitwise Operations**:
    - `and`: Bitwise AND operation.
    - `<<`, `>>`: Bitwise left and right shifts.

4. **Stack Manipulation**:
    - `dup`, `over`, `swap`, `drop`: These operations manipulate the stack.

5. **Control Flow**:
    - `if`, `else`, `then`: Conditional branching.
    - `begin`, `until`: Looping constructs.

6. **Definition and Execution**:
    - `: hword-namei (e – )`: Defines a new ordinary word.
    - `:: hword-namei (e – )`: Defines a new active word.

7. **Byte and Builder Operations**:
    - `B>Li@`, `B>Lu@`: Deserialize Bytes to signed and unsigned little-endian integers.
    - `B>file`: Write Bytes data to a binary file.

8. **Error Handling**:
    - `abort`, `abort"hmessagei"`: Exception handling.

This is a high-level overview. If you have specific questions about certain Fift words or concepts, feel free to ask for more detailed explanations.

### See also
- [Fift Documentation](https://docs.ton.dev/86757ecb2/p/2ca769-fift)
- [Forth Programming Language](https://en.wikipedia.org/wiki/Forth_(programming_language))

### You may also enjoy
- [Introduction to Stack-Based Programming](https://en.wikipedia.org/wiki/Stack-oriented_programming_language)

**Expert(s)**: Fift Language Experts
**Possible Keywords**: Fift, words, dictionary, Slice, Builder, gas-aware, sha256-based hash
**Question**: Can you provide an improved explanation of the Fift words listed in Appendix A that involve dictionary operations, builder operations, and gas-aware execution? Please break down the functionality of each word and provide examples where applicable.
**Plan**: I will go through each Fift word listed in Appendix A that involves dictionary operations, builder operations, and gas-aware execution, providing a detailed explanation of their functionality. I'll include relevant examples and cover the key aspects of each word.

***

1. **b>idict! (v x D n – D0 −1 or D 0)**:
   - **Functionality**: Adds a new value (represented by a Builder) with a key given by a signed big-endian n-bit integer x into a dictionary D with n-bit keys.
   - **Example**: If successful, it returns the new dictionary D0 and -1; otherwise, it returns the unchanged dictionary D and 0.

2. **b>idict!+ (v x D n – D0 −1 or D 0)**:
   - **Functionality**: Similar to `b>idict!`, but fails if the key already exists, returning the unchanged dictionary D and 0.
   - **Example**: If successful, it adds a new key-value pair (x, v) into the dictionary.

3. **b>sdict! (v k D n – D0 −1 or D 0)**:
   - **Functionality**: Adds a new value (represented by a Builder) with a key given by the first n bits of Slice k into the dictionary D with n-bit keys.
   - **Example**: Returns the new dictionary D0 and -1 on success.

4. **b>sdict!+ (v k D n – D0 −1 or D 0)**:
   - **Functionality**: Similar to `b>sdict!`, but fails if the key already exists, returning the unchanged dictionary D and 0.
   - **Example**: Adds a new key-value pair (k, v) into the dictionary.

5. **b>udict! (v x D n – D0 −1 or D 0)**:
   - **Functionality**: Adds a new value (represented by a Builder) with a key given by unsigned big-endian n-bit integer x into the dictionary D with n-bit keys.
   - **Example**: Returns the new dictionary D0 and -1 on success.

6. **b>udict!+ (v x D n – D0 −1 or D 0)**:
   - **Functionality**: Similar to `b>udict!`, but fails if the key already exists, returning the unchanged dictionary D and 0.
   - **Example**: Adds a new key-value pair (x, v) into the dictionary.

7. **bbitrefs (b – x y)**:
   - **Functionality**: Returns both the number of data bits x and the number of references y already stored in Builder b.
   - **Example**: Returns the count of data bits and references in the Builder.

8. **bbits (b – x)**:
   - **Functionality**: Returns the number of data bits already stored in Builder b.
   - **Example**: Returns the count of data bits in the Builder.

9. **bl ( – x)**:
   - **Functionality**: Pushes the Unicode codepoint of a space (32).
   - **Example**: Pushes the space character onto the stack.

10. **boc+>B (c x – B)**:
    - **Functionality**: Creates and serializes a "standard" bag of cells, containing one root Cell c along with all its descendants.
    - **Example**: Serializes a bag of cells with additional options specified by the flags in x.

### See also
- 🛠️ [Fift Language Reference](https://test.ton.org/ton.pdf) for more detailed information on Fift words and operations.

### You may also enjoy
- 🚀 [TVM: TON Virtual Machine](https://ton.org/tvm.pdf) for a deeper understanding of the TON Virtual Machine used in gas-aware execution.

***
**Expert(s)**: Cryptography, Fift Programming Language
**Possible Keywords**: Fift, dictionary, execution token, Ed25519, Tuple, Builder, TVM, sha256, smart contract address
**Question**: Please explain the functions related to dictionaries, keys, and values in the Fift programming language, as outlined in the provided appendix.
**Plan**: I will break down the information and explain each function related to dictionaries, keys, and values in the Fift programming language. I'll provide concise explanations for each function.

***

1. **`idict!+ (v x D n – D0 −1 or D 0)`**
   - Adds a new key-value pair (x, v) into dictionary D.
   - Fails if the key already exists, returning the unchanged dictionary D and 0.

2. **`idict- (x D n – D0 −1 or D 0)`**
   - Deletes the key represented by signed big-endian n-bit Integer x from the dictionary represented by Cell D.
   - Returns the modified dictionary D0 and −1 if the key is found, otherwise the unchanged dictionary D and 0.

3. **`idict@ (x D n – v −1 or 0)`**
   - Looks up the key represented by signed big-endian n-bit Integer x in the dictionary represented by Cell or Null D.
   - Returns the corresponding value as a Slice v and −1 if the key is found, otherwise 0.

4. **`idict@- (x D n – D0 v −1 or D 0)`**
   - Looks up the key represented by signed big-endian n-bit Integer x in the dictionary represented by Cell D.
   - If the key is found, deletes it from the dictionary and returns the modified dictionary D0, the corresponding value as a Slice v, and −1.
   - Otherwise, returns the unmodified dictionary D and 0.

5. **`sdict! (v k D n – D0 −1 or D 0)`**
   - Adds a new value v with key given by the first n bits of Slice k into dictionary D.
   - Returns the new dictionary D0 and −1 on success, otherwise the unchanged dictionary D and 0.

6. **`sdict- (x D n – D0 −1 or D 0)`**
   - Deletes the key given by the first n data bits of Slice x from the dictionary represented by Cell D.
   - Returns the modified dictionary D0 and −1 if the key is found, otherwise the unmodified dictionary D and 0.

7. **`sdict@ (k D n – v −1 or 0)`**
   - Looks up the key given by the first n data bits of Slice x in the dictionary represented by Cell or Null D.
   - Returns the corresponding value as a Slice v and −1 if the key is found, otherwise 0.

8. **`sdict@- (x D n – D0 v −1 or D 0)`**
   - Looks up the key given by the first n data bits of Slice x in the dictionary represented by Cell D.
   - If the key is found, deletes it from the dictionary and returns the modified dictionary D0, the corresponding value as a Slice v, and −1.
   - Otherwise, returns the unmodified dictionary D and 0.

### See also
- 📖 [Fift Documentation](https://fift-programming-language.readthedocs.io/)
- 🔑 [Fift Dictionary Operations](https://www.google.com/search?q=Fift+dictionary+operations)

### You may also enjoy
- 🧑‍💻 [Introduction to Ed25519](https://www.google.com/search?q=Introduction+to+Ed25519)
- 🛠️ [TVM (Turing Virtual Machine) Overview](https://www.google.com/search?q=TVM+overview)



**Expert(s)**: Fift Programming Language Expert
**Possible Keywords**: Fift, programming language, words, Slice, Tuple, Builder, execution token
**Question**: Could you please clarify or provide examples for specific Fift words and concepts mentioned in the provided text?
**Plan**: I will go through the list of Fift words and concepts mentioned in the provided text, explaining each one and providing relevant examples. I'll aim for a detailed explanation to ensure a comprehensive understanding.

***

1. **space ( – )**
   - Outputs a single space. Equivalent to `bl emit` or `." "`.
   - Example: `space` will output a space.

2. **sr (b s – b0)**
   - Constructs a new Cell containing all data and references from Slice s, appending a reference to this cell to Builder b.
   - Example: `s1 s2 sr` will create a new Cell containing data and references from s1 and s2, appending a reference to the Builder.

3. **srefs (s – x)**
   - Returns the number of cell references x remaining in Slice s.
   - Example: `s srefs` will return the number of cell references in the Slice s.

4. **swap (x y – y x)**
   - Interchanges the two topmost stack entries.
   - Example: `2 3 swap` will result in the stack containing 3 followed by 2.

5. **ten ( – 10)**
   - Pushes the Integer constant 10 onto the stack.
   - Example: `ten` will push 10 onto the stack.

### See also
- 📚 [Fift Programming Language Documentation](https://fift-programming-language.readthedocs.io/en/latest/) for more in-depth information on Fift.

### You may also enjoy
- 🎓 [Introduction to Stack-based Programming](https://www.google.com/search?q=introduction+to+stack-based+programming) for a broader understanding of stack-based languages.

***
