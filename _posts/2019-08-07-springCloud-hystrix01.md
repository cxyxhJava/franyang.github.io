---
layout:     post
title:      Hystrix简介
subtitle:   Hystrix入门第一篇
date:       2019-02-19
author:     程序猿小哈
header-img: img/post-bg-debug.png
catalog: 	true
tags:
    - spring
    - 框架
---

# Hystrix的简介

### 为什么需要hystrix

服务雪崩：
多微服务调用是,服务A调用B和服务C,服务B和服务C又调用其他微服务,这就是所谓的**扇出**.如果扇出的链路上某个微服务的调用响应时间过长或不可用,对服务A的调用就会占用越来越多的系统资源,进而引起系统崩溃.

对于高流量的应用来说,单一的后端依赖可能会导致所有服务器的所有资源在几秒钟内饱和,进而导致服务间的延迟增加,备份队列,线程和其他系统资源紧张,导致整个系统发生更多的次联故障.这些都表示需要对故障和延迟进行隔离和管理,以便单个依赖关系的失败,不能影响整个应用程序或系统.

这就需要Hystrix来进行故障和延迟的隔离和管理
![熔断器图1](/postImg/hystrix01.jpg "")

### Hystrix介绍
Hystrix:一个用于处理分布式系统`延迟`和`故障`的开源库,在分布式系统里,许多依赖不可避免的会调用失败,比如超时、异常等.hystrix能保证在一个依赖出问题的情况下,`不会导致整体服务失败,避免次联故障,以提高分布式系统的弹性`.

Hystrix本身是一种开关装置,当服务单元故障后,通过熔断(类似熔断保险丝),`向调用方法放回一个符合预期的,可处理的备选响应(FallBack),而不是长时间的等待或者抛出调用方无法处理的异常`,这样就保证了服务调用方的线程不会被长时间,不必要的占用。


### 服务熔断
当某个服务故障或者异常引起,直接熔断整个服务,而不是等待此服务超时。

1.在application上添加 `@EnableCircuitBreaker`或者`@EnableHystrix` 开启对熔断器的支持
2.方法上添加`@HystrixCommand(fallbackMethod = "备选回调方法")` 给方法添加熔断回调方法。


### 服务降级
从整体负荷考虑,当某个服务熔断以后,服务器不再可用,此时客户端可以自己准备一个本地的fallback回调,放回一个省缺值。保证服务可用,而不出异常。

在`@FeginClient` 中添加 `fallbackFactory`属性  自定义本地工厂类实现fallbackFactory,配置默认回调方法。

### 服务监控 hystrixDashboard
监控服务
1.maven导入hystrixDashboard的依赖包
2.application类上添加 `@EnableHystrixDashboard`注解
3.被监控服务添加`actuator`完善监控信息
4.启动后页面地址localhost:9001/hystrix















