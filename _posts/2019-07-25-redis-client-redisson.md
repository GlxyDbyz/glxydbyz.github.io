---
layout:     post
title:      Java驻内存数据网格 Redisson 的使用
subtitle:   Redisson 简单例子
date:       2019-07-25
author:     壹芝
header-img: img/post-bg-coffee.jpg
catalog: true
tags:
    - Redis
    - Redisson
    - Java
    - In-Memory Data Grid
---

## Java驻内存数据网格 Redisson 的使用

###### 整理自本人的有道历史云笔记

#### 一. Redisson 简介
> Redisson是一个在Redis的基础上实现的Java驻内存数据网格（In-Memory Data Grid）。它不仅提供了一系列的分布式的Java常用对象，还提供了许多分布式服务。其中包括(BitSet, Set, Multimap, SortedSet, Map, List, Queue, BlockingQueue, Deque, BlockingDeque, Semaphore, Lock, AtomicLong, CountDownLatch, Publish / Subscribe, Bloom filter, Remote service, Spring cache, Executor service, Live Object service, Scheduler service) Redisson提供了使用Redis的最简单和最便捷的方法。Redisson的宗旨是促进使用者对Redis的关注分离（Separation of Concern），从而让使用者能够将精力更集中地放在处理业务逻辑上。

#### 二. Maven 依赖
```
<dependency>
    <groupId>org.redisson</groupId>
    <artifactId>redisson</artifactId>
    <version>3.11.0</version>
</dependency>
```

#### 三. 简单例子

##### 1. 初始化
```
    // redisson初始化
    RedissonClient redisson = Redisson.create();
        
```

##### 2. 初始化实现,支持单点/主从/sentinel/cluster/
```
    /**
     * Create sync/async Redisson instance with default config
     *
     * @return Redisson instance
     */
    public static RedissonClient create() {
        Config config = new Config();
        config.useSingleServer()
        .setTimeout(1000000)
        .setAddress("redis://127.0.0.1:6379");
//        config.useMasterSlaveConnection().setMasterAddress("127.0.0.1:6379").addSlaveAddress("127.0.0.1:6389").addSlaveAddress("127.0.0.1:6399");
//        config.useSentinelConnection().setMasterName("mymaster").addSentinelAddress("127.0.0.1:26389", "127.0.0.1:26379");
//        config.useClusterServers().addNodeAddress("127.0.0.1:7000");
        return create(config);
    }
```

##### 3. 分布式 AtomicLong 例子
```

    /********************** RAtomicLong AbcAsync **********************/
    RAtomicLong atomicLong = redisson.getAtomicLong("atomic_long");
    // get
    log.info("{}", atomicLong.get());
    // incr decr & get
    log.info("{}", atomicLong.incrementAndGet());
    log.info("{}", atomicLong.decrementAndGet());
    // 获取并增加xxx
    log.info("{}", atomicLong.getAndAdd(12L));
    // xxx Async异步操作
    atomicLong.incrementAndGetAsync();
    
    /**************************** RObject ****************************/
    // 重命名
    atomicLong.renamenx("r_object");
    atomicLong.rename("r_object");
    // 是否存在
    atomicLong.isExists();
    // 内存占用
    atomicLong.sizeInMemory();
    // 移动其他DB
    atomicLong.move(1);
    // 删除
    atomicLong.delete();
    atomicLong.unlink();
    // touch
    atomicLong.touch();
    //  dump & restore
    byte[] dump = atomicLong.dump();
    // atomicLong.restore(xxx);
    
    /*************************** RExpirable ***************************/
    // 过期
    atomicLong.expire(1, TimeUnit.MINUTES);
    atomicLong.expireAt(System.currentTimeMillis() + 200L);
    // 清楚过期
    atomicLong.clearExpire();
```

##### 4. 分布锁例子(读写锁,可重入锁)

```
    /******************** RLock RReadWriteLock etc *********************/
    // redisson 锁(默认实现reentrant lock,可重入,非公平)
    RLock lock = redisson.getLock("lock_me");
    // 读锁
    RLock readLock = redisson.getRedLock(lock);
    // 读写锁
    RReadWriteLock readWriteLock = redisson.getReadWriteLock("lock_me");
    // 公平锁
    RLock fairLock = redisson.getFairLock("lock_me");

    // 尝试锁定
    boolean locked = lock.tryLock();
    // 等待锁定
    locked = lock.tryLock(1, TimeUnit.MINUTES);

    if (locked) {
        // do something
        // 释放获取的锁定
        lock.unlock();
    }
    // 强制释放全部锁定
    lock.forceUnlock();

```

##### 5. 分布式阻塞队列和列表

```
    /************************ RBlockingQueue etc ************************/
    // RBlockingQueue extends BlockingQueue
    RBlockingQueue<String> blockingQueue = redisson.getBlockingQueue("block_queue");
    blockingQueue.put("abc");
    blockingQueue.offer("123");
    blockingQueue.peek();
    blockingQueue.poll();
    blockingQueue.take();
    
    /************************ RList ************************/
    RList<String> list = redisson.getList("r_list");
    list.add("abc");
    list.add(0, "123");
    list.get(0, 1);
    list.remove("123", 1);
```

##### 6. 分布式地理位置信息和距离计算
```

    /******************************* RGeo ******************************/
    // RBlockingQueue extends BlockingQueue
    RGeo<String> geo = redisson.getGeo("china");
    geo.add(120.17, 30.27, "hangzhou");
    geo.add(116.40, 39.91, "beijing");
    log.info("dist {} km", geo.dist("hangzhou", "beijing", GeoUnit.KILOMETERS));

```

##### 7. 分布式分数排序集合

```
    /*************************** RScoredSortedSet ************************/
    // RScoredSortedSet
    RScoredSortedSet<String> scoredSortedSet = redisson.getScoredSortedSet("scoreSortSet");
    scoredSortedSet.add(99.5, "Tom");
    scoredSortedSet.add(88.3, "Jerry");
    scoredSortedSet.getScore("Jerry");
```

##### 8. 分布式布隆过滤器

```
    /***************************** RBloomFilter **************************/
    // RBloomFilter
    RBloomFilter<String> bloomFilter = redisson.getBloomFilter("bloomFilter");
    // 重置 bloomFilter 初次使用执行一次
    bloomFilter.tryInit(200000, 0.01);

    log.info("{}", bloomFilter.add("key1"));
    log.info("{}", bloomFilter.add("key2"));
    log.info("{}", bloomFilter.contains("key2"));

    // 关闭
    redisson.shutdown();
        
```