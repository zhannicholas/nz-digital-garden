---
tags:
- Rust
title: Programming Rust, 2nd Edition - Error Handling
categories:
date: 2023-04-04
lastMod: 2023-04-04
---


Rust has two kinds of error handling: panic and `Result`s.

Ordinary errors are handled using the  `Result`  type.  `Result` s typically represent problems caused by things outside the program. Panic is for the other kind of error, the kind that *should never happen*.

## Panic

  + A program panics when it encounters something so messed up that there must be a bug in the program itself.

  + There’s a macro  `panic!()` , for cases where your own code discovers that it has gone wrong, and you therefore need to trigger a panic directly.

  + When these errors that shouldn’t happen do happen—what then? Remarkably, Rust gives you a choice. Rust can either **unwind the stack** when a panic happens or **abort the process**. Unwinding is the default.

  + ### Unwinding

    + In Rust, a panic typically proceeds as follows:

      + An error message is printed to the terminal:

`txt
thread 'main' panicked at 'attempt to divide by zero', pirates.rs:3780
note: Run with `RUST_BACKTRACE=1` for a backtrace.
`

        + If you set the  `RUST_BACKTRACE`  environment variable, as the messages suggests, Rust will also dump the stack at this point.

      + The stack is unwound. This is a lot like C++ exception handling.Any temporary values, local variables, or arguments that the current function was using are dropped, in the reverse of the order they were created.

        + {{< logseq/orgNOTE >}}Once the current function call is cleaned up, we move on to its caller, dropping its variables and arguments the same way. Then we move to *that* function’s caller, and so on up the stack.
{{< / logseq/orgNOTE >}}

      + Finally, the thread exits. If the panicking thread was the main thread, then the whole process exits (with a nonzero exit code).

    + Panic is **safe**. It doesn’t violate any of Rust’s safety rules; even if you manage to panic in the middle of a standard library method, it will never leave a dangling pointer or a half-initialized value in memory. The idea is that Rust catches the invalid array access, or whatever it is, *before* anything bad happens. It would be unsafe to proceed, so Rust unwinds the stack. But the rest of the process can continue running.

    + Panic is **per thread**. One thread can be panicking while other threads are going on about their normal business.

    + There is also a way to *catch* stack unwinding, allowing the thread to survive and continue running. The standard library function  `std::panic::catch_unwind()`  does this.

  + ### Aborting

    + Stack unwinding is the default panic behavior, but there are two circumstances in which Rust does not try to unwind the stack.

      + If a  `.drop()`  method triggers a second panic while Rust is still trying to clean up after the first, this is considered fatal. Rust stops unwinding and aborts the whole process.

      + Also, Rust’s panic behavior is customizable. If you compile with  `-C panic=abort` , the *first* panic in your program immediately aborts the process.

## Result

  + Rust doesn’t have exceptions. Instead, functions that can fail have a return type that says so:

`rust
fn get_weather(location: LatLng) -> Result<WeatherReport, io::Error>
`

    + The  `Result`  type indicates possible failure. It either contains a success result, or an error result which explains what went wrong.

  + ### Catching Errors

    + The most thorough way of dealing with a  `Result`  is using a  `match`  expression.

`rust
match result {
  Ok(value) => { ... }
  Err(err) => { ... }
}
`

    + This is Rust’s equivalent of  `try/catch`  in other languages. It’s what you use when you want to handle errors head-on, not pass them on to your caller.

  + ### Propagating Errors

    + In most places where we try something that could fail, we don’t want to catch and handle the error immediately. Instead, if an error occurs, we usually want to let our caller deal with it. We want errors to *propagate* up the call stack.

    + Rust has a `?` operator that can propagate up the errors. You can add a `?` to any expression that produces a `Result`. The behavior of  `?`  depends on whether this function returns a success result or an error result:

      + On success, it unwraps the  `Result`  to get the success value inside.

      + On error, it immediately returns from the enclosing function, passing the error result up the call chain. To ensure that this works,  `?`  can only be used on a  `Result`  in functions that have a  `Result`  return type.

    + `?`  also works similarly with the  `Option`  type. In a function that returns  `Option` , you can use  `?`  to unwrap a value and return early in the case of  `None`.
