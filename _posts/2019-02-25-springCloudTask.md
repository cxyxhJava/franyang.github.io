---
layout:     post
title:      SpringCloudTask
subtitle:   快速入门和使用
date:       2019-12-11
author:     程序猿小哈
header-img: img/post-bg-os-metro.jpg
catalog: 	 true
tags:
    - 中间件
    - 消息队列
---

# SpringCloudTask
Spring Cloud Task使创建短期的微服务变得很容易。它提供了在生产环境中按需执行短期JVM进程的功能。

Spring Cloud Task使用关系数据库存储已执行任务的结果。虽然可以在不使用数据库的情况下开始开发任务(任务的状态作为任务存储库更新的一部分被记录下来)，但是对于生产环境，您希望使用受支持的数据库。Spring Cloud Task目前支持以下数据库:
DB2
H2
HSQLDB
MySql
Oracle
Postgres
SqlServer

本例中的第一个非引导注释是`@EnableTask`注释。这个类级注释告诉Spring Cloud Task引导它的功能。默认情况下，它导入一个额外的配置类`SimpleTaskConfiguration`。此附加配置注册任务存储库和基础设施以供其使用。

`TaskRepository`使用内存中的映射来记录任务的结果

对于生产环境来说，Map不是一个实用的解决方案，因为一旦任务结束，Map就会消失。但是，对于快速启动的体验，我们将其作为默认设置，并将更新的内容回显到日志中。在第8章配置部分(稍后的文档中)，我们将介绍如何自定义Spring Cloud Task提供的组件的配置。

W当我们的示例应用程序运行时，Spring Boot启动HelloWorldCommandLineRunner并输出“Hello, World!”信息发送到标准输出。TaskLifecyceListener在存储库中记录任务的开始和结束。

Spring包含许多方法来引导应用程序的逻辑。Spring Boot通过它的*Runner接口(CommandLineRunner或ApplicationRunner)提供了一种方便的方法，可以有组织地完成这一工作。表现良好的任务可以通过使用这两个运行程序之一来引导任何逻辑。


任务的生命周期从执行*Runner#run方法之前一直考虑到它们全部完成。Spring Boot允许应用程序使用多个*Runner实现，Spring Cloud Task也是如此。

除了`CommandLineRunner`或`ApplicationRunner`(例如，通过使用InitializingBean#afterPropertiesSet)之外，从其他机制引导的任何处理都不会被Spring Cloud Task记录下来


`SimpleTaskRepository`记录了任务存储库中条目的创建。
执行我们的命令，由“你好，世界!””输出。
`SimpleTaskRepository`在任务存储库中记录任务的完成情况。

Features 特性

在大多数情况下,现代云环境的设计围绕的是预期不会结束的进程的执行,如果它们确实结束了，它们通常会重新开始。
虽然大多数平台都有一些方法来运行流程，但是流程结束时不会重新启动，但是运行的结果通常不会以可消费的方式进行维护。

Spring Cloud Task offers the ability to execute short-lived processes in an environment and record the results. Doing so allows for a microservices architecture around short-lived processes as well as longer running services through the integration of tasks by messages.