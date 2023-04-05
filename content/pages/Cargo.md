---
tags:
- Rust
title: Cargo
categories:
date: 2022-10-30
lastMod: 2022-10-30
---


[Cargo](https://doc.rust-lang.org/cargo/)  is the [Rust](https://www.rust-lang.org/) [*package manager*](https://doc.rust-lang.org/cargo/appendix/glossary.html#package-manager). Cargo downloads your Rust [package](https://doc.rust-lang.org/cargo/appendix/glossary.html#package)'s dependencies, compiles your packages, makes distributable packages, and uploads them to [crates.io](https://crates.io/), the Rust community’s [*package registry*](https://doc.rust-lang.org/cargo/appendix/glossary.html#package-registry).

## Cargo Commands

  + 创建一个新的 Cargo 包

    + 默认 `cargo new <your-package-name>`，这也会将 [Git]({{< ref "/pages/Git" >}}) 作为新包的版本管理工具。

    + 如果不想让新包被版本管理，可以这样：`cargo new <your-package-name> --vcs none`。

    + 
