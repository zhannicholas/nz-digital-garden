---
tags:
- Rust
title: Programming Rust, 2nd Edition - Enums and Patterns
categories:
date: 2023-04-04
lastMod: 2023-04-04
---


## Enums

  + A Rust enum can contain not only type, but also data, even data of varying types.

  + The values in a Rust enum are called *variants* or *constructors*.

  + In all, Rust has three kinds of enum variant:

    + 1. Variants with no data correspond to unit-like structs. 
2. Tuple variants look and function just like tuple structs. 
3. Struct variants have curly braces and named fields.

    + {{< logseq/orgTIP >}}A single enum can have variants of all three kinds.
{{< / logseq/orgTIP >}}

  + All constructors and fields of an enum share the same visibility as the enum itself.

  + ### Enums with No Data


    + Enums with no data are like C-Style enums:

`rust
enum Ordering {
  Less,
  Equal,
  Greater,
}
`

    + In memory, values of C-style enums are stored as integers. Occasionally it’s useful to tell Rust which integers to use:

`rust
enum HttpStatus {
  Ok = 200,
  NotModified = 304,
  NotFound = 404,
  ...
}
`

      + Otherwise Rust will assign the numbers for you, starting at 0.

    + By default, Rust stores C-style enums using the smallest built-in integer type that can accommodate them. Most fit in a single byte. You can override Rust’s choice of in-memory representation by adding a  `#[repr]`  attribute to the enum

    + Casting a C-style enum to an integer is allowed. However, casting in the other direction, from the integer to the enum, is not.

      + {{< logseq/orgNOTE >}}Rust guarantees that an enum value is only ever one of the values spelled out in the `enum` declaration. An unchecked cast from an integer type to an enum type could break this guarantee, so it’s not allowed. 
{{< / logseq/orgNOTE >}}

  + ### Enums with Data


    + When it comes to data, enums have two variants: *tuple variants* and *struct variants*.

      + For example:

        + Tuple variants contain tuple(s) as data:

`rust
#[derive(Copy, Clone, Debug, PartialEq)]
enum RoughTime {
    InThePast(TimeUnit, u32),	// tuple variant
    JustNow,	// no data
    InTheFuture(TimeUnit, u32),	// tuple variant
}
`

        + Struct variants contain struct(s) as data:

`rust
enum Shape {
    Sphere { center: Point3d, radius: f32 },
    Cuboid { corner1: Point3d, corner2: Point3d },
}
`

  + ### Enums in Memory

    + In memory, enums with data are stored as a small integer *tag*, plus enough memory to hold all the fields of the largest variant. The tag field is for Rust’s internal use. It tells which constructor created the value and therefore which fields it has.

    + Rust makes no promises about enum layout, however, in order to leave the door open for future optimizations.

  + ### Generic Enums


    + Enums can be generic. Two examples from the standard library are among the most-used data types in the language:


`rust
enum Option<T> {
  None,
  Some(T),
}

enum Result<T, E> {
  Ok(T),
  Err(E),
}
`

      + The syntax for generic enums is the same as for generic structs.

    + One unobvious detail is that Rust can eliminate the tag field of  `Option<T>`  when the type  `T`  is a reference,  `Box` , or other smart pointer type. Since none of those pointer types is allowed to be zero, Rust can represent  `Option<Box<i32>>` , say, as a single machine word: 0 for  `None`  and nonzero for  `Some`  pointer. This makes such  `Option`  types close analogues to C or C++ pointer values that could be null. The difference is that Rust’s type system requires you to check that an `Option` is `Some` before you can use its contents. **This effectively eliminates null pointer dereferences**.

    + The only way to access the data in an enum is the safe way: using patterns.

## Patterns

  + Expressions *produce* values; patterns *consume* values.

  + Pattern matching an enum, struct, or tuple works as though Rust is doing a simple left-to-right scan, checking each component of the pattern to see if the value matches it. If it doesn’t, Rust moves on to the next pattern.

  + The following table summarized Rust patterns:


    + | Pattern type | Example | Notes |
| ---- | ---- | ---- |
| Literal |`100`, `"name"`| Matches an exact value; the name of a  `const`  is also allowed |
| Range |`0 ..= 100`, `'a' ..= 'k'`, `256..`| Matches any value in range, including the end value if given |
| Wildcard |  `_`  | Matches any value and ignores it |
| Variable |`name`, `mut count`| Like  `_`  but moves or copies the value into a new local variable |
|  `ref`  variable |`ref field`, `ref mut field`| Borrows a reference to the matched value instead of moving or copying it |
| Binding with subpattern |`val @ 0 ..= 99`, `ref circle @ Shape::Circle { .. }`| Matches the pattern to the right of  `@` , using the variable name to the left |
| Enum pattern |`Some(value)`, `None`, `Pet::Orca`|   |
| Tuple pattern |`(key, value)`, `(r, g, b)`|   |
| Array pattern |`[a, b, c, d, e, f, g]`, `[heading, carom, correction]`|   |
| Slice pattern |`[first, second]`, `[first, _, third]`, `[first, .., nth]`, `[]`|   |
| Struct pattern |`Color(r, g, b)`, `Point { x, y }`, `Card { suit: Clubs, rank: n }`, `Account { id, name, .. }`|   |
| Reference |`&value`, `&(k, v)`| Matches only reference values |
| Or patterns |<code>'a' &#124; 'A'</code>, <code>Some("left" &#124; "right")</code>|   |
| Guard expression |  `x if x * x <= r2`  | In  `match`  only (not valid in  `let` , etc.) |

  + ### Literals, Variable, and Wildcards in Patterns


    + When you need something like a C  `switch`  statement, use  `match`  with an integer value. Integer literals like  `0`  and  `1`  can serve as patterns:


`rust
match meadow.count_rabbits() {
  0 => {}  // nothing to say
  1 => println!("A rabbit is nosing around in the clover."),
  n => println!("There are {} rabbits hopping about in the meadow", n),
}
`

      + The pattern  `0`  matches if there are no rabbits in the meadow.  `1`  matches if there is just one.Otherwise, we reach the third pattern, n. This pattern is just a **variable name**. It can match any value, and **the matched value is moved or copied** into a new local variable. So in this case, the value of  `meadow.count_rabbits()`  is stored in a new local variable  `n` , which we then print.

    + Other literals, including Booleans, chracters, and even strings, can be used as patterns too.

    + If you need a catchall pattern, but you don’t care about the matched value, you can use a single underscore `_` as a pattern, the wildcard pattern.


      + {{< logseq/orgTIP >}}The wildcard pattern matches any value, but without storing it anywhere. 
Since Rust requires every match expression to handle all possible values, a wildcard is often required at the end.
{{< / logseq/orgTIP >}}

  + ### Tuple and Structure Patterns


    + Tuple patterns match tuples, they are useful any time you want to get multiple pieces of data involved in a single  `match`.


      + For example

`rust
fn describe_point(x: i32, y: i32) -> &'static str {
    use std::cmp::Ordering::*;
    match (x.cmp(&0), y.cmp(&0)) {
        (Equal, Equal) => "at the origin",
        (_, Equal) => "on the x axis",
        (Equal, _) => "on the y axis",
        (Greater, Greater) => "in the first quadrant",
        (Less, Greater) => "in the second quadrant",
        _ => "somewhere else",
    }
}
`

    + Struct patterns use curly braces, just like struct expressions. They contain a subpattern for each field:


`rust
match balloon.location {
    Point { x: 0, y: height } =>
        println!("straight up {} meters", height),
    Point { x: x, y: y } =>
        println!("at ({}m, {}m)", x, y),
}
`

      + In this example, if the first arm matches, then  `balloon.location.y`  is stored in the new local variable  `height` .

      + Patterns like  `Point { x: x, y: y }`  are common when matching structs, and the redundant names are visual clutter, so Rust has a shorthand for this:  `Point {x, y}` . The meaning is the same. This pattern still stores a point’s  `x`  field in a new local  `x`  and its  `y`  field in a new local  `y` .

      + Even with the shorthand, it is cumbersome to match a large struct when we only care about a few fields. To avoid this, use `..` to tell Rust you don’t care about any of the other fields.

  + ### Arrays and Slice Patterns


    + Array patterns match arrays.

`rust
fn hsl_to_rgb(hsl: [u8; 3]) -> [u8; 3] {
    match hsl {
        [_, _, 0] => [0, 0, 0],
        [_, _, 255] => [255, 255, 255],
        ...
    }
}
`

    + Slice patterns are similar, but unlike arrays, slices have variable lengths, so slice patters match not only on values but also on length. .. in a slice pattern matches any number of elements:

`rust
fn greet_people(names: &[&str]) {
    match names {
        [] => { println!("Hello, nobody.") },
        [a] => { println!("Hello, {}.", a) },
        [a, b] => { println!("Hello, {} and {}.", a, b) },
        [a, .., b] => { println!("Hello, everyone from {} to {}.", a, b) }
    }
}
`

  + ### Reference Patterns


    + Rust patterns support two features for working with references. `ref` patterns borrow parts of a matched value. `&` patterns match references.

    + **Matching a noncopyable value moves the value**.


`rust
match account {
    Account { name, language, .. } => {	
        ui.greet(&name, &language);
        ui.show_settings(&account);  // error: borrow of moved value: `account`
    }
}
`

      + Here, the fields  `account.name`  and  `account.language`  are moved into local variables  `name`  and  `language` . The rest of  `account`  is dropped. That’s why we can’t borrow a reference to it afterward.

      + {{< logseq/orgTIP >}}f name and language were both copyable values, Rust would copy the fields instead of moving them, and this code would be fine. 
{{< / logseq/orgTIP >}}

    + The `ref` keyword **borrows** matched values instead moving them:


`rust
match account {
    Account { ref name, ref language, .. } => {
        ui.greet(name, language);
        ui.show_settings(&account);  // ok
    }
}
`

      + You can use  `ref mut`  to borrow  `mut`  references.

    + The opposite kind of reference pattern is the `&` pattern. A pattern starting with `&` matches a reference.

    + The thing to remember is that **patterns and expressions are natural opposites**. The expression `(x, y)` makes two values into a new tuple, but the pattern `(x, y)` does the opposite: it matches a tuple and breaks out the two values. It’s the same with `&`. In an expression, `&` creates a reference. In a pattern, `&` matches a reference.

  + ### Match Guards


    + Sometimes a match arm has additional conditions that must be met before it can be considered a match.

    + Rust provides match guards, extra conditions that must be true in order for a match arm to apply, written as `if CONDITION`, between the pattern and the arm’s `=>` token:

`rust
fn check_move(current_hex: Hex, click: Point) -> game::Result<Hex> {
  match point_to_hex(click) {
    None => Err("That's not a game space."),
    Some(hex) if hex == current_hex =>
        Err("You are already there! You must click somewhere else"),
    Some(hex) => Ok(hex)
  }
}
`

      + If the pattern matches, but the condition is false, matching continues with the next arm.

  + ### Matching Multiple Possibilities


    + A pattern of the form pat1 | pat2 matches if either subpattern matches:


`rust
let at_end = match chars.peek() {
  Some(&'\r' | &'\n') | None => true,
  _ => false,
};
`

    + Use  `..=`  to match a whole range of values. Range patterns include the begin and end values, so  `'0' ..= '9'`  matches all the ASCII digits:


`rust
match next_char {
  '0'..='9' => self.read_number(),
  'a'..='z' | 'A'..='Z' => self.read_word(),
  ' ' | '\t' | '\n' => self.skip_whitespace(),
  _ => self.handle_punctuation(),
}
`

    + Rust also permits range patterns like  `x..` , which match any value from  `x`  up to the maximum value of the type.

  + ### Binding with @ Patterns


    + Finally, `x @ pattern` matches exactly like the given `pattern`, but on success, instead of creating variables for parts of the matched value, it creates a single variable `x` and moves or copies the whole value into it.
