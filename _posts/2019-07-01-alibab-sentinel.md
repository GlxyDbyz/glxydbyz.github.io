---
layout:     post
title:      SpringMvc 接入 Alibaba Sentinel
subtitle:   流控工具 Alibaba Sentinel 的使用
date:       2019-07-01
author:     壹芝
header-img: img/post-bg-yellow-duck.jpg
catalog: true
tags:
    - Alibaba Sentinel
    - Java
    - SpringMvc
---

## SpringMvc 接入 Alibaba Sentinel

###### 整理自本人的有道历史云笔记

> [Sentinel 官方文档](https://github.com/alibaba/Sentinel/wiki/%E4%BB%8B%E7%BB%8D/) 对 SpringMvc 的接入方式的介绍较少,故记录如下

##### Sentinel 是 Alibaba 中间件团队2018年开源的一款纯 Java 实现的流量控制、熔断降级、系统负载保护工具

Sentinel 具有以下特征:

- 丰富的应用场景：Sentinel 承接了阿里巴巴近 10 年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷、集群流量控制、实时熔断下游不可用应用等。

- 完备的实时监控：Sentinel 同时提供实时的监控功能。您可以在控制台中看到接入应用的单台机器秒级数据，甚至 500 台以下规模的集群的汇总运行情况。

- 广泛的开源生态：Sentinel 提供开箱即用的与其它开源框架/库的整合模块，例如与 Spring Cloud、Dubbo、gRPC 的整合。您只需要引入相应的依赖并进行简单的配置即可快速地接入 Sentinel。

- 完善的 SPI 扩展点：Sentinel 提供简单易用、完善的 SPI 扩展接口。您可以通过实现扩展接口来快速地定制逻辑。例如定制规则管理、适配动态数据源等。

![image](https://user-images.githubusercontent.com/9434884/50505538-2c484880-0aaf-11e9-9ffc-cbaaef20be2b.png)

### SpringMvc 接入流程

#### 1. 下载 sentinel-dashboard-1.6.2.jar 并启动 Sentinel Dashboard 

```
java -Dserver.port=8080 -Dcsp.sentinel.dashboard.server=localhost:8080 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard.jar
```
更多请查看 [Sentinel Dashboard 官方文档](https://github.com/alibaba/Sentinel/wiki/%E6%8E%A7%E5%88%B6%E5%8F%B0#2-%E5%90%AF%E5%8A%A8%E6%8E%A7%E5%88%B6%E5%8F%B0/)

#### 2. jar引入
```
    <dependency>
        <groupId>com.alibaba.csp</groupId>
        <artifactId>sentinel-core</artifactId>
        <version>1.6.2</version>
    </dependency>

    <dependency>
        <groupId>com.alibaba.csp</groupId>
        <artifactId>sentinel-transport-simple-http</artifactId>
       <version>1.6.2</version>
    </dependency>

    <dependency>
        <groupId>com.alibaba.csp</groupId>
        <artifactId>sentinel-web-servlet</artifactId>
        <version>1.6.2</version>
    </dependency>

    <dependency>
        <groupId>com.alibaba.csp</groupId>
        <artifactId>sentinel-annotation-aspectj</artifactId>
       <version>1.6.2</version>
    </dependency>
```

#### 3. 添加 SentinelCommonFilter 拦截器
```
    <filter>
        <filter-name>SentinelCommonFilter</filter-name>
        <filter-class>com.alibaba.csp.sentinel.adapter.servlet.CommonFilter</filter-class>
    </filter>
    
    <filter-mapping>
        <filter-name>SentinelCommonFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

#### 4. 注解方式添加要流控的资源和流控处理类

@SentinelResource 使用
```
    @SentinelResource(value = "ProductMsgCommonConsumerImpl.queryPageByParam", blockHandlerClass = ProductMsgBlockHandle.class, blockHandler = "blockHandlerForPage", entryType = EntryType.IN)
    public PageBean<ProductBO> queryPageByParam(ProductQuery query) {
    .......
    .......
    }
```

返回空列表,也可以返回其他兜底数据
```
import com.alibaba.csp.sentinel.slots.block.BlockException;
import com.alibaba.fastjson.JSON;
import lombok.extern.slf4j.Slf4j;

import java.util.LinkedList;
import java.util.List;

/**
 * @author fangzheng
 * @version $Id: ProductBlockHandel
 */
@Slf4j
public class ProductMsgBlockHandle {

    /**
     * 产品分页流量过滤返回值
     *
     * @param query
     * @param ex
     * @return
     */
    public static PageBean<ProductBO> blockHandlerForPage(ProductQuery query, BlockException ex) {
        log.warn("param [{}]", JSON.toJSON(productMsgAssembleDTO), ex);
        return new PageBean<>();
    }

    /**
     * 产品列表流量过滤返回值
     *
     * @param query
     * @param ex
     * @return
     */
    public static List<ProductBO> blockHandlerForList(ProductQuery query, BlockException ex) {
        log.warn("param [{}]", JSON.toJSON(productMsgAssembleDTO), ex);
        return new LinkedList<>();
    }
}

```

#### 5. 实现自己的配置保存DS,否则配置信息默认保存在内存中,重启就失效了
```

import com.alibaba.csp.sentinel.adapter.servlet.config.WebServletConfig;
import com.alibaba.csp.sentinel.datasource.Converter;
import com.alibaba.csp.sentinel.datasource.FileRefreshableDataSource;
import com.alibaba.csp.sentinel.datasource.FileWritableDataSource;
import com.alibaba.csp.sentinel.datasource.ReadableDataSource;
import com.alibaba.csp.sentinel.datasource.WritableDataSource;
import com.alibaba.csp.sentinel.slots.block.degrade.DegradeRule;
import com.alibaba.csp.sentinel.slots.block.degrade.DegradeRuleManager;
import com.alibaba.csp.sentinel.slots.block.flow.FlowRule;
import com.alibaba.csp.sentinel.slots.block.flow.FlowRuleManager;
import com.alibaba.csp.sentinel.slots.system.SystemRule;
import com.alibaba.csp.sentinel.slots.system.SystemRuleManager;
import com.alibaba.csp.sentinel.transport.util.WritableDataSourceRegistry;
import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.TypeReference;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;
import java.io.File;
import java.io.FileNotFoundException;
import java.util.List;

/**
 * SentinelRulesFileDsConfig
 * <p>
 * sentinel配置文件存储配置
 *
 * @author fangzheng
 * @version $Id: SentinelRulesFileDsConfig
 */
@Component
@Slf4j
public class SentinelRulesFileDsConfig {

    @PostConstruct
    public void init() {

        // 默认返回的降级json
        WebServletConfig.setBlockPage("http://xxxx.json");

        // A fake path.
        String rulesDir = "/usr/local/tomcat/conf/sentinel/rules";

        try {

            File dir = new File("/usr/local/tomcat/conf/sentinel/rules");
            if (!dir.exists()) {
                dir.mkdirs();
            }

            initFlowRuleDs(rulesDir);

            initDegradeRuleDs(rulesDir);

            initSystemRuleDs(rulesDir);

        } catch (Exception e) {
            log.error("init rules error", e);
        }
    }

    /**
     * 初始化降级规则配置ds
     *
     * @param rulesDir
     * @throws FileNotFoundException
     */
    private void initDegradeRuleDs(String rulesDir) throws FileNotFoundException {

        String degradeRuleFile = "degradeRule.json";
        String degradeRulePath = rulesDir + "/" + degradeRuleFile;

        ReadableDataSource<String, List<DegradeRule>> degradeRuleDs = new FileRefreshableDataSource<>(
                degradeRulePath, new Converter<String, List<DegradeRule>>() {
            @Override
            public List<DegradeRule> convert(String source) {
                return JSON.parseObject(source, new TypeReference<List<DegradeRule>>() {
                });
            }
        });

        // Register to xxx rule manager.
        DegradeRuleManager.register2Property(degradeRuleDs.getProperty());

        WritableDataSource<List<DegradeRule>> degradeRuleDsWDs = new FileWritableDataSource<>(degradeRulePath, new Converter<List<DegradeRule>, String>() {
            @Override
            public String convert(List<DegradeRule> flowRules) {
                return JSON.toJSONString(flowRules);
            }
        });

        // Register to writable data source registry so that rules can be updated to file
        // when there are rules pushed from the Sentinel Dashboard.
        WritableDataSourceRegistry.registerDegradeDataSource(degradeRuleDsWDs);
    }

    /**
     * 初始系统规则配置ds
     *
     * @param rulesDir
     * @throws FileNotFoundException
     */
    private void initSystemRuleDs(String rulesDir) throws FileNotFoundException {

        String systemRuleFile = "systemRule.json";
        String systemRulePath = rulesDir + "/" + systemRuleFile;

        ReadableDataSource<String, List<SystemRule>> systemRuleDs = new FileRefreshableDataSource<>(
                systemRulePath, new Converter<String, List<SystemRule>>() {
            @Override
            public List<SystemRule> convert(String source) {
                return JSON.parseObject(source, new TypeReference<List<SystemRule>>() {
                });
            }
        });

        // Register to xxx rule manager.
        SystemRuleManager.register2Property(systemRuleDs.getProperty());

        WritableDataSource<List<SystemRule>> systemRuleDsWDs = new FileWritableDataSource<>(systemRulePath, new Converter<List<SystemRule>, String>() {
            @Override
            public String convert(List<SystemRule> flowRules) {
                return JSON.toJSONString(flowRules);
            }
        });

        // Register to writable data source registry so that rules can be updated to file
        // when there are rules pushed from the Sentinel Dashboard.
        WritableDataSourceRegistry.registerSystemDataSource(systemRuleDsWDs);
    }

    /**
     * 初始化流控规则配置ds
     *
     * @param rulesDir
     * @throws FileNotFoundException
     */
    private void initFlowRuleDs(String rulesDir) throws FileNotFoundException {

        String flowRuleFile = "flowRule.json";
        String flowRulePath = rulesDir + "/" + flowRuleFile;

        ReadableDataSource<String, List<FlowRule>> ds = new FileRefreshableDataSource<>(
                flowRulePath, new Converter<String, List<FlowRule>>() {
            @Override
            public List<FlowRule> convert(String source) {
                return JSON.parseObject(source, new TypeReference<List<FlowRule>>() {
                });
            }
        });

        // Register to xxx rule manager.
        FlowRuleManager.register2Property(ds.getProperty());

        WritableDataSource<List<FlowRule>> wds = new FileWritableDataSource<>(flowRulePath, new Converter<List<FlowRule>, String>() {
            @Override
            public String convert(List<FlowRule> flowRules) {
                return JSON.toJSONString(flowRules);
            }
        });

        // Register to writable data source registry so that rules can be updated to file
        // when there are rules pushed from the Sentinel Dashboard.
        WritableDataSourceRegistry.registerFlowDataSource(wds);
    }
}

```

