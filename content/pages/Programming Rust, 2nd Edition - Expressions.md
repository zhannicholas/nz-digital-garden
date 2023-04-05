---
tags:
- Rust
title: Programming Rust, 2nd Edition - Expressions
categories:
date: 2023-04-04
lastMod: 2023-04-04
---


Most things in Rust are expressions. *expressions* are the building blocks that make up the body of Rust functions and thus the majority of Rust code.

Expressions have values. Statements don’t.

## Precedence and Associativity


  + Like most programming languages, Rust has *operator precedence* to determine the order of operations when an expression contains multiple adjacent operators.

    + The following table summarizes Rust expression syntax. In the table, operators are listed in order of precedence, from highest to lowest.


      + | Expression type | Example | Related traits |
| ---- | ---- | ---- |
| Array literal |  `[1, 2, 3]`  | |
| Repeat array literal |  `[0; 50]`  | |
| Tuple |  `(6, "crullers")`  | |
| Grouping |  `(2 + 2)`  | |
| Block |  `{ f(); g() }`  | |
| Control flow expressions |  `if ok { f() }`  | |
| |  `if ok { 1 } else { 0 }`  | |
| |  `if let Some(x) = f() { x } else { 0 }`  | |
| |  `match x { None => 0, _ => 1 }`  | |
| |  `for v in e { f(v); }`  | `std::iter::IntoIterator`  |
| |  `while ok { ok = f(); }`  | |
| |  `while let Some(x) = it.next() { f(x); }`  | |
| |  `loop { next_event(); }`  | |
| |  `break`  | |
| |  `continue`  | |
| |  `return 0`  | |
| Macro invocation |  `println!("ok")`  | |
| Path |  `std::f64::consts::PI`  | |
| Struct literal |  `Point {x: 0, y: 0}`  | |
| Tuple field access |  `pair.0`  | `Deref`, `DerefMut`  |
| Struct field access |  `point.x`  | `Deref` , `DerefMut`  |
| Method call |  `point.translate(50, 50)`  | `Deref`, `DerefMut` |
| Function call |  `stdin()`  | `Fn(Arg0, ...) -> T`, `FnMut(Arg0, ...) -> T` , `FnOnce(Arg0, ...) -> T` |
| Index |  `arr[0]`  | `Index` , `IndexMut`, `Deref` , `DerefMut` |
| Error check |  `create_dir("tmp")?`  | |
| Logical/bitwise NOT |  `!ok`  | `Not` |
| Negation |  `-num`  | `Neg`  |
| Dereference |  `*ptr`  | `Deref` , `DerefMut` |
| Borrow |  `&val`  | |
| Type cast |  `x as u32`  | |
| Multiplication |  `n * 2`  | `Mul` |
| Division |  `n / 2`  |  `Div`  |
| Remainder (modulus) |  `n % 2`  | `Rem`  |
| Addition |  `n + 1`  | `Add`  |
| Subtraction |  `n - 1`  | `Sub`  |
| Left shift |  `n << 1`  | `Shl` |
| Right shift |  `n >> 1`  | `Shr`  |
| Bitwise AND |  `n & 1`  | `BitAnd` |
| Bitwise exclusive OR |  `n ^ 1`  | `BitXor`  |
| Bitwise OR |  <code>n &#124; 1</code>  | `BitOr`  |
| Less than |  `n < 1`  | `std::cmp::PartialOrd` |
| Less than or equal |  `n <= 1`  | `std::cmp::PartialOrd` |
| Greater than |  `n > 1`  | `std::cmp::PartialOrd` |
| Greater than or equal |  `n >= 1`  |  `std::cmp::PartialOrd` |
| Equal |  `n == 1`  | `std::cmp::PartialEq`  |
| Not equal |  `n != 1`  | `std::cmp::PartialEq`  |
| Logical AND |  `x.ok && y.ok`  | |
| Logical OR |  <code>x.ok &#124;&#124; backup.ok</code>  | |
| End-exclusive range |  `start .. stop`  | |
| End-inclusive range |  `start ..= stop`  | |
| Assignment |  `x = val`  | |
| Compound assignment |  `x *= 1`  | `MulAssign`  |
| |  `x /= 1`  | `DivAssign`  |
| |  `x %= 1`  | `RemAssign`  |
| |`x += 1`  | `AddAssign`  |
| |  `x -= 1`  | `SubAssign`  |
| |  `x <<= 1`  | `ShlAssign`  |
| |  `x >>= 1`  | `ShrAssign`  |
| |  `x &= 1`  | `BitAndAssign`  |
| |  `x ^= 1`  | `BitXorAssign`  |
| |  <code>x &#124;= 1</code>  | `BitOrAssign`  |
| Closure |  <code>&#124;x, y&#124; x + y</code>  | |

      + 

  + All of the operators that can usefully be chained are left-associative. That is, a chain of operations such as  `a - b - c`  is grouped as  `(a - b) - c` , not  `a - (b - c)` . The operators that can be chained in this way are all the ones you might expect:`*   /   %   +   -   <<   >>   &   ^   |   &&   ||   as`

  + The comparison operators, the assignment operators, and the range operators  `..`  and  `..=`  can’t be chained at all.

## Blocks and Semicolons


  + Blocks (`{...}`) are the most general kind of expression. A block produces a value and can be used anywhere a value is needed.

  + The value of the block is the value of its last expression.

  + In Rust, an expression plus a semicolon(;) makes a statement, and statements don't have values.

`rust
let msg = {
  // let-declaration: semicolon is always required
  let dandelion_control = puffball.open();
  // expression + semicolon: method is called, return value dropped
  dandelion_control.release_all_seeds(launch_codes);
  // expression with no semicolon: method is called,
  // return value stored in `msg`
  dandelion_control.get_status()
};
`

## Declarations


  + In addition to expressions and semicolons, a block may contain any number of declarations. The most common are  `let`  declarations, which declare local variables:

`rust
let name: type = expr;
`

    + The type and initializer are optional. The semicolon is required.

  + A  `let`  declaration can declare a variable without initializing it. The variable can then be initialized with a later assignment.

  + It’s an error to use a variable before it’s initialized. (This is closely related to the error of using a value after it’s been moved. Rust really wants you to use values only while they exist!).

  + If you declare a new variable with the same name as a previous variable, then the previous variable is [shawdowed](https://rust-book.cs.brown.edu/ch03-01-variables-and-mutability.html#shadowing) by the second one.

  + A block can also contain *item declarations*. An item is simply any declaration that could appear globally in a program or module, such as a  `fn` ,  `struct` , or  `use` .

## if and match


  + ### if

    + The form of an  `if`  expression:

`rust
if condition1 {
  block1
} else if condition2 {
  block2
} else {
  block_n
}
`

      + Each `condition` must be an expression of type  `bool` ; true to form, Rust does not implicitly convert numbers or pointers to Boolean values.

    + An if-expression without an else-branch always returns the unit type.

  + ### match


    + The general form of a `match` expression is:

`rust
match value {
  pattern1 => expr1,
  pattern2 => expr2,
  ...,
  _ => expr_default
}
`

    + Rust checks the given value against each pattern in turn, starting with the first. When a pattern matches, the corresponding `expr` is evaluated, and the  `match`  expression is complete; no further patterns are checked. At least one of the patterns must match. **Rust prohibits  `match`  expressions that do not cover all possible values.**

    + The wildcard pattern `_` matches everything.  Placing a `_` pattern before other patterns means that it will have precedence over them. Those patterns will never match anything (and the compiler will warn you about it).

    + Every expression after `=>` is a arm. All arms of a  `match`  expression must have the same type.

  + ### if let

    + `if let` expression is another form of `if`:

`rust
if let pattern = expr {
  block1
} else {
  block2
}
`

      + The given expr either matches the pattern, in which case block1 runs, or doesn’t match, and block2 runs.

    + An `if let` expression is shorthand for a `match` with just one pattern:

`rust
match expr {
  pattern => { block1 }
  _ => { block2 }
}
`

## Loops


  + There are four looping expressions:

`rust
while condition {
  block
}

while let pattern = expr {
  block
}

loop {
  block
}

for pattern in iterable {
  block
}
`

  + Loops are expressions in Rust, but the value of a  `while`  or  `for`  loop is always  `()` , so their value isn’t very useful. A  `loop`  expression can produce a value if you specify one.

  + The  `while let`  loop is analogous to  `if let` . At the beginning of each loop iteration, the value of expr either matches the given pattern, in which case the block runs, or doesn’t, in which case the loop exits.

  + Use  `loop`  to write infinite loops. It executes the block repeatedly forever (or until a  `break`  or  `return`  is reached or the thread panics).

  + A  `for`  loop evaluates the iterable expression and then evaluates the block once for each value in the resulting iterator.

  + The  `..`  operator produces a *range*, a simple struct with two fields:  `start`  and  `end` .  `0..20`  is the same as  `std::ops::Range { start: 0, end: 20 }` .

## Control Flow in Loops


  + A  `break`  expression exits an enclosing loop. (In Rust,  `break`  works only in loops. It is not necessary in  `match`  expressions, which are unlike  `switch`  statements in this regard.)

  + Within the body of a  `loop` , you can give  `break`  an expression, whose value becomes that of the loop.

  + Naturally, all the  `break`  expressions within a  `loop`  must produce values with the same type, which becomes the type of the  `loop`  itself.

  + A  `continue`  expression jumps to the next loop iteration.

  + ### Lifetime labels in loops


    + A loop can be *labeled* with a lifetime. In the following example,  `'search:`  is a label for the outer  `for`  loop. Thus,  `break 'search`  exits that loop, not the inner loop:

`rust
'search:
for room in apartment {
  for spot in room.hiding_spots() {
      if spot.contains(keys) {
          println!("Your keys are {} in the {}.", spot, room);
          break 'search;
      }
  }
}
`

    + A  `break`  can have both a label and a value expression:

`rust
// Find the square root of the first perfect square
// in the series.
let sqrt = 'outer: loop {
  let n = next_number();
  for i in 1.. {
      let square = i * i;
      if square == n {
          // Found a square root.
          break 'outer i;
      }
      if square > n {
          // `n` isn't a perfect square, try the next
          break;
      }
  }
};
`

    + Labels can also be used with  `continue` .

## Return Expressions


  + A  `return`  expression exits the current function, returning a value to the caller.

  + `return`  without a value is shorthand for  `return ()` :

`rust
fn f() {     // return type omitted: defaults to ()
    return;  // return value omitted: defaults to ()
}
`

## Why Rust Has Loop


  + ***Flow-sensitive* analyses**: Several pieces of the Rust compiler analyze the flow of control through your program:

    + Rust checks that every path through a function returns a value of the expected return type. To do this correctly, it needs to know whether it’s possible to reach the end of the function.

    + Rust checks that local variables are never used uninitialized. This entails checking every path through a function to make sure there’s no way to reach a place where a variable is used without having already passed through code that initializes it.

    + Rust warns about unreachable code. Code is unreachable if *no* path through the function reaches it.

  + Expressions that don’t finish normally are assigned the special type `!`, and they’re exempt from the rules about types having to match.

    + For example, the function signature of `std::process:exit()` is:

`rust
fn exit(code: i32) -> !
`

    + The `!` means that exit() never returns. It’s a *divergent function*.

## Functions and Method Calls


  + The syntax for calling functions and methods is the same in Rust as in many other languages:

`rust
let x = gcd(1302, 462);  // function call
let room = player.location();  // method call
`

  + A third syntax is used for calling type-associated functions, like  `Vec::new()` :

`rust
let mut numbers = Vec::new();  // type-associated function call
`

  + One quirk of Rust syntax is that in a function call or method call, the usual syntax for generic types,  `Vec<T>` , does not work:

`rust
return Vec<i32>::with_capacity(1000);  // error: something about chained comparisons
let ramp = (0 .. n).collect<Vec<i32>>();  // same error
`

    + The problem is that in expressions,  `<`  is the less-than operator. The Rust compiler helpfully suggests writing  `::<T>`  instead of  `<T>`  in this case, and that solves the problem:

`rust
return Vec::<i32>::with_capacity(1000);  // ok, using ::<
let ramp = (0 .. n).collect::<Vec<i32>>();  // ok, using ::<
`

    + The symbol  `::<...>`  is affectionately known in the Rust community as the *turbofish*.

## Fields and Elements


  + The fields of a struct are accessed using familiar syntax. Tuples are the same except that their fields have numbers rather than names:

`rust
game.black_pawns   // struct field
coords.1           // tuple element
`

    + If the value to the left of the dot (`.`) is a reference or smart pointer type, it is automatically dereferenced, just as for method calls.

  + Square brackets access the elements of an array, a slice, or a vector:

`rust
pieces[i]          // array element
`

    + The value to the left of the brackets is automatically dereferenced.

  + Extracting a slice from an array or vector is straightforward:

`rust
let second_half = &game_moves[midpoint .. end];
`

    + The  `..`  operator allows either operand to be omitted; it produces up to four different types of object depending on which operands are present:

`rust
..      // RangeFull
a ..    // RangeFrom { start: a }
.. b    // RangeTo { end: b }
a .. b  // Range { start: a, end: b }
`

      + The latter two forms are *end-exclusive* (or *half-open*): the end value is not included in the range represented.

    + The  `..=`  operator produces *end-inclusive* (or *closed*) ranges, which do include the end value:

`rust
..= b    // RangeToInclusive { end: b }
a ..= b  // RangeInclusive::new(a, b)
`

## Reference Operators

  + The unary  `*`  operator is used to access the value pointed to by a reference.

  + The address-of operators: `&` and `&mut`:

    + ## Reference to Values


## Type Casts

  + Converting a value from one type to another usually requires an **explicit** cast in Rust. Casts use the  `as`  keyword:

`rust
let x = 17;              // x is type i32
let index = x as usize;  // convert to usize
`

  + Several kinds of casts are permitted:


    + Numbers may be cast from any of the built-in numeric types to any other.

      + Casting an integer to another integer type is always well-defined. Converting to a narrower type results in truncation. A signed integer cast to a wider type is sign-extended, an unsigned integer is zero-extended, and so on. In short, there are no surprises.

      + Converting from a floating-point type to an integer type rounds toward zero: the value of  `-1.99 as i32`  is  `-1` . If the value is too large to fit in the integer type, the cast produces the closest value that the integer type can represent: the value of  `1e6 as u8`  is  `255` .

    + Values of type  `bool`  or  `char` , or of a C-like  `enum`  type, may be cast to any integer type.

      + Casting in the other direction is not allowed, as  `bool` ,  `char` , and  `enum`  types all have restrictions on their values that would have to be enforced with run-time checks.

    + Some casts involving unsafe pointer types are also allowed.

  + We said that a conversion *usually* requires a cast. A few conversions involving reference types are so straightforward that the language performs them even without a cast. One trivial example is converting a  `mut`  reference to a non- `mut`  reference.

  + Several more significant automatic conversions can happen, though:

    + Values of type  `&String`  auto-convert to type  `&str`  without a cast.

    + Values of type  `&Vec<i32>`  auto-convert to  `&[i32]` .

    + Values of type  `&Box<Chessboard>`  auto-convert to  `&Chessboard` .

  + These are called *deref coercions*, because they apply to types that implement the  `Deref`  built-in trait. The purpose of  `Deref`  coercion is to make smart pointer types, like  `Box` , behave as much like the underlying value as possible. Using a  `Box<Chessboard>`  is mostly just like using a plain  `Chessboard` , thanks to  `Deref` .

## Closures

  + Rust has *closures*, lightweight function-like values. A closure usually consists of an argument list, given between vertical bars, followed by an expression:

`rust
let is_even = |x| x % 2 == 0;
`

  + Rust infers the argument types and return type. You can also write them out explicitly, as you would for a function. If you do specify a return type, then the body of the closure must be a block, for the sake of syntactic sanity:

`rust
let is_even = |x: u64| -> bool x % 2 == 0;  // error
- let is_even = |x: u64| -> bool { x % 2 == 0 };  // ok
`

  + Calling a closure uses the same syntax as calling a function:

`rust
assert_eq!(is_even(14), true);
`
