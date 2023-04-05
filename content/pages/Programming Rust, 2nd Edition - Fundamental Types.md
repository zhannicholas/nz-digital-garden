---
tags:
- Rust
title: Programming Rust, 2nd Edition - Fundamental Types
categories:
date: 2023-04-04
lastMod: 2023-04-04
---


## Example of types in Rust

  + | Type | Description | Values |
| ---- | ---- | ---- |
|  `i8` ,  `i16` ,  `i32` ,  `i64` ,  `i128` , `u8` ,  `u16` ,  `u32` ,  `u64` ,  `u128`  | Signed and unsigned integers, of given bit width |  `42` , `-5i8` ,  `0x400u16` ,  `0o100i16` , `20_922_789_888_000u64` , `b'*'`  ( `u8`  byte literal) |
|  `isize` ,  `usize`  | Signed and unsigned integers, the same size as an address on the machine (32 or 64 bits) |  `137` , `-0b0101_0010isize` , `0xffff_fc00usize`  |
|  `f32` ,  `f64`  | IEEE floating-point numbers, single and double precision |  `1.61803` ,  `3.14f32` ,  `6.0221e23f64`  |
|  `bool`  | Boolean |  `true` ,  `false`  |
|  `char`  | Unicode character, 32 bits wide |  `'*'` ,  `'\n'` ,  `'字'` ,  `'\x7f'` ,  `'\u{CA0}'`  |
|  `(char, u8, i32)`  | Tuple: mixed types allowed |  `('%', 0x7f, -1)`  |
|  `()`  | “Unit” (empty tuple) |  `()`  |
|  `struct S { x: f32, y: f32 }`  | Named-field struct |  `S { x: 120.0, y: 209.0 }`  |
|  `struct T (i32, char);`  | Tuple-like struct |  `T(120, 'X')`  |
|  `struct E;`  | Unit-like struct; has no fields |  `E`  |
|  `enum Attend { OnTime, Late(u32) }`  | Enumeration, algebraic data type |  `Attend::Late(5)` ,  `Attend::OnTime`  |
|  `Box<Attend>`  | Box: owning pointer to value in heap |  `Box::new(Late(15))`  |
|  `&i32` ,  `&mut i32`  | Shared and mutable references: non-owning pointers that must not outlive their referent |  `&s.y` ,  `&mut v`  |
|  `String`  | UTF-8 string, dynamically sized |  `"ラーメン: ramen".to_string()`  |
|  `&str`  | Reference to  `str` : non-owning pointer to UTF-8 text |  `"そば: soba"` ,  `&s[0..12]`  |
|  `[f64; 4]` ,  `[u8; 256]`  | Array, fixed length; elements all of same type |  `[1.0, 0.0, 0.0, 1.0]`   `[b' '; 256]`  |
|  `Vec<f64>`  | Vector, varying length; elements all of same type |  `vec![0.367, 2.718, 7.389]`  |
|  `&[u8]` , `&mut [u8]`  | Reference to slice: reference to a portion of an array or vector, comprising pointer and length |  `&v[10..20]` ,  `&mut a[..]`  |
|  `Option<&str>`  | Optional value: either  `None`  (absent) or  `Some(v)`  (present, with value  `v` ) |  `Some("Dr.")` ,  `None`  |
|  `Result<u64, Error>`  | Result of operation that may fail: either a success value  `Ok(v)` , or an error  `Err(e)`  |  `Ok(4096)` ,  `Err(Error::last_os_error())`  |
|  `&dyn Any` ,  `&mut dyn Read`  | Trait object: reference to any value that implements a given set of methods |  `value as &dyn Any` , `&mut file as &mut dyn Read`  |
|  `fn(&str) -> bool`  | Pointer to function |  `str::is_empty`  |
| (Closure types have no written form) | Closure |  <code>&#124;a, b&#124; { a*a + b*b }</code>  |

  + 

## Fixed-Width Numeric Types

  + The names of Rust’s numeric types follow a regular pattern, spelling out their width in bits, and the representation they use.


    + | Size (bits) | Unsigned integer | Signed integer | Floating-point |
| ---- | ---- | ---- |
| 8 |  `u8`  |  `i8`  |   |
| 16 |  `u16`  |  `i16`  |   |
| 32 |  `u32`  |  `i32`  |  `f32`  |
| 64 |  `u64`  |  `i64`  |  `f64`  |
| 128 |  `u128`  |  `i128`  |   |
| Machine word |  `usize`  |  `isize`  |

    + Here, a *machine word* is a value the size of an address on the machine the code runs on, 32 or 64 bits.

  + Unlike C and C++, Rust performs almost no numeric conversions implicitly.


    + {{< logseq/orgCAUTION >}}implicit integer conversions have a well-established record of causing bugs and security holes, especially when the integers in question represent the size of something in memory, and an unanticipated overflow occurs. 
{{< / logseq/orgCAUTION >}}

  + ### Integer Types

    + Rust’s unsigned integer types use their full range to represent positive values and zero.

      + | Type | Range |
| ---- | ---- | ---- |
|  `u8`  | 0 to 2^8–1 (0 to 255) |
|  `u16`  | 0 to 2^16−1 (0 to 65,535) |
|  `u32`  | 0 to 2^32−1 (0 to 4,294,967,295) |
|  `u64`  | 0 to 2^64−1 (0 to 18,446,744,073,709,551,615, or 18 quintillion) |
|  `u128`  | 0 to 2^128−1 (0 to around 3.4✕10^38) |
|  `usize`  | 0 to either 2^32−1 or 2^64−1 |

    + Rust’s signed integer types use the two’s complement representation, using the same bit patterns as the corresponding unsigned type to cover a range of positive and negative values

      + | Type | Range |
| ---- | ---- | ---- |
|  `i8`  | −2^7 to 2^7−1 (−128 to 127) |
|  `i16`  | −2^15 to 2^15−1 (−32,768 to 32,767) |
|  `i32`  | −2^31 to 2^31−1 (−2,147,483,648 to 2,147,483,647) |
|  `i64`  | −2^63 to 2^63−1 (−9,223,372,036,854,775,808 to 9,223,372,036,854,775,807) |
|  `i128`  | −2^127 to 2^127−1 (roughly -1.7✕10^38 to +1.7✕10^38) |
|  `isize`  | Either −2^31 to 2^31−1, or −2^63 to 2^63−1 |

    + Integer literals in Rust can take a suffix indicating their type: `42u8` is a `u8` value, and `1729isize` is an `isize`.

      + {{< logseq/orgTIP >}}If an integer literal lacks a type suffix, Rust puts off determining its type until it finds the value being used in a way that pins it down: stored in a variable of a particular type, passed to a function that expects a particular type, compared with another value of a particular type, or something like that. In the end, if multiple types could work, Rust defaults to `i32` if that is among the possibilities. Otherwise, Rust reports the ambiguity as an error.
{{< / logseq/orgTIP >}}

    + The prefixes  `0x` ,  `0o` , and  `0b`  designate hexadecimal, octal, and binary literals.

    + To make long numbers more legible, you can insert underscores among the digits.

      + For example, you can write the largest  `u32`  value as  `4_294_967_295` .

      + {{< logseq/orgTIP >}}The exact placement of the underscores is not significant, so you can break hexadecimal or binary numbers into groups of four digits rather than three, as in  `0xffff_ffff` , or set off the type suffix from the digits, as in  `127_u8` .
{{< / logseq/orgTIP >}}

    + You can convert from one integer type to another using the as operator. For example: 
`assert_eq!(  255_u8  as i8,     -1_i8);`

    + Note that method calls have a higher precedence than unary prefix operators, so be careful when applying methods to negated values.

      + For example, `-4_i32.abs()` would apply the `abs` method to the positive value `4`, producing positive `4`, and then negate that, producing `-4`.

  + ### Checked, Wrapping, Saturating, and Overflowing Arithmetic


    + When an integer arithmetic operation overflows, Rust panics, in a *debug* build. In a *release* build, the operation **wraps around**: it produces the value equivalent to the mathematically correct result modulo the range of the value.

    + These integer arithmetic methods fall in four general categories:


      + *Checked* operations return an  `Option`  of the result:  `Some(v)`  if the mathematically correct result can be represented as a value of that type, or  `None`  if it cannot. For example:

`rust
// The sum of 10 and 20 can be represented as a u8.
assert_eq!(10_u8.checked_add(20), Some(30));

// Unfortunately, the sum of 100 and 200 cannot.
assert_eq!(100_u8.checked_add(200), None);

// Do the addition; panic if it overflows.
let sum = x.checked_add(y).unwrap();

// Oddly, signed division can overflow too, in one particular case.
// A signed n-bit type can represent -2ⁿ⁻¹, but not 2ⁿ⁻¹.
assert_eq!((-128_i8).checked_div(-1), None);
`

      + *Wrapping* operations return the value equivalent to the mathematically correct result modulo the range of the value:

`rust
// The first product can be represented as a u16;
// the second cannot, so we get 250000 modulo 2¹⁶.
assert_eq!(100_u16.wrapping_mul(200), 20000);
assert_eq!(500_u16.wrapping_mul(500), 53392);
// Operations on signed types may wrap to negative values.
assert_eq!(500_i16.wrapping_mul(500), -12144);//
 In bitwise shift operations, the shift distance
// is wrapped to fall within the size of the value.
// So a shift of 17 bits in a 16-bit type is a shift
// of 1.
assert_eq!(5_i16.wrapping_shl(17), 10);
`

      + *Saturating* operations return the representable value that is closest to the mathematically correct result. In other words, the result is “clamped” to the maximum and minimum values the type can represent:

`rust
assert_eq!(32760_i16.saturating_add(10), 32767);
assert_eq!((-32760_i16).saturating_sub(10), -32768);
`

      + *Overflowing* operations return a tuple (result, overflowed), where result is what the wrapping version of the function would return, and overflowed is a bool indicating whether an overflow occurred:

`rust
assert_eq!(255_u8.overflowing_sub(2), (253, false));
assert_eq!(255_u8.overflowing_add(2), (1, true));
`

    + The operation names that follow the `checked_`, `wrapping_`, `saturating_`, or `overflowing_` prefix are shown in table below:


      + | Operation | Name suffix | Example |
| ---- | ---- | ---- |
| Addition |  `add`  |  `100_i8.checked_add(27) == Some(127)`  |
| Subtraction |  `sub`  |  `10_u8.checked_sub(11) == None`  |
| Multiplication |  `mul`  |  `128_u8.saturating_mul(3) == 255`  |
| Division |  `div`  |  `64_u16.wrapping_div(8) == 8`  |
| Remainder |  `rem`  |  `(-32768_i16).wrapping_rem(-1) == 0`  |
| Negation |  `neg`  |  `(-128_i8).checked_neg() == None`  |
| Absolute value |  `abs`  |  `(-32768_i16).wrapping_abs() == -32768`  |
| Exponentiation |  `pow`  |  `3_u8.checked_pow(4) == Some(81)`  |
| Bitwise left shift |  `shl`  |  `10_u32.wrapping_shl(34) == 40`  |
| Bitwise right shift |  `shr`  |  `40_u64.wrapping_shr(66) == 10`  |

  + ### Floating-Point Types


    + Rust provides IEEE single- and double-precision floating-point types.

      + | Type | Precision | Range |
| ---- | ---- | ---- |
|  `f32`  | IEEE single precision (at least 6 decimal digits) | Roughly –3.4 × 10^38 to +3.4 × 10^38 |
|  `f64`  | IEEE double precision (at least 15 decimal digits) | Roughly –1.8 × 10^308 to +1.8 × 10^308 |

    + Every part of a floating-point number after the integer part is optional, but at least one of the fractional part, exponent, or type suffix must be present, to distinguish it from an integer literal. The fractional part may consist of a lone decimal point, so `5.` is a valid floating-point constant.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052586/files/assets/pr2e_0301.png)

    + {{< logseq/orgTIP >}}If a floating-point literal lacks a type suffix, Rust checks the context to see how the values are used, much as it does for integer literals. If it ultimately finds that either floating-point type could fit, it chooses `f64` by default.
{{< / logseq/orgTIP >}}

    + {{< logseq/orgNOTE >}}For the purposes of type inference, Rust treats integer literals and floating-point literals as distinct classes: it will never infer a floating-point type for an integer literal, or vice versa.
{{< / logseq/orgNOTE >}}

    + The types `f32` and `f64` have associated constants for the IEEE-required special values like `INFINITY`, `NEG_INFINITY` (negative infinity), `NAN` (the not-a-number value), and `MIN` and `MAX` (the largest and smallest finite values).

## The bool Type

  + Rust’s Boolean type, `bool`, has the usual two values for such types, `true` and `false`.

  + Although a  `bool`  needs only a single bit to represent it, Rust uses an entire byte for a  `bool`  value in memory, so you can create a pointer to it.

## Characters

  + Rust’s character type  `char`  represents a single Unicode character, as a 32-bit value.

  + {{< logseq/orgNOTE >}}Rust uses the char type for single characters in isolation, but uses the UTF-8 encoding for strings and streams of text. So, a String represents its text as a sequence of UTF-8 bytes, not as an array of characters.
{{< / logseq/orgNOTE >}}

  + Character literals are characters enclosed in single quotes, like  `'8'`  or  `'!'` .

  + As with byte literals, backslash escapes are required for a few characters:

    + | Character | Rust character literal | Byte literal | Numeric equivalent |
| ---- | ---- | ---- | ---- | ---- |
| Single quote,  `'`  |  `'\''`  | `b'\''` | `39u8` |
| Backslash,  `\`  |  `'\\'`  | `b'\\'` | `92u8` |
| Newline |  `'\n'`  | `b'\n'` | `10u8` |
| Carriage return |  `'\r'`  | `b'\r'` | `13u8` |
| Tab |  `'\t'`  | `b'\t'` | `9u8 |

  + For characters that are hard to write or read, you can write their code in hexadecimal instead.


    + If the character’s code point is in the range U+0000 to U+007F (that is, if it is drawn from the ASCII character set), then you can write the character as  `'\xHH'` , where  `HH`  is a two-digit hexadecimal number.

      + For example, you can write a byte literal for the ASCII “escape” control character as  `b'\x1b'` , since the ASCII code for “escape” is `27`, or `1B` in hexadecimal.

      + Since byte literals are just another notation for `u8` values, consider whether a simple numeric literal might be more legible: it probably makes sense to use `b'\x1b'` instead of simply `27` only when you want to emphasize that the value represents an ASCII code.

    + You can write any Unicode character as  `'\u{HHHHHH}'` , where  `HHHHHH`  is a hexadecimal number up to six digits long, with underscores allowed for grouping as usual.

      + For example, the character literal  `'\u{CA0}'`  represents the character “ಠ”

  + A  `char`  always holds a Unicode code point in the range 0x0000 to 0xD7FF, or 0xE000 to 0x10FFFF.

  + Rust never implicitly converts between  `char`  and any other type. You can use the  `as`  conversion operator to convert a  `char`  to an integer type.

## Tuples

  + A *tuple* is a pair, or triple, quadruple, quintuple, etc. (hence, *n-tuple*, or *tuple*), of values of assorted types. You can write a tuple as a sequence of elements, separated by commas and surrounded by parentheses.

  + Tuple is similar to array, but the biggest difference is: Each element of a tuple can have a different type, whereas an array’s elements must be all the same type.

  + {{< logseq/orgTIP >}}Rust code often uses tuple types to return multiple values from a function.
{{< / logseq/orgTIP >}}

  + The other commonly used tuple type is the zero-tuple  `()` . This is traditionally called the *unit type* because it has only one value, also written  `()` . Rust uses the unit type where there’s no meaningful value to carry, but context requires some sort of type nonetheless.

  + For consistency’s sake, there are even tuples that contain a single value. The literal  `("lonely hearts",)`  is a tuple containing a single string; its type is  `(&str,)` . Here, the comma after the value is necessary to distinguish the singleton tuple from a simple parenthetic expression.

## Pointer Types

  + Rust has several types that represent memory addresses: *references*, *boxes* and *unsafe pointers*.

  + {{< logseq/orgTIP >}}Rust is designed to help keep allocations to a minimum, thus **values nest by default**. For example, The value `((0, 0), (1440, 900))` is stored as four adjacent integers.
This is great for memory efficiency, but as a consequence, when a Rust program needs values to point to other values, it must use pointer types explicitly.
{{< / logseq/orgTIP >}}

  + ### References

    + References are Rust's basic pointer type. At run time, a reference to an  `i32`  is a single machine word holding the address of the  `i32` , which may be on the stack or in the heap.

    + The expression  `&x`  produces a reference to  `x` ; in Rust terminology, we say that it *borrows a reference to  `x` *. Given a reference  `r` , the expression  `*r`  refers to the value  `r`  points to.

    + {{< logseq/orgCAUTION >}}Like a C pointer, a reference does not automatically free any resources when it goes out of scope.
{{< / logseq/orgCAUTION >}}

    + {{< logseq/orgTIP >}}Unlike C pointers, however, Rust references are never null: there is simply no way to produce a null reference in safe Rust. And unlike C, Rust tracks the ownership and lifetimes of values, so mistakes like dangling pointers, double frees, and pointer invalidation are ruled out at compile time.
{{< / logseq/orgTIP >}}

    + Rust references come in two flavors:

      + `&T`

        + An **immutable**, **shared** reference. You can have many shared references to a given value at a time, but they are read-only: modifying the value they point to is forbidden, as with  `const T*`  in C.

      + `&mut T`

        + A **mutable**, **exclusive** reference. You can read and modify the value it points to, as with a  `T*`  in C. But for as long as the reference exists, you may not have any other references of any kind to that value.

    + {{< logseq/orgTIP >}}The "**single writer or multiple reader**" rule:
either you can read and write the value, or it can be shared by any number of readers, but never both at the same time.
{{< / logseq/orgTIP >}}

  + ### Boxes

    + The simplest way to allocate a value in the heap is to use  `Box::new` :

`rust
let t = (12, "eggs");
let b = Box::new(t);	// allocate a tuple in the heap
`

    + The call to  `Box::new`  allocates enough memory to contain the tuple on the heap. When  `b`  goes out of scope, the memory is freed immediately, unless  `b`  has been *moved*—by returning it.

  + ### Unsafe Pointers (Raw Pointers)

    + Rust also has the raw pointer types  `*mut T`  and  `*const T` . Raw pointers really are just like pointers in C++. Using a raw pointer is unsafe, because Rust makes no effort to track what it points to.

    + {{< logseq/orgCAUTION >}}Raw pointers might be null, or they might point to memory that has been freed or that now contains a value of a different type.
{{< / logseq/orgCAUTION >}}

## Arrays, Vectors, and Slices

  + Rust has three types for representing a sequence of values in memory:

    + The type  `[T; N]`  represents an array of  `N`  values, each of type  `T` . An array’s size is a constant determined at compile time and is part of the type; you can’t append new elements or shrink an array.

    + The type  `Vec<T>` , called a *vector of  `T` s*, is a dynamically allocated, growable sequence of values of type  `T` . A vector’s elements live on the heap, so you can resize vectors at will: push new elements onto them, append other vectors to them, delete elements, and so on.

    + The types  `&[T]`  and  `&mut [T]` , called a *shared slice of  `T` s* and *mutable slice of  `T` s*, are references to a series of elements that are a part of some other value, like an array or vector. You can think of a slice as a pointer to its first element, together with a count of the number of elements you can access starting at that point. A mutable slice  `&mut [T]`  lets you read and modify elements, but can’t be shared; a shared slice  `&[T]`  lets you share access among several readers, but doesn’t let you modify elements.

  + ### Arrays

    + For the common case of a long array filled with some value, you can write  `[` , where *V* is the value each element should have, and *N* is the length. For example, `[0u8; 1024]` can be a one-kilo byte buffer, filled with zeros.

    + Rust has no notation for an uninitialized array. (In general, Rust ensures that code can never access any sort of uninitialized value.)

    + An array’s length is part of its type and fixed at compile time. If  `n`  is a variable, you can’t write  `[true; n]`  to get an array of  `n`  elements. When you need an array whose length varies at run time (and you usually do), use a vector instead.

  + ### Vectors

    + A vector  `Vec<T>`  is a resizable array of elements of type  `T` , allocated on the heap.

    + A  `Vec<T>`  consists of three values:

      + a pointer to the heap-allocated buffer for the elements, which is created and owned by the  `Vec<T>` ;

      + the number of elements that buffer has the capacity to store;

      + and the number it actually contains now (in other words, its length).

    + When the buffer has reached its capacity, adding another element to the vector entails allocating a larger buffer, copying the present contents into it, updating the vector’s pointer and capacity to describe the new buffer, and finally freeing the old one.

    + You can insert and remove elements wherever you like in a vector, although these operations shift all the elements after the affected position forward or backward, so they may be slow if the vector is long.

  + ### Slices


    + A [slice](https://doc.rust-lang.org/std/primitive.slice.html), written  `[T]`  without specifying the length, is a region of an array or vector. Since a slice can be any length, slices can’t be stored directly in variables or passed as function arguments. Slices are always passed by reference.

    + A reference to a slice is a *fat pointer*: a two-word value comprising a pointer to the slice’s first element, and the number of elements in the slice.

    + Whereas an ordinary reference is a non-owning pointer to a single value, a reference to a slice is a non-owning pointer to a range of consecutive values in memory. This makes slice references a good choice when you want to write a function that operates on either an array or a vector.

      + Suppose you run the following code:

`rust
let v: Vec<f64> = vec![0.0,  0.707,  1.0,  0.707];
let a: [f64; 4] =     [0.0, -0.707, -1.0, -0.707];
- let sv: &[f64] = &v;
let sa: &[f64] = &a;
`

      + In the last two lines, Rust automatically converts the  `&Vec<f64>`  reference and the  `&[f64; 4]`  reference to slice references that point directly to the data. By the end, memory looks like:

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052586/files/assets/pr2e_0302.png)

## String Types

  + Rust strings are sequences of Unicode characters. And Rust guarantees that strings are valid UTF-8.

  + ### String Literals


    + String literals are enclosed in double quotes. They use the same backslash escape sequences as  `char`  literals. For example: `let hello = "\"Hello", world.]n";`.

    + A string may span multiple lines:

`rust
println!("In the room the women come and go,
  Singing of Mount Abora");
`

      + The newline character in that string literal is included in the string and therefore in the output. So are the spaces at the beginning of the second line.

    + If one line of a string ends with a backslash, then the newline character and the leading whitespace on the next line are dropped:

`rust
println!("It was a bright, cold day in April, and \
  there were four of us—\
  more or less.");
`

      + This prints a single line of text.

    + Rust offers *raw strings*. A raw string is tagged with the lowercase letter  `r` . All backslashes and whitespace characters inside a raw string are included verbatim in the string. **No escape sequences are recognized**: `let path = r"C:\Program Files\xxx";`.

    + You can’t include a double-quote character in a raw string simply by putting a backslash in front of it—remember, we said *no* escape sequences are recognized. However, there is a cure for that too. The start and end of a raw string can be marked with pound signs:

`rust
println!(r###"
  This raw string started with 'r###"'.
  Therefore it does not end until we reach a quote mark ('"')
  followed immediately by three pound signs ('###'):
"###);
`

      + You can add as few or as many pound signs as needed to make it clear where the raw string ends.

  + ### Byte Strings


    + A string literal with the  `b`  prefix is a *byte string*. Such a string is a slice of  `u8`  values—that is, bytes—rather than Unicode text.

`rust
let method = b"GET";
assert_eq!(method, &[b'G', b'E', b'T']);
`

    + Byte strings can use all the syntax of string literal. Raw byte strings start with  `br"` .

    + Byte strings can’t contain arbitrary Unicode characters. They must make do with ASCII and  `\xHH`  escape sequences.

  + ### String in Memory


    + Although Rust strings are sequences of Unicode characters, but they are not stored in memory as arrays of  `char` s. Instead, they are stored using UTF-8, a variable-width encoding. Each ASCII character in a string is stored in one byte. Other characters take up multiple bytes.

    + A  `String`  has a resizable buffer holding UTF-8 text. The buffer is allocated on the heap, so it can resize its buffer as needed or requested.

      + {{< logseq/orgTIP >}}You can think of a String as a Vec<u8> that is guaranteed to hold well-formed UTF-8; in fact, this is how String is implemented.
{{< / logseq/orgTIP >}}

    + A  `&str`  (pronounced “stir” or “string slice”) is a reference to a run of UTF-8 text owned by someone else: it “borrows” the text. `&str`  is very much like  `&[T]` : a fat pointer to some data.

      + Like other slice references, a  `&str`  is a fat pointer, containing both the address of the actual data and its length. You can think of a  `&str`  as being nothing more than a  `&[u8]`  that is guaranteed to hold well-formed UTF-8.

    + The  [`str`](https://doc.rust-lang.org/std/primitive.str.html)  type, also called a ‘string slice’, is the most primitive string type. It is usually seen in its borrowed form,  `&str` . It is also the type of string literals,  `&'static str` .

    + `String`, `&str` and `str` in memory:

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052586/files/assets/pr2e_0303.png)

    + A string literal is a  `&str`  that refers to preallocated text, typically stored in read-only memory along with the program’s machine code.

    + A  `String`  or  `&str` ’s  `.len()`  method returns its length. The length is measured in bytes, not characters.

  + ### String


    + `String`  is analogous to  `Vec<T>`. Like a  `Vec` , each  `String`  has its own heap-allocated buffer that isn’t shared with any other  `String` . When a  `String`  variable goes out of scope, the buffer is automatically freed, unless the  `String`  was moved.

    + Strings support the `==`, `!=`, `<`, `<=`, `>`, `>=` operators.

    + {{< logseq/orgCAUTION >}}- Keep in mind that, given the nature of Unicode, simple  `char` -by- `char`  comparison does *not* always give the expected answers.  
{{< / logseq/orgCAUTION >}}

## Type Aliases

  + The  `type`  keyword can be used like  `typedef`  in C++ to declare a new name for an existing type:

`rust
type Bytes = Vec<u8>;
`
