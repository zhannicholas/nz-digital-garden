---
tags:
- Rust
title: Programming Rust, 2nd Edition - Traits and Generics
categories:
date: 2023-04-04
lastMod: 2023-04-04
---
Rust supports polymorphism with two related features: ***traits*** and ***generics***.

*Traits* are Rust’s take on interfaces or abstract base classes. A generic function or type can be used with values of many different types.

Generics and traits are closely related: generic functions use traits in bounds to spell out what types of arguments they can be applied to.

## Using Traits


  + A trait  is a feature that any given type may or may not support. Most often, a trait represents a capability: something a type can do.


    + A value that implements  `std::io::Write`  can write out bytes.

    + A value that implements  `std::iter::Iterator`  can produce a sequence of values.

    + A value that implements  `std::clone::Clone`  can make clones of itself in memory.

    + A value that implements  `std::fmt::Debug`  can be printed using  `println!()`  with the  `{:?}`  format specifier.

  + There is one unusual rule about trait methods: **the trait itself must be in scope**. Otherwise, all its methods are hidden:


`rust
let mut buf: Vec<u8> = vec![];
buf.write_all(b"hello")?;  // error: no method named `write_all`
`

    + Rust compiler will suggest us adding `use std::io::Write` to fix the error, thus bring trait `Write` into scope.

`rust
use std::io::Write;

let mut buf: Vec<u8> = vec![];
buf.write_all(b"hello");	// OK
`

    + Rust has this rule because you can use traits to add new methods to any type—even standard library types like  `u32`  and  `str` . Third-party crates can do the same thing. Clearly, this could lead to naming conflicts!

    + The reason  `Clone`  and  `Iterator`  methods work without any special imports is that they’re always in scope by default: they’re part of the standard prelude, names that Rust automatically imports into every module. In fact, the prelude is mostly a carefully chosen selection of traits.

  + ### Trait Objects


    + There are two ways of using traits to write polymorphic code in Rust: trait objects and generics.

    + Rust doesn’t permit variables of  trait type such as `dyn Write` :

`rust
use std::io::Write;
let mut buf: Vec<u8> = vec![];
let writer: dyn Write = buf;  // error: `Write` does not have a constant size
`

      + A variable’s size has to be known at compile time, and types that implement  `Write`  can be any size. In Rust, references are explicit:

`rust
let mut buf: Vec<u8> = vec![];
let writer: &mut dyn Write = &mut buf;	// ok
`

      + Only calls through  `&mut dyn Write`  incur the overhead of a dynamic dispatch, also known as a virtual method call, which is indicated by the  `dyn`  keyword in the type.  `dyn Write`  is known as a *trait object*.

    + A reference to a trait type, like  `writer` , is called a *trait object*. Like any other reference, a trait object points to some value, it has a lifetime, and it can be either  `mut`  or shared.

    + {{< logseq/orgTIP >}}What makes a trait object different is that Rust usually doesn’t know the type of the referent at compile time. So a trait object includes a little extra information about the referent’s type.
{{< / logseq/orgTIP >}}

    + #### Trait object layout

      + In memory, a trait object is a fat pointer consisting of a pointer to the value, plus a pointer to a table representing that value’s type. Each trait object therefore takes up two machine words.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052586/files/assets/pr2e_1101.png)

        + In Rust, the *virtual table*, or *vtable*,  is **generated once, at compile time, and shared by all objects of the same type**. Rust automatically uses the *vtable* when you call a method of a trait object, to determine which implementation to call.

        + Rust automatically converts ordinary references into trait objects when needed. However, the opposite is not allowed.

  + ### Generic Functions and Type Parameters


    + Below is a generic funciton:


`rust
fn say_hello<W: Write>(out: &mut W) -> std::io::Result<()> {
    out.write_all(b"hello world\n")?;
    out.flush()
}
`

      + The phrase  `<W: Write>`  is what makes the function generic. This is a *type parameter*. It means that throughout the body of this function,  `W`  stands for some type that implements the  `Write`  trait.

      + {{< logseq/orgTIP >}}- Type parameters are usually single uppercase letters, by convention.  
{{< / logseq/orgTIP >}}

      + Which type  `W`  stands for depends on how the generic function is used. Rust will infer the type  `W`  from the type of the argument. This process is known as *monomorphization*, and the compiler handles it all automatically.

    + Sometimes we need multiple abilities from a type parameter. This is done via the `+` sign:


`rust
use std::hash::Hash;
use std::fmt::Debug;

fn top_ten<T: Debug + Hash + Eq>(values: &Vec<T>) { ... }
`

      + The type `T` must supports `Debug`, `Hash` and `Eq` traits.

    + Generic functions can have multiple type parameters:


`rust
/// Run a query on a large, partitioned data set.
/// See <http://research.google.com/archive/mapreduce.html>.
fn run_query<M: Mapper + Serialize, R: Reducer + Serialize>(
  data: &DataSet, map: M, reduce: R) -> Results
{ ... }
`

      + As this example shows, the bounds can get to be so long that they are hard on the eyes. Rust provides an alternative syntax using the keyword  `where` :

`rust
fn run_query<M, R>(data: &DataSet, map: M, reduce: R) -> Results
  where M: Mapper + Serialize,
        R: Reducer + Serialize
{ ... }
`

      + The type parameters  `M`  and  `R`  are still declared up front, but the bounds are moved to separate lines. This kind of  `where`  clause is also allowed on generic structs, enums, type aliases, and methods—anywhere bounds are permitted.

    + A generic function can have both lifetime parameters and type parameters. Lifetime parameters come first:


`rust
/// Return a reference to the point in `candidates` that's
/// closest to the `target` point.
fn nearest<'t, 'c, P>(target: &'t P, candidates: &'c [P]) -> &'c P
  where P: MeasureDistance
{
  ...
}
`

      + {{< logseq/orgNOTE >}}Lifetimes never have any impact on machine code. 
{{< / logseq/orgNOTE >}}

  + ### Which to Use: trait objects or generics?

    + Use trait objects

      + Trait objects are the right choice whenever you need a collection of values of mixed types, all together.

      + Another possible reason to use trait objects is to reduce the total amount of compiled code. Rust may have to compile a generic function many times, once for each type it’s used with.

    + Outside a collection of values of mixed types and low-resource environments, generic have three important advantages over trait objects, with the result that in Rust, generics are the most common choice.

      + The first advantage is **speed**. Note the absence of the  `dyn`  keyword in generic function signatures. Because you specify the types at compile time, either explicitly or through type inference, the compiler knows exactly which  method to call. The  `dyn`  keyword isn’t used because there are no trait objects—and thus no dynamic dispatch—involved.

      + The second advantage of generics is that **not every trait can support trait objects**. Traits support several features, such as associated functions, that work only with generics: they rule out trait objects entirely.

      + The third advantage of generics is that **it’s easy to bound a generic type parameter with several traits at once**.

## Defining and Implementing Traits


  + Defining a trait is simple. Give it a name and list the type signatures of the trait methods.


`rust
/// A trait for characters, items, and scenery -
/// anything in the game world that's visible on screen.
trait Visible {
  /// Render this object on the given canvas.
  fn draw(&self, canvas: &mut Canvas);
- /// Return true if clicking at (x, y) should
  /// select this object.
  fn hit_test(&self, x: i32, y: i32) -> bool;
}
`

  + To implement a trait, use the syntax  `impl TraitName for Type` :


`rust
impl Visible for Broom {
  fn draw(&self, canvas: &mut Canvas) {
      for y in self.y - self.height - 1 .. self.y {
          canvas.write_at(self.x, y, '|');
      }
      canvas.write_at(self.x, self.y, 'M');
  }
  fn hit_test(&self, x: i32, y: i32) -> bool {
      self.x == x
      && self.y - self.height - 1 <= y
      && y <= self.y
  }
}
`

    + Note that this  `impl`  contains an implementation for each method of the  `Visible`  trait, and nothing else. Everything defined in a trait  `impl`  must actually be a feature of the trait; if we want to add a helper method in support of `Broom::draw()`, we would have to define it in a seperate `impl` block.

  + A trait can have default implementation for its method.

  + ### Traits and Other People's Types


    + Rust lets you **implement any trait on any type**, as long as either the trait or the type is introduced in the current crate.

      + This means that any time you want to add a method to any type, you can use a trait to do it.


`rust
trait IsEmoji {
  fn is_emoji(&self) -> bool;
}
/// Implement IsEmoji for the built-in character type.
impl IsEmoji for char {
  fn is_emoji(&self) -> bool {
      ...
  }
}
assert_eq!('$'.is_emoji(), false);
`

        + Like any other trait method, this new  `is_emoji`  method is only visible when  `IsEmoji`  is in scope.

        + The sole purpose of this particular trait is to add a method to an existing type,  `char` . This is called an ***extension trait***.

    + When you implement a trait, either the trait or the type must be new in the current crate. This is called the ***orphan rule***. It helps Rust ensure that trait implementations are unique.

  + ### Self in Traits


    + A trait can use the keyword  `Self`  as a type. The standard  `Clone`  trait, for example, looks like this (slightly simplified):

`rust
pub trait Clone {
  fn clone(&self) -> Self;
  ...
}
`

      + {{< logseq/orgCAUTION >}}Using  `Self`  as the return type here means that the type of  `x.clone()`  is the same as the type of  `x` , whatever that might be. If  `x`  is a  `String` , then the type of  `x.clone()`  is  `String` —not  `dyn Clone`  or any other cloneable type.
{{< / logseq/orgCAUTION >}}

    + Trait objects are really intended for the simplest kinds of traits. The more advanced features of traits are useful, but they can’t coexist with trait objects because **with trait objects, you lose the type information Rust needs to type-check your program**.

  + ### Subtraits


    + We can declare that a trait is an extension of another trait:


`rust
/// Someone in the game world, either the player or some other
/// pixie, gargoyle, squirrel, ogre, etc.
trait Creature: Visible {
  fn position(&self) -> (i32, i32);
  fn facing(&self) -> Direction;
  ...
}
`

    + The phrase  `trait Creature: Visible`  means that all creatures are visible. Every type that implements  `Creature`  must also implement the  `Visible`  trait:


`rust
impl Visible for Broom {
  ...
}
impl Creature for Broom {
  ...
}
`

    + We can implement the two traits in either order, but it’s an error to implement  `Creature`  for a type without also implementing  `Visible` . Here, we say that  `Creature`  is a *subtrait* of  `Visible` , and that  `Visible`  is  `Creature` ’s *supertrait*.

    + In Rust, a subtrait does not inherit the associated items of its supertrait; each trait still needs to be in scope if you want to call its methods.

  + ### Type-Associated Functions


    + Traits can include type-associated functions, Rust's analog to static method.

    + Type-associated functions are called using `::` syntax.

## Traits That Define Relationships Between Types

  + Traits can be used in situations where there are multiple types that have to work together.

  + ### Associated Types (or How Iterators Work)


    + Rust has a standard  `Iterator`  trait, defined like this:

`rust
pub trait Iterator {
  type Item;
  fn next(&mut self) -> Option<Self::Item>;
  ...
}
`

      + The first feature of this trait,  `type Item;` , is an *associated type*. Each type that implements  `Iterator`  must specify what type of item it produces.

      + The second feature, the  `next()`  method, uses the associated type in its return value. The type is written as  `Self::Item` , not just plain  `Item` , because  `Item`  is a feature of each type of iterator, not a standalone type.

  + ### Generic Traits (or How Operator Overloading Works)


    + Multiplication in Rust uses this trait:


`rust
/// std::ops::Mul, the trait for types that support `*`.
pub trait Mul<RHS> {
  /// The resulting type after applying the `*` operator
  type Output;

  /// The method for the `*` operator
  fn mul(self, rhs: RHS) -> Self::Output;
}
`

      + Mul is a generic trait. The type parameter, RHS, is short for *righthand side*.

      + The type parameter here means the same thing that it means on a struct or function:  `Mul`  is a generic trait, and its instances  `Mul<f64>` ,  `Mul<String>` ,  `Mul<Size>` , etc., are all different traits.

      + A single type—say,  `WindowSize` —can implement both  `Mul<f64>`  and  `Mul<i32>` , and many more. You would then be able to multiply a  `WindowSize`  by many other types. Each implementation would have its own associated  `Output`  type.

      + Generic traits get a special dispensation when it comes to the orphan rule: you can implement a foreign trait for a foreign type, so long as one of the trait’s type parameters is a type defined in the current crate.

  + ### impl Traits


    + `impl Trait` allows us to “erase” the type of a return value, specifying only the trait or traits it implements, without dynamic dispatch or a heap allocation.

    + `impl Trait`  is a form of static dispatch, so the compiler has to know the type being returned from the function at compile time in order to allocate the right amount of space on the stack and correctly access fields and methods on that type.

  + ### Associated Consts


    + Like structs and enums, traits can have associated constants. You can declare a trait with an associated constant using the same syntax as for a struct or enum:

`rust
trait Greet {
  const GREETING: &'static str = "Hello";
  fn greet(&self) -> String;
}
`

    + Associated consts in traits have a special power, though. Like associated types and functions, you can declare them but not give them a value:

`rust
trait Float {
  const ZERO: Self;
  const ONE: Self;
}
`

      + Then, implementors of the trait can define these values.

    + {{< logseq/orgCAUTION >}}Note that associated constants can’t be used with trait objects, since the compiler relies on type information about the implementation in order to pick the right value at compile time.
{{< / logseq/orgCAUTION >}}



  + 
