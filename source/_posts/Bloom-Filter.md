---
title: Bloom Filter
date: 2021-12-01 11:04:41
tags:
- 大数据
- 算法
- 快速查找
categories:
- 算法
- Bloom Filter
---

Bloom Filter的介绍文章网上很多，可以自行查阅。喜欢读英文的同学可以看英文版维基百科。[Bloom Filter](https://en.wikipedia.org/wiki/Bloom_filter#CITEREFBloom1970)。

既然Bloom Filter不是100%准确的，那么它的应用在什么样的场景呢？我目前的理解可以有以下两种场景：

- 低概率匹配错误对业务没有什么影响。例如网络爬虫中解决URL是否已经被访问过的问题。
- Bloom Filter在判定存在上不是100%准确的，但在判定不存在上是准确的。也就是维基中描述的（见下图）。典型的应用场景如HBase中对分区数据的快速确定是否包含目标数据。

![Bloom Filter Definitely Not](/images/20211201/bloom-filter-definitely-not.png)