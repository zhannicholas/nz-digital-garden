---
tags:
- Rust
title: Programming Rust, 2nd Edition - Operator Overloading
categories:
date: 2023-04-04
lastMod: 2023-04-04
---


Rust supports **Operator Overloading**, what we need to to is implementing a few traits according to our requirements. Summary of traits for operator overloading:


  + | Category | Trait | Operator(Expression) | Equivalent expression | 
| ---- | ---- | ---- | ---- |
| Unary operators |  `std::ops::Neg`  |  `-x`  | `x.neg()` |
| |  `std::ops::Not`  |  `!x`  | `x.not()` |
| Arithmetic operators |  `std::ops::Add`  |  `x + y`  | `x.add(y)` |
| |  `std::ops::Sub`  |  `x - y`  | `x.sub(y)` |
| |  `std::ops::Mul`  |  `x * y`  | `x.mul(y)` |
| |  `std::ops::Div`  |  `x / y`  | `x.div(y)` |
| |  `std::ops::Rem`  |  `x % y`  | `x.rem(y)` |
| Bitwise operators |  `std::ops::BitAnd`  |  `x & y`  | `x.bitand(y)` |
| |  `std::ops::BitOr`  |  <code>x &#124; y</code>  | `x.bitor(y)` |
| |  `std::ops::BitXor`  |  `x ^ y`  | `x.bitxor(y)` |
| |  `std::ops::Shl`  |  `x << y`  | `x.shl(y)` |
| |  `std::ops::Shr`  |  `x >> y`  | `x.shr(y)` |
| Compound assignment arithmetic operators |  `std::ops::AddAssign`  |  `x += y`  | `x.add_assign(y)` |
| |  `std::ops::SubAssign`  |  `x -= y`  | `x.sub_assign(y)` |
| |  `std::ops::MulAssign`  |  `x *= y`  | `x.mul_assign(y)` |
| |  `std::ops::DivAssign`  |  `x /= y`  | `x.div_assign(y)` |
| |  `std::ops::RemAssign`  |  `x %= y`  | `x.rem_assign(y)` |
| Compound assignment bitwise operators |  `std::ops::BitAndAssign`  |  `x &= y`  | `x.bitand_assign(y)` |
| |  `std::ops::BitOrAssign`  |  <code>x &#124;= y</code>  | `x.bitor_assign(y)` |
| |  `std::ops::BitXorAssign`  |  `x ^= y`  | `x.bitxor_assign(y)` |
| |  `std::ops::ShlAssign`  |  `x <<= y`  | `x.shl_assign(y)` |
| |  `std::ops::ShrAssign`  |  `x >>= y`  | `x.shr_assign(y)` |
| Comparison |  `std::cmp::PartialEq`  |  `x == y` ,  `x != y`  | `x.not_assign(y)` |
| |  `std::cmp::PartialOrd`  |  `x < y` ,   `x <= y` ,   `x > y` ,   `x >= y`  | `x.lt(y)`, `x.le(y)`, `x.gt(y)`, `x.ge(y)` |
| Indexing |  `std::ops::Index`  |  `x[y]` ,   `&x[y]`  | |
| |  `std::ops::IndexMut`  |  `x[y] = z` ,   `&mut x[y]`  | |

## Arithmetic and Bitwise Operators

  + In Rust, the expression `a + b` is actually shorthand for `a.add(b)`, a call to the add method of the standard library’s `std::ops::Add` trait.

  + Here’s the definition of  `std::ops::Add` :

`rust
trait Add<Rhs = Self> {
  type Output;
  fn add(self, rhs: Rhs) -> Self::Output;
}
`

    + In other words, the trait `Add<T>` is the ability to add a `T` value to yourself.

  + ### Unary Operators

    + All of Rust’s signed numeric types implement  `std::ops::Neg` , for the unary negation operator  `-` ; the integer types and  `bool`  implement  `std::ops::Not` , for the unary complement operator  `!` . There are also implementations for references to those types.

    + Note that `!` complements `bool` values and performs a bitwise complement (that is, flips the bits) when applied to integers; it plays the role of both the `!` and `~` operators from C and C++.

  + ### Compound Assignment Operators

    + A compound assignment expression is one like  `x += y`  or  `x &= y` : it takes two operands, performs some operation on them like addition or a bitwise AND, and stores the result back in the left operand. In Rust, the value of a compound assignment expression is always (), never the value stored.

    + Many languages have operators like these and usually define them as shorthand for expressions like  `x = x + y`  or  `x = x & y` . However, Rust doesn’t take that approach. Instead,  `x += y`  is shorthand for the method call  `x.add_assign(y)` , where  `add_assign`  is the sole method of the  `std::ops::AddAssign`  trait:

`rust
trait AddAssign<Rhs = Self> {
  fn add_assign(&mut self, rhs: Rhs);
}
`

## Equivalence Comparisions

  + Rust’s equality operators,  `==`  and  `!=` , are shorthand for calls to the  `std::cmp::PartialEq`  trait’s  `eq`  and  `ne`  methods:

`rust
assert_eq!(x == y, x.eq(&y));
assert_eq!(x != y, x.ne(&y));
`

  + Here’s the definition of  `std::cmp::PartialEq` :

`rust
trait PartialEq<Rhs = Self>
where
  Rhs: ?Sized,
{
  fn eq(&self, other: &Rhs) -> bool;
  fn ne(&self, other: &Rhs) -> bool {
      !self.eq(other)
  }
}
`

    + Since the  `ne`  method has a default definition, you only need to define  `eq`  to implement the  `PartialEq`  trait.

  + Unlike the arithmetic and bitwise traits, which take their operands by value, `PartialEq` takes its operands by reference.

  + **Why is this trait called  `PartialEq`** ? The traditional mathematical definition of an *equivalence relation*, of which equality is one instance, imposes three requirements. For any values  `x`  and  `y` :

    + If  `x == y`  is true, then  `y == x`  must be true as well. In other words, swapping the two sides of an equality comparison doesn’t affect the result.

    + If  `x == y`  and  `y == z` , then it must be the case that  `x == z` . Given any chain of values, each equal to the next, each value in the chain is directly equal to every other. Equality is contagious.

    + It must always be true that  `x == x` .

  + That last requirement might seem too obvious to be worth stating, but this is exactly where things go awry. Rust’s  `f32`  and  `f64`  are IEEE standard floating-point values. According to that standard, expressions like `0.0/0.0` and others with no appropriate value must produce special *not-a-number* values, usually referred to as `NaN` values. The standard further requires that a `NaN` value be treated as unequal to every other value—including itself. For example, the standard requires all the following behaviors:

`rust
assert!(f64::is_nan(0.0 / 0.0));
assert_eq!(0.0 / 0.0 == 0.0 / 0.0, false);
assert_eq!(0.0 / 0.0 != 0.0 / 0.0, true);
`

  + Furthermore, any ordered comparison with a `NaN` value must return false:

`rust
assert_eq!(0.0 / 0.0 < 0.0 / 0.0, false);
assert_eq!(0.0 / 0.0 > 0.0 / 0.0, false);
assert_eq!(0.0 / 0.0 <= 0.0 / 0.0, false);
assert_eq!(0.0 / 0.0 >= 0.0 / 0.0, false);
`

  + So while Rust’s  `==`  operator meets the first two requirements for equivalence relations, it clearly doesn’t meet the third when used on IEEE floating-point values. This is called a ***partial equivalence relation***, so Rust uses the name  `PartialEq`  for the  `==`  operator’s built-in trait. If you write generic code with type parameters known only to be  `PartialEq` , you may assume the first two requirements hold, but you should not assume that values always equal themselves.

  + That can be a bit counterintuitive and may lead to bugs if you’re not vigilant. If you’d prefer your generic code to require a full equivalence relation, you can instead use the  `std::cmp::Eq`  trait as a bound, which represents a full equivalence relation: if a type implements  `Eq` , then  `x == x`  must be  `true`  for every value  `x`  of that type. In practice, almost every type that implements  `PartialEq`  should implement  `Eq`  as well;  `f32`  and  `f64`  are the only types in the standard library that are  `PartialEq`  but not  `Eq` .

  + The standard library defines  `Eq`  as an extension of  `PartialEq` , adding no new methods:

`rust
trait Eq: PartialEq<Self> {}
`

  + If your type is  `PartialEq`  and you would like it to be  `Eq`  as well, you must explicitly implement  `Eq` , even though you need not actually define any new functions or types to do so.
