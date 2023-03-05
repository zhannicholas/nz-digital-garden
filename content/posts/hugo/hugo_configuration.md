---
author: Nicholas Zhan
title: "配置 Hugo"
date: 2023-02-09T21:32:08+08:00
draft: true
tags:
  - Hugo
---

[官方文档](https://gohugo.io/getting-started/configuration/#)。

## 配置文件

Hugo 支持`.toml`、 `.yaml` 、`.json`三种格式的配置文件。在一开始，配置文件的基本文件名（不含后缀的文件名）默认为 `config`。

从 0.110 版本开始， Hugo 的默认配置文件的基本文件名从 `config` 变为 `hugo`。这么做是为了让代码编辑器和构建工具能够识别出这是 Hugo 的配置文件，毕竟 `config` 这个词太宽泛了，很多工具都用 `config` 做配置文件名。

默认情况下，Hugo 会在网站的根目录寻找基本文件名为 `hugo`或 `config` 的文件作为网站的配置文件。

配置文件的查找顺序为：

1. `config.toml`

2. `config.yaml`

3. `config.json`



## 配置目录

除了使用单个配置文件外，Hugo还支持配置目录，方便我们更好地管理不同组织或不同环境下的配置。配置目录通过 `configDir` 指定，默认为 `/config`。



