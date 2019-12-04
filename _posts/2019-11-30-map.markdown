---
layout:     post
title:      "ConcurrentHashMap专题"
subtitle:   ""
date:       2019-11-30 16:15:00
author:     "MengWei"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Java
    - 面试题
    - ConcurrentHashMap
---

# ConcurrentHashMap专题知识

## ConcurrentHashMap的1.7和1.8的区别

|          | JDK1.7                                                       | JDK1.8                                                       |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 整体结构 | Segment+HashEntry+Unsafe                                     | synchronized+CAS+Node+Unsafe                                 |
| put()    | 先定位到Segment<br />再定位到桶<br />put全程加锁操作         | 直接定位到桶<br />判断first节点<br />1、为空则CAS插入<br />2、为-1表示正在扩容，则跟着一起扩容<br />3、else则加锁put |
| get()    | 类似，value生命为volatile，保证了修改的可见性，因此为无锁获取。 | 类似，value生命为volatile，保证了修改的可见性，因此为无锁获取。 |
| resize() | 单线程扩容，跟HashMap一样的扩容                              | 并发扩容。<br />HashMap扩容在1.8中由头插修改为尾插（避免死循环问题）<br />ConcurrentHashMap也是从尾部开始迁移<br />扩容前在桶的头部防止一个hash=-1的节点，告知别的线程该桶正在扩容 |
| size()   | 经典的思路计算两次，如果不变则返回，否则锁住全部Segment求和  | 维护了一个baseCount'字段，直接返回该值                       |

