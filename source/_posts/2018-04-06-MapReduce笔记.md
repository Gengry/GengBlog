---
title: MapReduce笔记
date: 2018-04-06 14:30:54
tags:
categories:
---

## MapReduce原理

![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180406/5.jpg) 

在将map的结果集传递给reduce之前对数据进行了分组（类似于对K2做了group by）

![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180406/6.jpg) 

## 执行步骤

### map任务处理

* 读取输入文件内容，解析成key、value对。对输入文件的每一行，解析成key、value对。每一个键值对调用一次map函数。
* 写自己的逻辑，对输入的key、value处理，转换成新的key、value输出。
* 对输出的key、value进行分区。
* 对不同分区的数据，按照key进行排序、分组。相同key的value放到一个集合中。
* (可选)分组后的数据进行归约。
 
### reduce任务处理
* 对多个map任务的输出，按照不同的分区，通过网络copy到不同的reduce节点。
* 对多个map任务的输出进行合并、排序。写reduce函数自己的逻辑，对输入的key、value处理，转换成新的key、value输出。
* 把reduce的输出保存到文件中。


<blockquote class="blockquote-center">今天最好的表现，是明天最低的要求。</blockquote>