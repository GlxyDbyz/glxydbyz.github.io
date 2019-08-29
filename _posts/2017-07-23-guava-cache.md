---
layout:     post
title:      Guava Cache
subtitle:   Guava Cache 简单例子
date:       2017-07-23
author:     壹芝
header-img: img/post-bg-coffee.jpg
catalog: true
tags:
    - Java
    - LocalCache
    - Guava
---
# 简介

> Google 出的 Guava 是 Java 核心增强的库，应用非常广泛。

#### Guava Cache 简单例子

###### 整理自本人的有道历史云笔记

```

package com.dbyz.base.guava;

import com.google.common.cache.Cache;
import com.google.common.cache.CacheBuilder;
import com.google.common.cache.CacheLoader;
import com.google.common.cache.LoadingCache;

import java.util.UUID;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicLong;

/**
 * CacheTest
 *
 * @author fangzheng
 * @version v0.1 2017-07-23 11:30
 */
@SuppressWarnings("UnstableApiUsage")
public class CacheTest {

    // -Xmx100M -Xms100M -verbose:gc -Xmn10M -XX:+UseConcMarkSweepGC -XX:+PrintGCDetails

    private static final AtomicLong ATOMIC_LONG = new AtomicLong(0);

    /**
     * Guava cache
     */
    private static final Cache<String, int[]> CACHE = CacheBuilder
            .newBuilder()
            // 软引用
            .softValues()
            // 默认大小
            .initialCapacity(10)
            // 最大大小
            .maximumSize(1024)
            // 并发数
            .concurrencyLevel(8)
            // 0.5秒过期
            .expireAfterWrite(500, TimeUnit.MILLISECONDS)
            .build();

    /**
     * Guava loading cache
     */
    private static final LoadingCache<String, String> LOADING_CACHE = CacheBuilder
            .newBuilder()
            // 软引用
            .softValues()
            // 默认大小
            .initialCapacity(10)
            // 最大大小
            .maximumSize(1024)
            // 并发数
            .concurrencyLevel(8)
            // 0.5秒过期
            .expireAfterWrite(500, TimeUnit.MILLISECONDS)
            .build(new CacheLoader<String, String>() {
                @Override
                public String load(String key) {
                    return UUID.randomUUID().toString();
                }
            });

    /**
     * Guava loading cache
     */
    private static final LoadingCache<String, String> LOADING_CACHE1 = CacheBuilder
            .newBuilder()
            // 软引用
            .softValues()
            // 默认大小
            .initialCapacity(10)
            // 最大大小
            .maximumSize(1024)
            // 并发数
            .concurrencyLevel(8)
            // 0.5秒过期
            .expireAfterWrite(500, TimeUnit.MILLISECONDS)
            .build(CacheLoader.from(UUID.randomUUID()::toString));

    public static void main(String[] args) throws Exception {

        while (true) {

            // 每0.1秒放置一个1mb的数据
            Thread.sleep(100L);

            CACHE.put(UUID.randomUUID().toString(), new int[1024 * 1024]);
            ATOMIC_LONG.incrementAndGet();

            // size函数返回的是近似值有偏差
            System.err.println(ATOMIC_LONG.get() + " ************ " + CACHE.size() + CACHE.asMap().keySet());
        }
    }
    
}


```