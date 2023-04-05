---
tags:
- Rust
title: Programming Rust, 2nd Edition - Ownership and Moving
categories:
date: 2023-04-04
lastMod: 2023-04-04
---


When it comes to managing memory, there are two characteristics we’d like from our programing languages:


  + We ʼ d like memory to be freed promptly, at a time of our choosing. This gives us control over the program’s memory consumption.

  + We never want to use a pointer to an object after it’s been freed. This would be undefined behavior, leading to crashes and security holes.

## Ownership

  + In Rust, the concept of ownership is built into the language itself and enforced by compile-time checks. **Every value has a single owner that determines its lifetime.** When the owner is freed—*dropped*, in Rust terminology—the owned value is dropped too.

  + **A variable owns its value.** When control leaves the block in which the variable is declared, the variable is dropped, so its value is dropped along with it.

`rust
fn test() {
  let a = "123";	// allocated here
}	// dropped here
`

  + Rust’s  `Box`  type serves as another example of ownership. A  `Box<T>`  is a pointer to a value of type  `T`  stored on the heap. Calling  `Box::new(v)`  allocates some heap space, moves the value  `v`  into it, and returns a  `Box`  pointing to the heap space. Since a  `Box`  owns the space it points to, when the  `Box`  is dropped, it frees the space too.

  + Just as variables own their values, structs own their fields, and tuples, arrays, and vectors own their elements.

  + The owners and their owned values form ***trees***: your owner is your parent, and the values you own are your children. And at the ultimate root of each tree is a variable; when that variable goes out of scope, the entire tree goes with it.

  + Rust programs don’t usually explicitly drop values.

    + {{< logseq/orgTIP >}}The way to drop a value in Rust is to remove it from the ownership tree somehow: by leaving the scope of a variable, or deleting an element from a vector, or something of that sort. At that point, Rust ensures the value is properly dropped, along with everything it owns.
{{< / logseq/orgTIP >}}

  + Rust’s safety guarantees are possible exactly because the relationships it may encounter in your code are more tractable.

  + Rust extends ownership in several ways:

    + You  can  move  values  from  one  owner  to  another. This allows you to  build,  rearrange, and tear down the tree.

    + Very simple types like integers, floating-point numbers, and characters are excused from the ownership rules. These are called  `Copy`  types.

    + The standard library provides the reference-counted pointer types  `Rc`  and  `Arc` , which allow values to have multiple owners, under some restrictions.

    + You can “borrow a reference” to a value; references are non-owning pointers, with limited lifetimes.

## Moves

  + In Rust, for most types, operations like assigning a value to a variable, passing it to a function, or returning it from a function don’t copy the value: they ***move***** it. The source relinquishes ownership of the value to the destination and becomes uninitialized; the destination now controls the value’s lifetime.** Rust programs build up and tear down complex structures one value at a time, one move at a time.

`rust
let s = vec!["udon".to_string(), "ramen".to_string(), "soba".to_string()];  // s owns the vector
let t = s;    // the ownership of the vector moves from s to t, s is uninitialized now
let u = s;   // since s is uninitialized now, this line will failed to compile because of Rust's prohibitions of using uninitialized values.
`

  + Assigning to a variable is slightly different, in that if you move a value into a variable that was already initialized, Rust drops the variable’s prior value.

`rust
let mut s = "Govinda".to_string();
s = "Siddhartha".to_string(); // value "Govinda" dropped here
`

  + Rust applies move semantics to almost any use of a value.

    + Passing arguments to functions moves ownership to the function’s parameters;

    + returning a value from a function moves ownership to the caller.

    + Building a tuple moves the values into the tuple.

    + And so on.

  + Moving values may sound inefficient, but there are two things to keep in mind.

    + First, the moves always apply to the **value proper**, not the heap storage they own. For vectors and strings, the value proper is the three-word header alone; the potentially large element arrays and text buffers sit where they are in the heap.

    + Second, the Rust compiler’s code generation is good at “seeing through” all these moves; in practice, the machine code often stores the value directly where it belongs.

  + The general principle of moving is that, if it’s possible for a variable to have had its value moved away and it hasn’t definitely been given a new value since, it’s considered uninitialized.

    + For example, if a variable still has a value after evaluating an  `if`  expression’s condition, then we can use it in both branches:


`rust
let x = vec![10, 20, 30];
if c {
  f(x); // ... ok to move from x here
} else {
  g(x); // ... and ok to also move from x here
}
h(x); // bad: x is uninitialized here if either path uses it
`

    + For similar reasons, moving from a variable in a loop is forbidden:


`rust
let x = vec![10, 20, 30];
while f() {
  g(x); // bad: x would be moved in first iteration,
        // uninitialized in second
}
`

    + That is, unless we’ve definitely given it a new value by the next iteration:


`rust
let mut x = vec![10, 20, 30];
while f() {
  g(x);           // move from x
  x = h();        // give x a fresh value
}
e(x);
`

## Copy Types: The Exception to Moves

  + In Rust, *most* types are moved, but *`Copy` types* are exceptions. Assigning a value of a  `Copy`  type copies the value, rather than moving it. The source of the assignment remains initialized and usable, with the same value it had before. Passing  `Copy`  types to functions and constructors behaves similarly.

  + The standard  `Copy`  types include all the machine integer and floating-point numeric types, the  `char`  and  `bool`  types, and a few others. A tuple or fixed-size array of  `Copy`  types is itself a  `Copy`  type.

    + {{< logseq/orgTIP >}}Only types for which a simple bit-for-bit copy suffices can be `Copy`.
As a rule of thumb, any type that needs to do something special when a value is dropped cannot be `Copy`.
{{< / logseq/orgTIP >}}

  + In Rust, every move is a byte-for-byte, shallow copy that leaves the source uninitialized.

  + One of Rust’s principles is that costs should be apparent to the programmer. Basic operations must remain simple. Potentially expensive operations should be explicit.

## Rc and Arc: Shared Ownership

  + Although most values have unique owners in typical Rust code, in some cases it’s difficult to find every value a single owner that has the lifetime you need; you’d like the value to simply live until everyone’s done using it. For these cases, Rust provides the reference-counted pointer types  `Rc`  and  `Arc` .

  + The  `Rc`  and  `Arc`  types are very similar; the only difference between them is that an  `Arc`  is safe to share between threads directly—the name  `Arc`  is short for *atomic reference count*—whereas a plain  `Rc`  uses faster non-thread-safe code to update its reference count.

  + For any type  `T` , an  `Rc<T>`  value is a pointer to a heap-allocated  `T`  that has had a reference count affixed to it. Cloning an  `Rc<T>`  value does not copy the  `T` ; instead, it simply creates another pointer to it and increments the reference count.

`rust
use std::rc::Rc;

// Rust can infer all these types; written out for clarity
let s: Rc<String> = Rc::new("shirataki".to_string());	// rc = 1
let t: Rc<String> = s.clone();	// rc = 2
let u: Rc<String> = s.clone();	// rc = 3

assert!(s.contains("shira"));	// rc = 2
assert_eq!(t.find("taki"), Some(5));	// rc = 1
println!("{} are quite chewy, almost bouncy, but lack flavor", u);	// rc = 0 and the String is dropped
`

  + A value owned by an  `Rc`  pointer is immutable.

  + {{< logseq/orgCAUTION >}}One well-known problem with using reference counts to manage memory is that, if there are ever two reference-counted values that point to each other, each will hold the other’s reference count above zero, so the values will never be freed
{{< / logseq/orgCAUTION >}}
