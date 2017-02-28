---
layout:     post
title:      "RDD vs DataSet"
subtitle:   "  RDD vs DataFrame vs DataSet"
date:       2017-02-28
author:     "Jie"
header-img: "img/post-bg-scala-version.jpg"
tags:
    - Spark
---

> Spark 2.0.2
 Scala 2.11.8
 Java 1.8.0_111

## RDD

Spark基础，用户指定数据序列化和反序列化。

## DataFrame

属于DataSet

```
type DataFrame = Dataset[Row]
```

## DataSet

> https://databricks.com/blog/2016/01/04/introducing-apache-spark-datasets.html

使用Encoder序列化和反序列化，可以说DataSet的数据集是Encoder。

> DataSet有SQLContext，queryExecution，Encoder三个属性，而前两个不参与序列化。

DataSet 相对与RDD，最重要的区别就是Schema，
而且经过Tungsten和Catalyst优化，能减少内存使用和加快计算速度。

```
// -Xmx1g
// Not enough space to cache
sc.parallelize(1 to 100000000).cache.count
```
从日志可以看到cache没有足够内存, 没有cache，action需要重新计算.

```
sc.parallelize(1 to 100000000).toDS.cache.coun
```
从UI看到占用93.5 MB

```
// Cached Partitions 2
// Fraction Cached 25%
// Size in Memory 104.1 MB
val rdd = sc.parallelize(1 to 10000000).map(_.toString).cache
rdd.count
// Cached Partitions 	8
// Fraction Cached 100%
// Size in Memory 104.1 MB
val ds = sc.parallelize(1 to 10000000).map(_.toString).toDS.cache
ds.count
```