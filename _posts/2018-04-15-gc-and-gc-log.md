---
layout:     post
title:      常见垃圾回收方法和对应GC日志分析
subtitle:   GC日志分析
date:       2018-04-15
author:     壹芝
header-img: img/post-bg-coffee.jpg
catalog: true
tags:
    - JVM
    - GC
    - Java
---

## 常见垃圾回收方法和对应GC日志分析

###### 整理自本人的有道历史云笔记

### 一. G1GC

##### 1. 参数
```
 -XX:+UseG1GC -XX:InitialHeapSize=134217728 -XX:+ManagementServer -XX:MaxHeapSize=2147483648 -XX:+PrintGC -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops
```

##### 2. 日志

```
0.813: [GC pause (G1 Evacuation Pause) (young) 14M->2721K(128M), 0.0052813 secs]
1.651: [GC pause (G1 Evacuation Pause) (young) 22M->7544K(128M), 0.0181880 secs]
2.961: [GC pause (G1 Evacuation Pause) (young) 80M->16M(128M), 0.0189118 secs]
3.426: [GC pause (G1 Evacuation Pause) (young) 82M->22M(128M), 0.0117063 secs]
3.777: [GC pause (G1 Evacuation Pause) (young) 88M->32M(128M), 0.0138918 secs]
4.464: [GC pause (G1 Evacuation Pause) (young) 98M->38M(128M), 0.0130757 secs]
4.904: [GC pause (G1 Evacuation Pause) (young) 104M->46M(128M), 0.0132804 secs]
5.160: [GC pause (G1 Evacuation Pause) (young) 105M->53M(128M), 0.0136565 secs]
5.511: [GC pause (G1 Evacuation Pause) (young) 106M->60M(128M), 0.0084614 secs]
5.806: [GC pause (G1 Evacuation Pause) (young) 107M->66M(128M), 0.0085023 secs]
5.996: [GC pause (G1 Evacuation Pause) (young) (initial-mark) 108M->72M(128M), 0.0084679 secs]
6.005: [GC concurrent-root-region-scan-start]
6.009: [GC concurrent-root-region-scan-end, 0.0043269 secs]
6.009: [GC concurrent-mark-start]
6.038: [GC concurrent-mark-end, 0.0282858 secs]
6.038: [GC remark, 0.0037107 secs]
6.042: [GC cleanup 80M->45M(128M), 0.0002855 secs]
6.042: [GC concurrent-cleanup-start]
6.042: [GC concurrent-cleanup-end, 0.0000191 secs]
6.269: [GC pause (G1 Evacuation Pause) (young) 73M->41M(128M), 0.0054785 secs]
6.277: [GC pause (G1 Evacuation Pause) (mixed) 42M->37M(128M), 0.0037051 secs]
6.662: [GC pause (G1 Evacuation Pause) (young) 111M->48M(128M), 0.0078344 secs]
6.982: [GC pause (G1 Evacuation Pause) (young) 104M->52M(128M), 0.0153188 secs]
7.394: [GC pause (G1 Evacuation Pause) (young) 105M->57M(128M), 0.0093325 secs]
7.703: [GC pause (G1 Evacuation Pause) (young) 107M->64M(128M), 0.0080060 secs]
8.001: [GC pause (G1 Evacuation Pause) (young) 106M->70M(128M), 0.0142363 secs]
8.277: [GC pause (G1 Evacuation Pause) (young) (initial-mark) 108M->77M(128M), 0.0123245 secs]
8.289: [GC concurrent-root-region-scan-start]
8.294: [GC concurrent-root-region-scan-end, 0.0048657 secs]
8.294: [GC concurrent-mark-start]
8.318: [GC concurrent-mark-end, 0.0241535 secs]
8.318: [GC remark, 0.0041866 secs]
8.323: [GC cleanup 83M->57M(128M), 0.0003586 secs]
8.323: [GC concurrent-cleanup-start]
8.323: [GC concurrent-cleanup-end, 0.0000125 secs]
```
---

### 二. SerialGC

##### 1. 参数

```
-XX:+UseSerialGC -XX:InitialHeapSize=134217728 -XX:+ManagementServer -XX:MaxHeapSize=2147483648 -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops
```

##### 2. 日志
```
1.623: [GC (Allocation Failure) 1.623: [DefNew: 34944K->4351K(39296K), 0.0183744 secs] 34944K->5499K(126720K), 0.0184879 secs] [Times: user=0.02 sys=0.01, real=0.02 secs] 
3.278: [GC (Allocation Failure) 3.278: [DefNew: 39295K->3701K(39296K), 0.0180378 secs] 40443K->8397K(126720K), 0.0181336 secs] [Times: user=0.01 sys=0.01, real=0.01 secs] 
3.734: [GC (Allocation Failure) 3.734: [DefNew: 38645K->4352K(39296K), 0.0093910 secs] 43341K->11329K(126720K), 0.0094765 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
3.951: [GC (Allocation Failure) 3.951: [DefNew: 39296K->4352K(39296K), 0.0134845 secs] 46273K->12600K(126720K), 0.0136286 secs] [Times: user=0.01 sys=0.01, real=0.01 secs] 
4.167: [GC (Allocation Failure) 4.167: [DefNew: 39296K->4352K(39296K), 0.0068491 secs] 47544K->13904K(126720K), 0.0069466 secs] [Times: user=0.02 sys=0.00, real=0.00 secs] 
4.741: [GC (Allocation Failure) 4.741: [DefNew: 39296K->4352K(39296K), 0.0074685 secs] 48848K->16355K(126720K), 0.0075686 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
5.048: [GC (Allocation Failure) 5.048: [DefNew: 39296K->4352K(39296K), 0.0098970 secs] 51299K->18207K(126720K), 0.0100541 secs] [Times: user=0.03 sys=0.00, real=0.01 secs] 
5.337: [GC (Allocation Failure) 5.337: [DefNew: 39296K->4352K(39296K), 0.0088744 secs] 53151K->19947K(126720K), 0.0090067 secs] [Times: user=0.03 sys=0.00, real=0.00 secs] 
5.543: [GC (Allocation Failure) 5.543: [DefNew: 39296K->4351K(39296K), 0.0078132 secs] 54891K->21749K(126720K), 0.0079419 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
5.675: [GC (Allocation Failure) 5.675: [DefNew: 39295K->4352K(39296K), 0.0062014 secs] 56693K->23175K(126720K), 0.0063013 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
5.908: [GC (Allocation Failure) 5.908: [DefNew: 39296K->4352K(39296K), 0.0079283 secs] 58119K->25558K(126720K), 0.0080434 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
6.046: [GC (Allocation Failure) 6.046: [DefNew: 39296K->4352K(39296K), 0.0092343 secs] 60502K->28506K(126720K), 0.0093372 secs] [Times: user=0.02 sys=0.01, real=0.01 secs] 
6.217: [GC (Allocation Failure) 6.217: [DefNew: 39296K->4351K(39296K), 0.0079757 secs] 63450K->31006K(126720K), 0.0080807 secs] [Times: user=0.02 sys=0.00, real=0.00 secs] 
6.430: [GC (Allocation Failure) 6.430: [DefNew: 39295K->4351K(39296K), 0.0067948 secs] 65950K->32435K(126720K), 0.0069775 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
6.621: [GC (Allocation Failure) 6.621: [DefNew: 39295K->4351K(39296K), 0.0080398 secs] 67379K->35098K(126720K), 0.0081398 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
6.836: [GC (Allocation Failure) 6.836: [DefNew: 39295K->4351K(39296K), 0.0075888 secs] 70042K->37909K(126720K), 0.0076863 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
7.031: [GC (Allocation Failure) 7.031: [DefNew: 39295K->4352K(39296K), 0.0071039 secs] 72853K->40551K(126720K), 0.0072190 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
7.180: [GC (Allocation Failure) 7.180: [DefNew: 39296K->4352K(39296K), 0.0076475 secs] 75495K->42991K(126720K), 0.0077571 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
7.357: [GC (Allocation Failure) 7.357: [DefNew: 39296K->4352K(39296K), 0.0085039 secs] 77935K->45224K(126720K), 0.0086316 secs] [Times: user=0.02 sys=0.00, real=0.00 secs] 
7.526: [GC (Allocation Failure) 7.526: [DefNew: 39296K->4351K(39296K), 0.0078201 secs] 80168K->48547K(126720K), 0.0079294 secs] [Times: user=0.01 sys=0.01, real=0.01 secs] 
7.666: [GC (Allocation Failure) 7.666: [DefNew: 39295K->4351K(39296K), 0.0066354 secs] 83491K->50795K(126720K), 0.0067320 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
7.820: [GC (Allocation Failure) 7.820: [DefNew: 39295K->4352K(39296K), 0.0065459 secs] 85739K->53043K(126720K), 0.0066403 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
7.963: [GC (Allocation Failure) 7.963: [DefNew: 39296K->4351K(39296K), 0.0059325 secs] 87987K->54308K(126720K), 0.0060173 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
8.146: [GC (Allocation Failure) 8.146: [DefNew: 39295K->4352K(39296K), 0.0054155 secs] 89252K->55380K(126720K), 0.0054983 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
8.338: [GC (Allocation Failure) 8.338: [DefNew: 39296K->4352K(39296K), 0.0082618 secs] 90324K->57992K(126720K), 0.0083477 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
8.635: [GC (Allocation Failure) 8.635: [DefNew: 39296K->4352K(39296K), 0.0076442 secs] 92936K->60459K(126720K), 0.0077227 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
8.785: [GC (Allocation Failure) 8.785: [DefNew: 39296K->4351K(39296K), 0.0077143 secs] 95403K->63078K(126720K), 0.0077976 secs] [Times: user=0.00 sys=0.01, real=0.01 secs] 
8.991: [GC (Allocation Failure) 8.991: [DefNew: 39295K->4352K(39296K), 0.0054608 secs] 98022K->64173K(126720K), 0.0055536 secs] [Times: user=0.00 sys=0.01, real=0.01 secs] 
9.137: [GC (Allocation Failure) 9.137: [DefNew: 39296K->667K(39296K), 0.0039781 secs] 99117K->61131K(126720K), 0.0040545 secs] [Times: user=0.01 sys=0.00, real=0.00 secs] 
9.495: [GC (Allocation Failure) 9.495: [DefNew: 35611K->2851K(39296K), 0.0053303 secs] 96075K->63316K(126720K), 0.0054232 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
9.633: [Full GC (Metadata GC Threshold) 9.633: [Tenured: 60464K->26224K(87424K), 0.0405248 secs] 79323K->26224K(126720K), [Metaspace: 20720K->20720K(1069056K)], 0.0406465 secs] [Times: user=0.04 sys=0.01, real=0.04 secs] 
10.204: [GC (Allocation Failure) 10.204: [DefNew: 35072K->474K(39424K), 0.0025162 secs] 61296K->26699K(126848K), 0.0026061 secs] [Times: user=0.01 sys=0.00, real=0.00 secs] 
10.712: [GC (Allocation Failure) 10.713: [DefNew: 35546K->3269K(39424K), 0.0048534 secs] 61771K->29493K(126848K), 0.0049541 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
11.022: [GC (Allocation Failure) 11.022: [DefNew: 38341K->2513K(39424K), 0.0061290 secs] 64565K->29683K(126848K), 0.0062210 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
11.308: [GC (Allocation Failure) 11.308: [DefNew: 37585K->3502K(39424K), 0.0063852 secs] 64755K->31499K(126848K), 0.0064908 secs] [Times: user=0.02 sys=0.00, real=0.00 secs] 
11.516: [GC (Allocation Failure) 11.516: [DefNew: 38574K->2898K(39424K), 0.0068697 secs] 66571K->31618K(126848K), 0.0069705 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
11.770: [GC (Allocation Failure) 11.770: [DefNew: 37970K->2387K(39424K), 0.0092322 secs] 66690K->31435K(126848K), 0.0093572 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
12.108: [GC (Allocation Failure) 12.108: [DefNew: 37459K->1789K(39424K), 0.0055417 secs] 66507K->32284K(126848K), 0.0056338 secs] [Times: user=0.01 sys=0.00, real=0.00 secs] 
12.285: [GC (Allocation Failure) 12.285: [DefNew: 36861K->4352K(39424K), 0.0084448 secs] 67356K->37600K(126848K), 0.0085326 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
12.612: [GC (Allocation Failure) 12.612: [DefNew: 39424K->2249K(39424K), 0.0082902 secs] 72672K->39842K(126848K), 0.0083995 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
13.004: [GC (Allocation Failure) 13.004: [DefNew: 37321K->1507K(39424K), 0.0069211 secs] 74914K->40571K(126848K), 0.0070220 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
13.339: [GC (Allocation Failure) 13.339: [DefNew: 36579K->2096K(39424K), 0.0051045 secs] 75643K->41160K(126848K), 0.0051902 secs] [Times: user=0.01 sys=0.00, real=0.00 secs] 
13.440: [Full GC (Metadata GC Threshold) 13.440: [Tenured: 39064K->38245K(87424K), 0.0733976 secs] 50623K->38245K(126848K), [Metaspace: 34660K->34660K(1081344K)], 0.0735510 secs] [Times: user=0.21 sys=0.00, real=0.07 secs] 
```

--- 

### 三. ParallelGC

##### 1. 参数

```
-XX:+UseParallelGC -XX:+UseParallelOldGC -XX:InitialHeapSize=134217728 -XX:+ManagementServer -XX:MaxHeapSize=2147483648 -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops
```

##### 2. 日志
```
1.532: [GC (Allocation Failure) [PSYoungGen: 33280K->5113K(38400K)] 33280K->5667K(125952K), 0.0077908 secs] [Times: user=0.02 sys=0.01, real=0.01 secs] 
3.061: [GC (Allocation Failure) [PSYoungGen: 38393K->5114K(38400K)] 38947K->7496K(125952K), 0.0132321 secs] [Times: user=0.03 sys=0.01, real=0.01 secs] 
3.609: [GC (Allocation Failure) [PSYoungGen: 38394K->5092K(38400K)] 40776K->12124K(125952K), 0.0086209 secs] [Times: user=0.02 sys=0.01, real=0.00 secs] 
3.793: [GC (Allocation Failure) [PSYoungGen: 38372K->5093K(38400K)] 45404K->16615K(125952K), 0.0082094 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
4.019: [GC (Allocation Failure) [PSYoungGen: 38373K->5093K(38400K)] 49895K->21618K(125952K), 0.0127640 secs] [Times: user=0.03 sys=0.01, real=0.02 secs] 
4.242: [GC (Allocation Failure) [PSYoungGen: 38373K->9597K(78848K)] 54898K->26130K(166400K), 0.0129527 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
4.760: [GC (Allocation Failure) [PSYoungGen: 76157K->12279K(78848K)] 92690K->32715K(166400K), 0.0376684 secs] [Times: user=0.05 sys=0.02, real=0.03 secs] 
5.677: [GC (Allocation Failure) [PSYoungGen: 78839K->13808K(147968K)] 99275K->36134K(235520K), 0.0241345 secs] [Times: user=0.04 sys=0.01, real=0.03 secs] 
7.365: [GC (Allocation Failure) [PSYoungGen: 145904K->16890K(148992K)] 168230K->49823K(236544K), 0.0466083 secs] [Times: user=0.05 sys=0.01, real=0.05 secs] 
9.159: [GC (Allocation Failure) [PSYoungGen: 148986K->23019K(228352K)] 181919K->64514K(315904K), 0.0324634 secs] [Times: user=0.03 sys=0.02, real=0.03 secs] 
10.410: [GC (Allocation Failure) [PSYoungGen: 228331K->29152K(234496K)] 269826K->84150K(322048K), 0.0424855 secs] [Times: user=0.05 sys=0.02, real=0.04 secs] 
11.782: [GC (Allocation Failure) [PSYoungGen: 234464K->28219K(365056K)] 289462K->86188K(452608K), 0.0418808 secs] [Times: user=0.05 sys=0.02, real=0.04 secs] 
12.707: [GC (Metadata GC Threshold) [PSYoungGen: 93980K->9809K(367104K)] 151948K->71647K(454656K), 0.0197780 secs] [Times: user=0.02 sys=0.01, real=0.02 secs] 
12.727: [Full GC (Metadata GC Threshold) [PSYoungGen: 9809K->0K(367104K)] [ParOldGen: 61837K->30458K(81920K)] 71647K->30458K(449024K), [Metaspace: 20836K->20836K(1069056K)], 0.0727269 secs] [Times: user=0.13 sys=0.02, real=0.08 secs] 
18.195: [GC (Allocation Failure) [PSYoungGen: 326656K->19443K(503296K)] 357114K->49909K(585216K), 0.0250258 secs] [Times: user=0.05 sys=0.01, real=0.03 secs]
19.072: [GC (Metadata GC Threshold) [PSYoungGen: 105848K->3608K(543232K)] 136315K->40160K(625152K), 0.0114646 secs] [Times: user=0.03 sys=0.00, real=0.01 secs]
19.084: [Full GC (Metadata GC Threshold) [PSYoungGen: 3608K->0K(543232K)] [ParOldGen: 36551K->33654K(97280K)] 40160K->33654K(640512K), [Metaspace: 34760K->34760K(1081344K)], 0.0774916 secs] [Times: user=0.24 sys=0.00, real=0.08 secs]
```

--- 

### 四. CMS

##### 1. 参数

```
-XX:+UseConcMarkSweepGC -XX:+UseParNewGC -XX:InitialHeapSize=134217728 -XX:+ManagementServer -XX:MaxHeapSize=2147483648 -XX:MaxNewSize=348966912 -XX:MaxTenuringThreshold=6 -XX:OldPLABSize=16 -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops
```

##### 2. 日志

```
2.215: [GC (Allocation Failure) 2.215: [ParNew: 34944K->4352K(39296K), 0.0208502 secs] 34944K->5478K(126720K), 0.0210053 secs] [Times: user=0.02 sys=0.00, real=0.02 secs] 
3.802: [GC (Allocation Failure) 3.802: [ParNew: 39296K->4351K(39296K), 0.0240829 secs] 40422K->9315K(126720K), 0.0242022 secs] [Times: user=0.05 sys=0.00, real=0.02 secs] 
4.276: [GC (Allocation Failure) 4.276: [ParNew: 39295K->4352K(39296K), 0.0076599 secs] 44259K->11976K(126720K), 0.0078175 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
4.608: [GC (Allocation Failure) 4.608: [ParNew: 39296K->4351K(39296K), 0.0066430 secs] 46920K->15798K(126720K), 0.0067270 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
4.810: [GC (Allocation Failure) 4.810: [ParNew: 39295K->4352K(39296K), 0.0073201 secs] 50742K->18094K(126720K), 0.0074164 secs] [Times: user=0.01 sys=0.00, real=0.00 secs] 
5.029: [GC (Allocation Failure) 5.029: [ParNew: 39296K->4351K(39296K), 0.0060781 secs] 53038K->19988K(126720K), 0.0061599 secs] [Times: user=0.01 sys=0.00, real=0.00 secs] 
5.204: [GC (Allocation Failure) 5.204: [ParNew: 39295K->4352K(39296K), 0.0062820 secs] 54932K->21590K(126720K), 0.0064513 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
5.859: [GC (Allocation Failure) 5.859: [ParNew: 39296K->4352K(39296K), 0.0069225 secs] 56534K->24240K(126720K), 0.0070216 secs] [Times: user=0.02 sys=0.00, real=0.00 secs] 
6.119: [GC (Allocation Failure) 6.119: [ParNew: 39296K->4352K(39296K), 0.0053306 secs] 59184K->26223K(126720K), 0.0054348 secs] [Times: user=0.01 sys=0.00, real=0.00 secs] 
6.308: [GC (Allocation Failure) 6.308: [ParNew: 39296K->4352K(39296K), 0.0063112 secs] 61167K->29383K(126720K), 0.0064065 secs] [Times: user=0.02 sys=0.00, real=0.00 secs] 
6.558: [GC (Allocation Failure) 6.558: [ParNew: 39296K->4352K(39296K), 0.0084669 secs] 64327K->32409K(126720K), 0.0085710 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
6.769: [GC (Allocation Failure) 6.771: [ParNew: 39296K->4352K(39296K), 0.0063498 secs] 67353K->36184K(126720K), 0.0086800 secs] [Times: user=0.02 sys=0.00, real=0.00 secs] 
6.977: [GC (Allocation Failure) 6.977: [ParNew: 39296K->4352K(39296K), 0.0056891 secs] 71128K->39132K(126720K), 0.0057944 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
7.111: [GC (Allocation Failure) 7.111: [ParNew: 39296K->4351K(39296K), 0.0067887 secs] 74076K->41640K(126720K), 0.0068703 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
7.245: [GC (Allocation Failure) 7.245: [ParNew: 39295K->4352K(39296K), 0.0068674 secs] 76584K->43975K(126720K), 0.0069758 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
7.381: [GC (Allocation Failure) 7.381: [ParNew: 39296K->4351K(39296K), 0.0064284 secs] 78919K->45693K(126720K), 0.0065116 secs] [Times: user=0.01 sys=0.00, real=0.00 secs] 
7.543: [GC (Allocation Failure) 7.543: [ParNew: 39295K->4352K(39296K), 0.0060510 secs] 80637K->47086K(126720K), 0.0061529 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
7.666: [GC (Allocation Failure) 7.666: [ParNew: 39296K->4352K(39296K), 0.0073885 secs] 82030K->48871K(126720K), 0.0074777 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
7.673: [GC (CMS Initial Mark) [1 CMS-initial-mark: 44519K(87424K)] 48879K(126720K), 0.0007097 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
7.674: [CMS-concurrent-mark-start]
7.724: [CMS-concurrent-mark: 0.049/0.049 secs] [Times: user=0.12 sys=0.02, real=0.05 secs] 
7.724: [CMS-concurrent-preclean-start]
7.725: [CMS-concurrent-preclean: 0.001/0.001 secs] [Times: user=0.01 sys=0.00, real=0.00 secs] 
7.725: [CMS-concurrent-abortable-preclean-start]
7.865: [GC (Allocation Failure) 7.865: [ParNew: 39296K->4352K(39296K), 0.0061582 secs] 83815K->51075K(126720K), 0.0062451 secs] [Times: user=0.01 sys=0.00, real=0.01 secs] 
8.026: [CMS-concurrent-abortable-preclean: 0.059/0.301 secs] [Times: user=0.50 sys=0.04, real=0.30 secs] 
8.026: [GC (CMS Final Remark) [YG occupancy: 28873 K (39296 K)]8.026: [Rescan (parallel) , 0.0044755 secs]8.031: [weak refs processing, 0.0003764 secs]8.031: [class unloading, 0.0059077 secs]8.037: [scrub symbol table, 0.0015518 secs]8.039: [scrub string table, 0.0004965 secs][1 CMS-remark: 46723K(87424K)] 75597K(126720K), 0.0137085 secs] [Times: user=0.02 sys=0.00, real=0.02 secs] 
8.040: [CMS-concurrent-sweep-start]
8.053: [CMS-concurrent-sweep: 0.013/0.013 secs] [Times: user=0.03 sys=0.00, real=0.01 secs] 
8.053: [CMS-concurrent-reset-start]
8.056: [CMS-concurrent-reset: 0.003/0.003 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
```

