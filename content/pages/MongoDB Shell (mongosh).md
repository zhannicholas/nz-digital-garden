---
tags:
- MongoDB
title: MongoDB Shell (mongosh)
categories:
date: 2022-09-01
lastMod: 2022-09-01
---


[Docs](https://www.mongodb.com/docs/mongodb-shell/).

连接数据库：`mongosh <connection-string>`

查看 server version: `db.serverStatus().version`

数据库操作

  + 显示所有数据库：`show dbs`

  + 查看当前所在的数据库：`db`

  + 切换数据库：`use <db-name>`

集合（collection）操作

  + 显示当前数据库下所有集合：`show collections`

  + 删除集合：`db.getCollection(<collection-name>).drop()`


