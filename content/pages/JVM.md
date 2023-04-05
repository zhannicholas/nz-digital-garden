---
tags:
- JVM
title: JVM
categories:
date: 2022-11-11
lastMod: 2022-11-11
---


## 显示一个正在运行的 Java 应用的各项参数

  + 使用 `ps`: `ps -aux | grep "java"`

  + 使用[`jcmd`](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/tooldescr006.html)：

    + `jcmd [pid] VM.flags`

    + `jcmd [pid] VM.system_properties`

    + `jcmd [pid] VM.command_line`

  + 
