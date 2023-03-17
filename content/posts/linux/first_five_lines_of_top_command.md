---
author: Nicholas Zhan
title: "Linux top 命令入门：前五行剖析"
date: 2023-03-07T21:15:04+08:00
draft: false
language: zh
featured_image:
summary: 当 Linux 系统出现问题时，管理员常用 `top` 命令来分析系统资源的消耗情况。`top` 是一个大杀器，输出内容十分丰富，不仅有 CPU、内存等硬件资源的消耗情况，还有各种进程信息。
categories:
  - Linux
tags:
  - Linux
---

当 Linux 系统出现问题时，管理员常用 `top` 命令来分析系统资源的消耗情况。`top` 是一个大杀器，输出内容十分丰富，不仅有 CPU、内存等硬件资源的消耗情况，还有各种进程信息。
这些信息都是动态的，实时展示着系统的当前状态。这篇文章主要解释 `top` 命令前五行所展示的内容，即系统资源的宏观情况：
* `top`：系统摘要
* `Tasks`：进程状态
* `%CPU(s)`：CPU 使用情况
* `MiB Mem`：物理内存（主存）的使用情况
* `MiB Swap`：虚拟内存（swap 分区）的使用情况


![The output of top command](/images/linux/linux_top_command_output.png)


## 第一行 top

```text
top - 21:43:01 up 61 days, 23:35,  2 users,  load average: 0.00, 0.01, 0.00
```

这一行的内容为系统摘要信息，和 `uptime` 命令的输出相同。
* `21:43:01`：系统当前时间
* `up 61 days, 23:35`：up 表示系统已经运行了 61 天 23 小时 35 分钟
* `2 users`：有两个已登录用户
* `load average: 0.00, 0.01, 0.00`：系统在过去 1 分钟、5 分钟和 15 分钟的平均负载分别为 0.00、0.01 和 0.00。

系统负载是指以 0 到 1.0 为参考，CPU 负载的百分比。这个值也可能高于 1.0，此时就说明处理器过载了。

## 第二行 Tasks

```text
Tasks: 170 total,   1 running, 169 sleeping,   0 stopped,   0 zombie
```

第二行展示系统内进程状态的统计信息:
* `170 total`：当前进程总数为 170，进程总数为处于各个不同状态的进程数之和
* `1 running`：有 1 个进程正在运行
* `169 sleeping`：有 169 个进程正在等待资源
* `0 stopped`：有 0 个进程已停止
* `0 zombie`：有 0 个僵尸进程。

在 Linux 系统中，进程有以下几种状态：
* RUNNING & RUNNABLE （R）：RUNNING 表示 CPU 正在执行该进程，RUNNABLE 表示进程执行所需要的资源都已具备，可以被 CPU 执行了。
* INTTERRUPTIBLE_SLEEP （S）：处于睡眠状态，但是可以被信号（signal）唤醒
* UNINTERRUPTIBLE_SLEEP （D）：处于睡眠状态，但是不会被信号（signal）唤醒
* STOPPED （T）：进程停止，被挂起。
* ZOMBIE （Z）：

## 第三行 %CPU(s)

```text
%Cpu(s):  0.2 us,  0.4 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
```

第三行展示 CPU 在上次刷新之后，各种状态的时间百分比：
* `us`：在用户空间运行进程的时间（不含 nice）
* `sy`：在内核空间运行进程的时间
* `ni`：在用户空间运行被 nice 进程的时间
* `id`：空闲时间
* `wa`：等待 I/O 完成
* `hi`：处理硬件中断所花费的时间
* `si`：处理软件中断所花费的时间
* `st`：虚拟机管理器（Hypervisor）从虚拟机偷走的时间

## 第四行 MiB Mem

```text
MiB Mem :   7444.7 total,    295.2 free,   2007.7 used,   5141.8 buff/cache
```

第四行提供物理内存的使用情况。内存的不一定是 MiB，也可能是 KiB、GiB、PiB 等。根据单位的不同，内存使用情况的展示也有些区别，不同单位的具体换算如下：
```text
KiB = kibibyte = 1024 bytes
MiB = mebibyte = 1024 KiB = 1,048,576 bytes
GiB = gibibyte = 1024 MiB = 1,073,741,824 bytes
TiB = tebibyte = 1024 GiB = 1,099,511,627,776 bytes
PiB = pebibyte = 1024 TiB = 1,125,899,906,842,624 bytes
EiB = exbibyte = 1024 PiB = 1,152,921,504,606,846,976 bytes
```

* `total`：系统安装的总内存
* `free`：可用内存
* `used`：已使用内存
* `buff/cache`：等待写出的缓存

## 第五行 MiB Swap

```text
MiB Swap:      0.0 total,      0.0 free,      0.0 used.   5120.6 avail Mem
```

当物理内存耗尽时，Linux 可以从硬盘借一部分空间弥补主存的不足，借来的这部分磁盘空间就是交换空间（Swap），可以当 RAM 使用。

* `total`：交换空间的总大小
* `free`：空闲的交换空间
* `used`：已用交换空间
* `avail`：可用作内存交换的交换空间大小
