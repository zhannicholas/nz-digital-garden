---
tags:
- Rust
title: Programming Rust, 2nd Edition - References
categories:
date: 2023-04-04
lastMod: 2023-04-04
---


Rust has non-owning pointer types called *references*, which have no effect on their referents’ lifetimes.

You must make it apparent in your code that no reference can possibly outlive the value it points to. To emphasize this, Rust refers to creating a reference to some value as *borrowing* the value: **what you have borrowed, you must eventually return to its owner**.

## Reference to Values
  + A reference lets you **access a value without affecting its ownership**. References come in two kinds:

    + A ***shared reference*** lets you read but not modify its referent. However, you can have as many shared references to a particular value at a time as you like. The expression  `&e`  yields a shared reference to  `e` ’s value; if  `e`  has the type  `T` , then  `&e`  has the type  `&T` , pronounced “ref  `T` .” Shared references are  `Copy` .

    + If you have a ***mutable reference*** to a value, you may both read and modify the value. However, you may not have any other references of any sort to that value active at the same time. The expression  `&mut e`  yields a mutable reference to  `e` ’s value; you write its type as  `&mut T` , which is pronounced “ref mute  `T` .” Mutable references are not  `Copy` .

  + {{< logseq/orgTIP >}}You can think of the distinction between shared and mutable references as a way to enforce a **multiple readers or single writer** rule at compile time. 
{{< / logseq/orgTIP >}}

  + When we pass a value to a function in a way that moves ownership of the value to the function, we say that we have passed it *by value*.

  + If we instead pass the function a reference to the value, we say that we have passed the value *by reference*, the function just **borrowed** the value from it's owner. And that's why we said references are non-owning pointers.

## Working with References
  + In Rust, references are created explicitly with the  `&`  operator, and dereferenced explicitly with the  `*`  operator:


`rust
let x = 10;
let r = &x;             // &x is a shared reference to x
assert!(*r == 10);      // explicitly dereference r
`

  + Since references are so widely used in Rust, the  `.`  operator implicitly dereferences its left operand, if needed:


`rust
struct Anime { name: &'static str, bechdel_pass: bool }
let aria = Anime { name: "Aria: The Animation", bechdel_pass: true };
let anime_ref = &aria;
assert_eq!(anime_ref.name, "Aria: The Animation");
- // Equivalent to the above, but with the dereference written out:
assert_eq!((*anime_ref).name, "Aria: The Animation");
`

  + The  `.`  operator can also implicitly borrow a reference to its left operand, if needed for a method call.


    + For example,  `Vec` ’s  `sort`  method takes a mutable reference to the vector, so these two calls are equivalent:

`rust
let mut v = vec![1973, 1968];
v.sort();           // implicitly borrows a mutable reference to v
(&mut v).sort();    // equivalent, but more verbose
`

  + Rust permits references to references:


`rust
struct Point { x: i32, y: i32 }
let point = Point { x: 1000, y: 729 };
let r: &Point = &point;
let rr: &&Point = &r;
let rrr: &&&Point = &rr;
`

    + (We’ve written out the reference types for clarity, but you could omit them; there’s nothing here Rust can’t infer for itself.) The  `.`  operator follows as many references as it takes to find its target:

`rust
assert_eq!(rrr.y, 729);
`

  + Rust references are never null. There is no default initial value for a reference (you can’t use any variable until it’s been initialized, regardless of its type) and Rust won’t convert integers to references (outside of  `unsafe`  code), so you can’t convert zero into a reference.

  + Rust also includes two kinds of ***fat pointers***, two-word values carrying the address of some value, along with some further information necessary to put the value to use.


    + A reference to a slice is a fat pointer, carrying the starting address of the slice and its length.

    + Rust’s other kind of fat pointer is a *trait object*, a reference to a value that implements a certain trait. A trait object carries a value’s address and a pointer to the trait’s implementation appropriate to that value, for invoking the trait’s methods.

## Reference Safety

  + Rust tries to assign each reference type in your program a ***lifetime*** that meets the constraints imposed by how it is used. A lifetime is some stretch of your program for which a reference could be safe to use: a statement, an expression, the scope of some variable, or the like.

  + {{< logseq/orgNOTE >}}Lifetimes are entirely figments of Rust’s compile-time imagination. At run time, a reference is nothing but an address; its lifetime is part of its type and has no run-time representation.
{{< / logseq/orgNOTE >}}

  + The variable’s lifetime must *contain* or *enclose* that of the reference borrowed from it.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052586/files/assets/pr2e_0503.png)

  + If you store a reference in a variable  `r` , the reference’s (`&r`'s) type must be good for the entire lifetime of the variable (`r`), from its initialization until its last use. If the reference can’t live at least as long as the variable does, then at some point  `r`  will be a dangling pointer. We say that the reference’s lifetime must contain or enclose the variable’s.

    + > Here, `&r` is a reference, and `r` is the reference's (`&r`) referent.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052586/files/assets/pr2e_0504.png)

  + Receive references as functions arguments

    + `fn f(p: i32) {...}` is the simplified version of `fn f<'a>(p: &'a i32) {...}`.

      + Here, the lifetime `'a` (pronounced “tick A”) is a lifetime parameter of `f`. You can read `<'a>` as “for any lifetime `'a`” so when we write `fn f<'a>(p: &'a i32)`, we’re defining a function that takes a reference to an `i32` with any given lifetime `'a`.

  + When a function takes a single reference as an argument and returns a single reference, Rust assumes that the two must have the same lifetime.

    + For example, `fn smallest(v: &[i32]) -> &i32 {...}` is the same as `fn smallest<'a>(v: &'a [i32]) -> &'a i32 {...}`.

  + Lifetimes in function signatures let Rust assess the relationships between the references you pass to the function and those the function returns, and they ensure they’re being used safely.

  + Every type in Rust has a lifetime, including  `i32`  and  `String` . Most are simply  `'static` , meaning that values of those types can live for as long as you like.

## Sharing Versus Mutation

  + Rust’s rules for mutation and sharing:

    + **Shared access is read-only access.**

      + Values borrowed by shared references are read-only. Across the lifetime of a shared reference, neither its referent, nor anything reachable from that referent, can be changed *by anything*. There exist no live mutable references to anything in that structure, its owner is held read-only, and so on. It’s really frozen.

      + **Mutable access is exclusive access.**

        + A value borrowed by a mutable reference is reachable exclusively via that reference. Across the lifetime of a mutable reference, there is no other usable path to its referent or to any value reachable from there. The only references whose lifetimes may overlap with a mutable reference are those you borrow from the mutable reference itself.

    + Each kind of reference affects what we can do with the values along the owning path to the referent, and the values reachable from the referent.

      + Borrowing a reference affects what you can do with other values in the same ownership tree.


![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781492052586/files/assets/pr2e_0509.png)

        + {{< logseq/orgCAUTION >}}Note that in both cases, the path of ownership leading to the referent cannot be changed for the reference’s lifetime. For a shared borrow, the path is read-only; for a mutable borrow, it’s completely inaccessible. So there’s no way for the program to do anything that will invalidate the reference.
{{< / logseq/orgCAUTION >}}

    + And Rust applies these rules everywhere: if we borrow, say, a shared reference to a key in a  `HashMap` , we can’t borrow a mutable reference to the  `HashMap`  until the shared reference’s lifetime ends.
