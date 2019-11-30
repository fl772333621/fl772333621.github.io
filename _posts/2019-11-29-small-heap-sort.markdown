---
layout:     post
title:      "堆排序（大顶堆、小顶堆）解决TopN的问题"
subtitle:   "解决TopN的问题"
date:       2019-11-29 11:15:00
author:     "MengWei"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Java
    - 面试题
    - TopN
    - 堆排序
    - 大顶堆
    - 小顶堆
---

# 堆排序（大顶堆、小顶堆）解决TopN的问题

## 如何在10亿数中找出前1000大的数

[【漫画】如何在10亿数中找出前1000大的数](https://mp.weixin.qq.com/s?__biz=MzIwNTc4NTEwOQ==&mid=2247486030&idx=1&sn=a5b2d98f595ad049f0b55cdefb930365&chksm=972adb34a05d522270879c2bf7990a25b7433d738ce5f52f23a3e48b53f74c73be8075e7e148&scene=0&subscene=131&ascene=7&devicetype=android-26&version=26070334&nettype=WIFI&abtest_cookie=BAABAAoACwANABMABAAllx4AV5keAICZHgCJmR4AAAA%3D&lang=zh_CN&pass_ticket=Z8SghZSiaU7M1Wgtfag%2B5uGPoJmq92mdfwDxVHw%2BoJnZ2wPEZZkDHzsPJ4NbhA7H&wx_header=1)

如上文章讲解了TopN的一些常规及非常规解决方案，可以参考。

本次重点关注的是小顶堆解决方案。对如上文章的小顶堆实现添加详细注解。

**请注意**

小顶堆区分build和adjust

|          | build方法                                  | adjust方法                                                   |
| -------- | ------------------------------------------ | ------------------------------------------------------------ |
| 构建速度 | 慢<br />因为需要检测全部节点是否符合小顶堆 | 快<br />在已知heap符合小顶堆的前提下，<br />更改heap[0]，则仅仅需要调整heap[0]即可<br />（这样很大的可能性是局部调整） |
| 适用场景 | 重新构建小顶堆                             | 局部调整小顶堆                                               |



## 构建小顶堆准备工作

即将举例heap={5,4,3,2,1}进行构建小顶堆

前提，一定要将heap理解为一颗完全二叉树，如下图的结构，但实际上存储仍然是int数组，

元素位置交换也仅仅是int数组内index的变化，也就是位置的变化

![01](/img/in-post/2019-11-29-small-heap-sort/heap5-01.png)

## 构建小顶堆-build()方法描述

该种方法和所引用的文章内的build方法雷同（实则拷贝而来），之所以叫自上而下构建小顶堆，是因为是按照index顺序从左到右遍历heap数组进行小顶堆的维护



1. 自上而下，检测heap[index=0]=5的元素，无parent，跳过处理

2. 自上而下，检测heap[index=1]=4的元素，小于heap[parent=0]=5，则交换，交换后如下图，**同时请注意，交换后index=0和1都发生了变化，需要返工，再次从index=min(0,1)开始重复**

   ![01](/img/in-post/2019-11-29-small-heap-sort/heap5-02.png)

3. 自上而下，检测heap[index=0]=4的元素，无parent，跳过

4. 自上而下，检测heap[index=1]=5的元素，大于heap[parent=0]=4，跳过处理

5. 自上而下，检测heap[index=2]=3的元素，小于heap[parent=0]=4，则交换，交换后如下图，**同时请注意，交换后index=0和2都发生了变化，需要返工，再次从index=min(0,2)开始重复**

   ![01](/img/in-post/2019-11-29-small-heap-sort/heap5-03.png)

6. 自上而下，检测heap[index=0]=3的元素，无parent，跳过

7. 自上而下，检测heap[index=1]=5的元素，大于heap[parent=0]=3，跳过处理

8. 自上而下，检测heap[index=2]=4的元素，大于heap[parent=0]=3，跳过处理

9. 自上而下，检测heap[index=3]=2的元素，小于heap[parent=1]=5，则交换，交换后如下图，**同时请注意，交换后index=1和3都发生了变化，需要返工，再次从index=min(1,3)开始重复**

    ![01](/img/in-post/2019-11-29-small-heap-sort/heap5-04.png)

10. 自上而下，检测heap[index=1]=2的元素，小于heap[parent=0]=3，则交换，交换后如下图，**同时请注意，交换后index=0和1都发生了变化，需要返工，再次从index=min(0,1)开始重复**

    ![05](/img/in-post/2019-11-29-small-heap-sort/heap5-05.png)

11. 自上而下，检测heap[index=0]=2的元素，无parent，跳过处理

12. 自上而下，检测heap[index=1]=3的元素，大于heap[parent=0]=2，跳过处理

13. 自上而下，检测heap[index=2]=4的元素，大于heap[parent=0]=2，跳过处理

14. 自上而下，检测heap[index=3]=5的元素，大于heap[parent=1]=3，跳过处理

15. 自上而下，检测heap[index=4]=1的元素，小于heap[parent=1]=3，则交换，交换后如下图，**同时请注意，交换后index=1和4都发生了变化，需要返工，再次从index=min(1,4)开始重复**

    ![06](/img/in-post/2019-11-29-small-heap-sort/heap5-06.png)

16. 自上而下，检测heap[index=1]=1的元素，小于heap[parent=0]=2，则交换，交换后如下图，**同时请注意，交换后index=0和1都发生了变化，需要返工，再次从index=min(0,1)开始重复**

    ![07](/img/in-post/2019-11-29-small-heap-sort/heap5-07.png)

17. 自上而下，检测heap[index=0]=1的元素，无parent，跳过处理

18. 自上而下，检测heap[index=1]=2的元素，大于heap[parent=0]=1，跳过处理

19. 自上而下，检测heap[index=2]=4的元素，大于heap[parent=0]=1，跳过处理

20. 自上而下，检测heap[index=3]=5的元素，大于heap[parent=1]=2，跳过处理

21. 自上而下，检测heap[index=4]=3的元素，大于heap[parent=1]=2，跳过处理，

 **经历了21个步骤完成自上而下构建小顶堆，可以看到元素自上而下的升序排列**(不保证自左到右或自右到左的升序排列)



## 构建小顶堆build()方法Java实现

核心方法如下

```java
import org.junit.Test;
import java.util.Arrays;

public class SmallHeap {

    @Test
    public void testBuildSmallHeap() {
        buildSmallHeap(new int[]{5, 4, 3, 2, 1});
    }

    /**
     * 自上而下的构建小顶堆<br />
     * 核心是做比较，parent<=我则不操作<br />
     * parent>我则交换我和parent<br />
     * 缺点：发生swap后需要返工，需要到swap的两个index中较小的位置处重新开始处理（这样增加了处理次数，需要while循环）<br />
     *
     * @param heap 待构建为小顶堆的无序数组
     */
    private void buildSmallHeap(int[] heap) {
        for (int i = 1; i < heap.length; i++) {
            System.out.print("准备处理,位置为" + i);
            System.out.println(Arrays.toString(heap));
            // 因为swap后需要返工，所以定义一个ti保存返工后的最新位置
            int ti = i;
            // 是否我的parent大于我，如果大则交换
            while (heap[parent(ti)] > heap[ti]) {
                int tti = parent(ti);
                swap(heap, ti, tti);
                System.out.print("处理位置为" + i + ", 交换 " + ti + "和" + tti + ", 最新返工位置为" + tti);
                System.out.println(Arrays.toString(heap));
                // 修正返工位置
                ti = tti;
            }
        }
    }

    private int parent(int i) {
        return (i - 1) / 2;
    }

    private void swap(int[] heap, int i, int j) {
        int tmp = heap[i];
        heap[i] = heap[j];
        heap[j] = tmp;
    }
}
```

输出结果如下

```
准备处理,位置为1[5, 4, 3, 2, 1]
处理位置为1, 交换 1和0, 最新返工位置为0[4, 5, 3, 2, 1]
准备处理,位置为2[4, 5, 3, 2, 1]
处理位置为2, 交换 2和0, 最新返工位置为0[3, 5, 4, 2, 1]
准备处理,位置为3[3, 5, 4, 2, 1]
处理位置为3, 交换 3和1, 最新返工位置为1[3, 2, 4, 5, 1]
处理位置为3, 交换 1和0, 最新返工位置为0[2, 3, 4, 5, 1]
准备处理,位置为4[2, 3, 4, 5, 1]
处理位置为4, 交换 4和1, 最新返工位置为1[2, 1, 4, 5, 3]
处理位置为4, 交换 1和0, 最新返工位置为0[1, 2, 4, 5, 3]
```

## 调整小顶堆准备工作

调整小顶堆的一个前提就是，heap已经是小顶堆了，仅仅是heap[0]做了替换才需要调整。

而且该替换是把heap[0]替换为更大的值了。

**举例：**

已知一个小顶堆heap={18, 29, 71, 56, 30}，其小顶堆样式如下图

![11](/img/in-post/2019-11-29-small-heap-sort/heap5-11.png)

依次添加93, 44, 75, 20, 65, 68, 34到小顶堆

## 调整小顶堆-adjust()方法描述

1. 检测新元素93大于heap[0]=18，替换heap[0]=93

   ![12](/img/in-post/2019-11-29-small-heap-sort/heap5-12.png)

2. 检测是否需要调整，检测heap[0]大于heap[left(0)]或heap[right(0)]，需要调整

3. 下沉调整，当前index=0，min(heap[left(0)], heap[right(0)])=heap[left(0)]，故交换0和left(0)，index=left(0)

   ![13](/img/in-post/2019-11-29-small-heap-sort/heap5-13.png)

4. 下沉调整，当前index=1，min(heap[left(1)], heap[right(1)])=heap[right(1)]，故交换1和right(1)，index=right(1)

   ![14](/img/in-post/2019-11-29-small-heap-sort/heap5-14.png)

5. 下沉调整，当前index=4，无left和right，结束本次调整

6. 检测新元素44大于heap[0]=29，替换heap[0]=44

   ![15](/img/in-post/2019-11-29-small-heap-sort/heap5-15.png)

7. 检测是否需要调整，检测heap[0]大于heap[left(0)]或heap[right(0)]，需要调整

8. 下沉调整，当前index=0，min(heap[left(0)], heap[right(0)])=heap[left(0)]，故交换0和left(0)，index=left(0)

   ![16](/img/in-post/2019-11-29-small-heap-sort/heap5-16.png)

9. 下沉调整，当前index=1，left和right符合，结束本次调整

10. 检测新元素75大于heap[0]=30，替换heap[0]=75

    ![17](/img/in-post/2019-11-29-small-heap-sort/heap5-17.png)

11. 检测是否需要调整，检测heap[0]大于heap[left(0)]或heap[right(0)]，需要调整

12. 下沉调整，当前index=0，min(heap[left(0)], heap[right(0)])=heap[left(0)]，故交换0和left(0)，index=left(0)

    ![18](/img/in-post/2019-11-29-small-heap-sort/heap5-18.png)

13. 下沉调整，当前index=1，min(heap[left(1)], heap[right(1)])=heap[right(1)]，故交换1和left(1)，index=left(1)

    ![19](/img/in-post/2019-11-29-small-heap-sort/heap5-19.png)

14. 下沉调整，当前index=1，left和right符合，结束本次调整

15. 检测新元素20小于heap[0]=44，跳过调整

16. 检测新元素65大于heap[0]=44，替换heap[0]=65

    ![20](/img/in-post/2019-11-29-small-heap-sort/heap5-20.png)

17. 检测是否需要调整，检测heap[0]大于heap[left(0)]或heap[right(0)]，需要调整

18. 下沉调整，当前index=0，min(heap[left(0)], heap[right(0)])=heap[left(0)]，故交换0和left(0)，index=left(0)

    ![21](/img/in-post/2019-11-29-small-heap-sort/heap5-21.png)

19. 下沉调整，当前index=1，left和right符合，结束本次调整

20. 检测新元素68大于heap[0]=56，替换heap[0]=68

    ![22](/img/in-post/2019-11-29-small-heap-sort/heap5-22.png)

21. 检测是否需要调整，检测heap[0]大于heap[left(0)]或heap[right(0)]，需要调整

22. 下沉调整，当前index=0，min(heap[left(0)], heap[right(0)])=heap[left(0)]，故交换0和left(0)，index=left(0)

    ![23](/img/in-post/2019-11-29-small-heap-sort/heap5-23.png)

23. 下沉调整，当前index=1，left和right符合，结束本次调整

24. 检测新元素34小于heap[0]=65，跳过调整

25. 完成全部调整



## 调整小顶堆-adjust()方法Java实现

核心方法如下

```java
import org.junit.Test;
import java.util.Arrays;

public class SmallHeap {

    @Test
    public void testAdjustSmallHeap() {
        // 创建一个合规的heap
        int[] heap = {18, 29, 71, 56, 30};
        // 待新增的元素列表
        int[] unsortedArrays = new int[]{93, 44, 75, 20, 65, 68, 34};
        // 将待新增的元素逐个加入到heap中，然后调整heap
        for (int unsortedArray : unsortedArrays) {
            if (unsortedArray > heap[0]) {
                heap[0] = unsortedArray;
                adjustSmallHeap(heap);
            }
        }
    }

    /**
     * 调整小顶堆<br />
     * 直接用构建小顶堆不可以吗？为什么还需要调整小顶堆？<br />
     * 答：构建小顶堆需要检测全部节点<br />
     * 而调整小顶堆在已知仅仅堆顶发生了变化的前提下，有可能调整很小<br />
     * 调整方案：总体而言是大数下沉
     *
     * @param heap 待构建为小顶堆的无序数组
     */
    private void adjustSmallHeap(int[] heap) {
        int i = 0;
        System.out.println("新增元素heap[0]=" + heap[0]);
        while (i < heap.length / 2 && (heap[i] > heap[left(i)] || heap[i] > heap[right(i)])) {
            System.out.print("调整位置为" + i);
            System.out.print(Arrays.toString(heap));
            int ti = heap[left(i)] < heap[right(i)] ? left(i) : right(i);
            swap(heap, i, ti);
            System.out.print(" 交换" + i + "和" + ti + " 调整后 ");
            System.out.println(Arrays.toString(heap));
            i = ti;
        }
        System.out.print("完成全部调整");
        System.out.println(Arrays.toString(heap));
    }

    private int left(int i) {
        return i * 2 + 1;
    }

    private int right(int i) {
        return i * 2 + 2;
    }

    private void swap(int[] heap, int i, int j) {
        int tmp = heap[i];
        heap[i] = heap[j];
        heap[j] = tmp;
    }
}
```

输出结果如下：

```
新增元素heap[0]=93
调整位置为0[93, 29, 71, 56, 30] 交换0和1 调整后 [29, 93, 71, 56, 30]
调整位置为1[29, 93, 71, 56, 30] 交换1和4 调整后 [29, 30, 71, 56, 93]
完成全部调整[29, 30, 71, 56, 93]
新增元素heap[0]=44
调整位置为0[44, 30, 71, 56, 93] 交换0和1 调整后 [30, 44, 71, 56, 93]
完成全部调整[30, 44, 71, 56, 93]
新增元素heap[0]=75
调整位置为0[75, 44, 71, 56, 93] 交换0和1 调整后 [44, 75, 71, 56, 93]
调整位置为1[44, 75, 71, 56, 93] 交换1和3 调整后 [44, 56, 71, 75, 93]
完成全部调整[44, 56, 71, 75, 93]
新增元素heap[0]=65
调整位置为0[65, 56, 71, 75, 93] 交换0和1 调整后 [56, 65, 71, 75, 93]
完成全部调整[56, 65, 71, 75, 93]
新增元素heap[0]=68
调整位置为0[68, 65, 71, 75, 93] 交换0和1 调整后 [65, 68, 71, 75, 93]
完成全部调整[65, 68, 71, 75, 93]
```

## 合并build方法和adjust方法到同一个类

```java
import org.junit.Test;
import java.util.Arrays;

public class SmallHeap {

    @Test
    public void testBuildSmallHeap() {
        buildSmallHeap(new int[]{5, 4, 3, 2, 1});
    }

    @Test
    public void testAdjustSmallHeap() {
        // 创建一个合规的heap
        int[] heap = {18, 29, 71, 56, 30};
        // 待新增的元素列表
        int[] unsortedArrays = new int[]{93, 44, 75, 20, 65, 68, 34};
        // 将待新增的元素逐个加入到heap中，然后调整heap
        for (int unsortedArray : unsortedArrays) {
            if (unsortedArray > heap[0]) {
                heap[0] = unsortedArray;
                adjustSmallHeap(heap);
            }
        }
    }

    /**
     * 自上而下的构建小顶堆<br />
     * 核心是做比较，parent<=我则不操作<br />
     * parent>我则交换我和parent<br />
     * 缺点：发生swap后需要返工，需要到swap的两个index中较小的位置处重新开始处理（这样增加了处理次数，需要while循环）<br />
     *
     * @param heap 待构建为小顶堆的无序数组
     */
    private void buildSmallHeap(int[] heap) {
        for (int i = 1; i < heap.length; i++) {
            System.out.print("准备处理,位置为" + i);
            System.out.println(Arrays.toString(heap));
            // 因为swap后需要返工，所以定义一个ti保存返工后的最新位置
            int ti = i;
            // 是否我的parent大于我，如果大则交换
            while (heap[parent(ti)] > heap[ti]) {
                int tti = parent(ti);
                swap(heap, ti, tti);
                System.out.print("处理位置为" + i + ", 交换 " + ti + "和" + tti + ", 最新返工位置为" + tti);
                System.out.println(Arrays.toString(heap));
                // 修正返工位置
                ti = tti;
            }
        }
    }

    private int parent(int i) {
        return (i - 1) / 2;
    }

    /**
     * 调整小顶堆<br />
     * 直接用构建小顶堆不可以吗？为什么还需要调整小顶堆？<br />
     * 答：构建小顶堆需要检测全部节点<br />
     * 而调整小顶堆在已知仅仅堆顶发生了变化的前提下，有可能调整很小<br />
     * 调整方案：总体而言是大数下沉
     *
     * @param heap 待构建为小顶堆的无序数组
     */
    private void adjustSmallHeap(int[] heap) {
        int i = 0;
        System.out.println("新增元素heap[0]=" + heap[0]);
        while ((left(i) < heap.length && heap[i] > heap[left(i)]) || (right(i) < heap.length && heap[i] > heap[right(i)])) {
            System.out.print("调整位置为" + i);
            System.out.print(Arrays.toString(heap));
            int ti = heap[left(i)] < heap[right(i)] ? left(i) : right(i);
            swap(heap, i, ti);
            System.out.print(" 交换" + i + "和" + ti + " 调整后 ");
            System.out.println(Arrays.toString(heap));
            i = ti;
        }
        System.out.print("完成全部调整");
        System.out.println(Arrays.toString(heap));
    }

    private int left(int i) {
        return i * 2 + 1;
    }

    private int right(int i) {
        return i * 2 + 2;
    }

    private void swap(int[] heap, int i, int j) {
        int tmp = heap[i];
        heap[i] = heap[j];
        heap[j] = tmp;
    }
}
```





## 解决TopN问题的准备

小顶堆已经知道如何构建了，而且构建出来的小顶堆具有特点，自上而下数字是升序排列的。

利用这个特性来解决TopN的问题。

从10亿数中找出前1000大的数，那么解决方案就是

1. 从10亿数中取出前1000个数，构建一个长度为1000的小顶堆heap
2. 从第1001个数开始，逐个比较是否比heap[0]大，如果大，那么heap[0]=该数则调整小顶堆

通过如上的两个步骤，即可通过一次遍历就找出Top1000的元素



## 解决TopN的问题-Java实现

先来计算一下10亿个int占了多少空间（我们假设1K=1000,）。

一个int占用4个字节(32b)=4B

4B * 10_0000_0000 ≈ 4B * K * K * K = 4GB

对于个人PC来讲已经超过了最大内存，所以 int[] array = new int[10_0000_0000]; 是会`java.lang.OutOfMemoryError: Java heap space`

所以我们简化一下数目。求1万个数字中的Top100，代码如下

```java
@Test
public void findTop100In10000() {
	int[] unsortedArrays = new int[1_0000];
	Random random = new Random();
	for (int i = 0; i < unsortedArrays.length; i++) unsortedArrays[i] = random.nextInt();
	// 创建一个长度为100的heap
	int[] heap = Arrays.copyOfRange(unsortedArrays, 0, 100);
	// 将heap构建为小顶堆
	buildSmallHeap(heap);
    // 剩余元素逐个添加入heap并调整heap
	for (int i = heap.length; i < unsortedArrays.length; i++) {
		if (unsortedArrays[i] > heap[0]) {
			heap[0] = unsortedArrays[i];
			adjustSmallHeap(heap);
		}
	}
}
```

