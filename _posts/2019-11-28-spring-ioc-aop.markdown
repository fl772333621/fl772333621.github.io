---
layout:     post
title:      "Spring IOC AOP"
subtitle:   "IOC和AOP的相关面试题"
date:       2019-11-28 11:15:00
author:     "MengWei"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Java
    - 面试题
    - AOP
    - IOC
    - DI

---
# Spring IOC 基本原理

简而言之，IOC就是控制反转。如何实现控制反转，则使用DI，即使用依赖注入。我需要什么Bean，不再由我自己去创建，交由Spring去创建去销毁去管理其生命周期。
所以IOC的基础是有一个spring容器，由这个容器统一管理全部bean的生命周期。这个容器一般是BeanFactory和ApplicationContext。
BeanFactory内加载配置文件，解析Bean，并完成依赖注入。
ApplicationContext内国际化、AOP、访问资源、消息发送等。

# 什么是DI机制？

依赖注入和控制反转是同一个概念。
当我需要另一个Bean的时候，传统的设计方法是自己去new出来，用Spring的DI机制，就由Spring容器负责这个Bean的生命周期，并哪里需要这个Bean就注入到哪里。
Spring注入的两种方式：一种是设置注入，另一种是构造注入。构造注入可以决定依赖关系的顺序。

# 什么是AOP
AOP是面向切面编程。在Spring中主要表现为两个方面。
1. 面向切面编程提供声明式事务管理
2. spring支持用户自定义的切面
