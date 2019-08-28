---
layout:     post
title:      Guava EventBus
subtitle:   EventBus 简单例子
date:       2017-08-23
author:     壹芝
header-img: img/post-bg-coffee.jpg
catalog: true
tags:
    - Java
    - EventBus
    - Guava
---
# 简介

> Google 出的 Guava 是 Java 核心增强的库，应用非常广泛。

#### Guava EventBus 简单例子

###### 整理自本人的有道历史云笔记

```
package com.dbyz.base.guava;

import com.google.common.eventbus.AsyncEventBus;
import com.google.common.eventbus.EventBus;
import com.google.common.eventbus.Subscribe;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.SynchronousQueue;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

/**
 * EventBusTest
 *
 * @author fangzheng
 * @version 1.0 : EventBusTest, v0.1 2017-08-23 16:09
 */
@SuppressWarnings("UnstableApiUsage")
public class EventBusTest {

    public static void main(String[] args) {

        // 初始化执行线程池
        ExecutorService test = new ThreadPoolExecutor(0, 1,
                1, TimeUnit.SECONDS,
                new SynchronousQueue<>(),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.CallerRunsPolicy());

        // 初始化eb
        EventBus eventBus = new AsyncEventBus("test", test);

        // 初始化监听器 & 注册监听器
        EventListener eventListener = new EventListener();
        eventBus.register(eventListener);

        // 发送事件
        eventBus.post(1L);
        eventBus.post(2);

        // 注销监听器 & 关闭执行线程池
        eventBus.unregister(eventListener);
        test.shutdown();
    }

    static class EventListener {

        /**
         * 关注需要关注的事件
         *
         * @param event
         * @throws InterruptedException
         * @see Subscribe
         */
        @Subscribe
        public void onInteger(Integer event) throws InterruptedException {
            System.out.println(Thread.currentThread() + "  integer:" + event);
            Thread.sleep(100);
        }

        @Subscribe
        public void onLong(Long event) throws InterruptedException {
            System.out.println(Thread.currentThread() + "  Long:" + event);
            Thread.sleep(100);
        }

        @Subscribe
        public void onObject(Object event) throws InterruptedException {
            System.out.println(Thread.currentThread() + "  Object:" + event);
            Thread.sleep(100);
        }

    }
}


```