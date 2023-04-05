---
tags:
- Geohash
title: Geohash
categories:
date: 2022-11-23
lastMod: 2022-11-26
---


[Geohash: Wikipedia](https://en.wikipedia.org/wiki/Geohash) 是 Gustavo Niemeyer 在 2008 年发明的一个地理编码系统（geocode system），它将地理坐标（经纬度）编码成一个由数字和字母组成的字符串。

  + Geohash 表示的是一个范围，字符串越长，表示的位置就越精确。但是，太长的字符串对存储空间的消耗也很大。如果我们想节省存储空间并且对位置精度要求不那么高时，可以使用较短的字符串。

    + 将邻近搜索（proximity search）转换为字符串前缀匹配。

  + Geohash 保证：若两个 geohash 的共同前缀越长，它们对应的空间位置就越接近。反过来则是不成立的，因为两个非常接近的点可能的共同前缀可能非常短，甚至没有。两个点处在不同的格子？

    + 本初子午线两侧的格子就是一个典型的例子，距离近，却没有共同前缀

  + Geohash 是一个分层的空间数据结构，每个大的区块可以被划分为很多小区块。而每个小区块都可以进一步划分为更小的区块。

    + 为了表示地球上的一个点，我们递归地将地球表面划分为越来越小的网格，随着网格越来越小，位置的精度越来越高，geohash 的长度也越来越长。

  + Geohash的用途

    + 作为唯一标识符

    + 表示位置（可以存入数据库），方便搜索，因为它表示的是一个矩形区域。

      + 将二维数据（经纬度）编码成一维数据（geohash）的对索引很友好，单个索引比复合索引要好用

      + 可以用于搜索附近的xxx。通常，两个点越接近，它们的geohash越接近（前缀相同，末尾不同）

    + 可以用来查找附近的点

  + Geohash解决的问题

    + 经纬度检索效率低

    + 位置隐私

  + Geohash编解码

    + 算法

      + 纬度范围[-90, 90]，精度范围[-180, 180]。

      + 二分

      + 奇数位放经度，偶数位放纬度。

      + 通过 geohash 可以计算出经纬度，通过经纬度也可以计算出 geohash

    + 编码（经纬度 -> geohash）

    + 解码（geohash -> 经纬度）

  + 使用限制

    + 相近的位置可能有不同的geohash

    + 非线性，将球体上的点映射到二维坐标。

  + [Z阶曲线（z-order curve）](https://en.wikipedia.org/wiki/Z-order_curve)



工具

  + https://bhargavchippada.github.io/mapviz/ ：一个可以将 geohash 对应的矩形框展示在 GoogleMap 和 MapBox 上的在线网站

  + [geohash explorer](https://chrishewett.com/blog/geohash-explorer/)：一个可以将geohash 展示在 OpenStreetMap 上的网站

  + 

  + 

  + 

[Geohashing: Wikipedia](https://en.wikipedia.org/wiki/Geohashing): [Geohashing](https://geohashing.site/geohashing/Main_Page) 是一个游戏

![](https://xkcd.com/426/)

参考资料

  + [The A-Z of Geohashing: What You Need to Know](https://www.iunera.com/kraken/fabric/geohashing/).

  + [Notes on Geohashing](https://eugene-eeo.github.io/blog/geohashing.html)

  + 
