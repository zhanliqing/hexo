---
title: spark map flatmap区别联系
date: 2017-08-15 15:16:24
tags: spark
categorys: spark
---

spark中map是一对一的变换，也就是一个元素经过map变换后还是一个元素，而flatMap是扁平化变换也就是一对多变化，flatMap返回的是一个array,但输出到下个变换时，会把array拆成多个单元素来处理，下面举一个例子

```scala
scala> val bb = sc.textFile("/home/user/liqing.zhan/bb")
scala> bb.collect.foreach(println)

abc
dd aa cc de er ad as d s as  d  d sdf as d
df
sa
dd ee e d g a ew sa
df

scala> bb.map(_.split(" ")).collect.foreach(println)
[Ljava.lang.String;@3ad9f06a
[Ljava.lang.String;@72cb5d7d
[Ljava.lang.String;@19b89a23
[Ljava.lang.String;@6d1a4522
[Ljava.lang.String;@1835b24b
[Ljava.lang.String;@303d2485

scala> bb.flatMap(_.split("\\w+")).collect.foreach(println)

abc
dd
aa
cc
de
er
ad
as
d
s
as
d
d
sdf
as
d
df
sa
dd
ee
e
d
g
a
ew
sa
df

word count
scala> bb.flatMap(_.split("\\W+")).map(line=>(line,1)).reduceByKey(_+_).sortByKey().collect.foreach(println)

a.map(_.split(",")).map(item => (s"${item(0)}-${item(1)}", item(2))).groupByKey.map(item => (item._1, item._2.toList.sortWith(_.toInt<_.toInt))).flatMap(item=>item._2.map(x=>(item._1,x))).collect.foreach(println)
```