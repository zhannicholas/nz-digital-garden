---
tags:
- Bash
title: Bash
categories:
date: 2022-08-26
lastMod: 2022-08-26
---


# Bash 与 Bash 脚本


  + ## Shell


    + Shell 脚本是解释执行的。

      + {{< logseq/orgQUOTE >}}Shell scripts are **interpreted**, not compiled. The shell reads commands from the script line per line and searches for those commands on the system, while a compiler converts a program into machine readable form, an executable file - which may then be used in a shell script.
{{< / logseq/orgQUOTE >}}

    + Shell 有很多种，常见的有 sh（Bourne Shell）、bash（Bourne Again shell）、csh（C shell）、tcsh（TENEX C shell）、ksh（Korn shell）、tmux等

    + 查看系统内已经有的 shell：`cat/etc/shells`

    + 查看当前用户默认的 shell：`cat/etc/passwd | grep $USER` （输出的最后就是当前用户默认的 shell，一般都是 bash）

  + ## Bash


    + Bash 是 Shell 的一种，具备很多 Shell 不具备的特性。

    + ### Bash  启动时加载的文件


      + 当 bash 在不同的情况下被调用时（invoked），它会在启动时会读取并执行一些特定的文件，文件的读取顺序会在下列出，如果某个文件不存在，bash 就会继续查找下一个文件。

      + #### *Invoked as an interactive login shell* *，* *or with `--login`*


        + 交互式（interactive）bash 意味着我们可以输入命令，shell 通常会从它连接到的终端（terminal）读取命令，然 将执行结果输出到终端。而 login shell 则表示系统会在使用者使用 shell 之前对其进行身份的认证（通常要使用提供用户名和密码）。这种情况下 bash 启动时读取的文件有：

          + `/etc/profile`

          + `~/.bash_profile`, `~/.bash_login` 或 `~/.profile`：第一个存在的可读文件会被读取

          + `~/.bash_logout`：退出时读取

      + #### *Invoked as an interactive non-login shell*


        + non-login shell 表示系统在使用者使用 shell 之前不会对其进行身份的认证。这种情况下读取的文件有：

          + `~/.bashrc`

      + #### *Invoked non-interactively*


        + 所有 bash 脚本使用的都是非交互式（non-interactively）shell。这时读取的文件有：

          + 由变量 `BASH_ENV` 定义的文件，不过在搜索这个文件时并不会用上 `PATH`

      + #### *Invoked with the sh command*


        + 这个时候读取的文件有：


          + `/etc/profile`

          + `~/.profile`

    + ### 如何判断一个 shell 是否是交互式的呢


      + 通过查看特殊参数 `-` 的内容，我们可以判断当前 shell 是否是交互式的。如果它的内容含有字母 `i`，则说明当前 shell 是交互式 shell，否则是非交互式 shell。

`bash
~$ echo $-
himBHs
~$ echo 'echo $-' > ./non-interactive-shell.sh
~$ chmod +x ./non-interactive-shell.sh
~$ ./non-interactive-shell.sh
hB
`

  + ## Shell  语法


    + 如果输入不是注释（comment），shell 会读入并将其拆分成单词（word）和操作符（operator），使用 quoting rules 定义输入中每个字符的含义。然后，这些单词和操作符会被翻译成命令和其它结构（constructs）。Shell分析输入的过程如下：

      + Shell 从文件、字符串或用户的终端读取输入

      + 按照 quoting rules 将输入分解为单词和操作符。这些符号（token）被元字符（metacharacter）分隔。别名（alias）展开被执行

      + Shell 将这些符号解析（分析和替换）成为简单命令或复合命令

      + Bash 执行各种 shell 展开（expansion），将展开的符号分解成文件名（filename）、命令（command）和参数（argument）列表

      + 如果有必要的话，重定向被执行。重定向操作符和它们的操作数被从参数列表中移除

      + 命令被执行

      + （可选）shell 等待命令执行完毕并收集命令的退出状态。

# 编写并调试脚本


  + Shell 脚本的第一行应该以 `#!` 开始，后面是执行此脚本的 shell 的路径。例如：`#!/bin/bash` 。空白行也算作有效行。

  + ## 调试脚本


    + 若要调试（debug）整个脚本，可以使用 bash 的 `-x`（在执行命令之前打印命令的 trace 信息）选项，例如：`bash -x your_script.sh`。

    + 而 bash内置的 `set` 可以允许我们只展示关注部分的调试信息，`set` 可以在脚本中使用任意多次。被调试的部分位于 `set -x` （开启调试）和 `set +x`（关闭调试），注意 `+/-`的区别。

# 变量（ Variables ）

  + Shell 变量通常是大写的。按照作用域的不同，变量分为全局变量（global variables）和局部变量（local variables）。


    + 全局变量也就是我们通常说的环境变量（environment variables），对所有的 shell 都有效。查看环境变量的命令：`env` 或 `printenv`。

    + 局部变量只对当前的 shell 有效。使用 shell 内置的 set 命令（不带任何选项参数）就可以输出所有变量（包括环境变量）和一系列的函数。

  + 除了作用域，变量也可以按内容的类型进行分类，有四种：


    + 字符串变量（String variables）

    + 整型变量（Integer variables）

    + 常量变量（Constant variables）

    + 数组变量（Array variables）

  + ## 创建变量


    + 变量名是大小写敏感的，默认大写。有时候，我们也会用小写命名局部变量，而用大写命名全局变量。变量名可以包含数字，但是不能以数字开头。定义变量的语法为：`VARNAME="value"`。与我们常用的编程语言不同，**shell 中变量定义中的等号两侧是不能有空格的**，否则会出现错误。

  + ## 导出变量


    + 在shell 中创建的变量只对当前 shell 有效，它们都是局部变量。如果我们在当前 shell 中使用 `bash` 或类似的命令开启一个子 shell，父 shell 中定义的变量是不可用的。要将变量传递到子 shell 中，我们可以使用 `export` 命令导出变量，导出后的变量就会被当作环境变量对待。

  + ## Shell  的保留变量（Reserved variables）


    + 太多了，具体查看：[https://tldp.org/LDP/Bash-Beginners-Guide/html/sect_03_02](https://tldp.org/LDP/Bash-Beginners-Guide/html/sect_03_02).

    + 比如：`PATH`、`HOME`、`PS1`、`PS2`、`PWD`、`UID` 等。`PS` 即 `prompt string`。

  + ## 特殊参数（ Special parameters）


    + 有一些参数我们只能引用，而不能对它们进行赋值，因为这些参数会被 Shell 特殊处理。

      + | Character | Definition|
|------------|----------|
| $* | Expands to the positional parameters, starting from one. When the expansion occurs within double quotes, it expands to a single word with the value of each parameter separated by the first character of the IFS special variable. |
| $@ | Expands to the positional parameters, starting from one. When the expansion occurs within double quotes, each parameter expands to a separate word. |
| $# | Expands to the number of positional parameters in decimal. |
| $? | Expands to the exit status of the most recently executed foreground pipeline. |
| $- | A hyphen expands to the current option flags as specified upon invocation, by the **set** built-in command, or those set by the shell itself (such as the -i). |
| $$ | Expands to the process ID of the shell. |
| $! | Expands to the process ID of the most recently executed background (asynchronous) command. |
| $0 | Expands to the name of the shell or shell script. |
| $_ | The underscore variable is set at shell startup and contains the absolute file name of the shell or script being executed as passed in the argument list. Subsequently, it expands to the last argument to the previous command, after expansion. It is also set to the full pathname of each command executed and placed in the environment exported to that command. When checking mail, this parameter holds the name of the mail file. |

    + 

  + ## Quoting Characters


    + 在很多上下文中，很多字符或者单词都有特殊的含义，用引号将它们引起来可以消除这些特殊含义。

      + 转义字符（`\` ）

        + 转义字符（Escape characters）用来移除**其后单个字符**的特殊含义。

      + 单引号（ `''` ）

        + 单引号（Single quotes）被用来移除两个单引号之间所有字符的特殊含义，即单引号中的所有字符都是字面值（literal value）。两个单引号之间是不能有单引号的，即便这个单引号被转义都不行。

      + 双引号（`""` ）

        + 双引号（Doublequotes）会移除两个双引号之间除美元符号（`$`）、反引号（\`）和反斜杠（`\`）之外的所有字符的特殊含义。美元符号和反引号的特殊含义在双引号之间会保留，而反斜杠只有在其后的字符是美元符号、反引号、双引号、反斜杠或换行符时才保留特殊含义，输入流会将这些字符前的反斜杠去掉。

        + 双引号之间是可以有由反斜杠转义的双引号的。

  + ## 变量的类型


    + 很多情况下，我们在定义 shell 变量时并不会显式给出变量类型。如果有必要，我们可以使用 `declare` 语句限制赋值给变量的值的类型。`declare` 语句的语法为：`declare OPTION(s) VARIABLE=value`。支持的 `OPTION(s)` 有：


      + | Option | Meaning |
| ---- | ---- | ---- |
| -a | Variable is an array. |
| -f | Use function names only. |
| -i | The variable is to be treated as an integer; arithmetic evaluation is performed when the variable is assigned a value. |
| -p | Display the attributes and values of each variable. When -p is used, additional options are ignored. |
| -r | Make variables read-only. These variables cannot then be assigned values by subsequent assignment statements, nor can they be unset. |
| -t | Give each variable the *trace* attribute. |
| -x | Mark each variable for export to subsequent commands via the environment. |

    + ### 常量


      + 只读的变量就是常量（constant），使用 `readonly` 就可以创建出不可被改变的变量，即常量。语法为 `readonly OPTION VARIABLE(s)`

    + ### 数组


      + Shell 中的数组就是一个包含多个值的变量，数组的大小没有限制，下标从 `0` 开始。数组的声明方法有两种：

        + 间接声明：`ARRAY[INDEXNR]=value`。

        + 显式声明：`declare -a ARRAYNAME`。

      + 数组变量也可以使用复合赋值的方式创建：`ARRAY=(value1 value2 … valueN)`。

      + 给数组添加缺失的或额外的成员的语法为：`ARRAYNAME[indexnumber]=value`。

      + 删除数组本身或数组成员的命令是：`unset`。

      + 

  + ## 操作变量


    + `${#VAR}`：计算变量包含多少个字符或计算数组中元素的数量

    + `${VAR:-WORD}`：变量替换。若变量 `VAR` 未定义或为 `null`，则替换为 `WORD` 的展开，否则保持 `VAR` 的值

    + `${VAR:OFFSET:LENGTH}`：跳过 `VAR` 开头的 `OFFSET` 个字符，然后截取出 `LENGTH` 个字符

    + `${VAR#WORD}` 或 `${VAR##WORD}`：删除 `VAR` 中匹配上 `WORD` 展开的模式。`#` 和 `##` 分别表示最短匹配和最长匹配

    + `${VAR/PATTERN/STRING}` 或 `${VAR//PATTERN/STRING}`：替换变量中的某些部分

# Shell  展开（shell expansion）


  + ### 大括号展开（ Brace expansion ）

    + 大括号展开的形式为：一个可选的前导符（PREAMBLE）、一组位于一对大括号之间的由逗号分隔的字符串和一个可选的跋（POSTSCRIPT）。例如：

`bash
~$ echo sp{el,il,al}l
spell spill spall
`

  + ### 波浪号展开（ tilde expansion ）

    + 如果一个单词以没有被引起来的波浪号（`~`）开始，则在第一个没有被引起来的斜杠（若没有斜杠，则一直到最后一个字符）之前的字符将被视作波浪号前缀（tidle-prefix）。如果波浪号前缀中没有字符被引起来，那么波浪号前缀中的这些字符就会被当作一个可能的登录用户名。如果这个登录用户名是 `null` 字符串，则波浪号被替换为 shell 变量 HOME。如果 HOME 变量没有被设置，则替换为执行这个 shell 的用户的主目录。

    + 如果波浪号前缀是`~+`，那么它会被替换为变量 `PWD` 的值。如果波浪号前缀是`~-`，那么它会被替换为变量 `OLDPWD` 的值。

  + ### Shell  参数和变量展开

    + 美元符号（`$`）用于参数展开、命令替换或算术展开。被展开的参数名或符号可能被包裹在大括号中。

    + 最基本的参数展开的形式是`${PARAMETER}`。如果我们想在某个变量不存在时创建这个变量，则可以使用`${VAR:=value}`

  + ### 命令替换

    + 命令替换（command substitution）允许我们用命令的输出来替换命令本身，它有两种形式：

      + `$(command)`

      + \`command\`

  + ### 算术展开

    + 算术展开（arithmetic expansion）允许我们用一个算术表达式计算得到的值替换表达式本身。它也有两种形式：

      + `$(( EXPRESSION ))`

      + `$[ EXPRESSION ]`

  + ### 进程替换

    + 进程替换（Process substitution）在支持命名管道（FIFO）和命名打开文件的 `/dev/fd` 方法的系统上。形式也有两种：

      + `<(LIST)`

      + `>(LIST)`

  + ### 分词（ word splitting ）

    + Shell 会扫描不在双引号之内的参数展开、命令替换和算术展开的结果，用于分词。分隔符为变量 `IFS` 中的每个字符，默认为`<space><tab><newline>`

  + ### 文件名替换

    + 分词之后，如果 `bash` 的 `-f` 选项没有被设置，`bash` 就会扫描每个单词，寻找字符 `*`、`?`、`[`。如果这三个字符一个都没找到，单词会被视为一个模式（PATTERN），然后被替换为一个按照字段序排列与这个模式相匹配的文件名列表。

  + 

# 正则表达式（Regular expressions）


  + A *regular expression* is a pattern that describes a set of strings.

  + The fundamental building blocks are the regular expressions that match a single character. Most characters, including all letters and digits, are regular expressions that match themselves. Any metacharacter with special meaning may be quoted by preceding it with a backslash.

  + ## Regular expression metacharacters


    + A regular expression may be followed by one of several repetition operators (metacharacters):

      + **Regular expression operators**

        + | Operator | Effect |
| ---- | ---- | ---- |
| . | Matches any single character. |
| ? | The preceding item is optional and will be matched, at most, once. |
| * | The preceding item will be matched zero or more times. |
| + | The preceding item will be matched one or more times. |
| {N} | The preceding item is matched exactly N times. |
| {N,} | The preceding item is matched N or more times. |
| {N,M} | The preceding item is matched at least N times, but not more than M times. |
| - | represents the range if it's not first or last in a list or the ending point of a range in a list. |
| ^ | Matches the empty string at the beginning of a line; also represents the characters not in the range of a list. |
| $ | Matches the empty string at the end of a line. |
| \b | Matches the empty string at the edge of a word. |
| \B | Matches the empty string provided it's not at the edge of a word. |
| \< | Match the empty string at the beginning of word. |
| \> | Match the empty string at the end of word. |

# Sed


  + `sed`（Stream EDitor）可以对从文件或管道读取的文本进行基本的变换操作，然后将结果输出到标准输出。`sed` 本身并不会修改原始的输入。

  + `sed` 可以使用正则表达式进行文本的模式替换和删除。

# Awk


  + `awk` 是一个很流行的 UNIX 流编辑器，而 `gawk` 则是 `awk` 的 GNU 版本。很多情况下，`awk` 被链接到 `gawk`，所以一般都叫 `awk`。`awk` 的名字来源于创建这门编程语言的三个人姓名（Aho、Kernighan 和 Weinberger）的首字母。

  + `awk` 的基本功能是在文件中搜索包含一个或多个模式（pattern）的行或文本单元，当某一行满足某个模式时，会在该行上执行特殊的操作。

  + `awk` 最大的不同在于它是数据驱动（data-driven）的，即”you describe the data you want to work with and then what to do when you find it“。

  + 在运行 `awk` 时，我们需要声明一个`awk`  程序，这个程序告诉 `awk` 应该做什么，它包含一系列的规则（rule），每个规则都声明了一个用于搜索的模式和找到模式后要执行的一个操作（action）。如果程序很短，直接在命令行给出即可（`awk PROGRAM inputfile(s)`），也可以将 `awk` 程序放到一个单独的脚本中，然后使用`-f`  参数读取（`awk -f PROGRAM-FILE inputfile(s)`）。

  + ## `print`


    + `awk` 的 `print` 命令可以用来输出从输入文件选取的数据。`awk` 每读取文件中的一行，就会使用 `FS`（输入字段分隔符，input field seperator）对该行进行拆分。`FS` 是 `awk` 的一个内置变量，通常默认为一个或多个空格（space）或制表符（tab）。拆分后会得到一系列的变量：`$1`、`$2`、…、`$N`，它们分别持有输出行的第`1`、`2`、…、最后一个字段。而变量`$0`  持有的是整行的值。

    + 示例命令： `ls -l | awk '{print $5 $9 }'`

  + ## 使用正则表达式


    + 正则表达式可以被用作模式对文本中的每一行（一行就是一条记录（record））进行测试，使用时只需要将正则表达式用斜杠（`/`）引起来即可，例如：`df -h | awk '/dev/hd/ {print $6 "\t: " $5}'`

    + 完整的语法为：`awk 'EXPRESSION { PROGRAM }' files(s)`

    + ### 特殊模式

      + 若要在 `awk` 处理文本前或处理完文本后执行某些操作，可以分别使用 `BEGIN` 和 `END` 语句。

  + ## `awk`  中的变量


    + 字段分隔符（field seperator）告知 `awk` 如何将记录（record）分割成字段（field），它可以是单个字符，也可以是一个正则表达式。字段分隔符用内置变量 `FS` 表示，我们可以在 `BEGIN` 模式中使用赋值操作符（`=`）对它进行修改，例如`awk 'BEGIN {FS=":" } {print $1 "\t" $5 }' /etc/passwd` 使用冒号（`:`）作为分隔符。

    + 在 `print` 输出时，如果语法正确（即参数之间使用逗号分隔），字段默认使用空格分隔，语法错误会导致输出字段之间的分隔符被省略，字段会一个挨一个的输出。输出字段分隔符（output field seperator）使用变量 `OFS` 表示，我们同样可以在 `BEGIN` 模式中修改它的默认值。

    + 默认情况下，`print` 语句的输出被称为输出记录（output record）。每条 `print` 命令都会产生一条输出记录，然后跟着一个被称为输出记录分隔符（output record separator）的字符串。输出记录分隔符用变量 `ORS` 表示，它的值默认为 `\n`，可以在 `BEGIN` 语句中修改。

    + `awk` 内置的 `NR` 变量记录着已处理的记录的行数，`awk` 每读取一个新的输入行，`NR` 的值就会加一。

# 条件表达式


  + ## `if`


    + 在 shell 中，`if` 表达式的精简语法为：`if TEST-COMMANDS; then CONSEQUENT-CONMMANDS; fi`

    + `TEST-COMMANDS` 列表执行后，如果它的返回状态是 `0`，那么 `CONSEQUENT-COMMANDS` 列表就会被执行，其中最后一条命令的退出状态就是整个 `if` 表达式的返回状态。如果没有条件为 `true`，则返回 `0`。

    + ### Primary Expressions


      + 我们一般将构成 `TEST-COMMANDS` 的表达式称为 `primaries`。

        + **Primary expressions**


          + | Primary | Meaning |
| ---- | ---- | ---- |
| [ -a FILE ] | True if FILE exists. |
| [ -b FILE ] | True if FILE exists and is a block-special file. |
| [ -c FILE ] | True if FILE exists and is a character-special file. |
| [ -d FILE ] | True if FILE exists and is a directory. |
| [ -e FILE ] | True if FILE exists. |
| [ -f FILE ] | True if FILE exists and is a regular file. |
| [ -g FILE ] | True if FILE exists and its SGID bit is set. |
| [ -h FILE ] | True if FILE exists and is a symbolic link. |
| [ -k FILE ] | True if FILE exists and its sticky bit is set. |
| [ -p FILE ] | True if FILE exists and is a named pipe (FIFO). |
| [ -r FILE ] | True if FILE exists and is readable. |
| [ -s FILE ] | True if FILE exists and has a size greater than zero. |
| [ -t FD ] | True if file descriptor FD is open and refers to a terminal. |
| [ -u FILE ] | True if FILE exists and its SUID (set user ID) bit is set. |
| [ -w FILE ] | True if FILE exists and is writable. |
| [ -x FILE ] | True if FILE exists and is executable. |
| [ -O FILE ] | True if FILE exists and is owned by the effective user ID. |
| [ -G FILE ] | True if FILE exists and is owned by the effective group ID. |
| [ -L FILE ] | True if FILE exists and is a symbolic link. |
| [ -N FILE ] | True if FILE exists and has been modified since it was last read. |
| [ -S FILE ] | True if FILE exists and is a socket. |
| [ FILE1 -nt FILE2 ] | True if FILE1 has been changed more recently than FILE2, or if FILE1 exists and FILE2 does not. |
| [ FILE1 -ot FILE2 ] | True if FILE1 is older than FILE2, or is FILE2 exists and FILE1 does not. |
| [ FILE1 -ef FILE2 ] | True if FILE1 and FILE2 refer to the same device and inode numbers. |
| [ -o OPTIONNAME ] | True if shell option "OPTIONNAME" is enabled. |
| [ -z STRING ] | True of the length if "STRING" is zero. |
| [ -n STRING ] or [ STRING ] | True if the length of "STRING" is non-zero. |
| [ STRING1 {{< logseq/mark >}} STRING2 ] | True if the strings are equal. "=" may be used instead of "{{< / logseq/mark >}}" for strict POSIX compliance. |
| [ STRING1 != STRING2 ] | True if the strings are not equal. |
| [ STRING1 < STRING2 ] | True if "STRING1" sorts before "STRING2" lexicographically in the current locale. |
| [ STRING1 > STRING2 ] | True if "STRING1" sorts after "STRING2" lexicographically in the current locale. |
| [ ARG1 OP ARG2 ] | "OP" is one of -eq, -ne, -lt, -le, -gt or -ge. These arithmetic binary operators return true if "ARG1" is equal to, not equal to, less than, less than or equal to, greater than, or greater than or equal to "ARG2", respectively. "ARG1" and "ARG2" are integers. |

    + ### Combining Expressions


      + 表达式之间也可以进行组合

      + **Combining expressions**


        + | Operation | Effect |
| ---- | ---- | ---- |
| [ ! EXPR ] | True if **EXPR** is false. |
| [ ( EXPR ) ] | Returns the value of **EXPR**. This may be used to override the normal precedence of operators. |
| [ EXPR1 -a EXPR2 ] | True if both **EXPR1** and **EXPR2** are true. |
| [ EXPR1 -o EXPR2 ] | True if either **EXPR1** or **EXPR2** is true. |

    + The `[` (or `test`) built-in evaluates conditional expressions using a set of rules based on the number of arguments.

    + {{< logseq/orgTIP >}}`[]` 与 `[[]]` 的区别在于：`[[` 会阻止 shell 进行变量名的分词操作、阻止路径名展开。
{{< / logseq/orgTIP >}}

  + ## `if/then/else`


    + `if/then/else` 的语法为：`if TEST-COMMANDS; then CONSEQUENT-CONMMANDS; else ALTERNATE-CONSEQUENT-COMMANDS; fi`。

  + ## `if/then/elif/else`


    + `if` 表达式的完整的语法为：`if TEST-COMMANDS; then CONSEQUENT-CONMMANDS; elif MORE-TEST-COMMANDS; then MORE-CONSEQUENT-COMMANDS; else ALTERNATE-CONSEQUENT-COMMANDS; fi`。

  + ## `case`


    + `case` 表达式的语法为：`case EXPRESSION in CASE1) COMMAND-LIST;; CASE2) COMMAND-LIST;;… CASEN) COMMAND-LIST;; easc`。

# 循环


  + ## `for`

    + For 循环的语法为：`for NAME [in LIST ]; do COMMANDS; done`。

    + 如果`[in LIST]`不存在，则它会被替换为 `in $@`，`for` 循环会为每一个位置参数执行一遍 `COMMANDS`。

  + ## `while`

    + `while` 循环的语法为：`while CONTROL-COMMAND; do CONSEQUENT-COMMANDS; done`。

  + ## `until`

    + `until` 循环的语法为：`until TEST-COMMAND; do CONSEQUENT-COMMANDS; done`。

  + ## `select`

    + `select` 一般用于生成菜单，语法为：`select WORD [in LIST]; do RESPECTIVE-COMMANDS; done`。

  + 

# 函数

  + 在 shell 中，定义函数的语法为：`function FUNCTION {CONMMANDS; }` 或 `FUNCTION() { COMMANDS;}`。

  + 大括号内最后一条命令的退出状态就是整个函数的退出状态。大括号与函数体之间必须有空白符，否则会出错。函数体应该以分号或者一个新行结尾。

# 重定向与文件描述符


  + 文件输入和输出是通过整数句柄（integer handle）实现的，这些整数句柄跟踪着指定进程打开的所有文件。这些数字就是文件描述符（file descriptor）。

  + 最常见的文件描述符是标准输入（stdin）、标准输出（stdout）和标准错误（stderr），它们的文件描述符数字分别是`0`、`1`、`2`。这些数字和相应的设备都是系统保留的，我们可以通过命令 `ls -l /dev/std` 查看。

  + 每个进程看到的 `/proc/self` 目录下的东西都不一样，因为 `/proc/self` 实际上是一个指向 `/proc/<process_ID>` 的符号链接。

  + 当执行一条命令时，会遵循以下步骤：


    + 如果上一条命令的标准输出被管道到当前命令的标准输入，那么 `/proc/<current_process_ID>/fd/0` 会被更新为与
  `/proc/<previous_process_ID>/fd/1` 相同的匿名管道

    + 如果当前命令的标准输出被管道到下一条命令的标准输入，那么 `/proc/<current_process_ID>/fd/1` 会被更新为另一个匿名管道

    + 当前命令中的重定向从左到右依次被处理

    + 一个命令后的重定向 `N>&M` 或 `N<&M` 的效果与创建或更新符合链接 `/proc/self/fd/N` 与 `/proc/self/fd/M` 到同一个目标的效果一样

    + 重定向 `N>file` 和 `N<file` 与创建或更新符号链接到 `/proc/self/fd/N` 到同一个目标文件的效果一样

    + 文件描述符闭包 `N>&-` 的效果与删除符号链接`/proc/self/fd/N`  一样

    + 当前命令被执行

  + `&>FILE` 的作用是同时将标准输出和标准错误重定向到指定文件，它与 `> FILE 2>&1` 等价。

  + 连接符（`-`）的作用是告知程序从管道读取输入。

  + 

# 捕捉信号

  + 不同 Linux 发行版支持的信号（signal）可能不同，可以使用 `man -k signal` 命令查看系统支持的信号。

  + 使用 shell 发送控制信号

    + **Control signals in Bash**
| Standard key combination | Meaning |
| ---- | ---- | ---- |
| **Ctrl**+**C** | The interrupt signal, sends SIGINT to the job running in the foreground. |
| **Ctrl**+**Y** | The *delayed suspend* character. Causes a running process to be stopped when it attempts to read input from the terminal. Control is returned to the shell, the user can foreground, background or kill the process. Delayed suspend is only available on operating systems supporting this feature. |
| **Ctrl**+**Z** | The *suspend* signal, sends a *SIGTSTP* to a running program, thus stopping it and returning control to the shell. |

  + 我们经常使用 `kill` 来杀死一个进程，而根据 `kill` 命令中信号的不同，程序结束的行为也会有所差异。常见的 `kill` 信号有：

    + | Signal name | Signal value | Effect |
| ---- | ---- | ---- |
| SIGHUP | 1 | Hangup |
| SIGINT | 2 | Interrupt from keyboard |
| SIGKILL | 9 | Kill signal |
| SIGTERM | 15 | Termination signal |
| SIGSTOP | 17,19,23 | Stop the process |

  + 在杀死一个或一批进程时，`SIGTERM` 信号是相对安全的，因为被杀死的进程可以在退出前执行收尾操作，比如清理数据、关闭文件等。而 `SIGKILL` 就不会给进程执行收尾操作的机会，可能导致异常。

# Worth to Read

  + [Bash Guide for Beginners](https://tldp.org/LDP/Bash-Beginners-Guide/html/index.html).

  + [Advanced Bash-Scripting Guide](https://tldp.org/LDP/abs/html/index.html).
