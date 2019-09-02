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
###### 整理自本人的有道历史云笔记

#### 一. 常用hash方法列举

加法hash:
```
    /**
     * 加法hash
     *
     * @param str
     * @return
     */
    public int addHash(String str) {
        int hash = 0;
        if (str != null && str.length() > 0) {
            byte[] bytes;
            try {
                bytes = str.getBytes("UTF-8");
                for (byte b : bytes) {
                    hash += Math.abs(b);
                }
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }
        }
        return hash;
    }
```

乘法hash:
```
    /**
     * 乘法hash
     *
     * @param str
     * @return
     */
    public int multiHash(String str) {
        int hash = 0;
        if (str != null && str.length() > 0) {
            byte[] bytes;
            try {
                bytes = str.getBytes("UTF-8");
                for (byte b : bytes) {
                    hash *= Math.abs(b);
                }
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }
        }
        return hash;
    }
```

位移hash:
```
    /**
     * 位移hash
     *
     * @param str
     * @return
     */
    public int displaceHash(String str) {
        int hash = 0;
        if (str != null && str.length() > 0) {
            byte[] bytes;
            try {
                bytes = str.getBytes("UTF-8");
                for (byte b : bytes) {
                    hash = hash << 5 ^ Math.abs(b) << 2;
                }
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
            }
        }
        return hash;
    }
```

RS Hash Function:
```
    /**
     * RS Hash Function
     *
     * @param str
     * @return
     */
    public long rsHash(String str) {
        int b = 378551;
        int a = 63689;
        long hash = 0;

        for (int i = 0; i < str.length(); i++) {
            hash = hash * a + str.charAt(i);
            a = a * b;
        }
        return hash;
    }
```

JS Hash Function:
```
    /**
     * JS Hash Function
     *
     * @param str
     * @return
     */
    public long jsHash(String str) {
        long hash = 1315423911;

        for (int i = 0; i < str.length(); i++) {
            hash ^= ((hash << 5) + str.charAt(i) + (hash >> 2));
        }

        return hash;
    }
```

P. J. Weinberger Hash Function:
```

    /**
     * P. J. Weinberger Hash Function
     *
     * @param str
     * @return
     */
    public long pjwHash(String str) {
        long bitsInUnsignedInt = (long) (4 * 8);
        long threeQuarters = (long) ((bitsInUnsignedInt * 3) / 4);
        long oneEighth = (long) (bitsInUnsignedInt / 8);
        long highBits = (long) (0xFFFFFFFF) << (bitsInUnsignedInt - oneEighth);
        long hash = 0;
        long test = 0;

        for (int i = 0; i < str.length(); i++) {
            hash = (hash << oneEighth) + str.charAt(i);

            if ((test = hash & highBits) != 0) {
                hash = ((hash ^ (test >> threeQuarters)) & (~highBits));
            }
        }

        return hash;
    }

```

ELF Hash Function:
```
    /**
     * ELF Hash Function
     *
     * @param str
     * @return
     */
    public long elfHash(String str) {
        long hash = 0;
        long x = 0;

        for (int i = 0; i < str.length(); i++) {
            hash = (hash << 4) + str.charAt(i);

            if ((x = hash & 0xF0000000L) != 0) {
                hash ^= (x >> 24);
            }
            hash &= ~x;
        }

        return hash;
    }
```

BKDR Hash Function:
```
    /**
     * BKDR Hash Function
     *
     * @param str
     * @return
     */
    public long bkdrHash(String str) {
        // 31 131 1313 13131 131313 etc..
        long seed = 131;
        long hash = 0;

        for (int i = 0; i < str.length(); i++) {
            hash = (hash * seed) + str.charAt(i);
        }

        return hash;
    }
```

SDBM Hash Function:
```
    /**
     * SDBM Hash Function
     *
     * @param str
     * @return
     */
    public long sdbmHash(String str) {
        long hash = 0;

        for (int i = 0; i < str.length(); i++) {
            hash = str.charAt(i) + (hash << 6) + (hash << 16) - hash;
        }

        return hash;
    }
```

DJB Hash Function:
```
    /**
     * DJB Hash Function
     *
     * @param str
     * @return
     */
    public long djbHash(String str) {
        long hash = 5381;

        for (int i = 0; i < str.length(); i++) {
            hash = ((hash << 5) + hash) + str.charAt(i);
        }

        return hash;
    }
```

DEK Hash Function:
```
    /**
     * DEK Hash Function
     *
     * @param str
     * @return
     */
    public long dekHash(String str) {
        long hash = str.length();

        for (int i = 0; i < str.length(); i++) {
            hash = ((hash << 5) ^ (hash >> 27)) ^ str.charAt(i);
        }

        return hash;
    }

```

BP Hash Function:
```
    /**
     * BP Hash Function
     *
     * @param str
     * @return
     */
    public long bpHash(String str) {
        long hash = 0;

        for (int i = 0; i < str.length(); i++) {
            hash = hash << 7 ^ str.charAt(i);
        }

        return hash;
    }
```

FNV Hash Function:
```

    /**
     * FNV Hash Function
     *
     * @param str
     * @return
     */
    public long fnvHash(String str) {
        long fnv_prime = 0x811C9DC5;
        long hash = 0;

        for (int i = 0; i < str.length(); i++) {
            hash *= fnv_prime;
            hash ^= str.charAt(i);
        }

        return hash;
    }
```

AP Hash Function:
```
    /**
     * AP Hash Function
     *
     * @param str
     * @return
     */
    public long APHash(String str) {
        long hash = 0xAAAAAAAA;

        for (int i = 0; i < str.length(); i++) {
            if ((i & 1) == 0) {
                hash ^= ((hash << 7) ^ str.charAt(i) * (hash >> 3));
            } else {
                hash ^= (~((hash << 11) + str.charAt(i) ^ (hash >> 5)));
            }
        }

        return hash;
    }
```

#### 二. Guava Hashing 简单例子

> Google 出的 Guava 是 Java 核心增强的库，应用非常广泛。  
Guava 常用 Hash function 如下例子


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