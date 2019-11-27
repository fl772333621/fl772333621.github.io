---
layout:     post
title:      "volatile ReentrantLock synchronized"
subtitle:   "volatile ReentrantLock synchronized"
date:       2019-11-26 12:00:00
author:     "MengWei"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Java
    - 面试题
    - volatile
    - ReentrantLock
    - synchronized
    - CAS
---

# 1 volatile 能使得一个非原子操作变成原子操作吗? 

```java
import org.junit.Test;
import java.util.concurrent.atomic.AtomicInteger;

public class MyTest {
    
    private int count1 = 0;
    private volatile int count2 = 0;
    private AtomicInteger count3 = new AtomicInteger(0);

    @Test
    public void testcount() throws InterruptedException {
        final int iMax = 1000;
        final int jMax = 10000;
        for (int i = 0; i < iMax; i++) {
            new Thread(() -> {
                for (int j = 0; j < jMax; j++) {
                    count1++;
                    count2++;
                    count3.incrementAndGet();
                }
            }).start();
        }
        Thread.sleep(10 * 1000);
        System.out.println(count1);
        System.out.println(count2);
        System.out.println(count3);
        System.out.println(iMax * jMax);
    }

}
```

多线程并发环境下，以上的输出结果是什么？

>4967248（可能不是该数目，但是肯定不足10000000）
>4910317（可能不是该数目，但是肯定不足10000000）
>10000000
>10000000

综上，可知volatile不可以使非原子操作变为原子操作。AtomicInteger.incrementAndGet()可以！



# 2 AtomicInteger是怎么实现原子操作的

1.  volatile修饰的变量能够在线程间保持可见性 ，注意仅仅是保证了线程可见。拿到值后再做操作很有可能值就已经发生了变化，也就是读到的值很快就过期了。

2. CAS保证写正确。compareAndSet需要两个参数。expect和update，except表示当前值，如果当前值跟volatile读到的值不同，则表示读到了过期值则再读一次。update表示更新的目标值，对于incrementAndGet则update=expect+1。

3. unsafe内的主要操作就是while循环，直到操作成功！

   ```java
   /**
    * Atomically sets the value to the given updated value
    * if the current value {@code ==} the expected value.
    *
    * @param expect the expected value
    * @param update the new value
    * @return {@code true} if successful. False return indicates that
    * the actual value was not equal to the expected value.
    */
   public final boolean compareAndSet(int expect, int update) {
   	return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
   }
   ```

   

# 3 CAS 的缺点

CAS是一种常见的降低读写锁冲突，保证数据一致性的乐观锁机制。

CAS乐观锁机制可以提升吞吐量，并保证一致性，但极端情况下可能出现ABA问题。

1. ABA问题

   >原始值是A
   >
   >线程1和线程2都想从A修改为B，任意一个成功后后续线程3再将B修改为A，保持最终结果是
   >
   >假设线程成功顺序是1,3,2，那么对于线程2来说，它看到的A已经不是原始的A了。
   >
   >优化方向：CAS不能只对比值，还必须要确保是原来的数据
   >
   >常见实践：添加版本号字段，一个数据一个版本号，数据发生任何变化，版本号都变化。

2. 循环时间长，CPU开销大

   > CAS中使用的失败重试机制，会无限自旋直到成功，会过度消耗CPU



# 4 Synchronized是可重入锁吗？

 synchronized 是可重入锁！ 

可重入锁定义：线程请求进入一个由其他线程持有的对象锁时，该线程会阻塞。而当线程请求进入自己持有的对象锁时，请求会成功。



# 5 ReentrantLock可重入锁的原理

ReentrantLock是一种可重入的互斥锁，又称独占锁。

ReentrantLock的实现是一种自旋锁，通过循环调用CAS操作来实现加锁。

每一个锁关联一个 **线程持有者** 和 **计数器**。

当计数器为0表示没有被任何线程持有，大家都可以来抢占。

当某一个线程持有该锁后，计数器+1，此时其他线程想来持有该锁，看到计数器不等于0则进入等待。

持有该锁的线程再次请求这个锁时候，计数器继续+1，不必等待。

持有该锁的线程退出同步代码块的时候，计数器-1，如果计数器=0则释放该锁。



# 6 synchronized和ReentrantLock的区别

|          | synchronized                                                 | ReentrantLock                                             |
| -------- | ------------------------------------------------------------ | --------------------------------------------------------- |
| 可重入性 | 有                                                           | 有                                                        |
| 锁的实现 | JVM实现                                                      | JDK实现                                                   |
| 性能区别 | 优化前性能很差。<br />添加了偏向锁和自旋锁后，两者性能差异不明显。<br />官方推荐使用synchronized |                                                           |
| 功能区别 | 自动控制加锁和释放锁                                         | 手工控制加锁和释放锁<br />加锁 lock()<br />释放锁unlock() |

ReentrantLock独有的功能：

1. ReentrantLock可指定使用公平锁还是不公平锁。而synchronized只能是非公平锁
2. ReentrantLock提供了中断等待的机制 lockInterruptibly() 
3. ReentrantLock提供了Condition类用来分组唤醒线程，synchronized只能是随机唤醒



# 7 可重入锁的公平锁模式和非公平锁模式



```java
import org.junit.Test;
import java.util.concurrent.locks.ReentrantLock;

public class ReentrantLockTest {

    @Test
    public void testUnfairLock() throws InterruptedException {
        ReentrantLock unfairLock = new ReentrantLock(false);
        for (int i = 0; i < 100; i++) {
            final int fi = i;
            new Thread(() -> {
                // 该处的sleep用来保证按照时间顺去lock
                sleep(fi);
                // 加锁
                unfairLock.lock();
                System.out.println(fi + " 获取锁!");
                // do something
                sleep(100);
                // 释放锁
                unfairLock.unlock();
                System.out.println(fi + " 释放锁!");
            }).start();
        }
        Thread.sleep(1000 * 10);
        System.out.println("如上输出为unfair的输出结果，可以看到数字不连续，也就是发生了争抢！");
    }

    @Test
    public void testFairLock() throws InterruptedException {
        ReentrantLock unfairLock = new ReentrantLock(true);
        for (int i = 0; i < 100; i++) {
            final int fi = i;
            new Thread(() -> {
                // 该处的sleep用来保证按照时间顺去lock
                sleep(fi);
                // 加锁
                unfairLock.lock();
                System.out.println(fi + " 获取锁!");
                // do something
                sleep(100);
                // 释放锁
                unfairLock.unlock();
                System.out.println(fi + " 释放锁!");
            }).start();
        }
        Thread.sleep(1000 * 10);
        System.out.println("如上输出为fair的输出结果，可以看到数字连续，也就是无争抢！");
    }

    private void sleep(int milliseconds) {
        try {
            Thread.sleep(milliseconds);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

}
```

