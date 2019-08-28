---
layout:     post
title:      常用hash算法
subtitle:   Guava Hashing 简单例子
date:       2017-05-13
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

#### Guava Hashing 简单例子

###### 整理自本人的有道历史云笔记

```
package com.dbyz.base.guava;
   
   import com.google.common.hash.HashFunction;
   import com.google.common.hash.Hashing;
   import lombok.extern.slf4j.Slf4j;
   
   import java.nio.charset.StandardCharsets;
   
   /**
    * HashingTest
    *
    * @author fangzheng
    * @version v0.1 2017-05-13 14:21
    */
   @Slf4j
   @SuppressWarnings("UnstableApiUsage")
   public class HashingTest {
   
       public static void main(String[] args) {
   
           byte[] bytes = "123456".getBytes(StandardCharsets.UTF_8);
   
           // SIP
           HashFunction function = Hashing.sipHash24();
           log.info("SIP {}", function.hashBytes(bytes).toString());
   
           // MD5
           function = Hashing.md5();
           log.info("MD5 {}", function.hashBytes(bytes).toString());
   
           // SHA
           function = Hashing.sha256();
           log.info("SHA256 {}", function.hashBytes(bytes).toString());
   
           function = Hashing.sha512();
           log.info("SHA512 {}", function.hashBytes(bytes).toString());
   
           // CRC
           function = Hashing.crc32();
           log.info("CRC32 {}", function.hashBytes(bytes).toString());
   
           function = Hashing.crc32c();
           log.info("CRC32C {}", function.hashBytes(bytes).toString());
   
           //MURMUR3
           function = Hashing.murmur3_32();
           log.info("MURMUR3_32 {}", function.hashBytes(bytes).toString());
   
           function = Hashing.murmur3_32(0);
           log.info("MURMUR3_32 {}", function.hashBytes(bytes).toString());
   
           function = Hashing.murmur3_128();
           log.info("MURMUR3_128 {}", function.hashBytes(bytes).toString());
   
           function = Hashing.murmur3_128(0);
           log.info("MURMUR3_128 {}", function.hashBytes(bytes).toString());
   
           // GFH 每次启动都不同随机性(启动时间的毫秒值)
           function = Hashing.goodFastHash(32);
           log.info("GFH 32 {}", function.hashBytes(bytes).toString());
   
           function = Hashing.goodFastHash(128);
           log.info("GFH 128 {}", function.hashBytes(bytes).toString());
   
           // HMAC_SHA256
           function = Hashing.hmacSha256("key".getBytes());
           log.info("HMAC_SHA256 {}", function.hashBytes(bytes).toString());
   
           function = Hashing.hmacSha512("key".getBytes());
           log.info("HMAC_SHA512 {}", function.hashBytes(bytes).toString());
   
           // HMAC_MD5
           function = Hashing.hmacMd5("key".getBytes());
           log.info("HMAC_MD5 {}", function.hashBytes(bytes).toString());
       }
   }

```