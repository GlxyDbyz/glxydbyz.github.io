---
layout:     post
title:      JVM常用参数和工具使用
subtitle:   JVM常用参数和工具使用
date:       2018-05-13
author:     壹芝
header-img: img/post-bg-coffee.jpg
catalog: true
tags:
    - JVM
    - GC
    - Java
---

## JVM常用参数和工具使用

###### 整理自本人的有道历史云笔记

### 一. JVM常用参数

##### 1. 最小堆
 > -Xms2G  

> -XX:InitialHeapSize=2G

##### 2. 最大堆
> -Xmx2G

> -XX:MaxHeapSize=2G

##### 3. 新时代内存
> -Xmn512M

> XX:MaxNewSize=512M 

> -XX:NewSize=512M

##### 4. Eden 和 Survivor的比值
> -XX:SurvivorRatio=8

##### 5. 线程栈大小
> -Xss256K

> -XX:ThreadStackSize=256K

##### 6. 元数据空间
> -XX:MetaspaceSize=512M

> -XX:MaxMetaspaceSize=512M

##### 7. JDK7(永生带非堆内存)
 > -XX:PermSize=256M
 
 > -XX:MaxPermSize=256M
 
 
##### 8. 本地代码缓存
>  -XX:InitialCodeCacheSize=20M

> -XX:ReservedCodeCacheSize=100M
 
> -XX:+UseCodeCacheFlushing=true


##### 9. 内存溢出
> -XX:+HeapDumpOnOutOfMemoryError

> -XX:HeapDumpPath=/user/local/tomcat/logs/heapdump.hprof

##### 10 .内存溢出之后执行固定脚本
> -XX:OnOutOfMemoryError="sh ~/sendSms.sh"

---

### 二. JVM常用工具

##### 1. 查看当前JVM堆信息（会触发一次Full GC ）
> jmap -histo:live <pid>

> jmap -dump:live,file=java_dump.bin <pid>

> jhat java_dump.bin  

> http://localhost:7000/histo/

> http://localhost:7000/oql/

##### 2. 查看线程状态
> jstack <pid>

> jstack -lmF <pid>

##### 3. 查看JVM GC和堆栈使用情况
> jstat -gc [PID] times time  字节数

> jstat -gcutil [PID] times time  比例值


##### 4. 查看默认命令行参数
> java -XX:+PrintCommandLineFlags -XX:+PrintGCDetails -version

> jps -v 

##### 5. 查看默认非Stable参数（-XX）
> java -XX:+PrintFlagsFinal

> jinfo -flags [PID]

##### 6. 设置某个JVM非Stable参数
> jinfo -flag [+|-]PrintGCDateStamps <pid> 

> jinfo -flag PrintGCDateStamps=<value> <pid>

##### 7. 查看默认非标准参数-X
> java -X

---

### 三. 默认参数设置推荐

##### 1. 4G 双核 64位系统推荐参数

```
-Xmx2688M -Xms2688M -Xmn960M -XX:MaxPermSize=512M
-XX:PermSize=512M -XX:+UseConcMarkSweepGC
-XX:+UseCMSInitiatingOccupancyOnly
-XX:CMSInitiatingOccupancyFraction=70
-XX:+ExplicitGCInvokesConcurrentAndUnloadsClasses
-XX:+CMSClassUnloadingEnabled -XX:+CMSScavengeBeforeRemark
-XX:ErrorFile=/user/local/tomcat/java//hs_err_pid%p.log 
```

##### 2. 部分参数解释

```
-Xmx2688M -Xms2688M -Xmn960M 
-XX:MaxMetaspaceSize=512M
-XX:MetaspaceSize=512M 
-XX:+UseConcMarkSweepGC                  使用CMS垃圾收集器
-XX:+UseCMSInitiatingOccupancyOnly       触发cms gc的老生代使用率
-XX:CMSInitiatingOccupancyFraction=70
-XX:+CMSClassUnloadingEnabled            CMSGC是否启用类卸载
-XX:+ParallelRefProcEnabled
-XX:+CMSScavengeBeforeRemark             在cms gc remark之前做一次ygc，减少gc roots扫描的对象数，从而提高remark的效率 
-XX:ErrorFile=/user/local/tomcat/java/hs_err_pid%p.log  carsh日志路径
-XX:+ExplicitGCInvokesConcurrentAndUnloadsClasses
-XX:+PrintGCDetails
-XX:+PrintGCDateStamps 
-verbose:class                            跟踪GC类日志
-XX:+HeapDumpOnOutOfMemoryError           OOM Dump路径
-XX:HeapDumpPath=/user/local/tomcat/java  
-Xloggc:/user/local/tomcat/java/gc.log    GC日志路径
-XX:+PrintHeapAtGC                        在GC前后打印GC日志
-XX:+PrintClassHistogramBeforeFullGC      FullGC前后跟踪类视图
-XX:+PrintClassHistogramAfterFullGC 
-XX:+PrintCommandLineFlags                打印启动参数
-XX:+PrintGCApplicationConcurrentTime     打印进程并发执行时间
-XX:+PrintGCApplicationStoppedTime        打印应用暂停的时间
-XX:+PrintTenuringDistribution            打印YGC各个年龄段的对象分布

```


---