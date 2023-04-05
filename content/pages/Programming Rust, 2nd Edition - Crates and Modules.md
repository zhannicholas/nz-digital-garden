---
tags:
- Rust
title: Programming Rust, 2nd Edition - Crates and Modules
categories:
date: 2023-04-04
lastMod: 2023-04-04
---


## Crates

  + Rust programs are made of *crates*. Each crate is a complete, cohesive unit: all the source code for a single library or executable, plus any associated tests, examples, tools, configuration, and other junk

  + The easiest way to see what crates are and how they work together is to use  `cargo build`  with the  `--verbose`  flag to build an existing project that has some dependencies.


    + A project's dependencies are declared in `Cargo.toml` file under the `[dependencies]` section.


      + For example:


`toml
[dependencies]
num = "0.4"
`

      + The word *dependencies* here just means other crates this project uses: code we’re depending on. We found these crates on [crates.io](https://crates.io/), the Rust community’s site for open source crates.

      + The dependencies specified in `Cargo.toml` may have other dependencies, which are *transitive dependencies* of our project. The collection of all these dependency relationships, which tells Cargo everything it needs to know about what crates to build and in what order, is known as the *dependency graph* of the crate.

      + Once it has the source code, Cargo compiles all the crates. It runs  `rustc` , the Rust compiler, once for each crate in the project’s dependency graph. When compiling libraries, Cargo uses the  `--crate-type lib`  option. This tells  `rustc`  not to look for a  `main()`  function but instead to produce an *.rlib* file containing compiled code that can be used to create binaries and other *.rlib* files.

      + When compiling a program, Cargo uses  `--crate-type bin` , and the result is a binary executable for the target platform.

      + With each  `rustc`  command, Cargo passes  `--extern`  options, giving the filename of each library the crate will use.

  + ### Editions


    + Rust has extremely strong compatibility guarantees. Any code that compiled on Rust 1.0 must compile just as well on Rust 1.50 or, if it’s ever released, Rust 1.900.

    + But sometimes there are compelling proposals for extensions to the language that would cause older code to no longer compile. To evolve without breaking existing code, Rust uses *editions*.


      + To avoid breaking existing code, each crate indicates which edition of Rust it is written in with a line like this in the  `[package]`  section atop its *Cargo.toml* file:

`toml
edition = "2021"
`

    + Rust promises that the compiler will always accept all extant editions of the language, and programs can freely mix crates written in different editions.


      + In other words, a crate’s edition only affects how its source code is construed; edition distinctions are gone by the time the code has been compiled. This means there’s no pressure to update old crates just to continue to participate in the modern Rust ecosystem. Similarly, there’s no pressure to keep your crate on an older edition to avoid inconveniencing its users. You only need to change editions when you want to use new language features in your own code.

    + The [Rust Edition Guide](https://oreil.ly/bKEO7) covers the changes introduced in each edition and provides good background on the edition system.

  + ### Build Profiles


    + There are several configuration settings you can put in your *Cargo.toml* file that affect the  `rustc`  command lines that  `cargo`  generates


      + | Command line | Cargo.toml section used |
| ---- | ---- | ---- |
|  `cargo build`  |  `[profile.dev]`  |
|  `cargo build --release`  |  `[profile.release]`  |
|  `cargo test`  |  `[profile.test]`  |

## Modules

  + Whereas crates are about code sharing between projects, *modules* are about code organization *within* a project.

  + Modules act as Rust’s namespaces, containers for the functions, types, constants, and so on that make up your Rust program or library. A module looks like this:


`rust
mod spores {
  use cells::{Cell, Gene};
  /// A cell made by an adult fern. It disperses on the wind as part of
  /// the fern life cycle. A spore grows into a prothallus -- a whole
  /// separate organism, up to 5mm across -- which produces the zygote
  /// that grows into a new fern. (Plant sex is complicated.)
  pub struct Spore {
      ...
  }
  /// Simulate the production of a spore by meiosis.
  pub fn produce_spore(factory: &mut Sporangium) -> Spore {
      ...
  }
  /// Extract the genes in a particular spore.
  pub(crate) fn genes(spore: &Spore) -> Vec<Gene> {
      ...
  }
  /// Mix genes to prepare for meiosis (part of interphase).
  fn recombine(parent: &mut Cell) {
      ...
  }
  ...
}
`

    + A module is a collection of *items*, named features like the  `Spore`  struct and the two functions in this example. The  `pub`  keyword makes an item public, so it can be accessed from outside the module.

    + {{< logseq/orgTIP >}}Marking an item as `pub` is often known as “exporting” that item.
{{< / logseq/orgTIP >}}

    + One function is marked  `pub(crate)` , meaning that it is available anywhere inside this crate, but isn’t exposed as part of the external interface. It can’t be used by other crates, and it won’t show up in this crate’s documentation.

    + Anything that isn’t marked  `pub`  is private and can only be used in the same module in which it is defined, or any child modules.

  + ### Nested Modules


    + Modules can nest, and it’s fairly common to see a module that’s just a collection of submodules:

`rust
mod plant_structures {
  pub mod roots {
      ...
  }
  pub mod stems {
      ...
  }
  pub mod leaves {
      ...
  }
}
`

    + If you want an item in a nested module to be visible to other crates, be sure to mark it *and all enclosing modules* as public.

    + It’s also possible to specify  `pub(super)` , making an item visible to the parent module only, and  `pub(in <path>)` , which makes it visible in a specific parent module and its descendants. This is especially useful with deeply nested modules:

`rust
mod plant_structures {
  pub mod roots {
      pub mod products {
          pub(in crate::plant_structures::roots) struct Cytokinin {
              ...
          }
      }
  use products::Cytokinin; // ok: in `roots` module
  }
  use roots::products::Cytokinin; // error: `Cytokinin` is private
}
  // error: `Cytokinin` is private
use plant_structures::roots::products::Cytokinin;
`

  + ### Modules in Separate Files


    + A module can have its own directory. When Rust sees  `mod spores;` , it checks for both *spores.rs* and *spores/mod.rs*; if neither file exists, or both exist, that’s an error.

    + It’s also possible to use a file and directory with the same name to make up a module.

    + There are three options for modules in Rust

      + modules in their file

      + modules in their own directory with a *mod.rs*

      + modules in their own file with a supplementary directory containing submodules.

  + ### Paths and Imports


    + The  `::`  operator is used to access features of a module. Code anywhere in your project can refer to any standard library feature by writing out its path.

    + We can use `use` keyword to import features into our modules.

      + For example:

`rust
use std::mem;
 if s1 > s2 {
  mem::swap(&mut s1, &mut s2);
}
`

        + The  `use`  declaration causes the name  `mem`  to be a local alias for  `std::mem`  throughout the enclosing block or module.

    + {{< logseq/orgTIP >}}The best style of imports: import types, traits, and modules (like `std::mem`) and then use relative paths to access the functions, constants, and other members within.
{{< / logseq/orgTIP >}}

    + Even though  `use`  declarations are just aliases, they can be public:


`rust
// in plant_structures/mod.rs
...
pub use self::leaves::Leaf;
pub use self::roots::Root;
`

      + This means that  `Leaf`  and  `Root`  are public items of the  `plant_structures`  module. They are still simple aliases for  `plant_structures::leaves::Leaf`  and  `plant_structures::roots::Root` .

    + You can use  `as`  to import an item but give it a different name locally:

`rust
use std::io::Result as IOResult;
`

    + Modules do *not* automatically inherit names from their parent modules.

    + By default, paths are relative to the current module, and `self`  is also a synonym for the current module.

    + The keywords  `super`  and  `crate`  have a special meaning in paths:  `super`  refers to the parent module, and  `crate`  refers to the crate containing the current module.

      + {{< logseq/orgTIP >}}Using paths relative to the crate root rather than the current module makes it easier to move code around the project, since all the imports won’t break if the path of the current module changes. 
{{< / logseq/orgTIP >}}

    + Submodules can access private items in their parent modules with  `use super::*` .

    + Rust has a special kind of path called an *absolute path*, starting with  `::` , which always refers to an external crate.

  + ### Constants and Statics

    + In addition to functions, types, and nested modules, modules can also define *constants* and *statics*.

    + The  `const`  keyword introduces a constant. The syntax is just like  `let`  except that it may be marked  `pub` , and the type is required. Also,  `UPPERCASE_NAMES`  are conventional for constants:


`rust
pub const ROOM_TEMPERATURE: f64 = 20.0;  // degrees Celsius
`

    + The  `static`  keyword introduces a static item, which is nearly the same thing:


`rust
pub static ROOM_TEMPERATURE: f64 = 68.0;  // degrees Fahrenheit
`

    + A constant is a bit like a C++  `#define` : the value is compiled into your code every place it’s used. A static is a variable that’s set up before your program starts running and lasts until it exits. **Use constants for magic numbers and strings in your code. Use statics for larger amounts of data, or any time you need to borrow a reference to the constant value**.

## Attributes

  + Any item in a Rust program can be decorated with *attributes*. [Attributes](https://doc.rust-lang.org/reference/attributes.html) are Rust’s catchall syntax for writing miscellaneous instructions and advice to the compiler.

  + To attach an attribute to a whole crate, add it at the top of the *main.rs* or *lib.rs* file, before any items, and write  `#!`  instead of  `#`.

  + The  `#!`  tells Rust to attach an attribute to the enclosing item rather than whatever comes next.

## Specify Dependencies

  + We can specify the dependencies of our project in *Cargo.toml* in several ways:


    + By specifying version number

`toml
image = "0.6.1"
`

    + By specifying a Git repository URL and revision

`toml
image = { git = "https://github.com/Piston/image.git", rev="528f19c"}
`

      + Besides `rev`, `tag` and `branch` are also supported, because they are all ways of telling Git which revision of the source code to check out.

    + By specifying a directory that contains the crate's source code

`toml
image = { path = "vendor/image" }
`

  + ### versions


    + [Specifying Dependencies - The Cargo Book (rust-lang.org)](https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html).

    + When you write something like  `image = "0.13.0"`  in your *Cargo.toml* file, Cargo interprets this **rather** loosely. It uses the most recent version of  `image`  that is considered **compatible** with version 0.13.0.

      + {{< logseq/orgTIP >}}The compatibility rules are adapted from Semantic Versioning.
{{< / logseq/orgTIP >}}

    + Version numbers are **flexible** by default because otherwise the problem of which version to use would quickly become overconstrained.

    + Different projects have different needs when it comes to dependencies and versioning. You can specify an exact version or range of versions by using operators. For example: `image = "=0.10.0"`, `image = ">=1.0.5"`, `image = ">1.0.5 < 1.1.9"`, `image = "<=2.7.0"`.

    + The widecard `*` tells Cargo that any version will do. Unless some other *Cargo.toml* file contains a more specific constraint, Cargo will use the latest available version.

    + {{< logseq/orgCAUTION >}}Note that the compatibility rules mean that version numbers can’t be chosen purely for marketing reasons. They actually mean something. They’re a contract between a crate’s maintainers and its users.
{{< / logseq/orgCAUTION >}}

  + ### Cargo.lock


    + The version numbers in *Cargo.toml* are deliberately flexible, yet we don’t want Cargo to upgrade us to the latest library versions every time we build.


      + {{< logseq/orgCAUTION >}}What if Cargo upgrade your dependencies while you're debuging?
{{< / logseq/orgCAUTION >}}

    + Cargo has a built-in mechanism to prevent upgrading dependencies automatically. The first time you build a project, Cargo outputs a *Cargo.lock* file that records the exact version of every crate it used. Later builds will consult this file and continue to use the same versions. Cargo upgrades to newer versions only when you tell it to, either by manually bumping up the version number in your *Cargo.toml* file or by running  `cargo update`.

    + `cargo update`  only upgrades to the latest versions that are compatible with what you’ve specified in *Cargo.toml*.

    + *Cargo.lock* is automatically generated for you, and you normally won’t edit it by hand.


      + {{< logseq/orgTIP >}}If your project is an executable, you should commit *Cargo.lock* to version control. That way, everyone who builds your project will consistently get the same versions. The history of your Cargo.lock file will record your dependency updates.

If your project is an ordinary Rust library, don’t bother committing Cargo.lock.
{{< / logseq/orgTIP >}}

    + *Cargo.toml*’s flexible version specifiers make it easy to use Rust libraries in your project and maximize compatibility among libraries. *Cargo.lock*’s bookkeeping supports consistent, reproducible builds across machines. Together, they go a long way toward helping you avoid dependency hell.
