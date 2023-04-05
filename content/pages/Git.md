---
tags:
- Git
title: Git
categories:
date: 2022-06-19
lastMod: 2022-08-25
---


推荐阅读：[Pro Git](https://git-scm.com/book/en/v2)。

# 标签


  + 资料：[Git Basics: Tagging](https://git-scm.com/book/en/v2/Git-Basics-Tagging/)。

  + Git 支持两种类型的 标签：

    + `lightweight` tag. It's just a pointer to a specific commit, no other information is kept.

    + `annotated` tag. It's stored as full objects in the Git database, such as the tagger name, email, date and tagging message, etc.

    + {{< logseq/orgNOTE >}}`lightweight` tag 由于包含的信息极少，一般用作临时标签，或在不需要保存其它信息的时候使用。在其它情况下，我们都应当使用 `annotated` 标签，因为它包含所有信息。
{{< / logseq/orgNOTE >}}

# 常用命令

  + 同步 Github 上 fork 的仓库

`powershell
> cd <your-forked-repo>	
> git remote add <upstream-repo-name> <upstream-repo-url>
> git pull <upstream-repo-name> <branch-name>
> git push origin <branch-name>
`

  + ## 标签相关

    + 查看标签

      + 查看所有标签 : `git tag`。

      + 查看满足通配符的标签：`git tag -l <wildchar-pattern>` 或 `git tag --list <wildchar-pattern>`。

    + 创建标签

      + 创建 `lightweight` 标签（不能使用`-a`、`-s`和`-m`，仅提供标签名）：`git tag <tag-name>`。

      + 创建 `annotated` 标签：`git tag -a <tag-name> -m <tagging-message>`。

    + 推送标签到服务器

      + 单个标签：`git push origin <tag-name>`

      + 所有标签（包括 `lightweight` 和 `annotated`）：`git push origin --tags`

      + 所有 `annotated` 标签： `git push origin --follow-tags`

    + 删除标签：`git tag -d <tag-name>`

    + 查看最新的标签：`git describe`

      + 默认返回 `annotated` 标签中最新的那个

      + 查看所有标签中最新的：`git describe --tags`
