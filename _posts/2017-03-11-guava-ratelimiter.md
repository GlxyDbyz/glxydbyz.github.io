---
layout:     post
title:      Guava RateLimiter
subtitle:   RateLimiter 简单例子
date:       2017-03-11
author:     壹芝
header-img: img/post-bg-coffee.jpg
catalog: true
tags:
    - Java
    - RateLimiter
    - Guava
---
# 简介

> Google 出的 Guava 是 Java 核心增强的库，应用非常广泛。

#### Guava RateLimiter 简单例子

###### 整理自本人的有道历史云笔记

```

package com.dbyz.base.guava;

import com.google.common.util.concurrent.RateLimiter;
import lombok.extern.slf4j.Slf4j;

import java.time.Duration;

/**
 * RateLimiterTest
 *
 * @author fangzheng
 */
@Slf4j
@SuppressWarnings("UnstableApiUsage")
public class RateLimiterTest {

    public static void main(String[] args) {

        /**
         * 速率控制器 SmoothBursty extends SmoothRateLimiter
         * 速率相同
         */
        RateLimiter rateLimiter = RateLimiter.create(2);

        for (int i = 0; i < 20; i++) {
            rateLimiter.acquire();
            log.info("SmoothBursty {}", i);
        }

        /**
         * 预热速率控制器 SmoothWarmingUp
         * warmupPeriod 前较慢,后面变快,然后匀速
         */
        rateLimiter = RateLimiter.create(5, Duration.ofSeconds(1));

        for (int i = 0; i < 20; i++) {
            rateLimiter.acquire();
            log.info("SmoothWarmingUp {}", i);
        }
    }
}

```