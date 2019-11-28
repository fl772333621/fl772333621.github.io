# Spring IOC 基本原理

简而言之，IOC就是控制反转。如何实现控制反转，则使用DI，即使用依赖注入。我需要什么Bean，不再由我自己去创建，交由Spring去创建去销毁去管理其生命周期。
所以IOC的基础是有一个spring容器，由这个容器统一管理全部bean的生命周期。这个容器一般是BeanFactory和ApplicationContext。
BeanFactory内加载配置文件，解析Bean，并完成依赖注入。
ApplicationContext内国际化、AOP、访问资源、消息发送等。

# 什么是DI机制？

依赖注入和控制反转是同一个概念。
当我需要另一个Bean的时候，传统的设计方法是自己去new出来，用Spring的DI机制，就由Spring容器负责这个Bean的生命周期，并哪里需要这个Bean就注入到哪里。
Spring注入的两种方式：一种是设置注入，另一种是构造注入。构造注入可以决定依赖关系的顺序。