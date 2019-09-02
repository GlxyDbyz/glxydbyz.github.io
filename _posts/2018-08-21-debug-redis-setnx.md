---
layout:     post
title:      BUG实录 - Redis SETNX
subtitle:   一个 Redis SETNX 使用不当引起的 BUG
date:       2018-08-21
author:     壹芝
header-img: img/post-bg-debug.png
catalog: true
tags:
    - Redis
    - Java
    - Bug
    - Redisson
    
---

## BUG实录 - Redis SETNX

###### 整理自本人的有道历史云笔记

### 一. Redis SETNX 使用

```
> SETNX key value
```

> 可用版本： >= 1.0.0  
时间复杂度：O(1)   
只在键 key 不存在的情况下， 将键 key 的值设置为 value 。若键 key 已经存在， 则 SETNX 命令不做任何动作。  
SETNX 是『SET if Not eXists』(如果不存在，则 SET)的简写。  
返回值  
命令在设置成功时返回 1 ， 设置失败时返回 0 。


### 二. 项目中的错误代码实现

#### 1. 问题描述

> 项目中一个正常跑了几个月的定时任务突然停止了,排查日志发现是redis分布式锁获取失败导致

#### 2. 问题代码重现如下

业务代码:
```
    @Scheduled(cron = "10 10 1 * * ?")
    public void schedule() {

        String lockKey = RedisConstant.KEY_PREFIX + "BUSSINESS_SKILL:LOCK";

        // 锁定
        boolean lock = redisCommonClient.setNX(lockKey, "LOCK", 10);

        if (!lock) {
            LOGGER.error("key not obtain");
            return;
        }
        
        // ..... 业务代码省略
    }
```
Redis Client 代码实现,使用 SETNX 实现分布式锁:
```
    @Override
    public Boolean setNX(final String redisKey, final Object object, final int expire) {

        return redisTemplate.execute(new RedisCallback<Boolean>() {

            @Override
            public Boolean doInRedis(RedisConnection connection) throws DataAccessException {

                try {

                    // Key序列化
                    byte[] byteKey = stringRedisSerializer.serialize(redisKey);

                    // Object序列化
                    byte[] byteObj = redisSerializer.serialize(object);

                    // setNX
                    Boolean result = connection.setNX(byteKey, byteObj);

                    // expire大于0 并且set成功，则设置过期时间
                    if (expire > 0 && result != null && result) {

                        // 设置过期时间
                        result = connection.expire(byteKey, expire);

                        // 设置过期时间失败则删除key，返回失败
                        if (result == null || !result) {

                            connection.del(byteKey);

                            // 返回值设为false
                            result = Boolean.FALSE;
                        }
                    }
                    return result;

                } catch (Exception e) {
                    LOGGER.error("function setNX error", e);
                }

                return Boolean.FALSE;
            }
        });
```

#### 3. 问题所在

> 先调用setnx,成功之后调用 expire 正常情况下代码没有问题,高并发或者redis出现性能问题的时候 expire 可能不生效导致key永远不过期

#### 4. 解决办法使用 SET NX EX 

```
> SET key value EX expire NX

> SET key value NX EX expire 

> SET key value [EX seconds] [PX milliseconds] [NX|XX]

```

> 可用版本： >= 1.0.0  
时间复杂度： O(1)  
将字符串值 value 关联到 key 。
如果 key 已经持有其他值， SET 就覆写旧值， 无视类型。  
当 SET 命令对一个带有生存时间（TTL）的键进行设置之后， 该键原有的 TTL 将被清除。  
可选参数  
从 Redis 2.6.12 版本开始， SET 命令的行为可以通过一系列参数来修改：  
EX seconds ： 将键的过期时间设置为 seconds 秒。 执行 SET key value EX seconds 的效果等同于执行 SETEX key seconds value 。  
PX milliseconds ： 将键的过期时间设置为 milliseconds 毫秒。 执行 SET key value PX milliseconds 的效果等同于执行 PSETEX key milliseconds value 。  
NX ： 只在键不存在时， 才对键进行设置操作。 执行 SET key value NX 的效果等同于执行 SETNX key value 。  
XX ： 只在键已经存在时， 才对键进行设置操作。


优化后的代码:
```
    @Override
    public Boolean setNX(final String redisKey, final Object object, final int expire) {

        return redisTemplate.execute(new RedisCallback<Boolean>() {

            @Override
            public Boolean doInRedis(RedisConnection connection) throws DataAccessException {

                try {

                    // Key序列化
                    byte[] byteKey = stringRedisSerializer.serialize(redisKey);

                    // Object序列化
                    byte[] byteObj = redisSerializer.serialize(object);

                    // setNX
                    return null != connection.execute("set",
                            byteKey,
                            byteObj,
                            SafeEncoder.encode("NX"),
                            SafeEncoder.encode("EX"),
                            Protocol.toByteArray(expire)
                    );

                } catch (Exception e) {

                    // 打印日志
                    LOGGER.error("error", e);
                }

                return Boolean.FALSE;
            }
        });
    }
```

#### 5. 建议使用 [Redisson](http://blog.glxydbyz.top/2019/07/25/redis-client-redisson/) 替换原有的Redis Client 实现分布式锁


