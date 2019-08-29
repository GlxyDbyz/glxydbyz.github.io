---
layout:     post
title:      BloomFilter
subtitle:   BloomFilter 简单例子
date:       2017-06-24
author:     壹芝
header-img: img/post-bg-coffee.jpg
catalog: true
tags:
    - Java
    - BloomFilter
    - Guava
---
# 简介
> Bloom filter 是由 Howard Bloom 在 1970 年提出的二进制向量数据结构，它具有很好的空间和时间效率，被用来检测一个元素是不是集合中的一个成员。如果检测结果为是，该元素不一定在集合中；但如果检测结果为否，该元素一定不在集合中。因此Bloom filter具有100%的召回率。这样每个检测请求返回有“在集合内（可能错误）”和“不在集合内（绝对不在集合内）”两种情况，可见 Bloom filter 是牺牲了正确率和时间以节省空间。

#### Guava BloomFilter 简单例子

###### 整理自本人的有道历史云笔记

```

package com.dbyz.base.guava;

import com.google.common.hash.BloomFilter;
import com.google.common.hash.Funnels;
import lombok.extern.slf4j.Slf4j;

import java.nio.charset.Charset;
import java.util.LinkedList;
import java.util.List;
import java.util.UUID;

/**
 * BloomFilterTest
 *
 * @author fangzheng
 * @version v0.1 2017-06-24 15:20
 */
@Slf4j
@SuppressWarnings("UnstableApiUsage")
public class BloomFilterTest {

    // -Xmx100M -Xms100M -verbose:gc -Xmn10M -XX:+UseConcMarkSweepGC -XX:+PrintGCDetails

    public static void main(String[] args) {

        // 1Kw
        final int num = 10_000_000;

        List<String> strings = new LinkedList<>();
        for (int i = 0; i < num; i++) {
            // OOM
            // strings.add(UUID.randomUUID().toString());
        }

        // 优势:占用内存较小,hash计算速度快
        // 劣势:存在误判率
        final BloomFilter<String> bloomFilter = BloomFilter.create(
                // 算法
                Funnels.stringFunnel(Charset.forName("UTF-8")),
                // 可能的插入数
                num,
                // 期望可接受的错误率 0.001 = 千分之一
                0.001);

        int missCount = 0;

        for (int i = 0; i < num; i++) {
            // uuid
            String uuid = UUID.randomUUID().toString();

            // 不会OOM
            if (bloomFilter.mightContain(uuid)) {
                missCount++;
            } else {
                bloomFilter.put(uuid);
            }
        }

        // [main] INFO com.dbyz.base.guava.BloomFilterTest - miss count 1212
        // 本次的误判率 1212/1kw > 万分之一 < 千分之一
        log.info("miss count {}", missCount);
    }
}

```