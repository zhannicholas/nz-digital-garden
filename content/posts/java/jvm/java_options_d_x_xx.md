---
author: Nicholas Zhan
title: "JVM 参数：`-D`、`-X`、`-XX`，傻傻分不清？"
date: 2023-06-18T20:30:41+08:00
draft: false
language: zh-CN
featured_image:
summary: 
categories:
    - Java
tags:
    - Java
---

JVM 参数众多，我们常在启动一个 Java 程序时通过命令行（例如：`java -jar app.jar`）指定各种参数选项。很多同学就会对此感到疑惑，为什么有时候要用 `-D`，有时候却要用 `-X`，还有些时候用的却是 `-XX` 呢？

![](/images/java/jvm/java_d_x_xx.jpg)

今天，我就在这篇文章中讲一讲这些选项之间的差异。看完这篇文章之后，你将学到 JVM 选项的主要分类、不同分类的选项的主要用途，以及如何找出 JVM 支持的各种配置项。

> 想直接知道答案的同学可以直接跳到文章结尾。

## JVM 选项分类

JVM 其实支持三种类型的选项：标准选项（standard options）、非标准选项（non-standard options，又叫 extra-options）和高级选项（advanced options）。之所以有这么多选项，是因为 JVM 只是一个规范，它有不同的实现，例如 HotSpot、OpenJ9、GraalVM、Azul Zing 等。不同的 JVM 实现支持的选项会有所不同，但是有些选项是所有的 JVM 实现都会支持的，这类选项就是标准选项。

## 标准选项

标准选项是所有 JVM 实现都会支持的。打开控制台，输入 `java`，你不仅能看到 `java` 命令的使用手册，还能看到你机器上默认的 JVM 所支持的所有标准选项：
```bash
~$ java
Usage: java [options] <mainclass> [args...]
           (to execute a class)
   or  java [options] -jar <jarfile> [args...]
           (to execute a jar file)
   or  java [options] -m <module>[/<mainclass>] [args...]
       java [options] --module <module>[/<mainclass>] [args...]
           (to execute the main class in a module)
   or  java [options] <sourcefile> [args]
           (to execute a single source-file program)

 Arguments following the main class, source file, -jar <jarfile>,
 -m or --module <module>/<mainclass> are passed as the arguments to
 main class.

 where options include:

    -zero         to select the "zero" VM
    -dcevm        to select the "dcevm" VM
    -cp <class search path of directories and zip/jar files>
    -classpath <class search path of directories and zip/jar files>
    --class-path <class search path of directories and zip/jar files>
                  A : separated list of directories, JAR archives,
                  and ZIP archives to search for class files.
    -p <module path>
    --module-path <module path>...
                  A : separated list of directories, each directory
                  is a directory of modules.
    --upgrade-module-path <module path>...
                  A : separated list of directories, each directory
                  is a directory of modules that replace upgradeable
                  modules in the runtime image
    --add-modules <module name>[,<module name>...]
                  root modules to resolve in addition to the initial module.
                  <module name> can also be ALL-DEFAULT, ALL-SYSTEM,
                  ALL-MODULE-PATH.
    --enable-native-access <module name>[,<module name>...]
                  modules that are permitted to perform restricted native operations.
                  <module name> can also be ALL-UNNAMED.
    --list-modules
                  list observable modules and exit
    -d <module name>
    --describe-module <module name>
                  describe a module and exit
    --dry-run     create VM and load main class but do not execute main method.
                  The --dry-run option may be useful for validating the
                  command-line options such as the module system configuration.
    --validate-modules
                  validate all modules and exit
                  The --validate-modules option may be useful for finding
                  conflicts and other errors with modules on the module path.
    -D<name>=<value>
                  set a system property
    -verbose:[class|module|gc|jni]
                  enable verbose output for the given subsystem
    -version      print product version to the error stream and exit
    --version     print product version to the output stream and exit
    -showversion  print product version to the error stream and continue
    --show-version
                  print product version to the output stream and continue
    --show-module-resolution
                  show module resolution output during startup
    -? -h -help
                  print this help message to the error stream
    --help        print this help message to the output stream
    -X            print help on extra options to the error stream
    --help-extra  print help on extra options to the output stream
    -ea[:<packagename>...|:<classname>]
    -enableassertions[:<packagename>...|:<classname>]
                  enable assertions with specified granularity
    -da[:<packagename>...|:<classname>]
    -disableassertions[:<packagename>...|:<classname>]
                  disable assertions with specified granularity
    -esa | -enablesystemassertions
                  enable system assertions
    -dsa | -disablesystemassertions
                  disable system assertions
    -agentlib:<libname>[=<options>]
                  load native agent library <libname>, e.g. -agentlib:jdwp
                  see also -agentlib:jdwp=help
    -agentpath:<pathname>[=<options>]
                  load native agent library by full pathname
    -javaagent:<jarpath>[=<options>]
                  load Java programming language agent, see java.lang.instrument
    -splash:<imagepath>
                  show splash screen with specified image
                  HiDPI scaled images are automatically supported and used
                  if available. The unscaled image filename, e.g. image.ext,
                  should always be passed as the argument to the -splash option.
                  The most appropriate scaled image provided will be picked up
                  automatically.
                  See the SplashScreen API documentation for more information
    @argument files
                  one or more argument files containing options
    -disable-@files
                  prevent further argument file expansion
    --enable-preview
                  allow classes to depend on preview features of this release
To specify an argument for a long option, you can use --<name>=<value> or
--<name> <value>.
```

上面的命令输出的内容还是有点多，不过你可以看到：
* 选项 `-cp` 是用来指定 Class Path 的
* 选项 `-version` 是用来输出 Java 的版本信息的
* 通过 `-D` 可以设置系统属性
* `-X` 可以输出有关非标准选项（额外选项）的帮助信息
* ……

看到没有，我们前面提到的 `-D` 和 `-X` 都出现了。并且 Java 告诉我们， `-D` 是 JVM 的标准选项。

Java 允许我们以 `-D<name>=<value>` 这种键值对的形式设置系统属性，例如：`-Duser=Alice` 就设置了简单的系统属性，它的键为 `user`，值为 `Alice`。随后，我们就可以在程序中检索出 `user` 的值：
```java
System.getProperty("user"); //Alice
```

此外，我们也可以通过代码覆盖这个属性的值：
```java
System.setProperty("user", "Bob");
```

现在大家应该明白 `-D` 的用途了吧：**`-D` 是 JVM 的标准选项，我们可以通过它设置系统属性**。

## 非标准选项

和标准选项类似，我们也可以直接通过 `java` 命令获取 JVM 支持的所有非标准选项。
```bash
$ java -X

    -Xbatch           disable background compilation
    -Xbootclasspath/a:<directories and zip/jar files separated by :>
                      append to end of bootstrap class path
    -Xcheck:jni       perform additional checks for JNI functions
    -Xcomp            forces compilation of methods on first invocation
    -Xdebug           does nothing. Provided for backward compatibility.
    -Xdiag            show additional diagnostic messages
    -Xfuture          enable strictest checks, anticipating future default.
                      This option is deprecated and may be removed in a
                      future release.
    -Xint             interpreted mode execution only
    -Xinternalversion
                      displays more detailed JVM version information than the
                      -version option
    -Xlog:<opts>      Configure or enable logging with the Java Virtual
                      Machine (JVM) unified logging framework. Use -Xlog:help
                      for details.
    -Xloggc:<file>    log GC status to a file with time stamps.
                      This option is deprecated and may be removed in a
                      future release. It is replaced by -Xlog:gc:<file>.
    -Xmixed           mixed mode execution (default)
    -Xmn<size>        sets the initial and maximum size (in bytes) of the heap
                      for the young generation (nursery)
    -Xms<size>        set initial Java heap size
    -Xmx<size>        set maximum Java heap size
    -Xnoclassgc       disable class garbage collection
    -Xrs              reduce use of OS signals by Java/VM (see documentation)
    -Xshare:auto      use shared class data if possible (default)
    -Xshare:off       do not attempt to use shared class data
    -Xshare:on        require using shared class data, otherwise fail.
                      This is a testing option and may lead to intermittent
                      failures. It should not be used in production environments.
    -XshowSettings    show all settings and continue
    -XshowSettings:all
                      show all settings and continue
    -XshowSettings:locale
                      show all locale related settings and continue
    -XshowSettings:properties
                      show all property settings and continue
    -XshowSettings:vm
                      show all vm related settings and continue
    -XshowSettings:system
                      (Linux Only) show host system or container
                      configuration and continue
    -Xss<size>        set java thread stack size
    -Xverify          sets the mode of the bytecode verifier
                      Note that option -Xverify:none is deprecated and
                      may be removed in a future release.
    --add-reads <module>=<target-module>(,<target-module>)*
                      updates <module> to read <target-module>, regardless
                      of module declaration.
                      <target-module> can be ALL-UNNAMED to read all unnamed
                      modules.
    --add-exports <module>/<package>=<target-module>(,<target-module>)*
                      updates <module> to export <package> to <target-module>,
                      regardless of module declaration.
                      <target-module> can be ALL-UNNAMED to export to all
                      unnamed modules.
    --add-opens <module>/<package>=<target-module>(,<target-module>)*
                      updates <module> to open <package> to
                      <target-module>, regardless of module declaration.
    --limit-modules <module name>[,<module name>...]
                      limit the universe of observable modules
    --patch-module <module>=<file>(:<file>)*
                      override or augment a module with classes and resources
                      in JAR files or directories.
    --source <version>
                      set the version of the source in source-file mode.

These extra options are subject to change without notice.
```
`-X` 开头的选项属于非标准选项。很多小伙伴应该能从上面的输出内容中看到两个颇为熟悉的选项：`-Xms<size>` 和 `-Xmx<size>`。这两个参数是用来设置 JVM 堆大小的，前者设置的是堆大小的初始值和最小值，而后者设置的是堆大小的最大值。例如 `-Xms100m -Xmx1g` 会在启动 JVM 的时候将堆的初始大小设置为 100MB，允许堆最多使用 1GB 的内存。

需要注意的是，上面输出的这些非标准选项并不是所有 JVM 都支持，它们只是你机器上安装的 JVM 所支持的一些通用选项。非标准选项的支持是与 JVM 的具体实现紧密相关的，并且这些非标准选项可能在不通知你的情况下悄悄地被改变。

## 高级选项

高级选项以 `-XX` 开头，这些选项一般用于开发者调整 JVM 的行为、性能或输出调试信息等。根据参数值类型的不同，高级选项又可以分为两类：布尔类型的选项和带参数的选项。

### 布尔选项

布尔类型的选项不带参数，只是一个开关。开关是不需要参数的，你可以通过 `+` 启用某个功能（`-XX:+Option`），而通过 `-` 禁用某个功能（`-XX:-Option`）。例如，在 [HotSpot JVM](https://docs.oracle.com/en/java/javase/17/docs/specs/man/java.html#) 中，你可以通过 `-XX:+Inline` 启用方法内联。不过 HotSpot 为了提高性能，默认是开启了方法内联的，所以你可以通过 `-XX:-Inline` 关闭方法内联。

### 带参选项

还有一类高级选项是需要设置相应的参数值的，形式一般为：`-XX:OptionName=OptionValue`。我们来看一些例子：
* `-XX:ErrorFile=file.log` 告诉 JVM：当不可恢复的错误发生时，将错误信息写入 `file.log` 这个文件。
* `-XX:TreadStackSize=256k` 将线程栈的大小设置为 `256k`。
* `-XX:MaxHeapSize=1g` 将堆的最大大小限制为 `1GB`，等价于 `-Xmx1g`。
* ……

### 如何查看 JVM 的所有高级选项

> 这一部分涉及的命令适用于 HotSpot JVM，其它的 JVM 实现可能也支持相同或类似的功能。

JVM 可以自动根据机器的配置等信息调整一些选项的值，如果你想知道某些选项在 JVM 自动微调之前的初始值，那么你可以使用 `-XX:+PrintFlagsInitial`。

如果你想知道某些选项在 JVM 中实际生效的值，你可以使用 `-XX:+PrintFlagsFinal`。

如果你想知道哪些选项是被用户或 JVM 特意设置过，你可以使用 `-XX:+PrintCommandLineFlags`。

#### `-XX:+PrintFlagsInitial` 和 `-XX:+PrintFlagsFinal`

`-XX:+PrintFlagsFinal` 会打印出 JVM 最终在运行 Java 代码时采用的选项值，而 `-XX:+PrintFlagsInitial` 会打印出各个选项在 JVM 进行微调之前的默认值。
比较这两个的输出结果会非常有意思，你能学到 JVM 自动调整了哪些选项、你覆盖了哪些选项、这些选项的类型是什么、值是多少、选项是否与特定的平台有关等等。

我们先从 `java -XX:+PrintFlagsFinal -version` 开始。
```bash
$ java -XX:+PrintFlagsFinal -version
[Global flags]
// ...
size_t AsyncLogBufferSize                       = 2097152                                   {product} {default}
  intx AutoBoxCacheMax                          = 128                                    {C2 product} {default}
  intx BCEATraceLevel                           = 0                                         {product} {default}
  bool BackgroundCompilation                    = true                                   {pd product} {default}
size_t BaseFootPrintEstimate                    = 268435456                                 {product} {default}
  intx BiasedLockingBulkRebiasThreshold         = 20                                        {product} {default}
  intx BiasedLockingBulkRevokeThreshold         = 40                                        {product} {default}
  intx BiasedLockingDecayTime                   = 25000                                     {product} {default}
  intx BiasedLockingStartupDelay                = 0                                         {product} {default}
// ... 
openjdk version "17.0.5" 2022-10-18
OpenJDK Runtime Environment (build 17.0.5+8-Ubuntu-2ubuntu120.04)
OpenJDK 64-Bit Server VM (build 17.0.5+8-Ubuntu-2ubuntu120.04, mixed mode, sharing)
```

这条命令输出的内容非常多，有几百行，所以我只截取了其中的一部分。命令输出的第一行是 `[Global flags]`，随后的每一行都是 JVM 的一个配置项，最后面的几行是 JDK 的版本信息（也就是 `java -version` 的输出内容）。

第一行和最后三行之间的每一行都是一个选项。每个选项包含五列：
* 第一列是选项的类型，例如 `intx`
* 第二列是选项的名称，例如 `AsyncLogBufferSize`
* 第三列是 `=`。有些版本的 JDK 采用 `=` 表示默认值，`:=` 表示选项的值被覆盖过。但是测试发现 OpenJDK 17 已经不会像之前的 JDK 版本那样输出 `:=` 了。
* 第四列是选项的值
* 第五列是选项的类型。例如：`{product}` 表示该配置项是产品自带的（平台无关的），`{pd product}` 表示该配置项是平台相关的（platform-dependent），`{manageable}` 则表示配置项的值可以在运行时被动态调整。
* 第六列表示第四列值的来源。例如：`{default}` 表示选项目前是默认值，`{ergonomic}` 表示该选项是 JVM 自动微调设置的，`{command line}` 表示该选项是通过命令行设置的。
```bash
$ java -XX:+PrintFlagsFinal -version | grep command
     bool PrintFlagsFinal                          = true                                      {product} {command line}
```

其实还有第七列，但是由于我的 JVM 是产品版本的，没有相关支持。只有 debug 版本的 JVM 才支持：
```bash
$ java -XX:+PrintFlagsWithComments -version
Error: VM option 'PrintFlagsWithComments' is notproduct and is available only in debug version of VM.
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
```

相关细节可以在 JDK 的[源码](https://github.com/openjdk/jdk17/blob/4afbcaf55383ec2f5da53282a1547bac3d099e9d/src/hotspot/share/runtime/flags/jvmFlag.cpp#L150) 中找到。

`-XX:+PrintFlagsInitial`：打印出各个 JVM 参数的默认值。它的输出内容和 `-XX:PrintFlagsFinal` 类似。

在 OpenJDK 17 中，这两个选项输出行数是一样的，都是 559 行：
```bash
$ java -XX:+PrintFlagsInitial -version | wc -l
559
$ java -XX:+PrintFlagsFinal -version | wc -l
openjdk version "17.0.5" 2022-10-18
OpenJDK Runtime Environment (build 17.0.5+8-Ubuntu-2ubuntu120.04)
OpenJDK 64-Bit Server VM (build 17.0.5+8-Ubuntu-2ubuntu120.04, mixed mode, sharing)
559
```

比较这两个选项的输出非常有趣，你可以使用：
```bash
$ diff <(java -XX:+PrintFlagsInitial -version) <(java -XX:+PrintFlagsFinal -version)
```

但这 559 个选项并不就是 HotSpot 支持的所有选项，因为还有些选项是用于诊断虚拟机的，你可以使用 `-XX:+UnlockDiagnosticVMOptions`：
```bash
$ java -XX:+UnlockDiagnosticVMOptions -XX:+PrintFlagsFinal -version | wc -l
openjdk version "17.0.5" 2022-10-18
OpenJDK Runtime Environment (build 17.0.5+8-Ubuntu-2ubuntu120.04)
OpenJDK 64-Bit Server VM (build 17.0.5+8-Ubuntu-2ubuntu120.04, mixed mode, sharing)
771
```

还有些选项是实验性的，你可以使用 `-XX:+UnlockExperimentalVMOptions`：
```bash
$ java -XX:+UnlockDiagnosticVMOptions -XX:+UnlockExperimentalVMOptions -XX:+PrintFlagsFinal -version | w
c -l
openjdk version "17.0.5" 2022-10-18
OpenJDK Runtime Environment (build 17.0.5+8-Ubuntu-2ubuntu120.04)
OpenJDK 64-Bit Server VM (build 17.0.5+8-Ubuntu-2ubuntu120.04, mixed mode, sharing)
891
```

891 个了！但这还不是全部！从 Java 9 开始，[JVMCI](https://openjdk.java.net/jeps/243) 允许我们使用基于 Java 的编译器接口：
```bash
$ java -XX:+UnlockDiagnosticVMOptions -XX:+UnlockExperimentalVMOptions -XX:+JVMCIPrintProperties -XX:+EnableJVMCI -XX:+PrintFlagsFinal -version | wc -l
908
```


#### `-XX:+PrintCommandLineFlags`

`-XX:+PrintCommandLineFlags` 也是一个非常有用的参数，它可以打印出那些我们通过命令行指定或者 JVM 自动在命令行上设置的参数。
```bash
java -XX:+PrintCommandLineFlags -version
-XX:ConcGCThreads=2 -XX:G1ConcRefinementThreads=8 -XX:GCDrainStackTargetSize=64 -XX:InitialHeapSize=62422080 -XX:MarkStackSize=4194304 -XX:MaxHeapSize=998753280 -XX:MinHeapSize=6815736 -XX:+PrintCommandLineFlags -XX:ReservedCodeCacheSize=251658240 -XX:+SegmentedCodeCache -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC
openjdk version "17.0.5" 2022-10-18
OpenJDK Runtime Environment (build 17.0.5+8-Ubuntu-2ubuntu120.04)
OpenJDK 64-Bit Server VM (build 17.0.5+8-Ubuntu-2ubuntu120.04, mixed mode, sharing)
```

## 总结

* JVM 包含多种不同类型的参数选项
* `-D` 用来设置系统属性，属于标准选项
* `-X` 设置非标准选项，支持的选项范围跟具体的 JVM 实现有关
* `-XX` 设置高级选项，允许开发者调整 JVM 的行为、性能、输出调试信息，支持的选项范围也跟具体的 JVM 实现有关
* 布尔类型的高级选项是起到功能的开关作用，不带参数。使用 `+` 启用功能，使用 `-` 禁用功能；对于带参数的高级选项，需要指定参数值
* 使用 `java -XX:+UnlockDiagnosticVMOptions -XX:+UnlockExperimentalVMOptions -XX:+PrintFlagsFinal -version` 命令可以查看 JVM 所有的选项
