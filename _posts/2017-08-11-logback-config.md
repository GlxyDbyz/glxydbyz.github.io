---
layout:     post
title:      Logback配置记录
subtitle:   Logback配置文件备份
date:       2017-08-11
author:     壹芝
header-img: img/post-bg-coffee.jpg
catalog: true
tags:
    - Logback
    - Logger
    - Java
---

## Logback配置记录

###### 整理自本人的有道历史云笔记

#### Xml Config

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="false">

    <springProperty name="LOGBACK_LOG_PATH" source="logback.log.path" defaultValue="~/tmp/logs"/>

    <springProperty name="LOGBACK_STDOUT_LEVEL" source="logback.stdout.level" defaultValue="INFO"/>

    <property name="STDOUT_LEVEL" value="${LOGBACK_STDOUT_LEVEL}"/>

    <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径-->
    <property name="LOG_PATH" value="${LOGBACK_LOG_PATH}"/>

    <!-- 日志文件大小,超过这个大小将被压缩 -->
    <property name="LOG_MAX_SIZE" value="800MB"/>

    <!-- 模块名称， 影响日志配置名，日志文件名 -->
    <property name="MODULE_NAME" value="appointment"/>

    <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
    <property name="NORMAL_PATTERN"
              value="[%d{yyyy-MM-dd HH:mm:ss.SSS}] %boldYellow([%traceId]) - %highlight(%-5p) - %-22t - %boldBlue([%c.%method:%L]) - %logger{50} - %msg%n"/>

    <!--自定义-->
    <conversionRule conversionWord="traceId" converterClass="com.dbyz.base.trace.TraceIdLogConverter"/>

    <!-- 控制台输出 -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${NORMAL_PATTERN}</pattern>
        </encoder>

        <!--输出level级别及其以上的日志-->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>${STDOUT_LEVEL}</level>
        </filter>

    </appender>

    <!-- 按照每天生成日志文件 -->
    <appender name="INFO" class="ch.qos.logback.core.rolling.RollingFileAppender">

        <File>${LOG_PATH}/${MODULE_NAME}_info.log</File>

        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">

            <FileNamePattern>${LOG_PATH}/history/${MODULE_NAME}_info.log.%d{yyyy-MM-dd}.%i.zip</FileNamePattern>

            <!-- 文件输出日志 (文件大小策略进行文件输出，超过指定大小对文件备份) -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>${LOG_MAX_SIZE}</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>

            <!--以天为单位进行日志留存，超过maxHistory天的旧日志将被删除-->
            <maxHistory>7</maxHistory>

            <!--若使用totalSizeCap，maxHistory必须存在，且在满足maxHistory的过滤条件下才能进行totalSizeCap的过滤-->
            <totalSizeCap>16GB</totalSizeCap>

        </rollingPolicy>

        <append>true</append>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${NORMAL_PATTERN}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>INFO</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- 按照每天生成日志文件 -->
    <appender name="ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">

        <File>${LOG_PATH}/${MODULE_NAME}_error.log</File>

        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">

            <FileNamePattern>${LOG_PATH}/history/${MODULE_NAME}_error.log.%d{yyyy-MM-dd}.%i.zip</FileNamePattern>

            <!-- 文件输出日志 (文件大小策略进行文件输出，超过指定大小对文件备份) -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>${LOG_MAX_SIZE}</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>

            <!--以天为单位进行日志留存，超过maxHistory天的旧日志将被删除-->
            <maxHistory>7</maxHistory>

            <!--若使用totalSizeCap，maxHistory必须存在，且在满足maxHistory的过滤条件下才能进行totalSizeCap的过滤-->
            <totalSizeCap>5GB</totalSizeCap>

        </rollingPolicy>

        <append>true</append>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${NORMAL_PATTERN}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- 按照每天生成日志文件 -->
    <appender name="WARN" class="ch.qos.logback.core.rolling.RollingFileAppender">

        <File>${LOG_PATH}/${MODULE_NAME}_warn.log</File>

        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">

            <FileNamePattern>${LOG_PATH}/history/${MODULE_NAME}_warn.log.%d{yyyy-MM-dd}.%i.zip</FileNamePattern>

            <!-- 文件输出日志 (文件大小策略进行文件输出，超过指定大小对文件备份) -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>${LOG_MAX_SIZE}</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>

            <!--以天为单位进行日志留存，超过maxHistory天的旧日志将被删除-->
            <maxHistory>7</maxHistory>

            <!--若使用totalSizeCap，maxHistory必须存在，且在满足maxHistory的过滤条件下才能进行totalSizeCap的过滤-->
            <totalSizeCap>5GB</totalSizeCap>

        </rollingPolicy>

        <append>true</append>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${NORMAL_PATTERN}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>WARN</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <!-- 日志输出级别 -->
    <springProfile name="dev,test">
        <root level="INFO">
            <appender-ref ref="INFO"/>
            <appender-ref ref="ERROR"/>
            <appender-ref ref="WARN"/>
            <appender-ref ref="STDOUT"/>
        </root>
    </springProfile>

    <springProfile name="gray,pro">
        <root level="INFO">
            <appender-ref ref="INFO"/>
            <appender-ref ref="ERROR"/>
            <appender-ref ref="WARN"/>
        </root>
    </springProfile>
</configuration>

```

#### TraceId 打印

```
import ch.qos.logback.classic.pattern.ClassicConverter;
import ch.qos.logback.classic.spi.ILoggingEvent;

public class TraceIdLogConverter extends ClassicConverter {

    @Override
    public String convert(ILoggingEvent iLoggingEvent) {
        TraceContext context = TraceUtils.threadLocal.get();
        return context == null?"": context.getTraceId();
    }
}

```
