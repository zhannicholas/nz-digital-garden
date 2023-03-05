---
date: "2021-12-07T20:00:10+08:00"
title: "Notes from Java Performance"
authors: Nicholas Zhan
categories:
  - 读书笔记
tags:
  - Java
draft: false
toc: true
mathjax: true
---

> Notes from *Java Performance, 2nd Edition by Scott Oaks.*

# Fundamentals

To be a good Java Performance engineer, we need some specific knowledge. This knowledge falls into two broad categories:
* The performance of the Java Virtual Machine (JVM) itself: the way that the JVM is configured affects many aspects of a program’s performance.
* To understand how the features of the Java platform affect performance.

## JVM tuning flags

With a few exceptions, the JVM accepts two kinds of flags: boolean flags, and flags that require a parameter.
* Boolean flags use this syntax: `-XX:+FlagName` enables the flag, and `-XX:-FlagName` disables the flag.
* Flags that require a parameter use this syntax: `-XX:FlagName=something`, meaning to set the value of `FlagName` to `something`.

The process of automatically tuning flags based on the environment is called **ergonomics**.

## Hardware Platform

Two popular platform in use today: Virtual Machines and Software Containers (such as Docker).

Virtual machine sets up a completely isolated copy of the **operating system** on a subset of the hardware on which the virtual machine is running.

Docker container is just a process (potentially with resource constraints) within a running OS, it provides **isolation between processes**. By default, a Docker container is free to use all of the machine’s resources. But when the heap grows to it's maximum size and that size is larger than the memory assigned to the Docker container, the Docker container (and hence the JVM) will be **killed**.

## The Complete Performance Story

A **good algorithm** is the most important thing when it comes to fast performance.

A small well-written program will run faster than a large well-written program (write less code).

We should forget about small efficiencies, say about 97% of the time; **premature optimization is the root of all evil**. But premature optimization doesn't mean avoiding code constructs that are known to be bad for performance. For example:

```Java
log.log(Level.FINE, "I am here, and the value of X is "
        + calcX() + " and Y is " + calcY());
```

This code does a string concatenation that is likely unnecessary, since the message won’t be logged unless the logging level is set quite high. The suggested imporvement is:

```Java
if (log.isLoggable(Level.FINE)) {
    log.log(Level.FINE,
            "I am here, and the value of X is {} and Y is {}",
            new Object[]{calcX(), calcY()});
}
```

Remember, increasing load to a component in a system that is performing **badly** will make the entire system slower (For example, the database is always the bottleneck).

We should focus on the **common use case** scenarios. This principle manifests itself in several ways:
* Optimize code by profiling it and focusing on the operations in the profile taking the most time.
* Apply Occam’s razor to diagnosing performance problems.
* Write simple algorithms for the most common operations in an application.

## Performance testing categories

Three categories of code can be used for performance testing: microbenchmarks, macrobenchmarks, and mesobenchmarks.

### Microbenchmarks

A microbenchmark is a test designed to measure **a small unit** of performance in order to decide which of multiple alternate implementations is preferable.

But just-in-time compilation and garbage collection in Java make it difficult to write microbenchmarks correctly.

Some principles:
* Microbenchmarks must test a range of input
* Microbenchmarks must measure the correct input
* Microbenchmark code may behave differently in production

In microbenchmarks, a warm-up period is need, because one of the performance characteristics of Java is that **code performs better the more it is executed**.

### Macrobeanchmarks

The best thing to use to measure performance of an application is the **application itself**, in conjunction with any external resources it uses. This is a macrobenchmark.

### Mesobenchmarks

Mesobenchmarks are tests that occupy a middle ground between a microbenchmark and a full application.

## Throughput, Batching, and Response Time

Performance can be measured as **throughput (RPS)**, **elapsed time (batch time)**, or **response time**, and these three metrics are interrelated.

**Elapsed Time (Batch) Measurements**: how long it takes to accomplish a certain task. Performance is most often measured after the code in question has been executed long enough for it to have been compiled and optimized.

**Throughput Measurements**: A throughput measurement is based on the amount of work that can be accomplished in a certain period of time. This measurement is frequently referred to as transactions per second (TPS), requests per second (RPS), or operations per second (OPS).

**Response Time**: the amount of time that elapses between the sending of a request from a client and the receipt of the response. One difference between **average response time** and a **percentile response time** is in the way outliers affect the calculation of the average: since they are included as part of the average, large outliers will have a large effect on the average response time.

## Variability

**Test results vary over time**. Understanding when a difference is a real regression and when it is a random variation is difficult.

Testing code for changes like this is called regression testing. In a regression test, the original code is known as the *baseline*, and the new code is called the *specimen*.

In general, the larger the variation in a set of results, the harder it is to guess the probability that the difference in the averages is real or due to random chance.

Statistical significance does not mean statistical importance.

Correctly determining whether results from two tests are different requires a level of statistical analysis to make sure that perceived differences are not the result of random chance. The rigorous way to accomplish that is to use Student’s t-test to compare the results. Data from the t-test tells us the probability that a regression exists, but it doesn’t tell us which regressions should be ignored and which must be pursued. Finding that balance is part of the art of performance engineering.



## Test early, Test Often

Early, frequent testing is most useful if the following guidelines are followed:
* Automate everything
* Measure everything
* Run on the target system

## Operating System Tools and Analysis

### CPU Usage

CPU usage is typically divided into two categories: **user time** and **system time** (Windows refers to this as **privileged time**). User time is the percentage of time the CPU is executing application code, while system time is the percentage of time the CPU is executing kernel code.

The goal in performance is to **drive CPU usage as high as possible for as short a time as possible**. Driving the CPU usage higher is always the goal for **batch jobs**, because the job will be completed faster.

Both Windows and Unix systems allow you to monitor the number of threads that can be run (meaning that they are not blocked on I/O, or sleeping, and so on). Unix systems refer to this as the *run queue*: the first number in each line of `vmstat`'s output is the length of the run queue. Windows refers to this number as the *processor queue* and reports it (among other ways) via `typeperf`.

### Disk Usage

Monitoring disk usage has two important goals. The first pertains to the application itself: if the application is doing a lot of disk I/O, that I/O can easily become a bottleneck. The second is even if the application is not expected to perform a significant amount of I/O—is to help monitor if the system is swapping.

A system that is swapping—moving pages of data from main memory to disk, and vice versa—will have quite bad performance.

### Network Usage

Network usage is similar to disk traffic: the application might be inefficiently using the network so that bandwidth is too low, or the total amount of data written to a particular network interface might be more than the interface is able to handle.

Be sure to remember that the bandwidth is measured in bits per second (bps), although tools generally report bytes per second (Bps).

## Java Monitoring Tools

JDK provides many Java monitoring tools to help us gain insight in to the JVM:

* `jcmd`: Prints basic class, thread, and JVM information for a Java process. Usage: `jcmd process_id command optional_arguments`
* `jconsole`: Provides a graphical view of JVM activities, including thread usage, class usage, and GC activities.
  * Since `jconsole` requires a fair amount of system resources, running it on a production system can interfere with that system. But we can set up `jconsole` so that it can be **run locally and attach to a remote system**, which won't interfere with that remote system's performance.
* `jmap`: Provides heap dumps and other information about JVM memory usage.
* `jinfo`: Provides visibility into the system properties of the JVM, and allows some system properties to be set dynamically.
* `jstack`: Dumps the stacks of a Java process. Suitable for scripting.
* `jstat`: Provides information about GC and class-loading activities.
* `jvisualvm`: A GUI tool to monitor a JVM, profile a running application, and analyze JVM heap dumps.

Among these tools, nongraphical tools (expect `jconsole` and `jvisualvm`) are suitable for scripting.

These tools fits into broad areas:
* Basic VM information
* Thread information
* Class information
* Live GC analysis
* Heap dump postprocessing
* Profile a JVM

There's a lot of overlap with each tool's work. So it's better to focus on different areas.

### Basic VM information

#### Uptime

The length of time the JVM has been up can be found via this command:
```shell
$ jcmd <process_id> VM.uptime
```

#### System properties

The set of items in `System.getProperties()` can be displayed with either of these commands:
```shell
$ jcmd <process_id> VM.system_properties
```
or
```shell
$ jinfo -sysprops <process_id>
```

#### JVM version

The version of the JVM is obtained like this:
```shell
$ jcmd <process_id> VM.version
```

#### JVM command line

The command line can be displayed in the VM summary tab of `jconsole`, or via `jcmd`:
```shell
$ jcmd <process_id> VM.command_line
```

#### JVM tuning flags

The tuning flags in effect for an application can be obtained like this:
```shell
$ jcmd <process_id> VM.flags [-all]
```
Or, you can use `-XX:+PrintFlagsFinal` option on the command line to see platform-specific defaults.
```shell
$ java other_options -XX:+PrintFlagsFinal -version
```

Yet another way to see JVM options for a running application is with `jinfo`. Retrive the values of all flags in the proces:
```shell
$ jinfo -flags <process_id>
```
If `-flags` option is present, this command will provide information about all flags; ohterwise, it prints only those specified on the command line.

If you want to inspect the value of an individual flag, use `-flag` option:
```shell
$ jinfo -flag PrintGCDetails <process_id>
-XX:+PrintGCDetails
```
The advantage of `jinfo` is that is allows certain flag values to be changed during execution of the program. For example:
```shell
$ jinfo -flag -PrintGCDetails <process_id> # turns off PrintGCDetails
```
But this technique works only for those flags marked `manageable` in the output of `PrintFlagsFinal` command.

### Thread Information

If you have GUI, `jconsole` and `jvisualvm` are good tools to display real time information of threads running in an application.

The stacks can be obtained via `jstack`:
```shell
$ jstack <process_id>
```
Or `jcmd`:
```shell
$ jcmd <process_id> Thread.print
```

### Class Information

Information about the number of classes in use by an application can be obtained from `jconsole` or `jstat`. `jstat` can also provide information about class compilation.

### Live GC Analysis

Virtually every monitoring tool can report something about GC activity
* `jconsole` displays live graphs of the heap usage;
* `jcmd` allows GC operations to be performed;
* `jmap` can print heap summaries or information on the permanent generation or create a heap dump;
* `jstat` produces a lot of views of what the garbage collector is doing

### Heap Dump Postprocessing

The *heap dump* is a **snapshot** of the heap that can be analyzed with various tools, including `jvisualvm` and the Eclipse Memory Analyzer Tool (MAT). GUI tool `jvisualvm` and command line tool `jcmd` or `jmap` can capture heap snapshots.

### Profile a JVM

**Profilers** are the most important tool in a performance analyst’s toolbox. Profiling happens in one of two modes: sampling mode or instrumented mode.

#### Sampling Profilers

Sampling mode is the basic mode of profiling and carries the least amount of overhead.

In the common Java interface for profilers, the profiler can get the stack trace of a thread **only** when the thread is **at a safepoint**, so sampling becomes even less reliable.

Threads automatically go into a safepoint when they are:
* Blocked on a synchronized lock
* Blocked waiting for I/O
* Blocked waiting for a monitor
* Parked
* Executing Java Native Interface (JNI) code (unless they perform a GC locking function)

In addition, the JVM can set a flag asking for threads to go into a safepoint.

#### Instrumented Profilers

Instrumented profilers are much more intrusive than sampling profilers (they could have a greater effect on application than a sampling profiler), but they can also give more beneficial information about what’s happening inside a program.

Instrumented profilers work by altering the bytecode sequence of classes as they are loaded (inserting code to count the invocations, and so on).

Because of the changes introduced into the code via instrumentation, it is best to limit its use to a few classes. This means it is best used for second-level analysis: a sampling profiler can point to a package or section of code, and then the instrumented profiler can be used to drill into that code if needed.

### Java Flight Recorder

*Java Flight Recorder (JFR)* is a feature of the JVM that performs lightweight performance analysis of applications while they are running.

JFR collects a set of event data, the data stream is held in a circular buffer, so only the most recent events are available.

By default, JFR is set up so that it has very low overhead: an impact below 1% of the program’s performance.

JFR is initially disabled. To enable it, add the flag `-XX:+FlightRecorder` to the command line of the application (In Oracle's JDK 8, you must specify `XX:+UnlockCommercialFeatures` prior to it).

If you forget to include these flags, you can use `jinfo` to change their values and enable JFR.

Flight recordings are made in one of two modes: either for a fixed duration (1 minute in this case) or continuously.

We can start JFR when the program initially begins by `-XX:StartFlightRecording=string` flag. Or, use `jcmd <process_id> JRF.start [options_list]` to start a recording during a run, use `jcmd <process_id> JRF.dump [options_list]` to dump current data in the circular buffer, and use `jcmd <process_id> JRF.stop [options_list]` to abort a recording in process.

JFR is useful in performance analysis, but it is also useful when enabled on a production system so that you can examine the events that led up to a failure.

### Java Mission Control

The usual tool to examine JFR recordings is *Java Mission Control (jmc)*.

# Java SE API Tips

## Strings

Strings are (unsurprisingly) the most common Java object.

### Compact Strings

In Java 8, all strings are encoded as arrays of 16-bit characters, regardless of the encoding of the string. This is wasteful, because most Western locales can encode strings into 8-bit byte arrays.

In Java 11, strings are encoded as arrays of 8-bit bytes unless they explicitly need 16-bit characters; these strings are known as **compact strings**. This feature is controlled by the `-XX:+CompactStrings` flag, which is true by default.

### Duplicate Strings and String Interning

Since strings are immutable, it is often better to reuse the existing strings. The duplicate strings can be removed in three ways:
* Performing automatic deduplication via G1 GC
* Using the `intern()` method of the `String` class to create the canonical version of the string
* Using a custom method to create a canonical version of the string

#### String Deduplication

The simplest mechanism is to let the JVM find the duplicate strings and deduplicate them: arrange for all references to point to a single copy and then free the remaining copies. This is possible only when using G1 GC and only when specifying the `-XX:+UseStringDeduplication` flag (which by default is false).

String deduplication is not enabled by default for three reasons. First, it requires extra processing during the young and mixed phases of G1 GC, making them slightly longer. Second, it requires an extra thread that runs concurrently with the application, potentially taking CPU cycles away from application threads. And third, if there are few deduplicated strings, the memory use of the application will be higher (instead of lower); this extra memory comes from the bookkeeping involved in tracking all the strings to look for duplications.

If you want to see how string deduplication is behaving in your application, run it with the `-XX:+PrintStringDeduplicationStatistics` flag in Java 8, or the `-Xlog:gc+stringdedup*=debug` flag in Java 11.

The point at which the tenured string is eligible for collection is controlled via the `-XX:StringDeduplicationAgeThreshold=N` flag, which has a default value of 3.

#### String Interning

The typical way to handle duplicate strings at a programmatic level is to use the `intern()` method of the `String` class.

Interned strings are held in a special hash table that is in native memory (though the strings themselves are in the heap). This native hash table has a fixed size (the default value is 60,013 in Java 8 and 65,536 in Java 11). and you can set it when the JVM starts by using the flag `-XX:StringTableSize=N`.

The performance of the `intern()` method is dominated by how well the string table size is tuned. In order to see how the string table is performing, run your application with the `-XX:+PrintStringTableStatistics` argument (which is false by default).

#### String Concatenation

Don’t be afraid to use concatenation when it can be done on a single (logical) line, but never use string concatenation inside a loop unless the concatenated string is not used on the next loop iteration. Otherwise, always explicitly use a StringBuilder object for better performance.

## Buffered I/O

For file-based I/O using binary data, always use `BufferedInputStream` or `BufferedOutputStream` to wrap the underlying file stream. For file-based I/O using character (string) data, always wrap the underlying stream with BufferedReader or BufferedWriter.

When you convert between bytes and characters, operating on as large a piece of data as possible will provide the best performance.

## Classloading

The performance of classloading is the bane of anyone attempting to optimize either program startup or deployment of new code in a dynamic system.

**Class data sharing (CDS)** is a mechanism whereby the metadata for classes can be shared between JVMs. The first thing required to use CDS is a shared archive of classes. The second step is to use that class list to generate the shared archive like this:
```shell
$ java -Xshare:dump -XX:SharedClassListFile=filename \
    -XX:SharedArchiveFile=myclasses.jsa \
    ... classpath arguments ...
```
Finally, you use the shared archive to run the application like this:
```shell
$ java -Xshare:auto -XX:SharedArchiveFile=myclasses.jsa ... other args ...
```
The -Xshare command has three possible values:
* `off`: Don’t use class data sharing.
* `on`: Always use class data sharing.
* `auto`: Attempt to use class data sharing, but if for some reason the archive cannot be mapped, the application will proceed without it.

CDS will also save us some memory since the class data will be shared among processes.

## Random Numbers

Java comes with three standard random number generator classes: `java.util.Random`, `java.util.concurrent.ThreadLocalRandom`, and `java.security.SecureRandom`. These three classes have important performance differences.

The difference between the `Random` and `ThreadLocalRandom `classes is that the main operation (the `nextGaussian() `method) of the `Random` class is synchronized.

The difference between those classes and the `SecureRandom` class lies in the algorithm used. The `Random` class (and the `ThreadLocalRandom` class, via inheritance) implements a typical pseudorandom algorithm. While those algorithms are quite sophisticated, they are in the end deterministic. If the initial seed is known, it is possible to determine the exact series of numbers the engine will generate.

The `SecureRandom` class uses a system interface to obtain a seed for its random data. This is known as *entropy-based randomness* and is much more secure for operations that rely on random numbers.

On Linux systems, these two sources are */dev/random* (for seeds) and */dev/urandom* (for random numbers). The two systems handle that differently: */dev/random* will block until it has enough system events to generate the random data, and */dev/urandom* will fall back to a pseudorandom number generator (PRNG). The common consensus is use */dev/random* for seeds and */dev/urandom* for everything else.

## Java Native Interface

Performance tips about Java SE (particularly in the early days of Java) often say that if you want really fast code, you should use native code. In truth, if you are interested in writing the fastest possible code, avoid the Java Native Interface (JNI).

Well-written Java code will run at least as fast on current versions of the JVM as corresponding C or C++ code (it is not 1996 anymore). When an application is already written in Java, calling native code for performance reasons is almost always a bad idea.

## Logging

We should keep three basic principles in mind for application logs:
* First is to keep a balance between the data to be logged and level at which it is logged.
* The second principle is to use fine-grained loggers. it should be possible to enable small subsets of messages in a production environment without affecting the performance of the system.
* The third principle to keep in mind when introducing logging to code is to remember that it is easy to write logging code that has unintended side effects, even if the logging is not enabled.

## Lambdas and Anonymous Classes

The choice between using a lambda or an anonymous class should be dictated by ease of programming, since there is no difference between their performance.

Lambdas are not implemented as anonymous classes, so one exception to that rule is in environments where classloading behavior is important; lambdas will be slightly faster in that case.

## Stream and Filter Performance

One important performance feature of streams is that they can automatically parallelize code.

The first performance benefit from streams is that they are implemented as lazy data structures.

One reason, then, that filters can be so much faster than iterators is simply that they can take advantage of algorithmic opportunities for optimizations: the lazy filter implementation can end processing whenever it has done what it needs to do, processing less data.

# JIT Compiler

The **just-in-time (JIT) compiler** is the heart of the Java Virtual Machine; nothing controls the performance of your application more than the JIT compiler.

## Just-in-Time Compilers: An Overview

Computers can execute only a relatively few, specific instructions, which are called **machine code**. All programs that the CPU executes must therefore be translated into these instruction.

There are typically two kinds of programing language:
* *compiled language*, like C++. the program is written, and then a static compiler produces a binary. The assembly code in that binary is targeted to a particular CPU.
* *interpreted language*, like Python. The interpreter translates each line of the program into binary code as that line is executed.

Each system has advantages and disadvantages. Programs written in interpreted languages are portable, but might run slowly.

Programs written in compiled language are opposite. A good compiler takes several factors into account when it produces a binary. In addition, a good compiler will produce a binary that executes the statement to load the data, executes other instructions, and then—when the data is available—executes the addition. An interpreter that is looking at only one line of code at a time doesn’t have enough information to produce that kind of code.

For these (and other) reasons, interpreted code will almost always be measurably slower than compiled code: compilers have enough information about the program to provide optimizations to the binary code that an interpreter simply cannot perform. However, interpreted code does have the advantage of portability.

Java attempts to find a middle ground among them. Java applications are compiled into an intermediate low-level language called *Java bytecode*, which will be runned by the `java` binary. This gives Java the platform independency of an interpreted language. Because it is executing an idealized binary code, the `java` program is able to compile the code into the platform binary as the code executes. This compilation occurs as the program is executed: it happens “just in time.”

Remember, the *Java bytecode* is the key of Java's platform independence, rather than the `java` binary. `java` binary is platform dependent.

## HotSpot Compilation

In a typical program, only a small subset of code is executed frequently, and the performance of an application depends primarily on how fast those sections of code are executed. These critical sections are known as the hot spots of the application; the more the section of code is executed, the hotter that section is said to be.

### Register and Main Memory

One of the most important optimizations a compiler can make involves when to use values from main memory and when to store values in a register.

Retrieving a value from main memory is an expensive operation that takes multiple cycles to complete.

Register usage is a general optimization of the compiler, and typically the JIT will aggressively use registers.

## Tiered Compilation

Historically, JVM developers (and even some tools) sometimes referred to the compilers by the names `C1` (compiler 1, client compiler) and `C2` (compiler 2, server compiler).

The primary difference between the two compilers is their **aggressiveness** in compiling code. The C1 compiler begins compiling sooner than the C2 compiler does. This means that during the beginning of code execution, the C1 compiler will be faster, because it will have compiled correspondingly more code than the C2 compiler.

The engineering trade-off here is the knowledge the C2 compiler gains while it waits: that knowledge allows the C2 compiler to make better optimizations in the compiled code.

*tiered compilation* can be explicitly disabled with the `-XX:-TieredCompilation` flag (the default value of which is `true`);

## Common Compiler Flags

### Tuning the Code Cache

When the JVM compiles code, it holds the set of assembly-language instructions in the code cache. The code cache has a **fixed size**, and once it has filled up, the JVM is not able to compile any additional code.

The maximum size of the code cache is set via the `-XX:ReservedCodeCacheSize=N` flag. The code cache is managed like most memory in the JVM: there is an initial size (specified by `-XX:InitialCodeCacheSize=N`). Allocation of the code cache size starts at the initial size and increases as the cache fills up.

If a 1 GB code cache size is specified, the JVM will reserve 1 GB of native memory. That memory isn’t allocated until needed, but it is still reserved, which means that sufficient virtual memory must be available on your machine to satisfy the reservation.

In Java 11, the code cache is segmented into three parts:
* Nonmethod code
* Profiled code
* Nonprofiled code

### Inspecting the Compilation Process

The `-XX:+PrintCompilation` flag (which by default is `false`) gives us visibility into the workings of the compiler. If `PrintCompilation` is enabled, every time a method (or loop) is compiled, the JVM prints out a line with information about what has just been compiled.

JIT compilation is an asynchronous process: when the JVM decides that a certain method should be compiled, that method is placed in a queue.

Code that needs to be compiled sits in a compilation queue. The more code in the queue, the longer the program will take to achieve optimal performance.

### Tiered Compilation Levels

There are five levels of compilation, because the C1 compiler has three levels. So the levels of compilation are as follows:
* 0: Interpreted code
* 1: Simple C1 compiled code
* 2: Limited C1 compiled code
* 3: Full C1 compiled code
* 4: C2 compiled code

### Deoptimization

**Deoptimization** means that the compiler has to “undo” a previous compilation. The effect is that the performance of the application will be reduced—at least until the compiler can recompile the code in question.

Deoptimization occurs in two cases: when code is `made not entrant` and when code is `made zombie`.

#### Not entrant code

Two things cause code to be made not entrant. One is due to the way classes and interfaces work, and one is an implementation detail of tiered compilation.

Aside from a momentary point where the trap is processed, deoptimization has not affected the performance in any significant way.

When code is compiled by the C2 compiler, the JVM must replace the code already compiled by the C1 compiler. It does this by marking the old code as not entrant and using the same deoptimization mechanism to substitute the newly compiled (and more efficient) code.

#### Deoptimizing zombie code

When the code for certain Java class was made not entrant, but the objects of that class remained. Eventually all those objects were reclaimed by GC. When that happened, the compiler noticed that the methods of that class were now eligible to be marked as zombie code.

## Advanced Compiler Flags

### Compilation Thresholds

Compilation is based on two counters in the JVM: the number of times the method has been called, and the number of times any loops in the method have branched back. *Branching back* can effectively be thought of as the number of times a loop has completed execution, either because it reached the end of the loop itself or because it executed a branching statement like `continue`.

When tiered compilation is disabled, standard compilation is triggered by the value of the `-XX:CompileThreshold=N` flag

### Compilation Threads

The number of compiler threads can be adjusted by setting the `-XX:CICompilerCount=N` flag. For tiered compilation, one-third (but at least one) threads will be used to process the C1 compiler queue, and the remaining threads (but also at least one) will be used to process the C2 compiler queue.

### Inlining

One of the most important optimizations the compiler makes is to inline methods. Inlining is enabled by default. It can be disabled using the `-XX:-Inline` flag.

### Escape Analysis

The C2 compiler performs aggressive optimizations if escape analysis is enabled (`-XX:+DoEscapeAnalysis`, which is true by default). In rare cases, it will get things wrong.

## Tiered Compilation Trade-offs

### The `javac` Compiler

Most important is that the `javac` compiler—with one exception—doesn’t really affect performance at all. In particular:
* The `-g` option to include additional debugging information doesn’t affect performance.
* Using the `final` keyword in your Java program doesn’t produce faster compiled code.
* Recompiling with newer `javac` versions doesn’t (usually) make programs any faster.

JDK 11 introduces a new way of doing string concatenation that can be faster than previous versions, but it requires that code be recompiled in order to take advantage of it. That is the exception to the rule here.

### The GraalVM

The **GraalVM** is a new virtual machine. It provides a means to run Java code, of course, but also code from many other languages.

## Precompilation

### Ahead-of-Time Compilation

**Ahead-of-time (AOT) compilation** was first available in JDK 9 for Linux only, but in JDK 11 it is available on all platforms. AOT compilation allows you to compile some (or all) of your application in advance of running it. This compiled code becomes a shared library that the JVM uses when starting the application.

To use AOT compilation, you use the `jaotc` tool to produce a shared library containing the compiled classes that you select. Then that shared library is loaded into the JVM via a runtime argument.



### GraalVM Native Compilation

AOT compilation was beneficial for relatively large programs but didn’t help (and could hinder) small, quick-running programs. The GraalVM, on the other hand, can produce full native executables that run without the JVM. These executables are ideal for short-lived programs.

# Threading and Synchronization Performance

## Thread Pools and ThreadPoolExecutors

Threads can be managed by custom code in Java, or applications can utilize a thread pool.

Thread pools have a minimum and maximum number of threads. The minimum number of threads is kept around, waiting for tasks to be assigned to them. Because creating a thread is a fairly expensive operation, this speeds up the overall operation when a task is submitted: it is expected that an already existing thread can pick it up. On the other hand, threads require system resources—including native memory for their stacks—and having too many idle threads can consume resources that could be used by other processes. The maximum number of threads also serves as a necessary throttle, preventing too many tasks from executing at once.

### Setting the Maximum Number of Threads

There is no simple answer to exact maximum number of threads; it depends on characteristics of the workload and the hardware on which it is run. In particular, the optimal number of threads depends on how often each individual task will block.

### Setting the Minimum Number of Threads

Once the maximum number of threads in a thread pool has been determined, it’s time to determine the minimum number of threads needed.

The argument for setting the minimum number of threads to less than maximum number of threads is that it prevents the system from creating too many threads, which saves on system resources. It is true that each thread requires a certain amount of memory, particularly for its stack.

By default, when you create a `ThreadPoolExecutor`, it will start with only one thread. but you can pre-create some threads (using the `prestartAllCoreThreads()` method).

Keeping idle threads around usually has little impact on an application. Usually, the thread object itself doesn’t take a very large amount of heap space. The exception is to use too much thread-local storage.

### Thread Pool Task Sizes

The tasks pending for a thread pool are held in a queue or list; when a thread in the pool can execute a task, it pulls a task from the queue. As a result, thread pools typically limit the size of the queue of pending tasks. In any case, when the queue limit is reached, attempts to add a task to the queue will fail.

### Sizing a ThreadPoolExecutor

The `ThreadPoolExecutor` decides when to start a new thread based on the type of queue used to hold the tasks. There are three possibilities:

* **`SynchronousQueue`**: new tasks will start a new thread if all existing threads are busy and if the pool has less than the number of maximum threads. However, this queue has no way to hold pending tasks: if a task arrives and the maximum number of threads is already busy, the task is always rejected. So this choice is good for managing a small number of tasks, but otherwise may be unsuitable.
* **Unbounded queues**: When the executor uses an unbounded queue (such as `LinkedBlockedingQueue`), no task will ever be rejected (since the queue size is unlimited). In this case, the executor will use at most the number of threads specified by the core thread pool size: the maximum pool size is ignored.
* **Bounded queues**: Executors that use a bounded queue (e.g., `ArrayBlockingQueue`) employ a complicated algorithm to determine when to start a new thread. An additional thread will be started only when the queue is full, and a new task is added to the queue. The idea behind this algorithm is that the pool will operate with only the core threads (four) most of the time, even if a moderate number of tasks is in the queue waiting to be run. That allows the pool to act as a throttle (which is advantageous).

## The ForkJoinPool

The `ForkJoinPool` class is designed to work with divide-and-conquer algorithms: those where a task can be recursively broken into subsets. The subsets can be processed in parallel, and then the results from each subset are merged into a single result.

A thread inside a thread-pool executor cannot add another task to the queue and then wait for it to finish: once the thread is waiting, it cannot be used to execute one of the subtasks. `ForkJoinPool`, on the other hand, allows its threads to create new tasks and then suspend their current task. While the task is suspended, the thread can execute other pending tasks.

## Thread Synchronization

Strictly speaking, the atomic classes do not use synchronization, at least in CPU programming terms. Atomic classes utilize a **Compare and Swap (CAS)** CPU instruction, while synchronization requires exclusive access to a resource.

### Costs of Synchronization

Synchronized areas of code affect performance in two ways. First, the amount of time an application spends in a synchronized block affects the scalability of an application. Second, obtaining the synchronization lock requires CPU cycles and hence affects performance.

when an application is split up to run on multiple threads, the speedup it sees is defined by an equation known as *Amdahl’s law*:
$$
Speedup = \frac{1}{(1 - P) + P/N}
$$
`P` is the amount of the program that is run in parallel, and `N` is the number of threads utilized (assuming that each thread always has available CPU). That is why limiting the amount of code that lies in the serialized block is so important.

Uncontended `synchronized` locks are known as *uninflated locks*, and the cost of obtaining an uninflated lock is on the order of a few hundred nanoseconds. Uncontended CAS code will see an even smaller performance penalty.

### Avoiding Synchronization

If synchronization can be avoided altogether, locking penalties will not affect the application’s performance. Two general approaches can be used to achieve that.
* The first approach is to use different objects in each thread so that access to the objects will be uncontended.
* The second way to avoid synchronization is to use CAS-based alternatives.

In the general case, the following guidelines apply to the performance of CAS-based utilities compared to traditional synchronization:
* If access to a resource is uncontended, CAS-based protection will be slightly faster than traditional synchronization. If the access is always uncontended, no protection at all will be slightly faster still and will avoid corner-cases like the one you just saw with the register flushing from the `Vector` class.
* If access to a resource is lightly or moderately contended, CAS-based protection will be faster (often much faster) than traditional synchronization.
* As access to the resource becomes heavily contended, traditional synchronization will at some point become the more efficient choice. In practice, this occurs only on very large machines running many threads.
* CAS-based protection is not subject to contention when values are read and not written.

## JVM Thread Tunings

### Tuning Thread Stack Sizes

To change the stack size for a thread, use the `-Xss=N` flag (e.g., `-Xss=256k`).

### Biased Locking

The theory behind biased locking is that if a thread recently used a lock, the processor’s cache is more likely to still contain data the thread will need the next time it executes code protected by that same lock.

In the applications where different threads are equally likely to access the contended locks, a small performance improvement can be obtained by disabling biased locking via the `-XX:-UseBiasedLocking` option. Biased locking is enabled by default.

## Monitoring Threads and Locks

When analyzing an application’s performance for the efficiency of threading and synchronization, we should look for two things: the overall number of threads (to make sure it is neither too high nor too low) and the amount of time threads spend waiting for a lock or other resource.

The first caveat in looking at thread stacks is that the JVM can dump a thread’s stack only at safepoints. Second, stacks are dumped for each thread one at a time, so it is possible to get conflicting information from them: two threads can show up holding the same lock, or a thread can show up waiting for a lock that no other thread holds.

Thread stacks can show how significantly threads are blocked (since a thread that is blocked is already at a safepoint).

# Garbage Collection

One of the most attractive features of programming in Java is that developers needn’t explicitly manage the life cycle of objects: objects are created when needed, and when the object is no longer in use, the JVM automatically frees the object.

### Garbage Collection Overview

At a basic level, GC consists of finding objects that are in use and freeing the memory associated with the remaining objects (those that are not in use).

JVM periodically search the heap for unused objects by starting with objects that are GC roots, which are objects that are accessible from outside the heap.

The performance of GC is dominated by these basic operations: finding unused objects, making their memory available, and compacting the heap.

The pauses when all application threads are stopped are called **stop-the-world pauses**. These pauses generally have the greatest impact on the performance of an application, and minimizing those pauses is one important consideration when tuning GC.

### Generational Garbage Collectors

Though the details differ somewhat, most garbage collectors work by splitting the heap into generations. These are called the **old (or tenured) generation** and the **young generation**. The young generation is further divided into sections known as **eden** and the **survivor spaces** (though sometimes, eden is incorrectly used to refer to the entire young generation).

The rationale for having separate generations is that many objects are used for a very short period of time.

The garbage collector is designed to take advantage of the fact that many (and sometimes most) objects are only used temporarily. This is where the generational design comes in. Objects are first allocated in the young generation, which is a subset of the entire heap. When the young generation fills up, the garbage collector will stop all the application threads and empty out the young generation. Objects that are no longer in use are discarded, and objects that are still in use are moved elsewhere. This operation is called a minor GC or a young GC.

This design has two performance advantages. First, because the young generation is only a portion of the entire heap, processing it is faster than processing the entire heap. The second advantage arises from the way objects are allocated in the young generation. Since all surviving objects are moved, the young generation is automatically compacted when it is collected: at the end of the collection, eden and one of the survivor spaces are empty, and the objects that remain in the young generation are compacted within the other survivor space.

As objects are moved to the old generation, eventually it too will fill up, and the JVM will need to find any objects within the old generation that are no longer in use and discard them. The simpler algorithms stop all application threads, find the unused objects, free their memory, and then compact the heap. This process is called a **full GC**, and it generally causes a relatively long pause for the application threads.

On the other hand, it is possible to find unused objects while application threads are running. Because the phase where they scan for unused objects can occur without stopping application threads, these algorithms are called **concurrent collectors**. They are also called low-pause (and sometimes, incorrectly, pauseless) collectors since they minimize the need to stop all the application threads.

When using a concurrent collector, an application will typically experience fewer (and much shorter) pauses. The biggest trade-off is that **the application will use more CPU overall**. In other words, the benefit of avoiding long pause times with a concurrent collector comes at the expense of extra CPU usage.

### GC Algorithms

#### The serial garbage collector

The **serial garbage collector** is the simplest of the collectors. This is the default collector if the application is running on a client-class machine (32-bit JVMs on Windows) or on a single-processor machine.

The serial collector uses a single thread to process the heap. It will stop all application threads as the heap is processed (for either a minor or full GC). During a full GC, it will fully compact the old generation.

The serial collector is enabled by using the `-XX:+UseSerialGC` flag.

#### The throught collector

In JDK 8, the **throughput collector** is the default collector for any 64-bit machine with two or more CPUs. Because it uses multiple threads, the throughput collector is often called the **parallel collector**.

The throughput collector stops all application threads during both minor and full GCs, and it fully compacts the old generation during a full GC.

To enable it where necessary, use the flag `-XX:+UseParallelGC`.

#### The G1 GC collector

The **G1 GC (or garbage first garbage collector)** uses a concurrent collection strategy to collect the heap with minimal pauses. It is the default collector in JDK 11 and later for 64-bit JVMs on machines with two or more CPUs.

G1 GC divides the heap into regions, but it still considers the heap to have two generations.

G1 GC is enabled by specifying the flag `-XX:+UseG1GC`.

G1 GC operates on discrete regions within the heap. Each region (there are by default around 2,048) can belong to either the old or new generation, and the generational regions need not be contiguous.

G1 GC is called a concurrent collector because the marking of free objects within the old generation happens concurrently with the application threads.

Full GC in G1 GC collector is triggered primarily four times:
* Concurrent mode failure: G1 GC starts a marking cycle, but the old generation fills up before the cycle is completed. In that case, G1 GC aborts the marking cycle.
* Promotion failure: G1 GC has completed a marking cycle and has started performing mixed GCs to clean up the old regions. Before it can clean enough space, too many objects are promoted from the young generation, and so the old generation still runs out of space.
* Evacuation failure: When performing a young collection, there isn’t enough room in the survivor spaces and the old generation to hold all the surviving objects.
* Humongous allocation failure: Applications that allocate very large objects can trigger another kind of full GC in G1 GC.
* Metadata GC threshold： Metaspace is not collected via G1 GC, but still when it needs to be collected in JDK 8, G1 GC will perform a full GC (immediately preceded by a young collection) on the main heap.

G1 has multiple cycles (and phases within the concurrent cycle). A well-tuned JVM running G1 should experience only young, mixed, and concurrent GC cycles.

##### Tuning G1 GC

The major goal in tuning G1 GC is to make sure that no concurrent mode or evacuation failures end up requiring a full GC.

But one of the goals of G1 GC is that it shouldn’t have to be tuned that much. To that end, G1 GC is primarily tuned via a single flag: the same `-XX:MaxGCPauseMillis=N` flag.

Two sets of threads are used by G1 GC. The first set is controlled via the `-XX:ParallelGCThreads=N` flag. This value affects the number of threads used for phases when application threads are stopped. The second flag is `-XX:ConcGCThreads=N`, which affects the number of threads used for the concurrent remarking. The default value for the `ConcGCThreads` flag is defined as follows: `ConcGCThreads = (ParallelGCThreads + 2) / 4`.

If you want G1 GC run more (or less) frequently, use `-XX:InitiatingHeapOccupancyPercent=N` flag to indicate the G1 GC start background marking cycle. The default value of this flag is 45.

#### The CMS collector

**CMS collector** stops all application threads during a minor GC, which it performs with multiple threads.

CMS is officially deprecated in JDK 11 and beyond, and its use in JDK 8 is discouraged. The major flaw in CMS is that it has no way to compact the heap during its background processing. If the heap becomes fragmented (which is likely to happen at some point), CMS must stop all application threads and compact the heap, which defeats the purpose of a concurrent collector. This concurrent mode failure is a major reason CMS is deprecated.

CMS is enabled by specifying the flag `-XX:+UseConcMarkSweepGC`, which is false by default.

The primary concern when tuning CMS is to make sure that no concurrent mode or promotion failures occur.

CMS uses the `MaxGCPauseMllis=N` and `GCTimeRatio=N` settings to determine how large the heap and the generations should be.

Each CMS background thread will consume 100% of a CPU on a machine.

### Causing and Disabling explicit garbage collection

GC is typically caused when the JVM decides GC is necessary: a minor GC will be triggered when the new generation is full, a full GC will be triggered when the old generation is full, or a concurrent GC (if applicable) will be triggered when the heap starts to fill up.

Java provides a mechanism for applications to force a GC to occur: the `System.gc()` method. Calling that method is almost always a bad idea. This call always triggers a full GC (even if the JVM is running with G1 GC or CMS), so application threads will be stopped for a relatively long period of time. And calling this method will not make the application any more efficient.

Explicit GCs can be prevented by including `-XX:+DisableExplicitGC` in the JVM arguments. By default, that flag is false.

### Choosing a GC Algorithm

The trade-off between G1 GC and other collectors involves having available CPU cycles for G1 GC background threads, so let’s start with a CPU-intensive batch job. In a batch job, the CPU will be 100% busy for a long time, and in that case the serial collector has a marked advantage. That’s what I mean when I say that when you choose G1 GC, sufficient CPU is needed for its background threads to run.

If we’re more interested in interactive processing and response times, the throughput collector has a harder time beating G1 GC. If your server is short of CPU cycles such that the G1 GC and application threads compete for CPU, then G1 GC will yield worse response times.

### Experimental GC Algorithms

The pause times of an application are dominated by the time spent moving objects and making sure references to them are up-to-date.

Two experimental collectors are designed to address this problem. The first is the Z garbage collector, or ZGC; the second is the Shenandoah garbage collector. ZGC first appeared in JDK 11; Shenandoah GC first appeared in JDK 12 but has now been backported to JDK 8 and JDK 11.

o use these collectors, you must specify the `-XX:+UnlockExperimentalVMOptions` flag (by default, it is `false`). Then you specify either `-XX:+UseZGC` or `-XX:+UseShenandoahGC` in place of other GC algorithms.

Both collectors allow concurrent compaction of the heap, meaning that objects in the heap can be moved without stopping all application threads. This has two main effects:
* First, the heap is no longer generational.
* The second is that the latency of operations performed by the application threads can be expected to be reduced.

JDK 11 also contains a collector that does nothing: the **epsilon collector**.

## Basic GC Tuning

### Sizing the Heap

If the heap is too small, the program will spend too much time performing GC and not enough time performing application logic. The time spent in GC pauses is dependent on the size of the heap, so as the size of the heap increases, the duration of those pauses also increases.

A severe performance penalty happens when the OS swaps data from disk to RAM (which is an expensive operation to begin with). Hence, the first rule in sizing a heap is never to specify a heap that is larger than the amount of physical memory on the machine.

The size of the heap is controlled by two values: an initial value (specified with `-XmsN`) and a maximum value (`-XmxN`). Heap sizing is one of the JVM’s core ergonomic tunings.

Having an initial and maximum size for the heap allows the JVM to tune its behavior depending on the workload.

A good rule of thumb is to size the heap so that it is 30% occupied after a full GC.

If you know exactly what size heap the application needs, you may as well set both the initial and maximum values of the heap to that value (e.g., `-Xms4096m -Xmx4096m`). That makes GC slightly more efficient, because it never needs to figure out whether the heap should be resized.

### Sizing the Generations

The performance implication of different generation sizes should be clear: if there is a relatively larger young generation, young GC pause times will increase, but the young generation will be collected less often, and fewer objects will be promoted into the old generation. But on the other hand, because the old generation is relatively smaller, it will fill up more frequently and do more full GCs. Striking a balance is key.

The command-line flags to tune the generation sizes all adjust the size of the young generation; the old generation gets everything that is left over. A variety of flags can be used to size the young generation:
* `-XX:NewRatio=N`: Set the ratio of the young generation to the old generation.
* `-XX:NewSize=N`: Set the initial size of the young generation.
* `-XX:MaxNewSize=N`: Set the maximum size of the young generation.
* `-XmnN`: Shorthand for setting both NewSize and MaxNewSize to the same value.

The `NewRatio` value is used in this formula: `Initial Young Gen Size = Initial Heap Size / (1 + NewRatio)`

By default, then, the young generation starts out at 33% of the initial heap size.

#### Adaptive and Static Heap Size Tuning

At a global level, adaptive sizing can be disabled by turning off the `-XX:-UseAdaptiveSizePolicy` flag (which is `true` by default). To see how the JVM is resizing the spaces in an application, set the `-XX:+PrintAdaptiveSizePolicy` flag.

Tuning the throughput collector is all about pause times and striking a balance between the overall heap size and the sizes of the old and young generations.

There are two trade-offs to consider here. First, we have the classic programming trade-off of time versus space. A larger heap consumes more memory on the machine, and the benefit of consuming that memory is (at least to a certain extent) that the application will have a higher throughput.

The second trade-off concerns the length of time it takes to perform GC. The number of full GC pauses can be reduced by increasing the heap size, but that may have the perverse effect of increasing average response times because of the longer GC times. Similarly, full GC pauses can be shortened by allocating more of the heap to the young generation than to the old generation, but that, in turn, increases the frequency of the old GC collections.

Adaptive sizing in the throughput collector will resize the heap (and the generations) in order to meet its pause-time goals. Those goals are set with these flags: `-XX:MaxGCPauseMillis=N` and `-XX:GCTimeRatio=N`.

The `GCTimeRatio` flag specifies the amount of time you are willing for the application to spend in GC, it's default value is 99.

$$
ThroughputGoal = 1 - 1 / (1 + GCTimeRatio)
$$

$$
GCTimeRatio = Throughput / (1 - Throughput)
$$


### Sizing Metaspace

When the JVM loads classes, it must keep track of certain metadata about those classes. This occupies a separate heap space called the **metaspace**.

Information in the metaspace is used only by the compiler and JVM runtime, and the data it holds is referred to as **class metadata**.

Because the default size of metaspace is unlimited, an application (particularly in a 32-bit JVM) could run out of memory by filling up metaspace. Resizing the metaspace requires a full GC, so it is an expensive operation.

### Controlling Parallesim

All GC algorithms except the serial collector use multiple threads. The number of these threads is controlled by the `-XX:ParallelGCThreads=N` flag. The value of this flag affects the number of threads used for the following operations:
* Collection of the young generation when using `-XX:+UseParallelGC`
* Collection of the old generation when using `-XX:+UseParallelGC`
* Collection of the young generation when using `-XX:+UseG1GC`
* Stop-the-world phases of G1 GC (though not full GCs)

The total number of threads (where `N` is the number of CPUs) on a machine with more than eight CPUs is shown here: `ParallelGCThreads = 8 + ((N - 8) * 5 / 8)`.

## Advanced Tuning

### Tenuring and Survivor Spaces

Objects are moved into the old generation in two circumstances. First, the survivor spaces are fairly small. When the target survivor space fills up during a young collection, any remaining live objects in eden are moved directly into the old generation. Second, there is a limit to the number of GC cycles during which an object can remain in the survivor spaces. That limit is called the *tenuring threshold*.

The initial size of the survivor spaces is determined by the `-XX:InitialSurvivorRatio=N` flag, which is used in this equation: `survivor_space_size = new_size / (initial_survivor_ratio + 2)`.

The JVM may increase the survivor spaces size to a maximum determined by the setting of the `-XX:MinSurvivorRatio=N` flag. That flag is used in this equation: `maximum_survivor_space_size = new_size / (min_survivor_ratio + 2)`.

There is the question of how many GC cycles an object will remain ping-ponging between the survivor spaces before being moved into the old generation. That answer is determined by the tenuring threshold. The JVM continually calculates what it thinks the best tenuring threshold is. The threshold starts at the value specified by the `-XX:InitialTenuringThreshold=N` flag (the default is 7 for the throughput and G1 GC collectors, and 6 for CMS). The JVM will ultimately determine a threshold between 1 and the value specified by the `-XX:MaxTenuringThreshold=N` flag; for the throughput and G1 GC collectors, the default maximum threshold is 15, and for CMS it is 6.

### Allocating Large Objects

It is important to applications that frequently create a significant number of large objects. In this context, *large* is a relative term; it depends, as you’ll see, on the size of a particular kind of buffer within the JVM.This buffer is known as a **thread-local allocation buffer (TLAB)**.

Each thread has a dedicated region where it allocates objects—a thread-local allocation buffer, or TLAB. When objects are allocated directly in a shared space such as eden, some synchronization is required to manage the free-space pointers within that space.

By default, TLABs are enabled; they can be disabled by specifying `-XX:-UseTLAB`. They have a small size, so large objects cannot be allocated within a TLAB. Large objects must be allocated directly from the heap, which requires extra time because of the synchronization.

In addition, TLAB is just a section within eden.

Outside JFR, the best way to monitor the TLAB allocation by adding the `-XX:+PrintTLAB` flag to the command line in JDK 8 or including `tlab*=trace` in the log configuration for JDK 11.

Applications that spend a lot of time allocating objects outside TLABs will benefit from changes that can move the allocation to a TLAB. The size of the TLABs can be set explicitly using the flag `-XX:TLABSize=N`.

Objects that are allocated outside a TLAB are still allocated within eden when possible. If the object cannot fit within eden, it must be allocated directly in the old generation. G1 GC defines a *humongous object* as one that is half of the region size. Since G1 GC divides the heap into regions, each of which has a fixed size. he size of the regions will be set according to this formula (using log base 2): `region_size = 1 << log(Initial Heap Size / 2048)`.

## GC Tools

### Enabling GC Logging in JDK 8

JDK 8 provides multiple ways to enable the GC log. Specifying either of the flags `-verbose:gc` or `-XX:+PrintGC` will create a simple GC log (the flags are aliases for each other, and by default the log is disabled). The `-XX:+PrintGCDetails` flag will create a log with much more information.

In conjunction with the detailed log, it is recommended to include `-XX:+PrintGCTimeStamps` or `-XX:+PrintGCDateStamps` so that the time between GC operations can be determined.

The GC log is written to standard output, though that location can (and usually should) be changed with the `-Xloggc:filename` flag. Using `-Xloggc` automatically enables the simple GC log unless `PrintGCDetails` has also been enabled.

The amount of data that is kept in the GC log can be limited using log rotation.

Putting that all together, a useful set of flags for logging is as follows: `-Xloggc:gc.log -XX:+PrintGCTimeStamps -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFile=8 -XX:GCLogFileSize=8m`.

### Enabling GC Logging in JDK 11

To enable GC logging in JDK 11, use following flags: `-Xlog:gc*:file=gc.log:time:filecount=7,filesize=8M`. The colons divide the command into four sections.

For a scriptable solution, `jstat` is the tool of choice. `jstat` provides nine options to print different information about the heap; `jstat -options` will provide the full list. One useful option is `-gcutil`, which displays the time spent in GC as well as the percentage of each GC area that is currently filled. If you’ve forgotten to enable GC logging, this is a good substitute to watch how GC operates over time.

## A quick approach to choosing and tuning a garbage collector

Here’s a quick set of questions to ask yourself to help put everything in context:

* **Can your application tolerate some full GC pauses?**
  * If not, G1 GC is the algorithm of choice. Even if you
    can tolerate some full pauses, G1 GC will often be better than parallel GC unless your application is CPU bound.

* **Are you getting the performance you need with the default settings?**
  * Try the default settings first. As GC technology matures, the ergonomic (automatic) tuning gets better all the time. If you’re not getting the performance you need, make sure that GC is your problem. Look at the GC logs and see how much time you’re spending in GC and how frequently the long pauses occur. For a busy application, if you’re spending 3% or less time in GC, you’re not going to get a lot out of tuning (though you can always try to reduce outliers if that is your goal).

* **Are the pause times that you have somewhat close to your goal?**
  * If they are, adjusting the maximum pause time may be all you need. If they aren’t, you need to do something else. If the pause times are too large but your throughput is OK, you can reduce the size of the young generation (and for full GC pauses, the old generation); you’ll get more, but shorter, pauses.

* **Is throughput lagging even though GC pause times are short?**
  * You need to increase the size of the heap (or at least the young generation). More isn’t always better: bigger heaps lead to longer pause times. Even with a concurrent collector, a bigger heap means a bigger young generation by default, so you’ll see longer pause times for young collections. But if you can, increase the heap size, or at least the relative sizes of the generations.

* **Are you using a concurrent collector and seeing full GCs due to concurrent-mode failures?**
  * If you have available CPU, try increasing the number of concurrent GC threads or starting the background sweep sooner by adjusting InitiatingHeapOccupancyPercent. For G1, the concurrent cycle won’t start if there are pending mixed GCs; try reducing the mixed GC count target.

* **Are you using a concurrent collector and seeing full GCs due to promotion failures?**
  * In G1 GC, an evacuation failure (to-space overflow) indicates that the heap is fragmented, but that can usually be solved if G1 GC performs its background sweeping sooner and mixed GCs faster. Try increasing the number of concurrent G1 threads, adjusting InitiatingHeapOccupancyPercent, or reducing the mixed GC count target.

# Heap and Native Memory Best Practices

We have two conflicting goals here. The first general rule is to create objects sparingly and to discard them as quickly as possible. Using less memory is the best way to improve the efficiency of the garbage collector. On the other hand, frequently re-creating some kinds of objects can lead to worse overall performance (even if GC performance improves). If those objects are instead reused, programs can see substantial performance gains.

## Heap Analysis

### Heap Histograms

Heap histograms are a quick way to look at the number of objects within an application without doing a full heap dump. If a few particular object types are responsible for creating memory pressure in an application, a heap histogram is a quick way to find that.

Heap histograms can be obtained by using `jcmd`:
```shell
$ jcmd <process_id> GC.class_histogram
```
`jmap` can show heap histograms too:
```shell
$ jmap -histo <process_id>
```
The output from `jmap` includes objects that are eligible to be collected (dead objects). To force a full GC prior to seeing the histogram, run this command instead:
```shell
$ jmap -histo:live <process_id>
```

### Heap Dumps

To perform a deeper heap analysis, a *heap dump* is required. There are many tools can look at heap dumps. For example:
```shell
$ jcmd <process_id> GC.heap_dump /path/to/heap_dump.hprof
```

```shell
$ map -dump:live,file=/path/to/heap_dump.hprof <process_id>
```
`live` option in `jmap` will force a full GC to occur before the heap is dumped.

GUI tools like `jvisualvm` and `mat` can also dump heaps.

The first-pass analysis of a heap generally involves **retained memory**. The retained memory of an object is the amount of memory that would be freed if the object itself were eligible to be collected.

#### Shallow, Retained, and Deep Object Sizes

The **shallow size** of an object is the size of the object itself. If the object contains a reference to another object, the 4 or 8 bytes of the reference is included, but the size of the target object is not included.

The **deep size** of an object includes the size of the object it references.

Objects that retain a large amount of heap space are often called the *dominators* of the heap.

### Out-of-Memory Errors

The JVM throws an out-of-memory error under these circumstances:
* No native memory is available for the JVM.
* The metaspace is out of memory.
  * This error can have two root causes: The first is simply that the application uses more classes than can fit in the metaspace you’ve assigned. The second case is trickier: it involves a classloader memory leak.
* The Java heap itself is out of memory: the application cannot create any additional objects for the given heap size.
* The JVM is spending too much time performing GC.

#### Automatic Heap Dumps

Out-of-memory errors can occur unpredictably, making it difficult to know when to get a heap dump. Several JVM flags can help:
* `-XX:+HeapDumpOnOutOfMemoryError`: Turning on this flag (which is false by default) will cause the JVM to create a heap dump whenever an out-of-memory error is thrown.
* `-XX:HeapDumpPath=<path>`: This specifies the location where the heap dump will be written; the default is *java_pid.hprof* in the application’s current working directory. The path can specify either a directory (in which case the default filename is used) or the name of the actual file to produce.
* `-XX:+HeapDumpAfterFullGC`: This generates a heap dump after running a full GC.
* `-XX:+HeapDumpBeforeFullGC`: This generates a heap dump before running a full GC.

## Using Less Memory

The first approach to using memory more efficiently in Java is to use less heap memory.

There are mainly three ways to use less memory: reducing object size, using lazy initialization of objects, and using canonical objects.

### Reducing Object Size

Defining only required instance variables is one way to save space in an object. The less obvious case involves using smaller data types.

object sizes are always padded so that they are a multiple of 8 bytes.

The JVM will also pad objects that have an uneven number of bytes so that arrays of that object fit neatly along whatever address boundaries are optimal for the underlying architecture.

Even `null` instance variables consume space within object classes.

The OpenJDK project has a separate downloadable tool called `jol` that can calculate object sizes.

### Using Lazy Initialization

If objects are expensive to create, and it definitely makes sense to keep that object around rather than re-create it on demand. This is a case where lazy initialization can help.

Lazy initialization is best used when the operation in question is only infrequently used.

### Using Immutable and Canonical Objects

If something reduces to its most basic form, then it's canonical. These singular representations of immutable objects are known as the *canonical version* of the object.

Strings can call the `intern()` method to find a canonical version of the string.

To canonicalize an object, create a map that stores the canonical version of the object. To prevent a memory leak, make sure that the objects in the map are weakly referenced.

## Object Life-Cycle Management

### Object Reuse

Object reuse is commonly achieved in two ways: object pools and thread-local variables.

Overall, the longer objects are kept in the heap, the less efficient GC will be. So: object reuse is bad.

*Soft references*, which are discussed later in this section, are essentially a big pool of reusable objects.

The reason for reusing objects is that many objects are expensive to initialize, and reusing them is more efficient than the trade-off in increased GC time.

Thread-local objects are always available within the thread and needn’t be explicitly returned.

Microbenchmarking threads that contend on a lock is always unreliable.

### Soft, Weak, and Other References

An ordinary instance variable that refers to an object is a strong reference.

The `-XX:+PrintReferenceGC` flag (which is `false` by default). This allows you to see how much time is spent processing those references.

#### Soft References

Soft references are used when the object in question has a good chance of being reused in the future, but you want to let the garbage collector reclaim the object if it hasn’t been used recently. Soft references are essentially one large, least recently used (LRU) object pool.

When, exactly, is a soft reference freed? First the referent must not be strongly referenced elsewhere. If the soft reference is the only remaining reference to its referent, the referent is freed during the next GC cycle only if the soft reference has not recently been accessed. If we use pseudocode to display when a soft reference is freed, it will be:
```
long ms = SoftRefLRUPolicyMSPerMB * AmountOfFreeMemoryInMB;
if (now - last_access_to_reference > ms)
   free the reference
```

If the JVM completely runs out of memory or starts thrashing too severely, it will clear all soft references.

#### Weak References

objects that are only weakly referenced are reclaimed at every GC cycle. objects that are only weakly referenced are reclaimed at every GC cycle.

This is what is meant by simultaneous access in weak references It is as if we are saying to the JVM: “Hey, as long as someone else is interested in this object, let me know where it is, but if they no longer need it, throw it away and I will re-create it myself.” Compare that to a soft reference, which essentially says: “Hey, try to keep this around as long as there is enough memory and as long as it seems that someone is occasionally accessing it.”

Don’t make the mistake of thinking that a weak reference is just like a soft reference except that it is freed more quickly: a softly referenced object will be available for (usually) minutes or even hours, but a weakly referenced object will be available for only as long as its referent is still around (subject to the next GC cycle clearing it).

#### Finalizers and Cleaners

The finalizer queue is the reference queue used to process the Finalizer references when the referent is eligible for GC.

In JDK 11, it’s much easier to use the new `java.lang.ref.Cleaner` class in place of the `finalize()` method.

### Compressed Oops

Using simple programming, 64-bit JVMs are slower than 32-bit JVMs. This performance gap is because of the 64-bit object references: the 64-bit references take up twice the space (8 bytes) in the heap as 32-bit references (4 bytes). That leads to more GC cycles, since there is now less room in the heap for other data.

*Oops* stands for *ordinary object pointers*, which are the handles the JVM uses as object references.

Two implications:
* For heaps that are between 4 GB and 32 GB, use compressed oops. Compressed oops are enabled using the `-XX:+UseCompressedOops` flag.
* A program that uses a 31 GB heap and compressed oops will usually be faster than a program that uses a 33 GB heap.

## Native Memory Best Practices

In Unix-based systems, programs like `top` and `ps` can show you that data at a basic level; on Windows, you can use `perfmon` or `VMMap`.

Every time the JVM creates a thread, the OS allocates some native memory to hold that thread’s stack.

In Unix systems, the footprint of an application can be estimated by the *resident set size (RSS)* of the process. On Windows systems, the equivalent idea is called the *working set* of an application, which is what is reported by the task manager.

The distinction between allocated and reserved memory comes about as a result of the way the JVM (and all programs) manage memory.
* *Reserve memory* means the operating system promises that when the JVM attempts to allocate additional memory when it increases the size of the heap, that memory will be available.
* The (actually allocated) memory is known as the committed memory. The amount of committed memory will fluctuate as the heap resizes; in particular, as the heap size increases, the committed memory correspondingly increases.

### Native Memory Tracking

Using the option `-XX:NativeMemoryTracking=off|summary|detail` enables this visibility. By default, Native Memory Tracking (NMT) is off.

If the summary or detail mode is enabled, you can get the native memory information at any time from jcmd:
```shell
$ jcmd <process_id> VM.native_memory summary
```

### Native NIO buffers

Native byte buffers are important from a performance perspective, since they allow native code and Java code to share data without copying it.

The total amount of memory that can be allocated for direct byte buffers is specified by setting the `-XX:MaxDirectMemorySize=N` flag.

In particular, native memory is never compacted. Hence, allocation patterns in native memory can lead to the same fragmentation.

It is possible to run out of native memory in Java because of native memory fragmentation.

## JVM Tunings for the Operating System

### Large Pages

A page is a unit of memory used by operating systems to manage physical memory. It is the minimum unit of allocation for the operating system.

Large pages must be enabled at both the Java and OS levels. At the Java level, the `-XX:+UseLargePages` flag enables large page use; by default, this flag is `false`. Not all operating systems support large pages, and the way to enable them obviously varies.

Linux refers to large pages as huge pages.

#### Linux transparent huge pages

Linux kernels starting with version 2.6.32 support transparent huge pages. These offer (in theory) the same performance benefit as traditional huge pages, but they have some differences from traditional huge pages.
* First, traditional huge pages are locked into memory; they can never be swapped. For Java, this is an advantage. Transparent huge pages can be swapped to disk, which is bad for performance.
* Second, allocation of a transparent huge page is also significantly different from a traditional huge page. Traditional huge pages are set aside at kernel boot time; they are always available. Transparent huge pages are allocated on demand.
* Third, transparent huge pages are configured differently at both the OS and Java levels.

Because of the differences in swapping and allocation of transparent huge pages, they are often not recommended for use with Java; certainly their use can lead to unpredictable spikes in pause times.

#### Windows large pages

Windows large pages can be enabled on only server-based Windows versions.

# Java Servers and Database Performance Best Practices

## Java Servers

Scaling servers is mostly about effective use of threads, and that use requires event-driven, nonblocking I/O.

Some newer frameworks offer programming models based on reactive programming. At their cores, **reactive programming** is based on handling asynchronous data streams using an event-based paradigm.

### Java NIO Overview

Blocking I/O requires that the server has a one-to-one correspondence between client connections and server threads; each thread can handle only a single connection. This is particularly an issue for clients that want to use HTTP keepalive to avoid the performance impact of creating a new socket with every request.

NIO is classic event-driven programming: when data on a socket connection is available to be read, a thread (usually from a pool) is notified of that event. That thread reads the data, processes it (or passes the data to yet another thread to be processed), and then returns to the pool.

### Server Containers

The threads notify the system call when I/O is available and are called **selector threads**. Then a separate thread pool of **worker threads** handles the actual request/response to a client after the selector notifies them that I/O is pending for the client.

The selector and worker threads can be set up in various ways:
* Selector and worker thread pools can be separate. The selectors wait for notification on all sockets and hand off requests to the worker thread pool.
* Alternately, when the selector is notified about I/O, it reads (perhaps only part of) the I/O to determine information about the request. Then the selector forwards the request to different server thread pools, depending on the type of request.
* A selector pool accepts new connections on a `ServerSocket`, but after the connections are made, all work is handled in the worker thread pool. A thread in the worker thread pool will sometimes use the `Selector` class to wait for pending I/O about an existing connection, and it will sometimes be handling the notification from a worker thread that I/O for a client is pending (e.g., it will perform the request/response for the client).
* There needn’t be a distinction at all between threads that act as selectors and threads that handle requests. A thread that is notified about I/O available on a socket can process the entire request. Meanwhile, the other threads in the pool are notified about I/O on other sockets and handle the requests on those other sockets.

### Async Rest Servers

An alternative to tuning the request thread pool of a server is to defer work to another thread pool.

There are three reasons you would use an async response:
* To introduce more parallelism into the business logic.
* To limit the number of active threads.
* To properly throttle the server.

### JSON Processing

Given a series of JSON strings, a program must convert those strings into data suitable for processing by Java. This is called either **marshaling** or **parsing**, depending on the context and the resulting output. If the output is a Java object, the process is called **marshaling**; if the data is processed as it is read, the process is called **parsing**. The reverse—producing JSON strings from other data—is called **unmarshaling**.

## Database Performance Best Practices

There is no corresponding standard for NoSQL databases, and hence there is no standard platform support for accessing them.

### JDBC

The JDBC driver is the most important factor in the performance of database applications.

JDBC drivers come in four types (1–4). The driver types in wide use today are type 2 (which uses native code) and type 4 (which is pure Java).
* Type 1 drivers provide a bridge between Open Database Connectivity (ODBC) and JBDC. If an application must talk to a database using ODBC, it must use this driver. Type 1 drivers generally have quite bad performance.
* Type 2 drivers use a native library to access the database.
* Type 3 drivers are, like type 4 drivers, written purely in Java, but they are designed for a specific architecture in which a piece of middleware (sometimes, though usually not, an application server) provides an intermediary translation.
* Type 4 drivers are pure Java drivers that implement the wire protocol that the database vendor has defined for accessing their database.

Connections to a database are time-consuming to create, so JDBC connections are another prototypical object that you should reuse in Java.

In most circumstances, code should use a `PreparedStatement` rather than a `Statement` for its JDBC calls. This aids performance: prepared statements allow the database to reuse information about the SQL that is being executed. That saves work for the database on subsequent executions of the prepared statement. Prepared statements also have security and programming advantages, particularly in specifying parameters to the call.

Prepared statement pools operate on a per connection basis.

The size of the connection pool also matters because it is caching those prepared statements, which take up heap space (and often a lot of heap space).

Applications that process large amounts of data from a query should consider changing the fetch size of the data.

A trade-off exists between loading too much data in the application (putting pressure on the garbage collector) and making frequent database calls to retrieve a set of data.

### Transactions

Database transactions have two performance penalties. First, it takes time for the database to set up and then commit the transaction. Second, during a database transaction, it is common for the transaction to obtain a lock for a particular set of data.

Transactions are expensive to commit, so one goal is to perform as much work in a transaction as is possible. Unfortunately, that principle is completely at odds with another goal: because transactions can hold locks, they should be as short as possible.

Committing all the data at once offers the fastest performance.

Here are the basic transaction isolation modes (in order from most to least expensive):
* TRANSACTION_SERIALIZABLE: This is the most expensive transaction mode; it requires that all data accessed within the transaction be locked for the duration of the transaction.
* TRANSACTION_REPEATABLE_READ: This requires that all accessed data is locked for the duration of the transaction. However, other transactions can insert new rows into the table at any time. This mode can lead to phantom reads
* TRANSACTION_READ_COMMITTED: This mode locks only rows that are written during a transaction. This leads to nonrepeatable reads
* TRANSACTION_READ_UNCOMMITTED: This is the least expensive transaction mode. No locks are involved, so one transaction may read the written (but uncommitted) data in another transaction. This is known as a dirty read.

### JPA

The performance of JPA is directly affected by the performance of the underlying JDBC driver, and most of the performance considerations regarding the JDBC driver apply to JPA. JPA has additional performance considerations.

JPA achieves many of its performance enhancements by altering the bytecode of the entity classes.

In JDBC, we looked at two critical performance techniques: reusing prepared statements and performing updates in batches.

One common way to optimize writes to a database is to write only those fields that have changed.

The JPA Query Language (JPQL) doesn’t allow you to specify fields of an object to be retrieved.

#### JPA Caching

JPA is designed with that architecture in mind. Two kinds of caches exist in JPA. Each entity manager instance is its own cache: it will locally cache data that it has retrieved during a transaction. It will also locally cache data that is written during a transaction; the data is sent to the database only when the transaction commits.

When an entity manager commits a transaction, all data in the local cache can be merged into a global cache. The global cache is shared among all entity managers in the application. The global cache is also known as the Level 2 (L2) cache or the second-level cache; the cache in the entity manager is known as the Level 1, L1, or first-level cache.

### Summary

Properly tuning JDBC and JPA access to a database is one of the most significant ways to affect the performance of a middle-tier application. Keep in mind these best practices:
* Batch reads and writes as much as possible by configuring the JDBC or JPA configuration appropriately.
* Optimize the SQL the application issues. For JDBC applications, this is a question of basic, standard SQL commands. For JPA applications, be sure to consider the involvement of the L2 cache.
* Minimize locking where possible. Use optimistic locking when data is unlikely to be contended, and use pessimistic locking when data is contended.
* Make sure to use a prepared statement pool.
* Make sure to use an appropriately sized connection pool.
* Set an appropriate transaction scope: it should be as large as possible without negatively affecting the scalability of the application because of the locks held during the transaction.

# Summary of Tuning Flags

## Flags to tune the just-in-time compiler

| Flag | What it does | When to use it|
|------|--------------|---------------|
| `-server` | This flag no longer has any effect; it is silently ignored. | N/A |
| `-client` | This flag no longer has any effect; it is silently ignored. | N/A |
| `-XX:+TieredCompilation` | Uses tiered compilation. | Always, unless you are severely constrained for memory. |
| `-XX:ReservedCodeCacheSize=<MB>` | Reserves space for code compiled by the JIT compiler. | When running a large program and you see a warning that you are out of code cache. |
| `-XX:InitialCodeCacheSize=<MB>` | Allocates the initial space for code compiled by the JIT compiler. | If you need to preallocate the memory for the code cache (which is uncommon). |
| `-XX:CompileThreshold=<N>` | Sets the number of times a method or loop is executed before compiling it. | This flag is no longer recommended. |
| `-XX:+PrintCompilation` | Provides a log of operations by the JIT compiler. | When you suspect an important method isn’t being compiled or are generally curious as to what the compiler is doing. |
| `-XX:CICompilerCount=<N>` | Sets the number of threads used by the JIT compiler. | When too many compiler threads are being started. This primarily affects large machines running many JVMs. |
| `-XX:+DoEscapeAnalysis` | Enables aggressive optimizations by the compiler. | On rare occasions, this can trigger crashes, so it is sometimes recommended to be disabled. Don’t disable it unless you know it is causing an issue. |
| `-XX:UseAVX=<N>` | Sets the instruction set for use on Intel processors. | You should set this to 2 in early versions of Java 11; in later versions, it defaults to 2. |
| `-XX:AOTLibrary=<path>` | Uses the specified library for ahead-of-time compilation. | In limited cases, may speed up initial program execution. Experimental in Java 11 only. |

## Flags to choose the GC algorithm

| Flag | What it does | When to use it|
|------|--------------|---------------|
| `-XX:+UseSerialGC` | Uses a simple, single-threaded GC algorithm. | For single-core virtual machines and containers, or for small (100 MB) heaps. |
| `-XX:+UseParallelGC` | Uses multiple threads to collect both the young and old generations while application threads are stopped. | Use to tune for throughput rather than responsiveness; default in Java 8. |
| `-XX:+UseG1GC` | Uses multiple threads to collect the young generation while application threads are stopped, and background thread(s) to remove garbage from the old generation with minimal pauses. | When you have available CPU for the background thread(s) and you do not want long GC pauses. Default in Java 11. |
| `-XX:+UseConcMarkSweepGC` | Uses background thread(s) to remove garbage from the old generation with minimal pauses. | No longer recommended; use G1 GC instead. |
| `-XX:+UseParNewGC` | With CMS, uses multiple threads to collect the young generation while application threads are stopped. | No longer recommended; use G1 GC instead. |
| `-XX:+UseZGC` | Uses the experimental Z Garbage Collector (Java 12 only). | To have shorter pauses for young GC, which is collected concurrently. |
| `-XX:+UseShenandoahGC` | Uses the experimental Shenandoah Garbage Collector (Java 12 OpenJDK only). | To have shorter pauses for young GC, which is collected concurrently. |
| `-XX:+UseEpsilonGC` | Uses the experimental Epsilon Garbage Collector (Java 12 only). | If your app never needs to perform GC.

## Flags common to all GC algorithms

| Flag | What it does | When to use it|
|------|--------------|---------------|
| `-Xms` | Sets the initial size of the heap. | When the default initial size is too small for your application. |
| `-Xmx` | Sets the maximum size of the heap. | When the default maximum size is too small (or possibly too large) for your application. |
| `-XX:NewRatio` | Sets the ratio of the young generation to the old generation. | Increase this to reduce the proportion of the heap given to the young generation; lower it to increase the proportion of the heap given to the young generation. This is only an initial setting; the proportion will change unless adaptive sizing is turned off. As the young-generation size is reduced, you will see more frequent young GCs and less frequent full GCs (and vice versa). |
| `-XX:NewSize` | Sets the initial size of the young generation. | When you have finely tuned your application requirements. |
| `-XX:MaxNewSize` | Sets the maximum size of the young generation. | When you have finely tuned your application requirements. |
| `-Xmn` | Sets the initial and maximum size of the young generation. | When you have finely tuned your application requirements. |
| `-XX:MetaspaceSize=N` | Sets the initial size of the metaspace. | For applications that use a lot of classes, increase this from the default. |
| `-XX:MaxMetaspaceSize=N` | Sets the maximum size of the metaspace. | Lower this number to limit the amount of native space used by class metadata. |
| `-XX:ParallelGCThreads=N` | Sets the number of threads used by the garbage collectors for foreground activities (e.g., collecting the young generation, and for throughput GC, collecting the old generation). | Lower this value on systems running many JVMs, or in Docker containers on Java 8 before update 192. Consider increasing it for JVMs with very large heaps on very large systems. |
| `-XX:+UseAdaptiveSizePolicy` | When set, the JVM will resize various heap sizes to attempt to meet GC goals. | Turn this off if the heap sizes have been finely tuned. |
| `-XX:+PrintAdaptiveSizePolicy` | Adds information about how generations are resized to the GC log. | Use this flag to gain an understanding of how the JVM is operating. When using G1, check this output to see if full GCs are triggered by humongous object allocation. |
| `-XX:+PrintTenuringDistribution` | Adds tenuring information to the GC logs. | Use the tenuring information to determine if and how the tenuring options should be adjusted. |
| `-XX:InitialSurvivorRatio=N` | Sets the amount of the young generation set aside for survivor spaces. | Increase this if short-lived objects are being promoted into the old generation too frequently. |
| `-XX:MinSurvivorRatio=N` | Sets the adaptive amount of the young generation set aside for survivor spaces. | Decreasing this value reduces the maximum size of the survivor spaces (and vice versa). |
| `-XX:TargetSurvivorRatio=N` | The amount of free space the JVM attempts to keep in the survivor spaces. | Increasing this value reduces the size of the survivor spaces (and vice versa). |
| `-XX:InitialTenuringThreshold=N` | The initial number of GC cycles the JVM attempts to keep an object in the survivor spaces. | Increase this number to keep objects in the survivor spaces longer, though be aware that the JVM will tune it. |
| `-XX:MaxTenuringThreshold=N` | The maximum number of GC cycles the JVM attempts to keep an object in the survivor spaces. | Increase this number to keep objects in the survivor spaces longer; the JVM will tune the actual threshold between this value and the initial threshold. |
| `-XX:+DisableExplicitGC>` | Prevents calls to `System.gc()` from having any effect. | Use to prevent bad applications from explicitly performing GC. |
| `-XX:-AggressiveHeap` | Enables a set of tuning flags that are “optimized” for machines with a large amount of memory running a single JVM with a large heap. | It is better not to use this flag, and instead use specific flags as necessary . |

## Flags controlling GC logging

| Flag | What it does | When to use it|
|------|--------------|---------------|
| `-Xlog:gc*` | Controls GC logging in Java 11. | GC logging should always be enabled, even in production. Unlike the following set of flags for Java 8, this flag controls all options to Java 11 GC logging; see the text for a mapping of options for this to Java 8 flags. |
| `-verbose:gc` | Enables basic GC logging in Java 8. | GC logging should always be enabled, but other, more detailed logs are generally better. |
| `-Xloggc:<path>` | In Java 8, directs the GC log to a special file rather than standard output. | Always, the better to preserve the information in the log. |
| `-XX:+PrintGC` | Enables basic GC logging in Java 8. | GC logging should always be enabled, but other, more detailed logs are generally better. |
| `-XX:+PrintGCDetails` | Enables detailed GC logging in Java 8. | Always, even in production (the logging overhead is minimal). |
| `-XX:+PrintGCTimeStamps` | Prints a relative timestamp for each entry in the GC log in Java 8. | Always, unless datestamps are enabled. |
| `-XX:+PrintGCDateStamps` | Prints a time-of-day stamp for each entry in the GC log in Java 8. | Has slightly more overhead than timestamps, but may be easier to process. |
| `-XX:+PrintReferenceGC` | Prints information about soft and weak reference processing during GC in Java 8. | If the program uses a lot of those references, add this flag to determine their effect on the GC overhead. |
| `-XX:+UseGCLogFileRotation` | Enables rotations of the GC log to conserve file space in Java 8. | In production systems that run for weeks at a time when the GC logs can be expected to consume a lot of space. |
| `-XX:NumberOfGCLogFiles=N` | When logfile rotation is enabled in Java 8, indicates the number of logfiles to retain. | In production systems that run for weeks at a time when the GC logs can be expected to consume a lot of space. |
| `-XX:GCLogFileSize=N` | When logfile rotation is enabled in Java 8, indicates the size of each logfile before rotating it. | In production systems that run for weeks at a time when the GC logs can be expected to consume a lot of space. |

## Flags for the throughput collector

| Flag | What it does | When to use it|
|------|--------------|---------------|
| `-XX:MaxGCPauseMillis=N` | Hints to the throughput collector how long pauses should be; the heap is dynamically sized to attempt to meet that goal. | As a first step in tuning the throughput collector if the default sizing it calculates doesn’t meet application goals. |
| `-XX:GCTimeRatio=N` | Hints to the throughput collector how much time you are willing to spend in GC; the heap is dynamically sized to attempt to meet that goal. | As a first step in tuning the throughput collector if the default sizing it calculates doesn’t meet application goals. |

## Flags for the G1 collector

| Flag | What it does | When to use it|
|------|--------------|---------------|
| `-XX:MaxGCPauseMillis=N` | Hints to the G1 collector how long pauses should be; the G1 algorithm is adjusted to attempt to meet that goal. | As a first step in tuning the G1 collector; increase this value to attempt to prevent full GCs. |
| `-XX:ConcGCThreads=N` | Sets the number of threads to use for G1 background scanning. | When lots of CPU is available and G1 is experiencing concurrent mode failures. |
| `-XX:InitiatingHeapOccupancyPercent=N` | Sets the point at which G1 background scanning begins. | Lower this value if G1 is experiencing concurrent mode failures. |
| `-XX:G1MixedGCCountTarget=N` | Sets the number of mixed GCs over which G1 attempts to free regions previously identified as containing mostly garbage. | Lower this value if G1 is experiencing concurrent mode failures; increase it if mixed GC cycles take too long. |
| `-XX:G1MixedGCCountTarget=N` | Sets the number of mixed GCs over which G1 attempts to free regions previously identified as containing mostly garbage. | Lower this value if G1 is experiencing concurrent mode failures; increase it if mixed GC cycles take too long. |
| `-XX:G1HeapRegionSize=N` | Sets the size of a G1 region. | Increase this value for very large heaps, or when the application allocates very, very large objects. |
| `-XX:+UseStringDeduplication` | Allows G1 to eliminate duplicate strings. | Use for programs that have a lot of duplicate strings and when interning is impractical. |

## Flags for the CMS collector

| Flag | What it does | When to use it|
|------|--------------|---------------|
| `-XX:CMSInitiating​OccupancyFraction=N` | Determines when CMS should begin background scanning of the old generation. | When CMS experiences concurrent mode failures, reduces this value. |
| `-XX:+UseCMSInitiating​OccupancyOnly` | Causes CMS to use only `CMSInitiatingOccupancyFraction` to determine when to start CMS background scanning. | Whenever `CMSInitiatingOccupancyFraction` is specified. |
| `-XX:ConcGCThreads=N` | Sets the number of threads to use for CMS background scanning. | When lots of CPU is available and CMS is experiencing concurrent mode failures. |
| `-XX:+CMSIncrementalMode` | Runs CMS in incremental mode. No longer supported. | N/A |

## Flags for memory management

| Flag | What it does | When to use it|
|------|--------------|---------------|
| `-XX:+HeapDumpOnOutOfMemoryError` | Generates a heap dump when the JVM throws an out-of-memory error. | Enable this flag if the application throws out-of-memory errors due to the heap space or permgen, so the heap can be analyzed for memory leaks. |
| `-XX:HeapDumpPath=<path>` | Specifies the filename where automatic heap dumps should be written. | To specify a path other than _java\_pid<pid>.hprof_ for heap dumps generated on out-of-memory errors or GC events (when those options have been enabled). |
| `-XX:GCTimeLimit=<N>` | Specifies the amount of time the JVM can spend performing GC without throwing an `OutOfMemoryException` . | Lower this value to have the JVM throw an OOME sooner when the program is executing too many GC cycles. |
| `-XX:HeapFreeLimit=<N>` | Specifies the amount of memory the JVM must free to prevent throwing an `OutOfMemoryException` . | Lower this value to have the JVM throw an OOME sooner when the program is executing too many GC cycles. |
| `-XX:SoftRefLRUPolicyMSPerMB=N` | Controls how long soft references survive after being used. | Decrease this value to clean up soft references more quickly, particularly in low-memory conditions. |
| `-XX:MaxDirectMemorySize=N` | Controls how much native memory can be allocated via the `allocateDirect()` method of the `ByteBuffer` class. | Consider setting this if you want to limit the amount of direct memory a program can allocate. It is no longer necessary to set this flag to allocate more than 64 MB of direct memory. |
| `-XX:+UseLargePages` | Directs the JVM to allocate pages from the operating system’s large page system, if applicable. | If supported by the OS, this option will generally improve performance. |
| `-XX:+StringTableSize=N` | Sets the size of the hash table the JVM uses to hold interned strings. | Increase this value if the application performs a significant amount of string interning. |
| `-XX:+UseCompressedOops` | Emulates 35-bit pointers for object references. | This is the default for heaps that are less than 32 GB in size; there is never an advantage to disabling it. |
| `-XX:+PrintTLAB` | Prints summary information about TLABs in the GC log. | When using a JVM without support for JFR, use this to ensure that TLAB allocation is working efficiently. |
| `-XX:TLABSize=N` | Sets the size of the TLABs. | When the application is performing a lot of allocation outside TLABs, use this value to increase the TLAB size. |
| `-XX:-ResizeTLAB` | Disables resizing of TLABs. | Whenever `TLABSize` is specified, make sure to disable this flag. |

## Flags for native memory tracking

| Flag | What it does | When to use it|
|------|--------------|---------------|
| `-XX:NativeMemoryTracking= X` | Enable Native Memory Tracking. | When you need to see what memory the JVM is using outside the heap. |
| `-XX:+PrintNMTStatistics` | Prints Native Memory Tracking statistics when the program terminates. | When you need to see what memory the JVM is using outside the heap. |

## Flags for thread handling

| Flag | What it does | When to use it|
|------|--------------|---------------|
| `-Xss<N>` | Sets the size of the native stack for threads. | Decrease this size to make more memory available for other parts of the JVM. |
| `-XX:-BiasedLocking` | Disables the biased locking algorithm of the JVM. | Can help performance of thread pool–based applications. |

## Miscellaneous JVM flags

| Flag | What it does | When to use it|
|------|--------------|---------------|
| `-XX:+CompactStrings` | Uses 8-bit string representations when possible (Java 11 only). | Default; always use. |
| `-XX:-StackTraceInThrowable` | Prevents the stack trace from being gathered whenever an exception is thrown. | On systems with very deep stacks where exceptions are frequently thrown (and where fixing the code to throw fewer exceptions is not a possibility). |
| `-Xshare` | Controls class data sharing. | Use this flag to make new CDS archives for application code. |

## Flags for Java Flight Recorder

| Flag | What it does | When to use it|
|------|--------------|---------------|
| `-XX:+FlightRecorder` | Enables Java Flight Recorder. | Enabling Flight Recorder is always recommended, as it has little overhead unless an actual recording is happening (in which case, the overhead will vary depending on the features used, but still be relatively small). |
| `-XX:+FlightRecorderOptions` | Sets options for a default recording via the command line (Java 8 only). | Control how a default recording can be made for the JVM. |
| `-XX:+StartFlightRecorder` | Starts the JVM with the given Flight Recorder options. | Control how a default recording can be made for the JVM. |
| `-XX:+UnlockCommercialFeatures` | Allows the JVM to use commercial (non-open-source) features. | If you have the appropriate license, setting this flag is required to enable Java Flight Recorder in Java 8. |
