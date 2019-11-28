---
layout:     post
title:      "两个线程交替执行，分别输出奇数和偶数"
subtitle:   "奇数偶数分别打印"
date:       2019-11-28 11:15:00
author:     "MengWei"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Java
    - 面试题
    - 多线程
    - 奇数
    - 偶数
    - ReentrantLock
    - CAS

---

# 1 题目表述

Java多线程中，要求两个线程交替执行，一个输出奇数，一个输出偶数



# 2 使用ReentrantLock实现(可行)

两个线程分别输出奇数和偶数。

1. 抢占锁
2. 判断是否可以输出（线程1是当前为偶数才可输出，线程2是当前为奇数才可输出）
3. 可以输出则输出，不可输出则跳过
4. 释放锁

```java
import org.junit.Test;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.concurrent.locks.ReentrantLock;

public class ReentrantLockSolution {

    @Test
    public void test() throws InterruptedException {
        final ReentrantLock lock = new ReentrantLock(true);
        final AtomicInteger count = new AtomicInteger(0);
        Thread t1 = new Thread(() -> {
            while (true) {
                lock.lock();
                if (count.get() % 2 == 0) {
                    System.out.println("奇 " + count.incrementAndGet());
                }
                lock.unlock();
            }
        });
        Thread t2 = new Thread(() -> {
            while (true) {
                lock.lock();
                if (count.get() % 2 == 1) {
                    System.out.println("偶 " + count.incrementAndGet());
                }
                lock.unlock();
            }
        });
        t1.start();
        t2.start();
        Thread.sleep(1000);
        t1.interrupt();
        t2.interrupt();
    }
}

```

多线程并发环境下，以上的输出结果是什么？

>奇 1
>
>偶 2
>
>奇 3
>
>......




