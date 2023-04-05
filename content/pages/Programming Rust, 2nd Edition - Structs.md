---
tags:
- Rust
title: Programming Rust, 2nd Edition - Structs
categories:
date: 2023-04-04
lastMod: 2023-04-04
---


Rust structs, sometimes called *structures*, assemble several values of assorted types together into a single value so you can deal with them as a unit. Given a struct, you can read and modify its individual components. And a struct can have methods associated with it that operate on its components.

Rust has three kinds of struct types, ***named-field***, ***tuple-like***, and ***unit-like***, which differ in how you refer to their components:


  + a named-field struct gives a name to each component

  + a tuple-like struct identifies them by the order in which they appear.

  + Unit-like structs have no components at all.

## Named-Field Structs

  + The definition of a named-field struct type looks like this:


`rust
/// A rectangle of eight-bit grayscale pixels.
struct GrayscaleMap {
  pixels: Vec<u8>,
  size: (usize, usize)
}
`

  + The convention in Rust is for all types, structs included, to have names that capitalize the first letter of each word , a convention called *CamelCase* (or *PascalCase*). Fields and methods are lowercase, with words separated by underscores. This is called *snake_case*.

  + A struct expression starts with the type name and lists the name and value of each field, all enclosed in curly braces.

  + To access a struct’s fields, use the familiar  `.`  operator.

  + Like all other items, structs are private by default, visible only in the module where they’re declared and its submodules. You can make a struct visible outside its module by prefixing its definition with  `pub` . The same goes for each of its fields, which are also private by default.

  + Even if a struct is declared  `pub` , its fields can be private.

  + Other modules can use this struct and any public associated functions it might have, but can’t access the private fields by name or use struct expressions to create new struct values. That is, **creating a struct value requires all the struct’s fields to be visible**.

  + When creating a named-field struct value, you can use another struct of the same type to supply values for fields you omit. In a struct expression, if the named fields are followed by  `.. EXPR` , then any fields not mentioned take their values from  `EXPR` , which must be another value of the same struct type.

## Tuple-Like Structs

  + *Tuple-like struct* resembles a tuple:

`rust
struct Bounds(usize, usize);
`

    + You construct a value of this type much as you would construct a tuple, except that you must include the struct name.

  + The values held by a tuple-like struct are called *elements*, just as the values of a tuple are. You can access them by indxes.

  + {{< logseq/orgTIP >}}At the most fundamental level, named-field and tuple-like structs are very similar. The choice of which to use comes down to questions of legibility, ambiguity, and brevity. If you will use the . operator to get at a value’s components much at all, identifying fields by name provides the reader more information and is probably more robust against typos. If you will usually use pattern matching to find the elements, tuple-like structs can work nicely.
{{< / logseq/orgTIP >}}

## Unit-Like Structs

  + Unit-like Structs declare a struct type with no elements at all:

`rust
struct Onesuch;
`

  + A value of such a type occupies no memory, much like the unit type  `()`.

## Defining Methods with impl

  + You can define methods on your own struct types. Rather than appearing inside the struct definition, as in C++ or Java, Rust methods appear in a separate `impl` block.

  + An  `impl`  block is simply a collection of  `fn`  definitions, each of which becomes a method on the struct type named at the top of the block.

  + Functions defined in an  `impl`  block are called *associated functions*, since they’re associated with a specific type. The opposite of an associated function is a *free function*, one that is not defined as an  `impl`  block’s item.

  + Rust passes a method the value it’s being called on as its first argument, which must have the special name  `self` . Since  `self` ’s type is obviously the one named at the top of the  `impl`  block, or a reference to that, Rust lets you omit the type,

    + {{< logseq/orgNOTE >}}a Rust method must explicitly use `self` to refer to the value it was called on.
{{< / logseq/orgNOTE >}}

  + ### Passing Self as a Box, Rc, or Arc


    + > Sometimes, taking  `self`  by value like this, or even by reference, isn’t enough, so Rust also lets you pass  `self`  via smart pointer types.

    + A method’s  `self`  argument can also be a  `Box<Self>` ,  `Rc<Self>` , or  `Arc<Self>` . Such a method can only be called on a value of the given pointer type. Calling the method passes ownership of the pointer to it.

    + For method calls and field access, Rust automatically borrows a reference from pointer types like  `Box` ,  `Rc` , and  `Arc` , so  `&self`  and  `&mut self`  are almost always the right thing in a method signature, along with the occasional  `self` .

    + But if it does come to pass that some method needs ownership of a pointer to  `Self` , and its callers have such a pointer handy, Rust will let you pass it as the method’s  `self`  argument. To do so, you must spell out the type of  `self` , as if it were an ordinary parameter:

`rust
impl Node {
  fn append_to(self: Rc<Self>, parent: &mut Node) {
      parent.children.push(self);
  }
}
`

  + ### Type-Associated Functions

    + An  `impl`  block for a given type can also define functions that don’t take  `self`  as an argument at all. These are still associated functions, since they’re in an  `impl`  block, but they’re not methods, since they don’t take a  `self`  argument. To distinguish them from methods, we call them ***type-associated functions***.

    + type-associated functions are often used to provide constructor functions, like this:


`rust
impl Queue {
  pub fn new() -> Queue {
      Queue { older: Vec::new(), younger: Vec::new() }
  }
}
`

      + To use this function, we refer to it as  `Queue::new` : the type name, a double colon, and then the function name.

    + Although you can have many separate  `impl`  blocks for a single type, they must all be in the same crate that defines that type.

    + {{< logseq/orgNOTE >}}The fact that any type can have methods is one reason Rust doesn’t use the term object much, preferring to call everything a value.
{{< / logseq/orgNOTE >}}

## Generic Structs

  + Rust structs can be *generic*, meaning that their definition is a template into which you can plug whatever types you like.

  + In generic struct definitions, the type names used in  `<` angle brackets `>`  are called *type parameters*.

  + Every  `impl`  block, generic or not, defines the special type parameter  `Self`  (note the  `CamelCase`  name) to be whatever type we’re adding methods to.

## Interior Mutability

  + Interior mutability is that mutable data inside an otherwise immutable value.

  + Rust offers several flavors of interior mutability. The two most straightforward types:  `Cell<T>`  and  `RefCell<T>` , both in the  `std::cell`  module.

  + ### Cell<T>

    + A Cell<T> is a struct that contains a single private value of type T. The only special thing about a Cell is that you can get and set the field even if you don’t have mut access to the Cell itself:

      + `Cell::new(value)` : Creates a new  `Cell` , moving the given  `value`  into it.

      + `cell.get()`: Returns a copy of the value in the  `cell` .

      + `cell.set(value)` :Stores the given  `value`  in the  `cell` , dropping the previously stored value.

        + This method takes  `self`  as a non- `mut`  reference:

`rust
fn set(&self, value: T)    // note: not `&mut self`
`

    + `Cell`  does *not* let you call  `mut`  methods on a shared value. The  `.get()`  method returns a copy of the value in the cell, so it works only if  `T`  implements the  `Copy`  trait.

  + ### RefCell<T>

    + Like  `Cell<T>` ,  `RefCell<T>`  is a generic type that contains a single value of type  `T` . Unlike  `Cell` ,  `RefCell`  supports borrowing references to its  `T`  value:

      + `RefCell::new(value)` : Creates a new  `RefCell` , moving  `value`  into it.

      + `ref_cell.borrow()` : Returns a  `Ref<T>` , which is essentially just a shared reference to the value stored in  `ref_cell` .

        + This method panics if the value is already mutably borrowed; see details to follow.

      + `ref_cell.borrow_mut()` : Returns a  `RefMut<T>` , essentially a mutable reference to the value in  `ref_cell` .

        + This method panics if the value is already borrowed; see details to follow.

      + `ref_cell.try_borrow()` ,  `ref_cell.try_borrow_mut()` : Work just like  `borrow()`  and  `borrow_mut()` , but return a  `Result` . Instead of panicking if the value is already mutably borrowed, they return an  `Err`  value.
