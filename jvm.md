---
typora-root-url: img
---

  JVM (Java Virtual Machine)

# 一、引言

## 1. 什么是JVM?

### 定义：

Java Virtual Machine -java 程序的运行环境（java二进制字节码的运行环境）

好处：

- 一次编程，到处运行
- 自动内存管理，垃圾回收功能
- 数组下标越界越查检查
- 多态

比较：

jvm jre jdk

## 2. 学习JVM有什么用？

- 面试

- 理解底层的实现原理

- 中高级程序员的必备技能

## 3. 常见的JVM

![image-20220222233637760](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220222233637760.png)

## 4. 学习路线

![image-20220222233650314](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220222233650314.png)

# 二、内存结构

## 1. 程序计数器

![image-20220223210510675](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220223210510675.png)

### 定义：

Program Counter Register 程序计数器（寄存器）

- 作用，是记住下一条jvm指令的执行地址
- 特点
  - 是线程私有的
  - 不会存在内存溢出  

### 作用:

```java
0: getstatic #20 // PrintStream out = System.out;
3: astore_1 // --
4: aload_1 // out.println(1);
5: iconst_1 // --
6: invokevirtual #26 // --
9: aload_1 // out.println(2);
10: iconst_2 // --
11: invokevirtual #26 // --
14: aload_1 // out.println(3);
15: iconst_3 // --
16: invokevirtual #26 // --
19: aload_1 // out.println(4);
20: iconst_4 // --
21: invokevirtual #26 // --
24: aload_1 // out.println(5);
25: iconst_5 // --
26: invokevirtual #26 // --
29: return
```

## 2. 虚拟机栈

![](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220223210441388.png)

### 定义：

java Virtual Machine Stacks （Java 虚拟机栈）

- 每个线程运行时所需要的内存，称为虚拟机栈
- 每个栈由多个栈帧（Frame）组成，对应着每次方法调用时所占用的内存

- 每个线程只能有一个活动栈帧，对应着当前正在执行的那个方法

问题辨析

1. 垃圾回收是否涉及栈内存？
2. 栈内存分配越大越好吗？【栈内存分配越大，线程数分配就越少】
3. 方法内的局部量是否线程安全？
   - 如果方法内局部变量没有逃离方法的作用访问，它是线程安全的
   - 如果是局部变量引用了对象，并逃离方法的作用范围，需要考虑线程安全

### 栈内存溢出（StackOverFlow）:

- 栈帧过多导致栈内存溢出	
- 栈帧过大导致栈内存溢出  

### 线程运行诊断：

案例1： cpu 占用过多
定位

- 用top定位哪个进程对cpu的占用过高
- ps H -eo pid,tid,%cpu | grep 进程id （用ps命令进一步定位是哪个线程引起的cpu占用过高）
- jstack 进程id
  - 可以根据线程id 找到有问题的线程，进一步定位到问题代码的源码行号

案例2：程序运行很长时间没有结果  

## 3. 本地方法栈

![image-20220223205900251](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220223205900251.png)

## 4. 堆

![image-20220223205912020](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220223205912020.png)

## 定义：	

Heap 堆

- 通过 new 关键字，创建对象都会使用堆内存

特点

- 它是线程共享的，堆中对象都需要考虑线程安全的问题
- 有垃圾回收机制  

## 堆内存溢出：

## 堆内存诊断：

1. jps 工具
   - 查看当前系统中有哪些 java 进程

1. jmap 工具
   - 查看堆内存占用情况 jmap - heap 进程id
2. jconsole 工具
   - 图形界面的，多功能的监测工具，可以连续监测

案例

- 垃圾回收后，内存占用仍然很高  

## 5. 方法区

![image-20220223210441388](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220223210441388.png)

## 定义：

[JVM规范-方法区定义  ](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html)

## 组成：

![image-20220301205926214](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220301205926214.png)

![image-20220301205936964](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220301205936964.png)

## 方法区内存溢出

1.8 以前会导致永久代内存溢出  

```shell
* 演示永久代内存溢出 java.lang.OutOfMemoryError: PermGen space
* -XX:MaxPermSize=8m 
```

1.8 之后会导致元空间内存溢出  

```shell
* 演示元空间内存溢出 java.lang.OutOfMemoryError: Metaspace
* -XX:MaxMetaspaceSize=8m
```

场景

- spring
- mybatis

## 运行时常量池

- 常量池，就是一张表，虚拟机指令根据这张常量表找到要执行的类名、方法名、参数类型、字面量
  等信息
- 运行时常量池，常量池是 *.class 文件中的，当该类被加载，它的常量池信息就会放入运行时常量
  池，并把里面的符号地址变为真实地址  

## String Table

先看几道面试题：  

```java
String s1 = "a";
String s2 = "b";
String s3 = "a" + "b";
String s4 = s1 + s2;
String s5 = "ab";
String s6 = s4.intern();
// 问
System.out.println(s3 == s4);
System.out.println(s3 == s5);
System.out.println(s3 == s6);
String x2 = new String("c") + new String("d");
String x1 = "cd";
x2.intern();
// 问，如果调换了【最后两行代码】的位置呢，如果是jdk1.6呢
System.out.println(x1 == x2);
```

### String Table 特性

- 常量池中的字符串仅是符号，第一次用到时才变为对象
- 利用串池的机制，来避免重复创建字符串对象
- 字符串变量拼接的原理是 StringBuilder （1.8）
- 字符串常量拼接的原理是编译期优化
- 可以使用 intern 方法，主动将串池中还没有的字符串对象放入串池
  - 1.8 将这个字符串对象尝试放入串池，如果有则并不会放入，如果没有则放入串池， 会把串
  - 池中的对象返回
  - 1.6 将这个字符串对象尝试放入串池，如果有则并不会放入，如果没有会把此对象复制一份，
    放入串池， 会把串池中的对象返回  

### String Table 位置

```java
package cn.itcast.jvm.t1.stringtable;

import java.util.ArrayList;
import java.util.List;

/**
 * 演示 StringTable 位置
 * 在jdk8下设置 -Xmx10m(对堆的调优参数) -XX:-UseGCOverheadLimit(使用GC垃圾回收上限，以避免过多花费说收集少量的堆)
 * 在jdk6下设置 -XX:MaxPermSize=10m(非堆区分配的内存的最大上限)
 */
public class Demo1_6 {

    public static void main(String[] args) throws InterruptedException {
        List<String> list = new ArrayList<String>();
        int i = 0;
        try {
            for (int j = 0; j < 260000; j++) {
                list.add(String.valueOf(j).intern());
                i++;
            }
        } catch (Throwable e) {
            e.printStackTrace();
        } finally {
            System.out.println(i);
        }
    }
}
```

### String Table 垃圾回收

```java
package cn.itcast.jvm.t1.stringtable;

import java.util.ArrayList;
import java.util.List;

/**
 * 演示 StringTable 垃圾回收
 * -Xmx10m -XX:+PrintStringTableStatistics -XX:+PrintGCDetails -verbose:gc
 */
public class Demo1_7 {
    public static void main(String[] args) throws InterruptedException {
        int i = 0;
        try {
            for (int j = 0; j < 100000; j++) { // j=100, j=10000
                String.valueOf(j).intern();
                i++;
            }
        } catch (Throwable e) {
            e.printStackTrace();
        } finally {
            System.out.println(i);
        }

    }
}
```

### String Table 性能调优

演示串池大小对性能的影响

```java
package cn.itcast.jvm.t1.stringtable;

import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStreamReader;

/**
 * 演示串池大小对性能的影响
 * -Xms500m -Xmx500m -XX:+PrintStringTableStatistics -XX:StringTableSize=1009
 */
public class Demo1_24 {

    public static void main(String[] args) throws IOException {
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(new FileInputStream("linux.words"), "utf-8"))) {
            String line = null;
            long start = System.nanoTime();
            while (true) {
                line = reader.readLine();
                if (line == null) {
                    break;
                }
                line.intern();
            }
            System.out.println("cost:" + (System.nanoTime() - start) / 1000000);
        }


    }
}
```

演示 intern 减少内存占用

```java
package cn.itcast.jvm.t1.stringtable;

import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;

/**
 * 演示 intern 减少内存占用
 * -XX:StringTableSize=200000 -XX:+PrintStringTableStatistics
 * -Xsx500m -Xmx500m -XX:+PrintStringTableStatistics -XX:StringTableSize=200000
 */
public class Demo1_25 {

    public static void main(String[] args) throws IOException {

        List<String> address = new ArrayList<>();
        System.in.read();
        for (int i = 0; i < 10; i++) {
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(new FileInputStream("linux.words"), "utf-8"))) {
                String line = null;
                long start = System.nanoTime();
                while (true) {
                    line = reader.readLine();
                    if(line == null) {
                        break;
                    }
                    address.add(line.intern());
                }
                System.out.println("cost:" +(System.nanoTime()-start)/1000000);
            }
        }
        System.in.read();


    }
}
```

- 调整 -XX:StringTableSize=桶个数
- 字符串重复可使用intern()方法入串池

## 6. 直接内存

### 定义：

Direct Memory

- 常见于 NIO 操作时，用于数据缓冲区
- 分配回收成本较高，但读写性能高
- 不受 JVM 内存回收管理  

演示 ByteBuffer 作用

```java
package cn.itcast.jvm.t1.direct;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

/**
 * 演示 ByteBuffer 作用
 */
public class Demo1_9 {
    static final String FROM = "E:\\编程资料\\第三方教学视频\\youtube\\Getting Started with Spring Boot-sbPSjI4tt10.mp4";
    static final String TO = "E:\\a.mp4";
    static final int _1Mb = 1024 * 1024;

    public static void main(String[] args) {
        io(); // io 用时：1535.586957 1766.963399 1359.240226
        directBuffer(); // directBuffer 用时：479.295165 702.291454 562.56592
    }

    private static void directBuffer() {
        long start = System.nanoTime();
        try (FileChannel from = new FileInputStream(FROM).getChannel();
             FileChannel to = new FileOutputStream(TO).getChannel();
        ) {
            ByteBuffer bb = ByteBuffer.allocateDirect(_1Mb);
            while (true) {
                int len = from.read(bb);
                if (len == -1) {
                    break;
                }
                bb.flip();
                to.write(bb);
                bb.clear();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        long end = System.nanoTime();
        System.out.println("directBuffer 用时：" + (end - start) / 1000_000.0);
    }

    private static void io() {
        long start = System.nanoTime();
        try (FileInputStream from = new FileInputStream(FROM);
             FileOutputStream to = new FileOutputStream(TO);
        ) {
            byte[] buf = new byte[_1Mb];
            while (true) {
                int len = from.read(buf);
                if (len == -1) {
                    break;
                }
                to.write(buf, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        long end = System.nanoTime();
        System.out.println("io 用时：" + (end - start) / 1000_000.0);
    }
}
```

演示直接内存溢出

```java
package cn.itcast.jvm.t1.direct;

import java.nio.ByteBuffer;
import java.util.ArrayList;
import java.util.List;


/**
 * 演示直接内存溢出
 */
public class Demo1_10 {
    static int _100Mb = 1024 * 1024 * 100;

    public static void main(String[] args) {
        List<ByteBuffer> list = new ArrayList<>();
        int i = 0;
        try {
            while (true) {
                ByteBuffer byteBuffer = ByteBuffer.allocateDirect(_100Mb);
                list.add(byteBuffer);
                i++;
            }
        } finally {
            System.out.println(i);
        }
        // 方法区是jvm规范， jdk6 中对方法区的实现称为永久代
        //                  jdk8 对方法区的实现称为元空间
    }
}
```

禁用显式回收对直接内存的影响

```java
package cn.itcast.jvm.t1.direct;

import java.io.IOException;
import java.nio.ByteBuffer;

/**
 * 禁用显式回收对直接内存的影响
 */
public class Demo1_26 {
    static int _1Gb = 1024 * 1024 * 1024;

    /*
     * -XX:+DisableExplicitGC 显式的
     */
    public static void main(String[] args) throws IOException {
        ByteBuffer byteBuffer = ByteBuffer.allocateDirect(_1Gb);
        System.out.println("分配完毕...");
        System.in.read();
        System.out.println("开始释放...");
        byteBuffer = null;
        System.gc(); // 显式的垃圾回收，Full GC
        System.in.read();
    }
}
```

直接内存分配的底层原理：Unsafe

```java
 package cn.itcast.jvm.t1.direct;

import sun.misc.Unsafe;

import java.io.IOException;
import java.lang.reflect.Field;

/**
 * 直接内存分配的底层原理：Unsafe
 */
public class Demo1_27 {
    static int _1Gb = 1024 * 1024 * 1024;

    public static void main(String[] args) throws IOException {
        Unsafe unsafe = getUnsafe();
        // 分配内存
        long base = unsafe.allocateMemory(_1Gb);
        unsafe.setMemory(base, _1Gb, (byte) 0);
        System.in.read();

        // 释放内存
        unsafe.freeMemory(base);
        System.in.read();
    }

    public static Unsafe getUnsafe() {
        try {
            Field f = Unsafe.class.getDeclaredField("theUnsafe");
            f.setAccessible(true);
            Unsafe unsafe = (Unsafe) f.get(null);
            return unsafe;
        } catch (NoSuchFieldException | IllegalAccessException e) {
            throw new RuntimeException(e);
        }
    }
}
```

### 分配和回收原理

- 使用了 Unsafe 对象完成直接内存的分配回收，并且回收需要主动调用 freeMemory 方法
- ByteBuffer 的实现类内部，使用了 Cleaner （虚引用）来监测 ByteBuffer 对象，一旦ByteBuffer 对象被垃圾回收，那么就会由 ReferenceHandler 线程通过 Cleaner 的 clean 方法调用 freeMemory 来释放直接内存  

# 三、垃圾回收
## 1. 如何判断对象可以回收

### 引用计数法

![image-20220225223919438](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220225223919438.png)

### 可达性分析算法

- Java 虚拟机中的垃圾回收器采用可达性分析来探索所有存活的对象
- 扫描堆中的对象，看是否能够沿着 GC Root对象 为起点的引用链找到该对象，找不到，表示可以回收
- 哪些对象可以作为 GC Root ?  

### 四种引用

1. 强引用
   - 只有所有 GC Roots 对象都不通过【强引用】引用该对象，该对象才能被垃圾回收
   
2. 软引用（SoftReference）
   - 仅有软引用引用该对象时，在垃圾回收后，内存仍不足时会再次出发垃圾回收，回收软引用对象
   - 可以配合引用队列来释放软引用自身
   
   ```java
   package cn.itcast.jvm.t2;
   
   import java.io.IOException;
   import java.lang.ref.SoftReference;
   import java.util.ArrayList;
   import java.util.List;
   
   /**
    * 演示软引用
    * -Xmx20m -XX:+PrintGCDetails -verbose:gc
    */
   public class Demo2_3 {
   
       private static final int _4MB = 4 * 1024 * 1024;
   
   
   
       public static void main(String[] args) throws IOException {
           /*List<byte[]> list = new ArrayList<>();
           for (int i = 0; i < 5; i++) {
               list.add(new byte[_4MB]);
           }
   
           System.in.read();*/
           soft();
   
   
       }
   
       public static void soft() {
           // list --> SoftReference --> byte[]
   
           List<SoftReference<byte[]>> list = new ArrayList<>();
           for (int i = 0; i < 5; i++) {
               SoftReference<byte[]> ref = new SoftReference<>(new byte[_4MB]);
               System.out.println(ref.get());
               list.add(ref);
               System.out.println(list.size());
   
           }
           System.out.println("循环结束：" + list.size());
           for (SoftReference<byte[]> ref : list) {
               System.out.println(ref.get());
           }
       }
   }
   ```
   
   ```java
   package cn.itcast.jvm.t2;
   
   import java.lang.ref.Reference;
   import java.lang.ref.ReferenceQueue;
   import java.lang.ref.SoftReference;
   import java.util.ArrayList;
   import java.util.List;
   
   /**
    * 演示软引用, 配合引用队列
    */
   public class Demo2_4 {
       private static final int _4MB = 4 * 1024 * 1024;
   
       public static void main(String[] args) {
           List<SoftReference<byte[]>> list = new ArrayList<>();
   
           // 引用队列
           ReferenceQueue<byte[]> queue = new ReferenceQueue<>();
   
           for (int i = 0; i < 5; i++) {
               // 关联了引用队列， 当软引用所关联的 byte[]被回收时，软引用自己会加入到 queue 中去
               SoftReference<byte[]> ref = new SoftReference<>(new byte[_4MB], queue);
               System.out.println(ref.get());
               list.add(ref);
               System.out.println(list.size());
           }
   
           // 从队列中获取无用的 软引用对象，并移除
           Reference<? extends byte[]> poll = queue.poll();
           while( poll != null) {
               list.remove(poll);
               poll = queue.poll();
           }
   
           System.out.println("===========================");
           for (SoftReference<byte[]> reference : list) {
               System.out.println(reference.get());
           }
   
       }
   }
   ```
   
3. 弱引用（WeakReference）
   
   - 仅有弱引用引用该对象时，在垃圾回收时，无论内存是否充足，都会回收弱引用对象
   - 可以配合引用队列来释放弱引用自身
   
   ```java
   package cn.itcast.jvm.t2;
   
   import java.lang.ref.Reference;
   import java.lang.ref.ReferenceQueue;
   import java.lang.ref.SoftReference;
   import java.lang.ref.WeakReference;
   import java.util.ArrayList;
   import java.util.List;
   
   /**
    * 演示弱引用
    * -Xmx20m -XX:+PrintGCDetails -verbose:gc
    */
   public class Demo2_5 {
       private static final int _4MB = 4 * 1024 * 1024;
   
       public static void main(String[] args) {
           //  list --> WeakReference --> byte[]
           List<WeakReference<byte[]>> list = new ArrayList<>();
           for (int i = 0; i < 10; i++) {
               WeakReference<byte[]> ref = new WeakReference<>(new byte[_4MB]);
               list.add(ref);
               for (WeakReference<byte[]> w : list) {
                   System.out.print(w.get()+" ");
               }
               System.out.println();
   
           }
           System.out.println("循环结束：" + list.size());
       }
   }
   ```
   
4. 虚引用（PhantomReference）
   - 必须配合引用队列使用，主要配合 ByteBuffer 使用，被引用对象回收时，会将虚引用入队，由 Reference Handler 线程调用虚引用相关方法释放直接内存
   
5. 终结器引用（FinalReference）
   - 无需手动编码，但其内部配合引用队列使用，在垃圾回收时，终结器引用入队（被引用对象暂时没有被回收），再由 Finalizer 线程通过终结器引用找到被引用对象并调用它的 finalize
   - 方法，第二次 GC 时才能回收被引用对象  

## 2. 垃圾回收算法

### 标记清除

定义： Mark Sweep

- 速度较快  
- 有内存碎片

![image-20220226113649586](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220226113649586.png)

### 标记整理

定义：Mark Compact

- 速度慢 
- 无内存碎片

![image-20220226113718459](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220226113718459.png)

### 复制

定义：Copy

- 不会有内存碎片
- 需要占用双倍内存空间  

![image-20220226113804962](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220226113804962.png)

## 3. 分代垃圾回收

![image-20220226114029608](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220226114029608.png)

- 对象首先分`配在伊甸园区域
- 新生代空间不足时，触发 minor gc，伊甸园和 from 存活的对象使用 copy 复制到 to 中，存活的对象年龄加 1并且交换 from to
- minor gc 会引发 stop the world，暂停其它用户的线程，等垃圾回收结束，用户线程才恢复运行
- 当对象寿命超过阈值时，会晋升至老年代，最大寿命是15（4bit）
- 当老年代空间不足，会先尝试触发 minor gc，如果之后空间仍不足，那么触发 full gc，STW的时间更长  

相关VM参数

| 堆初始大小         | -Xms                                                         |
| ------------------ | ------------------------------------------------------------ |
| 堆最大大小         | -Xmx 或 -XX:MaxHeapSize=size                                 |
| 新生代大小         | -Xmn 或 (-XX:NewSize=size + -XX:MaxNewSize=size )            |
| 幸存区比例（动态） | -XX:InitialSurvivorRatio=ratio 和 -XX:+UseAdaptiveSizePolicy |
| 幸存区比例         | -XX:SurvivorRatio=ratio                                      |
| 晋升阈值           | -XX:MaxTenuringThreshold=threshold                           |
| 晋升详情           | -XX:+PrintTenuringDistribution                               |
| GC详情             | -XX:+PrintGCDetails -verbose:gc                              |
| FullGC 前 MinorGC  | -XX:+ScavengeBeforeFullGC                                    |

## 4. 垃圾回收器

1. 串行

   - 单线程

   - 堆内存较小，适合个人电脑

2. 吞吐量优先  

   - 让单位时间内，STW 的时间最短 0.2 0.2 = 0.4，垃圾回收时间占比最低，这样就称吞吐量高

3. 响应时间优先

   - 多线程

   - 堆内存较大，多核 cpu

   - 尽可能让单次 STW 的时间最短 0.1 0.1 0.1 0.1 0.1 = 0.5


### 串行

-XX:+UseSerialGC = Serial + SerialOld  

![image-20220226142931956](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220226142931956.png)

### 吞吐量优先

-XX:+UseParallelGC ~ -XX:+UseParallelOldGC  (开启一个，另一个也会开启)

-XX:GCTimeRatio=ratio

-XX:MaxGCPauseMillis=ms

-XX:ParallelGCThreads=n  

![image-20220226143026270](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220226143026270.png)

### 响应时间优先

-XX:+UseConcMarkSweepGC ~ -XX:+UseParNewGC ~ SerialOld

-XX:ParallelGCThreads=n ~ -XX:ConcGCThreads=threads

-XX:CMSInitiatingOccupancyFraction=percent

-XX:+CMSScavengeBeforeRemark  

![image-20220226143045234](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220226143045234.png)

### GI  

#### 定义：Garbage First

- 2004 论文发布
- 2009 JDK 6u14 体验
- 2012 JDK 7u4 官方支持
- 2017 JDK 9 默认

#### 适用场景

- 同时注重吞吐量（Throughput）和低延迟（Low latency），默认的暂停目标是 200 ms
- 超大堆内存，会将堆划分为多个大小相等的 Region
- 整体上是 标记+整理 算法，两个区域之间是 复制 算法

#### 相关 JVM 参数

- -XX:+UseG1GC
- -XX:G1HeapRegionSize=size
- -XX:MaxGCPauseMillis=time

#### G1垃圾回收阶段

![image-20220226163321709](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220226163321709.png)

#### Young Collection

- 会STW

![image-20220226170714958](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220226170714958.png)

![image-20220226170750301](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220226170750301.png)

![image-20220226170801963](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220226170801963.png)

#### Young Collection + CM

- 在 Young GC 时会进行 GC Root 的初始标记
- 老年代占用堆空间比例达到阈值时，进行并发标记（不会 STW），由下面的 JVM 参数决定  

![image-20220226170920679](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220226170920679.png)

#### Mixed Collection

会对 E、S、O 进行全面垃圾回收

- 最终标记（Remark）会 STW
- 拷贝存活（Evacuation）会 STW
  -XX:MaxGCPauseMillis=ms  

![image-20220226171000341](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220226171000341.png)

#### Full GC

- SerialGC
  - 新生代内存不足发生的垃圾收集 - minor gc
  - 老年代内存不足发生的垃圾收集 - full gc
- ParallelGC
  - 新生代内存不足发生的垃圾收集 - minor gc
  - 老年代内存不足发生的垃圾收集 - full gc
- CMS
  - 新生代内存不足发生的垃圾收集 - minor gc
  - 老年代内存不足
- G1
  - 新生代内存不足  发生的垃圾收集 - minor gc
  - 老年代内存不足  

#### Young Collection 跨代引用

新生代回收的跨代引用（老年代引用新生代）问题  

![image-20220226202418210](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220226202418210.png)

#### Remark

pre-write barrier + satb_mark_queue  

![image-20220226202454356](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220226202454356.png)

#### JDK 8u20字符串去重

- 优点：节省大量内存
- 缺点：略微多占用了 cpu 时间，新生代回收时间略微增加
  -XX:+UseStringDeduplication  

```java
String s1 = new String("hello"); // char[]{'h','e','l','l','o'}
String s2 = new String("hello"); // char[]{'h','e','l','l','o'}
```

- 将所有新分配的字符串放入一个队列
- 当新生代回收时，G1并发检查是否有字符串重复
- 如果它们值一样，让它们引用同一个 char[]
- 注意，与 String.intern() 不一样
  - String.intern() 关注的是字符串对象
  - 而字符串去重关注的是 char[]
  - 在 JVM 内部，使用了不同的字符串表  

#### JDK 8u40 并发标记类卸载

所有对象都经过并发标记后，就能知道哪些类不再被使用，当一个类加载器的所有类都不再使用，则卸载它所加载的所有类 -XX:+ClassUnloadingWithConcurrentMark 默认启用  

#### JDK 8060 回收巨型对象

- 一个对象大于 region 的一半时，称之为巨型对象
- G1 不会对巨型对象进行拷贝
- 回收时被优先考虑
- G1 会跟踪老年代所有 incoming 引用，这样老年代 incoming 引用为0 的巨型对象就可以在新生代垃圾回收时处理掉  

#### JDK9并发标记起始时间的调整

- 并发标记必须在堆空间占满前完成，否则退化为 FullGC
- JDK 9 之前需要使用 -XX:InitiatingHeapOccupancyPercent
- JDK 9 可以动态调整
  - -XX:InitiatingHeapOccupancyPercent 用来设置初始值
  - 进行数据采样并动态调整
  - 总会添加一个安全的空档空间  

#### JDK 9 更高效的回收 

- 250+增强
- 180+bug修复
- https://docs.oracle.com/en/java/javase/12/gctuning  

## 5. 垃圾回收调优  

预备知识

- 掌握 GC 相关的 VM 参数，会基本的空间调整

```shell
java -XX:+PrintFlagsFinal -version | findstr "GC"  
```

- 掌握相关工具
- 明白一点：调优跟应用、环境有关，没有放之四海而皆准的法则  

### 调优领域

- 内存
- 锁竞争
- cpu 占用
- io  

### 确定目标

- 【低延迟】还是【高吞吐量】，选择合适的回收器
- CMS，G1，ZGC
- ParallelGC
- Zing  

### 最快的GC

答案是不发生 GC

- 查看 FullGC 前后的内存占用，考虑下面几个问题
  - 数据是不是太多？
    - resultSet = statement.executeQuery("select * from 大表 limit n")
  - 数据表示是否太臃肿？
    - 对象图
    - 对象大小 16 Integer 24 int 4
  - 是否存在内存泄漏？
    - static Map map =
    - 软 
    - 弱
    - 第三方缓存实现  

### 新生代调优

- 新生代的特点
  - 所有的 new 操作的内存分配非常廉价
    - TLAB thread-local allocation buffer
  - 死亡对象的回收代价是零
  - 大部分对象用过即死
  - Minor GC 的时间远远低于 Full GC  

- 越大越好吗？

```shell
-Xmn Sets the initial and maximum size (in bytes) of the heap for the young generation (nursery).
```

> GC is performed in this region more often than in other regions. If the size for the young
> generation is too small, then a lot of minor garbage collections are performed. If the size is too
> large, then only full garbage collections are performed, which can take a long time to complete.
> Oracle recommends that you keep the size for the young generation greater than 25% and less
> than 50% of the overall heap size.

- 新生代能容纳所有【并发量 * (请求-响应)】的数据
- 幸存区大到能保留【当前活跃对象+需要晋升对象】
- 晋升阈值配置得当，让长时间存活对象尽快晋升
  - -XX:MaxTenuringThreshold=threshold
  - -XX:+PrintTenuringDistribution

```java
Desired survivor size 48286924 bytes, new threshold 10 (max 10)
- age 1: 28992024 bytes, 28992024 total
- age 2: 1366864 bytes, 30358888 total
- age 3: 1425912 bytes, 31784800 total
...
```

### 老年代调优

以 CMS 为例

- CMS 的老年代内存越大越好
- 先尝试不做调优，如果没有 Full GC 那么已经...，否则先尝试调优新生代
- 观察发生 Full GC 时老年代内存占用，将老年代内存预设调大 1/4 ~ 1/3
  - -XX:CMSInitiatingOccupancyFraction=percent  

### 案例

- 案例1 Full GC 和 Minor GC频繁
- 案例2 请求高峰期发生 Full GC，单次暂停时间特别长 （CMS）
- 案例3 老年代充裕情况下，发生 Full GC （CMS jdk1.7）  

# 四、类加载与字节码技术

## 1. 类文件结构

### 魔数

0~3 字节，表示它是否是【class】类型的文件
0000000 **ca fe ba be** 00 00 00 34 00 23 0a 00 06 00 15 09  

### 版本

4~7 字节，表示类的版本 00 34（52） 表示是 Java 8
0000000 ca fe ba be **00 00 00 34** 00 23 0a 00 06 00 15 09  

### 常量池

| CONSTANT_Class              | 7    |
| --------------------------- | ---- |
| CONSTANT_Fieldref           | 9    |
| CONSTANT_Methodref          | 10   |
| CONSTANT_InterfaceMethodref | 11   |
| CONSTANT_String             | 8    |
| CONSTANT_Integer            | 3    |
| CONSTANT_Float              | 4    |
| CONSTANT_Long               | 5    |
| CONSTANT_Double             | 6    |
| CONSTANT_NameAndType        | 12   |
| CONSTANT_Utf8               | 1    |
| CONSTANT_MethodHandle       | 15   |
| CONSTANT_MethodType         | 16   |
| CONSTANT_InvokeDynamic      |      |

8~9 字节，表示常量池长度，00 23 （35） 表示常量池有 #1~#34项，注意 #0 项不计入，也没有值
0000000 ca fe ba be 00 00 00 34 **00 23** 0a 00 06 00 15 09  

第#1项 0a 表示一个 Method 信息，00 06 和 00 15（21） 表示它引用了常量池中 #6 和 #21 项来获得
这个方法的【所属类】和【方法名】
0000000 ca fe ba be 00 00 00 34 00 23 **0a 00 06 00 15** 09  

第#2项 09 表示一个 Field 信息，00 16（22）和 00 17（23） 表示它引用了常量池中 #22 和 # 23 项
来获得这个成员变量的【所属类】和【成员变量名】
0000000 ca fe ba be 00 00 00 34 00 23 0a 00 06 00 15 09
0000020 **00 16 00 17** 08 00 18 0a 00 19 00 1a 07 00 1b 07  

第#3项 08 表示一个字符串常量名称，00 18（24）表示它引用了常量池中 #24 项
0000020 00 16 00 17 **08 00 18** 0a 00 19 00 1a 07 00 1b 07  

第#4项 0a 表示一个 Method 信息，00 19（25） 和 00 1a（26） 表示它引用了常量池中 #25 和 #26
项来获得这个方法的【所属类】和【方法名】
0000020 00 16 00 17 08 00 18 **0a 00 19 00 1a** 07 00 1b 07  

第#5项 07 表示一个 Class 信息，00 1b（27） 表示它引用了常量池中 #27 项
0000020 00 16 00 17 08 00 18 0a 00 19 00 1a **07 00 1b** 07  

第#6项 07 表示一个 Class 信息，00 1c（28） 表示它引用了常量池中 #28 项
0000020 00 16 00 17 08 00 18 0a 00 19 00 1a 07 00 1b **07**
0000040 **00 1c** 01 00 06 3c 69 6e 69 74 3e 01 00 03 28 29  

第#7项 01 表示一个 utf8 串，00 06 表示长度，3c 69 6e 69 74 3e 是【 <init> 】
0000040 00 1c **01 00 06 3c 69 6e 69 74 3e** 01 00 03 28 29  

第#8项 01 表示一个 utf8 串，00 03 表示长度，28 29 56 是【()V】其实就是表示无参、无返回值
0000040 00 1c 01 00 06 3c 69 6e 69 74 3e **01 00 03 28 29**
0000060 **56** 01 00 04 43 6f 64 65 01 00 0f 4c 69 6e 65 4e  

第#10项 01 表示一个 utf8 串，00 0f（15） 表示长度，4c 69 6e 65 4e 75 6d 62 65 72 54 61 62 6c 65
是【LineNumberTable】
0000060 56 01 00 04 43 6f 64 65 **01 00 0f 4c 69 6e 65 4e**
0000100 **75 6d 62 65 72 54 61 62 6c 65** 01 00 12 4c 6f 63

第#11项 01 表示一个 utf8 串，00 12（18） 表示长度，4c 6f 63 61 6c 56 61 72 69 61 62 6c 65 54 61
62 6c 65是【LocalVariableTable】
0000100 75 6d 62 65 72 54 61 62 6c 65 **01 00 12 4c 6f 63**
0000120 **61 6c 56 61 72 69 61 62 6c 65 54 61 62 6c 65** 01

第#12项 01 表示一个 utf8 串，00 04 表示长度，74 68 69 73 是【this】
0000120 61 6c 56 61 72 69 61 62 6c 65 54 61 62 6c 65 **01**
0000140 **00 04 74 68 69 73** 01 00 1d 4c 63 6e 2f 69 74 63

第#13项 01 表示一个 utf8 串，00 1d（29） 表示长度，是【Lcn/itcast/jvm/t5/HelloWorld;】
0000140 00 04 74 68 69 73 **01 00 1d 4c 63 6e 2f 69 74 63**
0000160 **61 73 74 2f 6a 76 6d 2f 74 35 2f 48 65 6c 6c 6f**  

第#14项 01 表示一个 utf8 串，00 04 表示长度，74 68 69 73 是【main】
0000200 57 6f 72 6c 64 3b **01 00 04 6d 61 69 6e** 01 00 16

第#15项 01 表示一个 utf8 串，00 16（22） 表示长度，是【([Ljava/lang/String;)V】其实就是参数为字符串数组，无返回值
0000200 57 6f 72 6c 64 3b 01 00 04 6d 61 69 6e **01 00 16**
0000220 **28 5b 4c 6a 61 76 61 2f 6c 61 6e 67 2f 53 74 72**
0000240 **69 6e 67 3b 29 56** 01 00 04 61 72 67 73 01 00 13

第#16项 01 表示一个 utf8 串，00 04 表示长度，是【args】
0000240 69 6e 67 3b 29 56 **01 00 04 61 72 67 73** 01 00 13

第#17项 01 表示一个 utf8 串，00 13（19） 表示长度，是【[Ljava/lang/String;】
0000240 69 6e 67 3b 29 56 01 00 04 61 72 67 73 **01 00 13** 0000260 **5b 4c 6a 61 76 61 2f 6c** **61 6e** 
**67 2f 53 74 72 69** 0000300 **6e 67 3b** 01 00 10 4d 65 74 68 6f 64 50 61 72 61

第#18项 01 表示一个 utf8 串，00 10（16） 表示长度，是【MethodParameters】
0000300 6e 67 3b **01 00 10 4d 65 74 68 6f 64 50 61 72 61**
0000320 **6d 65 74 65 72 73** 01 00 0a 53 6f 75 72 63 65 46

第#19项 01 表示一个 utf8 串，00 0a（10） 表示长度，是【SourceFile】
0000320 6d 65 74 65 72 73 **01 00 0a 53 6f 75 72 63 65 46**
0000340 **69 6c 65** 01 00 0f 48 65 6c 6c 6f 57 6f 72 6c 64

第#20项 01 表示一个 utf8 串，00 0f（15） 表示长度，是【HelloWorld.java】
0000340 69 6c 65 **01 00 0f 48 65 6c 6c 6f 57 6f 72 6c 64** 0000360 **2e 6a 61 76 61** 0c 00 07 00 08 07
00 1d 0c 00 1e

第#21项 0c 表示一个 【名+类型】，00 07 00 08 引用了常量池中 #7 #8 两项
0000360 2e 6a 61 76 61 **0c 00 07 00 08** 07 00 1d 0c 00 1e

第#22项 07 表示一个 Class 信息，00 1d（29） 引用了常量池中 #29 项
0000360 2e 6a 61 76 61 0c 00 07 00 08 **07 00 1d** 0c 00 1e  

第#23项 0c 表示一个 【名+类型】，00 1e（30） 00 1f （31）引用了常量池中 #30 #31 两项
0000360 2e 6a 61 76 61 0c 00 07 00 08 07 00 1d **0c 00 1e**
0000400 **00 1f** 01 00 0b 68 65 6c 6c 6f 20 77 6f 72 6c 64

第#24项 01 表示一个 utf8 串，00 0f（15） 表示长度，是【hello world】
0000400 00 1f **01 00 0b 68 65 6c 6c 6f 20 77 6f 72 6c 64**

第#25项 07 表示一个 Class 信息，00 20（32） 引用了常量池中 #32 项
0000420 **07 00 20** 0c 00 21 00 22 01 00 1b 63 6e 2f 69 74

第#26项 0c 表示一个 【名+类型】，00 21（33） 00 22（34）引用了常量池中 #33 #34 两项
0000420 07 00 20 **0c 00 21 00 22** 01 00 1b 63 6e 2f 69 74

第#27项 01 表示一个 utf8 串，00 1b（27） 表示长度，是【cn/itcast/jvm/t5/HelloWorld】
0000420 07 00 20 0c 00 21 00 22 **01 00 1b 63 6e 2f 69 74**
0000440 **63 61 73 74 2f 6a 76 6d 2f 74 35 2f 48 65 6c 6c**
0000460 **6f 57 6f 72 6c 64** 01 00 10 6a 61 76 61 2f 6c 61

第#28项 01 表示一个 utf8 串，00 10（16） 表示长度，是【java/lang/Object】
0000460 6f 57 6f 72 6c 64 **01 00 10 6a 61 76 61 2f 6c 61**
0000500 **6e 67 2f 4f 62 6a 65 63 74** 01 00 10 6a 61 76 61

第#29项 01 表示一个 utf8 串，00 10（16） 表示长度，是【java/lang/System】
0000500 6e 67 2f 4f 62 6a 65 63 74 **01 00 10 6a 61 76 61**
0000520 **2f 6c 61 6e 67 2f 53 79 73 74 65 6d** 01 00 03 6f

第#30项 01 表示一个 utf8 串，00 03 表示长度，是【out】
0000520 2f 6c 61 6e 67 2f 53 79 73 74 65 6d **01 00 03 6f**
0000540 **75 74** 01 00 15 4c 6a 61 76 61 2f 69 6f 2f 50 72

第#31项 01 表示一个 utf8 串，00 15（21） 表示长度，是【Ljava/io/PrintStream;】
0000540 75 74 **01 00 15 4c 6a 61 76 61 2f 69 6f 2f 50 72**
0000560 **69 6e 74 53 74 72 65 61 6d 3b** 01 00 13 6a 61 76  

第#32项 01 表示一个 utf8 串，00 13（19） 表示长度，是【java/io/PrintStream】
0000560 69 6e 74 53 74 72 65 61 6d 3b **01 00 13 6a 61 76**
0000600 **61 2f 69 6f 2f 50 72 69 6e 74 53 74 72 65 61 6d**

第#33项 01 表示一个 utf8 串，00 07 表示长度，是【println】
0000620 **01 00 07 70 72 69 6e** 74 6c 6e 01 00 15 28 4c 6a

第#34项 01 表示一个 utf8 串，00 15（21） 表示长度，是【(Ljava/lang/String;)V】
0000620 01 00 07 70 72 69 6e 74 6c 6e **01 00 15 28 4c 6a**
0000640 **61 76 61 2f 6c 61 6e 67 2f 53 74 72 69 6e 67 3b**
0000660 **29 56** 00 21 00 05 00 06 00 00 00 00 00 02 00 01  

### 访问标识与继承信息

21 表示该 class 是一个类，公共的
0000660 29 56 **00 21** 00 05 00 06 00 00 00 00 00 02 00 01

05 表示根据常量池中 #5 找到本类全限定名
0000660 29 56 00 21 **00 05** 00 06 00 00 00 00 00 02 00 01

06 表示根据常量池中 #6 找到父类全限定名
0000660 29 56 00 21 00 05 **00 06** 00 00 00 00 00 02 00 01

表示接口的数量，本类为 0
0000660 29 56 00 21 00 05 00 06 **00 00** 00 00 00 02 00 01  

| Flag Name      | Value  | Interpretation                                               |
| -------------- | ------ | ------------------------------------------------------------ |
| ACC_PUBLIC     | 0x0001 | Declared public ; may be accessed from outside its package.  |
| ACC_FINAL      | 0x0010 | Declared final ; no subclasses allowed.                      |
| ACC_SUPER      | 0x0020 | Treat superclass methods specially when invoked by the *invokespecial* instruction. |
| ACC_INTERFACE  | 0x0200 | Is an interface, not a class.                                |
| ACC_ABSTRACT   | 0x0400 | Declared abstract ; must not be instantiated.                |
| ACC_SYNTHETIC  | 0x1000 | Declared synthetic; not present in the source code.          |
| ACC_ANNOTATION | 0x2000 | Declared as an annotation type.                              |
| ACC_ENUM       | 0x4000 | Declared as an enum type.                                    |

### Field 信息

表示成员变量数量，本类为 0
0000660 29 56 00 21 00 05 00 06 00 00 **00 00** 00 02 00 01  

| FieldType     | Type      | Interpretation                                               |
| ------------- | --------- | ------------------------------------------------------------ |
| B             | byte      | signed byte                                                  |
| C             | char      | Unicode character code point in the Basic Multilingual Plane, encoded with UTF-16 |
| D             | double    | double-precision floating-point value                        |
| F             | float     | single-precision floating-point value                        |
| I             | int       | integer                                                      |
| J             | long      | long integer                                                 |
| L ClassName ; | reference | an instance of class ClassName                               |
| S             | short     | signed short                                                 |
| Z             | boolean   | true or false                                                |
| [             | reference | one array dimension                                          |

### Method 信息

表示方法数量，本类为 2
0000660 29 56 00 21 00 05 00 06 00 00 00 00 00 02 00 01
一个方法由 访问修饰符，名称，参数描述，方法属性数量，方法属性组成

- 红色代表访问修饰符（本类中是 public）
- 蓝色代表引用了常量池 #07 项作为方法名称
- 绿色代表引用了常量池 #08 项作为方法参数描述
- 黄色代表方法属性数量，本方法是 1
- 红色代表方法属性
  - 00 09 表示引用了常量池 #09 项，发现是【Code】属性
  - 00 00 00 2f 表示此属性的长度是 47
  - 00 01 表示【操作数栈】最大深度
  - 00 01 表示【局部变量表】最大槽（slot）数  
  - 2a b7 00 01 b1 是字节码指令
  - 00 00 00 02 表示方法细节属性数量，本例是 2
  - 00 0a 表示引用了常量池 #10 项，发现是【LineNumberTable】属性
    - 00 00 00 06 表示此属性的总长度，本例是 6
    - 00 01 表示【LineNumberTable】长度
    - 00 00 表示【字节码】行号 00 04 表示【java 源码】行号
  - 00 0b 表示引用了常量池 #11 项，发现是【LocalVariableTable】属性
    - 00 00 00 0c 表示此属性的总长度，本例是 12
    - 00 01 表示【LocalVariableTable】长度
    - 00 00 表示局部变量生命周期开始，相对于字节码的偏移量
    - 00 05 表示局部变量覆盖的范围长度
    - 00 0c 表示局部变量名称，本例引用了常量池 #12 项，是【this】
    - 00 0d 表示局部变量的类型，本例引用了常量池 #13 项，是【Lcn/itcast/jvm/t5/HelloWorld;】
    - 00 00 表示局部变量占有的槽位（slot）编号，本例是 0  

![image-20220227145047950](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227145047950.png)

- 红色代表访问修饰符（本类中是 public static）

- 蓝色代表引用了常量池 #14 项作为方法名称

- 绿色代表引用了常量池 #15 项作为方法参数描述

- 黄色代表方法属性数量，本方法是 2

- 红色代表方法属性（属性1）

  - 00 09 表示引用了常量池 #09 项，发现是【Code】属性

  - 00 00 00 37 表示此属性的长度是 55

  - 00 02 表示【操作数栈】最大深度

  - 00 01 表示【局部变量表】最大槽（slot）数

  - 00 00 00 05 表示字节码长度，本例是 9

  - b2 00 02 12 03 b6 00 04 b1 是字节码指令

  - 00 00 00 02 表示方法细节属性数量，本例是 2

  - 00 0a 表示引用了常量池 #10 项，发现是【LineNumberTable】属性

    - 00 00 00 0a 表示此属性的总长度，本例是 10
    - 00 02 表示【LineNumberTable】长度
    - 00 00 表示【字节码】行号 00 06 表示【java 源码】行号
    - 00 08 表示【字节码】行号 00 07 表示【java 源码】行号

  - 00 0b 表示引用了常量池 #11 项，发现是【LocalVariableTable】属性

    - 00 00 00 0c 表示此属性的总长度，本例是 12
    - 00 01 表示【LocalVariableTable】长度  

    - 00 10 表示局部变量名称，本例引用了常量池 #16 项，是【args】
    - 00 11 表示局部变量的类型，本例引用了常量池 #17 项，是【[Ljava/lang/String;】
    - 00 00 表示局部变量占有的槽位（slot）编号，本例是 0

![image-20220227145157975](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227145157975.png)

- 红色代表方法属性（属性2）
  - 00 12 表示引用了常量池 #18 项，发现是【MethodParameters】属性
  - 00 00 00 05 表示此属性的总长度，本例是 5
  - 01 参数数量
  - 00 10 表示引用了常量池 #16 项，是【args】
  - 00 00 访问修饰符  

​		![image-20220227145218049](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227145218049.png)

### 附加属性

- 00 01 表示附加属性数量
- 00 13 表示引用了常量池 #19 项，即【SourceFile】
- 00 00 00 02 表示此属性的长度
- 00 14 表示引用了常量池 #20 项，即【HelloWorld.java】
  ![image-20220227181851831](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227181851831.png)

参考文献
https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html  

## 2. 字节码指令

### 入门

接着上一节，研究一下两组字节码指令，一个是
`public cn.itcast.jvm.t5.HelloWorld();` 构造方法的字节码指令  

1. 2a => *aload_0* 加载 slot 0 的局部变量，即 this，做为下面的 *invokespecial* 构造方法调用的参数
2. b7 => *invokespecial* 预备调用构造方法，哪个方法呢？
3. 00 01 引用常量池中 #1 项，即【 Method java/lang/Object."<init>":()V 】
4. b1 表示返回

另一个是 `public static void main(java.lang.String[]);` 主方法的字节码指令

```
b2 00 02 12 03 b6 00 04 b1  
```

1. b2 => *getstatic* 用来加载静态变量，哪个静态变量呢？
2. 00 02 引用常量池中 #2 项，即【Field java/lang/System.out:Ljava/io/PrintStream;】
3. 12 => *ldc* 加载参数，哪个参数呢？
4. 03 引用常量池中 #3 项，即 【String hello world】
5. b6 => *invokevirtual* 预备调用成员方法，哪个方法呢？
6. 00 04 引用常量池中 #4 项，即【Method java/io/PrintStream.println:(Ljava/lang/String;)V】
7. b1 表示返回

请参考
https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5  

### javap 工具

自己分析类文件结构太麻烦了，Oracle 提供了 javap 工具来反编译 class 文件  

```java
C:\Users\fyp01\Desktop\资料-解密JVM\代码\jvm\jvm\src\cn\itcast\jvm\t5>javap -v HelloWorld.class
Classfile /C:/Users/fyp01/Desktop/资料-解密JVM/代码/jvm/jvm/src/cn/itcast/jvm/t5/HelloWorld.class
  Last modified 2022-2-27; size 442 bytes
  MD5 checksum 103606e24ec918e862312533fda15bbc
  Compiled from "HelloWorld.java"
public class cn.itcast.jvm.t5.HelloWorld
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #6.#15         // java/lang/Object."<init>":()V
   #2 = Fieldref           #16.#17        // java/lang/System.out:Ljava/io/PrintStream;
   #3 = String             #18            // hello world
   #4 = Methodref          #19.#20        // java/io/PrintStream.println:(Ljava/lang/String;)V
   #5 = Class              #21            // cn/itcast/jvm/t5/HelloWorld
   #6 = Class              #22            // java/lang/Object
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               main
  #12 = Utf8               ([Ljava/lang/String;)V
  #13 = Utf8               SourceFile
  #14 = Utf8               HelloWorld.java
  #15 = NameAndType        #7:#8          // "<init>":()V
  #16 = Class              #23            // java/lang/System
  #17 = NameAndType        #24:#25        // out:Ljava/io/PrintStream;
  #18 = Utf8               hello world
  #19 = Class              #26            // java/io/PrintStream
  #20 = NameAndType        #27:#28        // println:(Ljava/lang/String;)V
  #21 = Utf8               cn/itcast/jvm/t5/HelloWorld
  #22 = Utf8               java/lang/Object
  #23 = Utf8               java/lang/System
  #24 = Utf8               out
      stack=2, locals=1, args_size=1
         0: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;       
         3: ldc           #3                  // String hello world
         5: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)
V
         8: return
      LineNumberTable:
        line 6: 0
        line 7: 8
}
SourceFile: "HelloWorld.java"
```

### 图解方法执行流程

#### (1) 原始 java 代码

```java
package cn.itcast.jvm.t3.bytecode;
/**
 * 演示 字节码指令 和 操作数栈、常量池的关系
 */
public class Demo3_1 {
    public static void main(String[] args) {
        int a = 10;
        int b = Short.MAX_VALUE + 1;
        int c = a + b;
        System.out.println(c);
    }
}
```

#### (2) 编译后的字节码文件

```java
C:\Users\fyp01\Desktop\资料-解密JVM\代码\jvm\jvm\src\cn\itcast\jvm\t3\bytecode>javap -v Demo3_1.class
Classfile /C:/Users/fyp01/Desktop/资料-解密JVM/代码/jvm/jvm/src/cn/itcast/jvm/t3/bytecode/Demo3_1.class
  Last modified 2022-2-27; size 458 bytes
  MD5 checksum 9a972eb3f4d5db211d370df006319849
  Compiled from "Demo3_1.java"
public class cn.itcast.jvm.t3.bytecode.Demo3_1
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #7.#16         // java/lang/Object."<init>":()V
   #2 = Class              #17            // java/lang/Short
   #3 = Integer            32768
   #4 = Fieldref           #18.#19        // java/lang/System.out:Ljava/io/PrintStream;
   #5 = Methodref          #20.#21        // java/io/PrintStream.println:(I)V
   #6 = Class              #22            // cn/itcast/jvm/t3/bytecode/Demo3_1
   #7 = Class              #23            // java/lang/Object
   #8 = Utf8               <init>
   #9 = Utf8               ()V
  #10 = Utf8               Code
  #11 = Utf8               LineNumberTable
  #12 = Utf8               main
  #13 = Utf8               ([Ljava/lang/String;)V
  #14 = Utf8               SourceFile
  #15 = Utf8               Demo3_1.java
  #16 = NameAndType        #8:#9          // "<init>":()V
  #17 = Utf8               java/lang/Short
  #18 = Class              #24            // java/lang/System
  #19 = NameAndType        #25:#26        // out:Ljava/io/PrintStream;
  #20 = Class              #27            // java/io/PrintStream
  #21 = NameAndType        #28:#29        // println:(I)V
  #22 = Utf8               cn/itcast/jvm/t3/bytecode/Demo3_1
  #23 = Utf8               java/lang/Object
  #24 = Utf8               java/lang/System
  #25 = Utf8               out
  #26 = Utf8               Ljava/io/PrintStream;
  #27 = Utf8               java/io/PrintStream
  #28 = Utf8               println
  #29 = Utf8               (I)V
{
  public cn.itcast.jvm.t3.bytecode.Demo3_1();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 5: 0

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=4, args_size=1
         0: bipush        10
         2: istore_1
         3: ldc           #3                  // int 32768
         5: istore_2
         6: iload_1
         7: iload_2
         8: iadd
         9: istore_3
        10: getstatic     #4                  // Field java/lang/System.out:Ljava/io/PrintStream;
        13: iload_3
        14: invokevirtual #5                  // Method java/io/PrintStream.println:(I)V
        17: return
      LineNumberTable:
        line 7: 0
        line 8: 3
        line 9: 6
        line 10: 10
        line 11: 17
}
SourceFile: "Demo3_1.java"
```

#### (3) 常量池载入运行时常量池

![image-20220227204343498](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227204343498.png)

#### (4) 方法字节码载入方法区

![image-20220227204357784](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227204357784.png)

#### (5) main 线程开始运行，分配栈帧内存

（stack=2，locals=4）  

![image-20220227204421731](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227204421731.png)

#### (6) 执行引擎开始执行字节码

bipush 10

- 将一个 byte 压入操作数栈（其长度会补齐 4 个字节），类似的指令还有
- sipush 将一个 short 压入操作数栈（其长度会补齐 4 个字节）
- ldc 将一个 int 压入操作数栈
- ldc2_w 将一个 long 压入操作数栈（分两次压入，因为 long 是 8 个字节）
- 这里小的数字都是和字节码指令存在一起，超过 short 范围的数字存入了常量池  

![image-20220227204506659](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227204506659.png)

istore_1
将操作数栈顶数据弹出，存入局部变量表的 slot 1  

![image-20220227204636973](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227204636973.png)

![image-20220227204647394](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227204647394.png)

ldc #3

- 从常量池加载 #3 数据到操作数栈
- **注意** Short.MAX_VALUE 是 32767，所以 32768 = Short.MAX_VALUE + 1 实际是在编译期间计算
  好的  

![image-20220227221102895](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227221102895.png)

istore_2  

![image-20220227221201908](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227221201908.png)

![image-20220227221218136](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227221218136.png)

iload_1  

![image-20220227221238670](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227221238670.png)

iload_2  

![image-20220227221309797](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227221309797.png)

iadd  

![image-20220227221340817](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227221340817.png)

![image-20220227221352901](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227221352901.png)

istore_3  

![image-20220227221416616](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227221416616.png)

![image-20220227221424751](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227221424751.png)

getstatic #4  

![image-20220227221441418](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227221441418.png)

![image-20220227221456194](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227221456194.png)iload_3  

![image-20220227221513491](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227221513491.png)

![image-20220227221522148](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227221522148.png)

invokevirtual #5

- 找到常量池 #5 项
- 定位到方法区 java/io/PrintStream.println:(I)V 方法
- 生成新的栈帧（分配 locals、stack等）
- 传递参数，执行新栈帧中的字节码  

![image-20220227221541148](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220227221541148.png)

return

- 完成 main 方法调用，弹出 main 栈帧
- 程序结束  

### 练习 - 分析 i++

目的：从字节码角度分析 a++ 相关题目

源码：  

```java
package cn.itcast.jvm.t3.bytecode;
/**
 * 从字节码角度分析 a++ 相关题目
 */
public class Demo3_2 {
    public static void main(String[] args) {
        int a = 10;
        int b = a++ + ++a + a--;
        System.out.println(a);
        System.out.println(b);
    }
}
```

字节码：  

```
public static void main(java.lang.String[]);
        descriptor: ([Ljava/lang/String;)V
        flags: (0x0009) ACC_PUBLIC, ACC_STATIC
        Code:
        stack=2, locals=3, args_size=1
        0: bipush 10
        2: istore_1
        3: iload_1
        4: iinc 1, 1
        7: iinc 1, 1
        10: iload_1
        11: iadd
        12: iload_1
        13: iinc 1, -1
        16: iadd
        17: istore_2
        21: iload_1
        22: invokevirtual #3 // Method
        java/io/PrintStream.println:(I)V
        25: getstatic #2 // Field
        java/lang/System.out:Ljava/io/PrintStream;
        28: iload_2
        29: invokevirtual #3 // Method
        java/io/PrintStream.println:(I)V
        32: return
        LineNumberTable:
        line 8: 0
        line 9: 3
        line 10: 18
        line 11: 25
        line 12: 32
        LocalVariableTable:
        Start Length Slot Name Signature
        0 33 0 args [Ljava/lang/String;
        3 30 1 a I
        18 15 2 b I
```

分析：

- 注意 iinc 指令是直接在局部变量 slot 上进行运算
- a++ 和 ++a 的区别是先执行 iload 还是 先执行 iinc  

![image-20220228171324343](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228171324343.png)



![image-20220228171335717](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228171335717.png)

![image-20220228171351322](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228171351322.png)

![image-20220228171412011](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228171412011.png)

![image-20220228171432469](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228171432469.png)

![image-20220228171527482](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228171527482.png)

![image-20220228171543393](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228171543393.png)

![image-20220228171559475](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228171559475.png)

![image-20220228171612029](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228171612029.png)

![image-20220228171624544](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228171624544.png)

![image-20220228171641475](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228171641475.png)

### 条件判断指令

| 0x99 | ifeq      | 判断是否 == 0    |
| ---- | --------- | ---------------- |
| 0x9a | ifne      | 判断是否 != 0    |
| 0x9b | iflt      | 判断是否 < 0     |
| 0x9c | ifge      | 判断是否 >= 0    |
| 0x9d | ifgt      | 判断是否 > 0     |
| 0x9e | ifle      | 判断是否 <= 0    |
| 0x9f | if_icmpeq | 两个int是否 ==   |
| 0xa0 | if_icmpne | 两个int是否 !=   |
| 0xa1 | if_icmplt | 两个int是否 <    |
| 0xa2 | if_icmpge | 两个int是否 >=   |
| 0xa3 | if_icmpgt | 两个int是否 >    |
| 0xa4 | if_icmple | 两个int是否 <=   |
| 0xa5 | if_acmpeq | 两个引用是否 ==  |
| 0xa6 | if_acmpne | 两个引用是否 !=  |
| 0xc6 | ifnull    | 判断是否 == null |
| 0xc7 | ifnonnull | 判断是否 != null |

几点说明：

- byte，short，char 都会按 int 比较，因为操作数栈都是 4 字节
- goto 用来进行跳转到指定行号的字节码

源码：  

```java
public class Demo3_3 {
    public static void main(String[] args) {
        int a = 0;
        if(a == 0) {
            a = 10;
        } else {
            a = 20;
        }
    }
}
```

字节码：  

```
0: iconst_0
1: istore_1
2: iload_1
3: ifne 		12
6: bipush 		10
8: istore_1
9: goto 		15
12: bipush 		20
14: istore_1
15: return
```

> 思考
>
> 细心的同学应当注意到，以上比较指令中没有 long，float，double 的比较，那么它们要比较怎
> 么办？
>
> 参考 https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-6.html#jvms-6.5.lcmp  

### 循环控制指令

其实循环控制还是前面介绍的那些指令，例如 while 循环：  

```java
public class Demo3_4 {
    public static void main(String[] args) {
        int a = 0;
        while (a < 10) {
            a++;
        }
    }
}
```

字节码是：  

```
0: iconst_0
1: istore_1
2: iload_1
3: bipush 		10
5: if_icmpge 	14
8: iinc 		1, 1
11: goto 		2
14: return
```

再比如 do while 循环：  

```java
public class Demo3_5 {
    public static void main(String[] args) {
        int a = 0;
        do {
            a++;
        } while (a < 10);
    }
}
```

字节码：

```
0: iconst_0 
1: istore_1 
2: iinc 1, 		1 
5: iload_1 
6: bipush 		10 
8: if_icmplt 	2 
11: return
```

最后再看看 for 循环：  

```java
public class Demo3_6 {
    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
        }
    }
}
```

字节码是：  

```
0: iconst_0
1: istore_1
2: iload_1
3: bipush 		10
5: if_icmpge 	14
8: iinc 1, 		1
11: goto 		2
14: return
```

> 注意
> 比较 while 和 for 的字节码，你发现它们是一模一样的，殊途也能同归  

### 联系 - 判断结果

请从字节码角度分析，下列代码运行的结果  

```java
public class Demo3_6_1 {
    public static void main(String[] args) {
        int i = 0;
        int x = 0;
        while (i < 10) {
            x = x++;
            i++;
        } S
        ystem.out.println(x); // 结果是 0
    }
}
```

### 构造方法

#### (1) `<cinit>()V`

```java
public class Demo3_8_1 {
    static int i = 10;
    static {
        i = 20;
    } s
    tatic {
        i = 30;
    }
}
```

编译器会按从上至下的顺序，收集所有 static 静态代码块和静态成员赋值的代码，合并为一个特殊的方
法 `<cinit>()V` ：  

```java
0: bipush       10
2: putstatic    #2      // Field i:I
5: bipush       20
7: putstatic    #2      // Field i:I
10: bipush      30
12: putstatic   #2      // Field i:I
15: return
```

`<cinit>()V` 方法会在类加载的初始化阶段被调用

> **练习
> **同学们可以自己调整一下 static 变量和静态代码块的位置，观察字节码的改动  

#### (2) `<init>()V`  

```java
package cn.itcast.jvm.t3.bytecode;

public class Demo3_8_2 {


    private String a = "s1";

    {
        b = 20;
    }

    private int b = 10;

    {
        a = "s2";
    }

    public Demo3_8_2(String a, int b) {
        this.a = a;
        this.b = b;
    }

    public static void main(String[] args) {
        Demo3_8_2 d = new Demo3_8_2("s3", 30);
        System.out.println(d.a);
        System.out.println(d.b);
    }
}
```

编译器会按从上至下的顺序，收集所有 {} 代码块和成员变量赋值的代码，形成新的构造方法，但原始构造方法内的代码总是在最后  

```java
public cn.itcast.jvm.t3.bytecode.Demo3_8_2(java.lang.String, int);
        descriptor: (Ljava/lang/String;I)V
        flags: ACC_PUBLIC
        Code:
        stack=2, locals=3, args_size=3
        0: aload_0
        1: invokespecial    #1      // super.<init>()V
        4: aload_0
        5: ldc              #2      // <- "s1"
        7: putfield         #3      // -> this.a
        10: aload_0
        11: bipush          20      // <- 20
        13: putfield        #4      // -> this.b
        16: aload_0
        17: bipush          10      // <- 10
        19: putfield        #4      // -> this.b
        22: aload_0
        23: ldc             #5      // <- "s2"
        25: putfield        #3      // -> this.a
        28: aload_0                 // ------------------------------
        29: aload_1                 // <- slot 1(a) "s3"            |
        30: putfield        #3      // -> this.a                    |
        33: aload_0                                                 |
        34: iload_2                 // <- slot 2(b) 30              |
        35: putfield        #4      // -> this.b --------------------
        38: return
        LineNumberTable: ...
        LocalVariableTable:
        Start Length Slot Name Signature
        0 39 0 this Lcn/itcast/jvm/t3/bytecode/Demo3_8_2;
        0 39 1 a Ljava/lang/String;
        0 39 2 b I
        MethodParameters: ...
```

### 方法调用

看一下几种不同的方法调用对应的字节码指令  

```java
package cn.itcast.jvm.t3.bytecode;

public class Demo3_9 {
    public Demo3_9() { }

    private void test1() { }

    private final void test2() { }

    public void test3() { }

    public static void test4() { }

    @Override
    public String toString() {
        return super.toString();
    }

    public static void main(String[] args) {
        Demo3_9 d = new Demo3_9();
        d.test1();
        d.test2();
        d.test3();
        d.test4();
        Demo3_9.test4();
        d.toString();
    }

}
```

字节码：

```
0: new           #3                  // class cn/itcast/jvm/t3/bytecode/Demo3_9
3: dup
4: invokespecial #4                  // Method "<init>":()V
7: astore_1
8: aload_1
9: invokespecial #5                  // Method test1:()V
12: aload_1
13: invokespecial #6                  // Method test2:()V
16: aload_1
17: invokevirtual #7                  // Method test3:()V
20: aload_1
21: pop
22: invokestatic  #8                  // Method test4:()V
25: invokestatic  #8                  // Method test4:()V
28: aload_1
29: invokevirtual #9                  // Method toString:()Ljava/lang/String;
32: pop
33: return
```

- new 是创建【对象】，给对象分配堆内存，执行成功会将【对象引用】压入操作数栈
- dup 是赋值操作数栈栈顶的内容，本例即为【对象引用】，为什么需要两份引用呢，一个是要配合 invokespecial 调用该对象的构造方法 "<init>":()V （会消耗掉栈顶一个引用），另一个要配合 astore_1 赋值给局部变量
- 最终方法（final），私有方法（private），构造方法都是由 invokespecial 指令来调用，属于静态绑定
- 普通成员方法是由 invokevirtual 调用，属于动态绑定，即支持多态
- 成员方法与静态方法调用的另一个区别是，执行方法前是否需要【对象引用】
- 比较有意思的是 d.test4(); 是通过【对象引用】调用一个静态方法，可以看到在调用invokestatic 之前执行了 pop 指令，把【对象引用】从操作数栈弹掉了
- 还有一个执行 invokespecial 的情况是通过 super 调用父类方法  

### 多态的原理

```java
package cn.itcast.jvm.t3.bytecode;

import java.io.IOException;

/**
 * 演示多态原理，注意加上下面的 JVM 参数，禁用指针压缩
 * -XX:-UseCompressedOops -XX:-UseCompressedClassPointers
 */
public class Demo3_10 {

    public static void test(Animal animal) {
        animal.eat();
        System.out.println(animal.toString());
    }

    public static void main(String[] args) throws IOException {
        test(new Cat());
        test(new Dog());
        System.in.read();
    }
}

abstract class Animal {
    public abstract void eat();

    @Override
    public String toString() {
        return "我是" + this.getClass().getSimpleName();
    }
}

class Dog extends Animal {

    @Override
    public void eat() {
        System.out.println("啃骨头");
    }
}

class Cat extends Animal {

    @Override
    public void eat() {
        System.out.println("吃鱼");
    }
}
```

#### 1）运行代码

停在 System.in.read() 方法上，这时运行 jps 获取进程 id

#### 2）运行 HSDB 工具

进入 JDK 安装目录，执行  

```shell
java -cp ./lib/sa-jdi.jar sun.jvm.hotspot.HSDB
```

进入图形界面 attach 进程 id  

#### 3） 查找某个对象

打开 Tools -> Find Object By Query

输入 `select d from cn.itcast.jvm.t3.bytecode.Dog d` 点击 Execute 执行  

![image-20220228210437787](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228210437787.png)

#### 4）查看对象内存结构

点击超链接可以看到对象的内存结构，此对象没有任何属性，因此只有对象头的 16 字节，前 8 字节是MarkWord，后 8 字节就是对象的 Class 指针

但目前看不到它的实际地址  

![image-20220228210518395](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228210518395.png)

#### 5) 查看对象 Class 的内存地址

可以通过 Windows -> Console 进入命令行模式，执行

```shell
mem 0x00000001299b4978 2
```

mem 有两个参数，参数 1 是对象地址，参数 2 是查看 2 行（即 16 字节）
结果中第二行 0x000000001b7d4028 即为 Class 的内存地址  

![image-20220228210550701](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228210550701.png)

#### 6）查看类的 vtable

- 方法1：Alt+R 进入 Inspector 工具，输入刚才的 Class 内存地址，看到如下界面  

![image-20220228210856636](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228210856636.png)

- 方法2：或者 Tools -> Class Browser 输入 Dog 查找，可以得到相同的结果  

![image-20220228210913577](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220228210913577.png)

无论通过哪种方法，都可以找到 Dog Class 的 vtable 长度为 6，意思就是 Dog 类有 6 个虚方法（多态相关的，final，static 不会列入）
那么这 6 个方法都是谁呢？从 Class 的起始地址开始算，偏移 0x1b8 就是 vtable 的起始地址，进行计算得到：  

```
0x000000001b7d4028
1b8 +
---------------------
0x000000001b7d41e0
```

通过 Windows -> Console 进入命令行模式，执行  

```
mem 0x000000001b7d41e0 6
0x000000001b7d41e0: 0x000000001b3d1b10
0x000000001b7d41e8: 0x000000001b3d15e8
0x000000001b7d41f0: 0x000000001b7d35e8
0x000000001b7d41f8: 0x000000001b3d1540
0x000000001b7d4200: 0x000000001b3d1678
0x000000001b7d4208: 0x000000001b7d3fa8
```

就得到了 6 个虚方法的入口地址

#### 7）验证方法地址

通过 Tools -> Class Browser 查看每个类的方法定义，比较可知  

```
Dog - public void eat() @0x000000001b7d3fa8
Animal - public java.lang.String toString() @0x000000001b7d35e8;
Object - protected void finalize() @0x000000001b3d1b10;
Object - public boolean equals(java.lang.Object) @0x000000001b3d15e8;
Object - public native int hashCode() @0x000000001b3d1540;
Object - protected native java.lang.Object clone() @0x000000001b3d1678;
```

对号入座，发现  

####  8）小结
当执行 invokevirtual 指令时，

1. 先通过栈帧中的对象引用找到对象
2. 分析对象头，找到对象的实际 Class
3. Class 结构中有 vtable，它在类加载的链接阶段就已经根据方法的重写规则生成好了
4. 查表得到方法的具体地址
5. 执行方法的字节码  



### 异常处理	

#### 1) try-catch  

```java
package cn.itcast.jvm.t3.bytecode;

public class Demo3_11_1 {

    public static void main(String[] args) {
        int i = 0;
        try {
            i = 10;
        } catch (Exception e) {
            i = 20;
        }
    }
}
```

> 注意
> 为了抓住重点，下面的字节码省略了不重要的部分  

```java
 public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=1, locals=3, args_size=1
         0: iconst_0
         1: istore_1
         2: bipush        10
         4: istore_1
         5: goto          12
         8: astore_2
         9: bipush        20
        11: istore_1
        12: return
      Exception table:
         from    to  target type
             2     5     8   Class java/lang/Exception
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            9       3     2     e   Ljava/lang/Exception;
            0      13     0  args   [Ljava/lang/String;
            2      11     1     i   I
      StackMapTable: ....
	  MethodParameters: ....
}
```

- 可以看到多出来一个 Exception table 的结构，[from, to) 是前闭后开的检测范围，一旦这个范围内的字节码执行出现异常，则通过 type 匹配异常类型，如果一致，进入 target 所指示行号
- 8 行的字节码指令 astore_2 是将异常对象引用存入局部变量表的 slot 2 位置  

#### 2) 多个 single-catch 块的情况  

```java
package cn.itcast.jvm.t3.bytecode;

public class Demo3_11_2 {

    public static void main(String[] args) {
        int i = 0;
        try {
            i = 10;
        } catch (ArithmeticException e) {
            i = 30;
        } catch (NullPointerException e) {
            i = 40;
        } catch (Exception e) {
            i = 50;
        }
    }
}
```

部分字节码如下：

```java
 public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=1, locals=3, args_size=1
         0: iconst_0
         1: istore_1
         2: bipush        10
         4: istore_1
         5: goto          26
         8: astore_2
         9: bipush        30
        11: istore_1
        12: goto          26
        15: astore_2
        16: bipush        40
        18: istore_1
        19: goto          26
        22: astore_2
        23: bipush        50
        25: istore_1
        26: return
      Exception table:
         from    to  target type
             2     5     8   Class java/lang/ArithmeticException
             2     5    15   Class java/lang/NullPointerException
             2     5    22   Class java/lang/Exception
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            9       3     2     e   Ljava/lang/ArithmeticException;
           16       3     2     e   Ljava/lang/NullPointerException;
           23       3     2     e   Ljava/lang/Exception;
            0      27     0  args   [Ljava/lang/String;
            2      25     1     i   I
      StackMapTable: ....
}
```

- 因为异常出现时，只能进入 Exception table 中一个分支，所以局部变量表 slot 2 位置被共用  

#### 3) multi-catch 的情况  

```java
package cn.itcast.jvm.t3.bytecode;

import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class Demo3_11_3 {

    public static void main(String[] args) {
        try {
            Method test = Demo3_11_3.class.getMethod("test");
            test.invoke(null);
        } catch (NoSuchMethodException | IllegalAccessException | InvocationTargetException e) {
            e.printStackTrace();
        }
    }

    public static void test() {
        System.out.println("ok");
    }
}
```

部分字节码如下：

```java
public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=3, locals=2, args_size=1
         0: ldc           #2                  // class cn/itcast/jvm/t3/bytecode/Demo3_11_3
         2: ldc           #3                  // String test
         4: iconst_0
         5: anewarray     #4                  // class java/lang/Class
         8: invokevirtual #5                  // Method java/lang/Class.getMethod:(Ljava/lang/String;[Ljava/lang/Class;)Ljava/lang/reflect/Method;
        11: astore_1
        12: aload_1
        13: aconst_null
        14: iconst_0
        15: anewarray     #6                  // class java/lang/Object
        18: invokevirtual #7                  // Method java/lang/reflect/Method.invoke:(Ljava/lang/Object;[Ljava/lang/Object;)Ljava/lang/Object;
        21: pop
        22: goto          30
        25: astore_1
        26: aload_1
        27: invokevirtual #11                 // Method java/lang/ReflectiveOperationException.printStackTrace:()V
        30: return
      Exception table:
         from    to  target type
             0    22    25   Class java/lang/NoSuchMethodException
             0    22    25   Class java/lang/IllegalAccessException
             0    22    25   Class java/lang/reflect/InvocationTargetException
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
           12      10     1  test   Ljava/lang/reflect/Method;
           26       4     1     e   Ljava/lang/ReflectiveOperationException;
            0      31     0  args   [Ljava/lang/String;
      StackMapTable: ....
}
```

#### 4) finally  

```java
package cn.itcast.jvm.t3.bytecode;

public class Demo3_11_4 {

    public static void main(String[] args) {
        int i = 0;
        try {
            i = 10;
        } catch (Exception e) {
            i = 20;
        } finally {
            i = 30;
        }
    }
}
```

部分字节码如下：

```java
public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=1, locals=4, args_size=1
         0: iconst_0
         1: istore_1
         2: bipush        10
         4: istore_1
         5: bipush        30
         7: istore_1
         8: goto          27
        11: astore_2
        12: bipush        20
        14: istore_1
        15: bipush        30
        17: istore_1
        18: goto          27
        21: astore_3
        22: bipush        30
        24: istore_1
        25: aload_3
        26: athrow
        27: return
      Exception table:
         from    to  target type
             2     5    11   Class java/lang/Exception
             2     5    21   any
            11    15    21   any
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
           12       3     2     e   Ljava/lang/Exception;
            0      28     0  args   [Ljava/lang/String;
            2      26     1     i   I
      StackMapTable: ....
}
```

可以看到 finally 中的代码被复制了 3 份，分别放入 try 流程，catch 流程以及 catch 剩余的异常类型流程 

### 练习 - finally 面试题

#### 1) finally 出现了 return

先问问自己，下面的题目输出什么？  

```java
package cn.itcast.jvm.t3.bytecode;

public class Demo3_12_1 {
    public static void main(String[] args) {
        int result = test();
        System.out.println(result);
    }

    public static int test() {
        try {
            return 10;
        } finally {
            return 20;
        }
    }
}
```

部分字节码如下：

```java
public static int test();
    descriptor: ()I
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=1, locals=2, args_size=0
         0: bipush        10
         2: istore_0
         3: bipush        20
         5: ireturn
         6: astore_1
         7: bipush        20
         9: ireturn
      Exception table:
         from    to  target type
             0     3     6   any
      StackMapTable: ....
}
```

- 由于 finally 中的 ireturn 被插入了所有可能的流程，因此返回结果肯定以 finally 的为准
- 至于字节码中第 2 行，似乎没啥用，且留个伏笔，看下个例子
- 跟上例中的 finally 相比，发现没有 athrow 了，这告诉我们：如果在 finally 中出现了 return，会
  吞掉异常，可以试一下下面的代码  

```java
package cn.itcast.jvm.t3.bytecode;

public class Demo3_12_1 {
    public static void main(String[] args) {
        int result = test();
        System.out.println(result);
    }

    public static int test() {
        try {
            int i = 1/0;
            return 10;
        } finally {
            return 20;
        }
    }
}
```

部分字节码如下：

```java
public static int test();
    descriptor: ()I
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=3, args_size=0
         0: iconst_1
         1: iconst_0
         2: idiv
         3: istore_0
         4: bipush        10
         6: istore_1
         7: bipush        20
         9: ireturn
        10: astore_2
        11: bipush        20
        13: ireturn
      Exception table:
         from    to  target type
             0     7    10   any
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            4       6     0     i   I
      StackMapTable: ....
}
```

#### 2) finally 对返回值影响

同样问问自己，下面的题目输出什么？  

```java
package cn.itcast.jvm.t3.bytecode;

public class Demo3_12_2 {
    public static void main(String[] args) {
        int result = test();
        System.out.println(result);
    }

    public static int test() {
        int i = 10;
        try {
            return i;
        } finally {
            i = 20;
        }
    }
}
```

部分字节码如下：

```java
  public static int test();
    descriptor: ()I
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=1, locals=3, args_size=0
         0: bipush        10
         2: istore_0
         3: iload_0
         4: istore_1           		// 暂存 10 到本地变量表 slot(1) 中，目的为了固定返回值 
         5: bipush        20
         7: istore_0
         8: iload_1					// 加载 10，因为有变量需要载入，常量就不需要载入
         9: ireturn					// 抛出 10
        10: astore_2
        11: bipush        20
        13: istore_0
        14: aload_2
        15: athrow
      Exception table:
         from    to  target type
             3     5    10   any
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            3      13     0     i   I
      StackMapTable: ....
}
```

### synchronized

```java
package cn.itcast.jvm.t3.bytecode;

public class Demo3_13 {

    public static void main(String[] args) {
        Object lock = new Object();
        synchronized (lock) {
            System.out.println("ok");
        }
    }
}
```

部分字节码如下：

```java
public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=4, args_size=1
         0: new           #2                  // class java/lang/Object
         3: dup
         4: invokespecial #1                  // Method java/lang/Object."<init>":()V
         7: astore_1
         8: aload_1
         9: dup
        10: astore_2
        11: monitorenter
        12: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
        15: ldc           #4                  // String ok
        17: invokevirtual #5                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
        20: aload_2
        21: monitorexit
        22: goto          30
        25: astore_3
        26: aload_2
        27: monitorexit
        28: aload_3
        29: athrow
        30: return
      Exception table:
         from    to  target type
            12    22    25   any
            25    28    25   any
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      31     0  args   [Ljava/lang/String;
            8      23     1  lock   Ljava/lang/Object;
      StackMapTable: number_of_entries = 2
        frame_type = 255 /* full_frame */
          offset_delta = 25
          locals = [ class "[Ljava/lang/String;", class java/lang/Object, class java/lang/Object ]
          stack = [ class java/lang/Throwable ]
        frame_type = 250 /* chop */
          offset_delta = 4
}
```

> 注意
>
> 方法级别的 synchronized 不会在字节码指令中有所体现  



## 3. 编译器处理

所谓的 `语法糖` ，其实就是指 java 编译器把 `*.java` 源码编译为 `*.class` 字节码的过程中，自动生成
和转换的一些代码，主要是为了减轻程序员的负担，算是 java 编译器给我们的一个额外福利（给糖吃
嘛）
**注意**，以下代码的分析，借助了 javap 工具，idea 的反编译功能，idea 插件 jclasslib 等工具。另外，
编译器转换的结果直接就是 class 字节码，只是为了便于阅读，给出了 `几乎等价` 的 java 源码方式，并
不是编译器还会转换出中间的 java 源码，切记。  

#### 1) 默认构造器  

```java
public class Candy1 {
}
```

编译成class后的代码：  

```java
public class Candy1 {
    // 这个无参构造是编译器帮助我们加上的
    public Candy1() {
        super(); // 即调用父类 Object 的无参构造方法，即调用 java/lang/Object."
<init>":()V
    }
}
```

#### 2) 自动拆装箱

这个特性是 `JDK 5` 开始加入的， 代码片段1 ：  

```java
package cn.itcast.jvm.t3.candy;

public class Candy2 {
    public static void main(String[] args) {
        Integer x = 1;
        int y = x;
    }
}
```

这段代码在 `JDK 5` 之前是无法编译通过的，必须改写为 代码片段2 :  

```java
public class Candy2 {
    public static void main(String[] args) {
        Integer x = Integer.valueOf(1);
        int y = x.intValue();
    }
}
```

显然之前版本的代码太麻烦了，需要在基本类型和包装类型之间来回转换（尤其是集合类中操作的都是包装类型），因此这些转换的事情在JDK 5 之后都由编译阶段完成。即代码片段1都会在编译阶段被转换为 代码片段2  

#### 3) 泛型集合取值	

泛型也是在 `JDK 5` 开始加入的特性，但 java 在编译泛型代码后会执行 `泛型擦除` 的动作，即泛型信息
在编译为字节码之后就丢失了，实际的类型都当做了 Object 类型来处理：  

```java
public class Candy3 {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        list.add(10); // 实际调用的是 List.add(Object e)
        Integer x = list.get(0); // 实际调用的是 Object obj = List.get(int index);
    }
}
```

所以在取值时，编译器真正生成的字节码中，还要额外做一个类型转换的操作：  

```java
// 需要将 Object 转为 Integer
Integer x = (Integer)list.get(0);
```

如果前面的 x 变量类型修改为 int 基本类型那么最终生成的字节码是：  

```java
// 需要将 Object 转为 Integer, 并执行拆箱操作
int x = ((Integer)list.get(0)).intValue()
```

还好这些麻烦事都不用自己做。

擦除的是字节码上的泛型信息，可以看到 LocalVariableTypeTable 仍然保留了方法参数泛型的信息  

```java
public static void main(java.lang.String[]) throws java.lang.Exception;
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=3, args_size=1
         0: new           #2                  // class java/util/ArrayList
         3: dup
         4: invokespecial #3                  // Method java/util/ArrayList."<init>":()V
         7: astore_1
         8: aload_1
         9: bipush        10
        11: invokestatic  #4                  // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
        14: invokeinterface #5,  2            // InterfaceMethod java/util/List.add:(Ljava/lang/Object;)Z
        19: pop
        20: aload_1
        21: iconst_0
        22: invokeinterface #6,  2            // InterfaceMethod java/util/List.get:(I)Ljava/lang/Object;
        27: checkcast     #7                  // class java/lang/Integer
        30: astore_2
        31: return
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      32     0  args   [Ljava/lang/String;
            8      24     1  list   Ljava/util/List;
           31       1     2     x   Ljava/lang/Integer;
      LocalVariableTypeTable:
        Start  Length  Slot  Name   Signature
            8      24     1  list   Ljava/util/List<Ljava/lang/Integer;>;
    Exceptions:
      throws java.lang.Exception
}
```

使用反射，仍然能够获得这些信息：  

```java\
public Set<Integer> test(List<String> list, Map<Integer, Object> map) {
}
```

```java
Method test = Candy3.class.getMethod("test", List.class, Map.class);
Type[] types = test.getGenericParameterTypes();
for (Type type : types) {
    if (type instanceof ParameterizedType) {
        ParameterizedType parameterizedType = (ParameterizedType) type;
        System.out.println("原始类型 - " + parameterizedType.getRawType());
        Type[] arguments = parameterizedType.getActualTypeArguments();
        for (int i = 0; i < arguments.length; i++) {
            System.out.printf("泛型参数[%d] - %s\n", i, arguments[i]);
        }
    }

}
```

​	输出

```
原始类型 - interface java.util.List
泛型参数[0] -class java.lang.String
原始类型 - interface java.util.Map
泛型参数[0] - class java.lang.Integer
泛型参数[1] - class java.lang.Object
```

#### 4) 可变参数

可变参数也是 `JDK 5` 开始加入的新特性： 例如：  

```java
package cn.itcast.jvm.t3.candy;

public class Candy4 {
    public static void foo(String... args) {
        String[] array = args; // 直接赋值
        System.out.println(array);
    }
    public static void main(String[] args) {
        foo("hello", "world");
    }
}
```

可变参数 `String... args` 其实是一个 String[] args ，从代码中的赋值语句中就可以看出来。 同
样 java 编译器会在编译期间将上述代码变换为：  

```java
package cn.itcast.jvm.t3.candy;

public class Candy4 {
    public static void foo(String[] args) {
        String[] array = args; // 直接赋值
        System.out.println(array);
    }
    public static void main(String[] args) {
        foo("hello", "world");
    }
}
```

> **注意** 如果调用了 foo() 则等价代码为 foo(new String[]{}) ，创建了一个空的数组，而不会
> 传递 null 进去  

#### 5) foreach 循环

仍是 `JDK 5` 开始引入的语法糖，数组的循环：  

```java
package cn.itcast.jvm.t3.candy;

public class Candy5_1 {
    public static void main(String[] args) {
        int[] array = {1, 2, 3, 4, 5}; // 数组赋初值的简化写法也是语法糖哦
        for (int e : array) {
            System.out.println(e);
        }
    }
}
```

会被编译器抓换为：

```java
package cn.itcast.jvm.t3.candy;
public class Candy5_1 {
    public Candy5_1() {
    }
    public static void main(String[] args) {
        int[] array = {1, 2, 3, 4, 5}; // 数组赋初值的简化写法也是语法糖哦
        for (int i = 0; i < array.length; ++i) {
            int e = array[i];
            System.out.println(e);
        }
    }
}
```

而集合的循环：  

```java
package cn.itcast.jvm.t3.candy;

import java.util.Arrays;
import java.util.List;

public class Candy5_2 {
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
        for (Integer i : list) {
            System.out.println(i);
        }
    }
}
```

实际被编译器转换为对迭代器的调用：  

```java
package cn.itcast.jvm.t3.candy;

import java.util.Arrays;
import java.util.List;

public class Candy5_2 {
    public Candy5_2() {
        
    }
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
        Iterator iter = list.iterator();
        while(iter.hasNext()) {
            Integer e = (Integer)iter.next();
            System.out.println(e);
        }
    }
}
```

> **注意** foreach 循环写法，能够配合数组，以及所有实现了 Iterable 接口的集合类一起使用，其
> 中 Iterable 用来获取集合的迭代器（ Iterator ）  

#### 6) switch 字符串

从 `JDK 7` 开始，switch 可以作用于字符串和枚举类，这个功能其实也是语法糖，例如：  

```java
package cn.itcast.jvm.t3.candy;

public class Candy6_1 {
    public static void choose(String str) {
        switch (str) {
            case "hello": {
                System.out.println("h");
                break;
            }
            case "world": {
                System.out.println("w");
                break;
            }
        }
    }
}
```

> 注意 switch 配合 String 和枚举使用时，变量不能为null，原因分析完语法糖转换后的代码应当自
> 然清楚

会被编译器转换为：  

```java
public class Candy6_1 {
    public Candy6_1() {
    } 
    public static void choose(String str) {
        byte x = -1;
        switch(str.hashCode()) {
            case 99162322: // hello 的 hashCode
                if (str.equals("hello")) {
                    x = 0;
                } b
                    reak;
            case 113318802: // world 的 hashCode
                if (str.equals("world")) {
                    x = 1;
                }
        } switch(x) {
            case 0:
                System.out.println("h");
                break;
            case 1:
                System.out.println("w");
        }
    }
}
```

可以看到，执行了两遍 switch，第一遍是根据字符串的 hashCode 和 equals 将字符串的转换为相应
byte 类型，第二遍才是利用 byte 执行进行比较。

为什么第一遍时必须既比较 hashCode，又利用 equals 比较呢？hashCode 是为了提高效率，减少可
能的比较；而 equals 是为了防止 hashCode 冲突，例如 BM 和 C. 这两个字符串的hashCode值都是
2123 ，如果有如下代码：  

```java
package cn.itcast.jvm.t3.candy;

public class Candy6_2 {
    public static void choose(String str) {
        switch (str) {
            case "BM": {
                System.out.println("h");
                break;
            }
            case "C.": {
                System.out.println("w");
                break;
            }
        }
    }
}
```

会被编译器转换为：  

```java
public class Candy6_2 {
    public Candy6_2() {
    } public static void choose(String str) {
        byte x = -1;
        switch(str.hashCode()) {
            case 2123: // hashCode 值可能相同，需要进一步用 equals 比较
                if (str.equals("C.")) {
                    x = 1;
                } else if (str.equals("BM")) {
                    x = 0;
                }
            default: // 上一个没有 break; 这里会继续执行
                switch(x) {
                    case 0:
                        System.out.println("h");
                        break;
                    case 1:
                        System.out.println("w");
                }
        }
    }
}
```

#### 7) switch 枚举

switch 枚举的例子，原始代码：  

```java
enum Sex {
	MALE, FEMALE
}
```

```java
public class Candy7 {
    public static void foo(Sex sex) {
        switch (sex) {
            case MALE:
                System.out.println("男"); break;
            case FEMALE:
                System.out.println("女"); break;
        }
    }
}
```

转换后代码：  

```java
public class Candy7 {
    /**
    * 定义一个合成类（仅 jvm 使用，对我们不可见）
    * 用来映射枚举的 ordinal 与数组元素的关系
    * 枚举的 ordinal 表示枚举对象的序号，从 0 开始
    * 即 MALE 的 ordinal()=0，FEMALE 的 ordinal()=1
    */
    static class $MAP {
        // 数组大小即为枚举元素个数，里面存储case用来对比的数字
        static int[] map = new int[2];
        static {
            map[Sex.MALE.ordinal()] = 1;
            map[Sex.FEMALE.ordinal()] = 2;
        }
    } 
    public static void foo(Sex sex) {
        int x = $MAP.map[sex.ordinal()];
        switch (x) {
            case 1:
                System.out.println("男");
                break;
            case 2:
                System.out.println("女");
                break;
        }
    }
}
```

#### 8) 枚举类

`JDK 7` 新增了枚举类，以前面的性别枚举为例：  

```java
enum Sex {
	ALE, FEMALE
}
```

转换后代码：  

```java
public final class Sex extends Enum<Sex> {
    public static final Sex MALE;
    public static final Sex FEMALE;
    private static final Sex[] $VALUES;
    static {
        MALE = new Sex("MALE", 0);
        FEMALE = new Sex("FEMALE", 1);
        $VALUES = new Sex[]{MALE, FEMALE};
    }
    private Sex(String name, int ordinal) {
        super(name, ordinal);
    } 
    public static Sex[] values() {
        return $VALUES.clone();
    } 
    public static Sex valueOf(String name) {
        return Enum.valueOf(Sex.class, name);
    }
}
```

#### 9) try-with-resources

DK 7 开始新增了对需要关闭的资源处理的特殊语法 try-with-resources`：  

```java
try(资源变量 = 创建资源对象){
    
} catch( ) {
    
}
```

其中资源对象需要实现 AutoCloseable 接口，例如 InputStream 、 OutputStream 、Connection 、 Statement 、 ResultSet 等接口都实现了 AutoCloseable ，使用 try-withresources 可以不用写 finally 语句块，编译器会帮助生成关闭资源代码，例如：  

```java
public class Candy9 {
    public static void main(String[] args) {
        try(InputStream is = new FileInputStream("d:\\1.txt")) {
            System.out.println(is);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}  
```

会被转换为：  

```java
public class Candy9 {
    public Candy9() {
    }
    public static void main(String[] args) {
        try {
            InputStream is = new FileInputStream("d:\\1.txt");
            Thread thread = null;
            try {
                System.out.println(is);
            } catch (Throwable e1) {
                // t 是我们代码出现的异常
                t = e1;
                throw e1;
            } finally {
                // 判断了资源不为空
                if (is != null) {
                    // 如果我们代码有异常
                    if (t != null) {
                        try {
                            is.close();
                        } catch (Throwable e2) {
                            // 如果 close 出现异常，作为被压制异常添加
                            t.addSuppressed(e2);
                        }
                    } else {
                        // 如果我们代码没有异常，close 出现的异常就是最后 catch 块中的 e
                        is.close();
                    }
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

为什么要设计一个 addSuppressed(Throwable e) （添加被压制异常）的方法呢？是为了防止异常信息的丢失（想想 try-with-resources 生成的 fianlly 中如果抛出了异常）：  

```java
public class Test6 {
    public static void main(String[] args) {
        try (MyResource resource = new MyResource()) {
            int i = 1/0;
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
class MyResource implements AutoCloseable {
    public void close() throws Exception {
            throw new Exception("close 异常");
    }
}
```

输出：  

```
java.lang.ArithmeticException: / by zero
at test.Test6.main(Test6.java:7)
Suppressed: java.lang.Exception: close 异常
at test.MyResource.close(Test6.java:18)
at test.Test6.main(Test6.java:6)
```

#### 10) 方法重写时的桥接方法

我们都知道，方法重写时对返回值分两种情况：

- 父子类的返回值完全一致
- 子类返回值可以是父类返回值的子类（比较绕口，见下面的例子）  

```java
class A {
    public Number m() {
        return 1;
    }
} 
class B extends A {
    @Override
    // 子类 m 方法的返回值是 Integer 是父类 m 方法返回值 Number 的子类
    public Integer m() {
        return 2;
    }
}
```

对于子类，java 编译器会做如下处理：  

```java
class B extends A {
    public Integer m() {
            return 2;
    }
    // 此方法才是真正重写了父类 public Number m() 方法
    public synthetic bridge Number m() {
        // 调用 public Integer m()
        return m();
    }
}
```

其中桥接方法比较特殊，仅对 java 虚拟机可见，并且与原来的 public Integer m() 没有命名冲突，可以
用下面反射代码来验证：  

```java
for (Method m : B.class.getDeclaredMethods()) {
	System.out.println(m);
}
```

会输出：  

```java
public java.lang.Integer test.candy.B.m()
public java.lang.Number test.candy.B.m()
```

#### 11) 匿名内部类

源代码：  

```java
public class Candy11 {
    public static void main(String args) {
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                System.out.println("ok");
            }
        };
    }
}
```

转换后代码：

```java
// 额外生成的类
final class Candy11$1 implements Runnable {
    Candy11$1() {
        
    }
    public void run() {
        System.ut.println("ok");
    }
}
```

```java
public class Candy11 {
    public static void main(String[] args) {
        Runnable runnable = new Candy11$1();
    }
}
```

引用局部变量的匿名内部类，源代码：

```java
public class Candy11 {
    public static void test(final int x) {
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                System.out.println("ok:" + x);
            }
        }
    }
}
```

转换后代码：

```java
// 额外生成的类
final class Candy11$1 implements Runnable {
    int val$x;
    Candy11$1(int x) {
        this.val$x = x;
    }
    // run 方法 重写后，是不能传参，故而通过构造器
    public void run() {
		System.out.println("ok:" + this.val$x); 
    }
}
```

```java
public class Candy11 {
    // final : 为了 与 匿名类 Candy11$1 的成员变量保持一致
    public static void test(final int x) {
        Runnable runnable = new Candy11$1(x);
    }
}
```

> **注意** 这同时解释了为什么匿名内部类引用局部变量时，局部变量必须是 final 的：因为在创建Candy11$1 对象时，将 x 的值赋值给了 Candy11$1 对象的 val属 性， 所以 x 不应 该再发生变化了 ， 如果变化，那 么 x 属性没有机会再跟着一起变化  

## 4. 类加载阶段

#### 1) 加载

- 将类的字节码载入方法区中，内部采用 C++ 的 `instanceKlass` 描述 java 类，它的重要 field 有：

  - _java_mirror 即 java 的类镜像，例如对 String 来说，就是 String.class，作用是把 klass 暴露给 java 使用
  - _super 即父类
  - _fields 即成员变量
  - _methods 即方法
  - _constants 即常量池
  - _class_loader 即类加载器
  - _vtable 虚方法表
  - _itable 接口方法表

- 如果这个类还有父类没有加载，先加载父类

- 加载和链接可能是交替运行的

  > **注意**
  >
  > - instanceKlass 这样的【元数据】是存储在方法区（1.8 后的元空间内），但 _java_mirror是存储在堆中
  > - 可以通过前面介绍的 HSDB 工具查看  

  ![image-20220301210046275](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220301210046275.png)

#### 2) 链接

##### **验证**
验证类是否符合 JVM规范，安全性检查
用 UE 等支持二进制的编辑器修改 HelloWorld.class 的魔数，在控制台运行  

```
C:\Users\fyp01\Desktop\资料-解密JVM\代码\jvm\jvm\src\cn\itcast\jvm\t5>java HelloWorld
Error: A JNI error has occurred, please check your installation and try again
Exception in thread "main" java.lang.ClassFormatError: Incompatible magic value 3405691578 in class file HelloWorld
        at java.lang.ClassLoader.defineClass1(Native Method)
        at java.lang.ClassLoader.defineClass(ClassLoader.java:763)
        at java.security.SecureClassLoader.defineClass(SecureClassLoader.java:142)
        at java.net.URLClassLoader.defineClass(URLClassLoader.java:467)
        at java.net.URLClassLoader.access$100(URLClassLoader.java:73)
        at java.net.URLClassLoader$1.run(URLClassLoader.java:368)
        at java.net.URLClassLoader$1.run(URLClassLoader.java:362)
        at java.security.AccessController.doPrivileged(Native Method)
        at java.net.URLClassLoader.findClass(URLClassLoader.java:361)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
        at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:338)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
        at sun.launcher.LauncherHelper.checkAndLoadMain(LauncherHelper.java:495)
```

##### **准备**
为 static 变量分配空间，设置默认值

- static 变量在 JDK 7 之前存储于 instanceKlass 末尾，从 JDK 7 开始，存储于 _java_mirror 末尾
- static 变量分配空间和赋值是两个步骤，**分配空间**在准备阶段完成，**赋值**在初始化阶段完成
- 如果 static 变量是 final 的基本类型，以及字符串常量，那么编译阶段值就确定了，赋值在准备阶段完成
- 如果 static 变量是 final 的，但属于引用类型，那么赋值也会在初始化阶段完成  

##### **解析**

将常量池中的符号引用解析为直接引用  

```java
package cn.itcast.jvm.t3.load;

import java.io.IOException;

/**
 * 解析的含义
 */
public class Load2 {
    public static void main(String[] args) throws ClassNotFoundException, IOException {
//        ClassLoader classloader = Load2.class.getClassLoader();
        // loadClass 方法不会导致类的解析和初始化
//        Class<?> c = classloader.loadClass("cn.itcast.jvm.t3.load.C");
        new C();
        System.in.read();
    }
}

class C {
    D d = new D();
}

class D {

}
```

#### 3) 初始化

##### `<cinit>()V` 方法

初始化即调用 `<cinit>()V` ，虚拟机会保证这个类的『构造方法』的线程安全

##### **发生的时机**

概括得说，类初始化是【懒惰的】

- main 方法所在的类，总会被首先初始化
- 首次访问这个类的静态变量或静态方法时
- 子类初始化，如果父类还没初始化，会引发
- 子类访问父类的静态变量，只会触发父类的初始化  
- Class.forName
- new 会导致初始化

不会导致类初始化的情况

- 访问类的 static final 静态常量（基本类型和字符串）不会触发初始化
- 类对象.class 不会触发初始化
- 创建该类的数组不会触发初始化  
- 类加载器的loadClass 方法
- Class.forName 的参数 2 为 false 时

```java
class A {
    static int a = 0;
    static {
        System.out.println("a init");
    }
}

class B extends A {
    final static double b = 5.0;
    static boolean c = false;
    static {
        System.out.println("b init");
    }
}
```

验证（实验时请先全部注释，每次只执行其中一个）  

```java
public class Load3 {
    static {
        System.out.println("main init");
    }
    public static void main(String[] args) throws ClassNotFoundException, IOException {
//        // 1. 静态常量不会触发初始化
//        System.out.println(B.b);
//        // 2. 类对象.class 不会触发初始化
//        System.out.println(B.class);
//        // 3. 创建该类的数组不会触发初始化
//        System.out.println(new B[0]);
        // 4. 不会初始化类 B，但会加载 B、A
        ClassLoader cl = Thread.currentThread().getContextClassLoader();
        cl.loadClass("cn.itcast.jvm.t3.load.B");
//        // 5. 不会初始化类 B，但会加载 B、A
//        ClassLoader c2 = Thread.currentThread().getContextClassLoader();
//        Class.forName("cn.itcast.jvm.t3.load.B", false, c2);
        System.in.read();


//        // 1. 首次访问这个类的静态变量或静态方法时
//        System.out.println(A.a);
//        // 2. 子类初始化，如果父类还没初始化，会引发
//        System.out.println(B.c);
//        // 3. 子类访问父类静态变量，只触发父类初始化
//        System.out.println(B.a);
//        // 4. 会初始化类 B，并先初始化类 A
//        Class.forName("cn.itcast.jvm.t3.load.B");


    }
}
```

#### 4) 练习

从字节码分析，使用 a，b，c 这三个常量是否会导致 E 初始化  

```java
package cn.itcast.jvm.t3.load;

public class Load4 {
    public static void main(String[] args) {
        System.out.println(E.a);
        System.out.println(E.b);
        System.out.println(E.c);

    }
}

class E {
    public static final int a = 10;
    public static final String b = "hello";
    public static final Integer c = 20;  // Integer.valueOf(20)  final 的 基本类型 + String 不会触发的，Integer 不是
    static {
        System.out.println("init E");
    }
}
```

典型应用 - 完成懒惰初始化单例模式  

```java
class Singleton {

    public static void test() {
        System.out.println("test");
    }

    private Singleton() {}

    private static class LazyHolder{
        // final 可以保证 构造方法执行结束后，final 成员变量 被 其他线程访问 是 可见的
        private static final Singleton SINGLETON = new Singleton();
        
        // jvm 保证 类 初始化 在 多线程下 被 同步加锁，静态代码块 在初始化 阶段完成
        static {
            System.out.println("lazy holder init");
        }
    }

    public static Singleton getInstance() {
        return LazyHolder.SINGLETON;
    }
}
```

以上的实现特点是：

- 懒惰实例化
- 初始化时的线程安全是有保障的  

## 5. 类加载器

以 JDK 8 为例：  

| 名称                    | 加载哪的类            | 说明                          |
| ----------------------- | --------------------- | ----------------------------- |
| Bootstrap ClassLoader   | JAVA_HOME/jre/lib     | 无法直接访问                  |
| Extension ClassLoader   | JAVA_HOME/jre/lib/ext | 上级为 Bootstrap，显示为 null |
| Application ClassLoader | classpath             | 上级为 Extension              |
| 自定义类加载器          | 自定义                | 上级为 Application            |

#### 1) 启动类加载器

用 Bootstrap 类加载器加载类：  

```java
package cn.itcast.jvm.t3.load;

public class F {
    static {
        System.out.println("bootstrap F init");
    }
}
```

执行  

```java
package cn.itcast.jvm.t3.load;

public class Load5_1 {
    public static void main(String[] args) throws ClassNotFoundException {
        Class<?> aClass = Class.forName("cn.itcast.jvm.t3.load.F");
        System.out.println(aClass.getClassLoader()); // AppClassLoader  ExtClassLoader
    }
}
```

输出  

```
E:\git\jvm\out\production\jvm>java -Xbootclasspath/a:. cn.itcast.jvm.t3.load.Load5_1
bootstrap F init
null
```

- -Xbootclasspath 表示设置 bootclasspath
- 其中 /a:. 表示将当前目录追加至 bootclasspath 之后
- 可以用这个办法替换核心类
  - java -Xbootclasspath:`<new bootclasspath>`
  - java -Xbootclasspath/a:<追加路径>
  - java -Xbootclasspath/p:<追加路径>  

#### 2) 扩展类加载器

```java
package cn.itcast.jvm.t3.load;

public class G {
    static {
//        System.out.println("ext G init");
        System.out.println("classpath G init");
    }
}
```

执行  

```java
package cn.itcast.jvm.t3.load;

/**
 * 演示 扩展类加载器
 * 在 C:\Program Files\Java\jdk1.8.0_91 下有一个 my.jar
 * 里面也有一个 G 的类，观察到底是哪个类被加载了
 */
public class Load5_2 {
    public static void main(String[] args) throws ClassNotFoundException {
        Class<?> aClass = Class.forName("cn.itcast.jvm.t3.load.G");
        System.out.println(aClass.getClassLoader());
    }
}
```

输出  

```
classpath G init
sun.misc.Launcher$AppClassLoader@18b4aac2
```

写一个同名的类  

```java
package cn.itcast.jvm.t3.load;

public class G {
    static {
        System.out.println("ext G init");
        //System.out.println("classpath G init");
    }
}
```

打个 jar 包  

```
E:\git\jvm\out\production\jvm>jar -cvf my.jar cn/itcast/jvm/t3/load/G.class
已添加清单
正在添加: cn/itcast/jvm/t3/load/G.class(输入 = 481) (输出 = 322)(压缩了 33%)
```

将 jar 包拷贝到 JAVA_HOME/jre/lib/ext
重新执行 Load5_2
输出  

```java
ext G init
sun.misc.Launcher$ExtClassLoader@29453f44
```

#### 3) 双亲委派模式

所谓的双亲委派，就是指调用类加载器的 loadClass 方法时，查找类的规则

> **注意**
>
> 这里的双亲，翻译为上级似乎更加合适，因为他们并没有继承关系

```java
    protected Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException {
    synchronized (getClassLoadingLock(name)) {
		// 1. 检查该类是否已经加载
        Class<?> c = findLoadedClass(name);
        if (c == null) {
            long t0 = System.nanoTime();
            try {
                if (parent != null) {
					// 2. 有上级的话，委派上级 loadClass
                    c = parent.loadClass(name, false);
                } else {
					// 3. 如果没有上级了（ExtClassLoader），则委派
                    BootstrapClassLoader
                            c = findBootstrapClassOrNull(name);
                }
            } catch (ClassNotFoundException e) {
            } i
            f (c == null) {
                long t1 = System.nanoTime();
				// 4. 每一层找不到，调用 findClass 方法（每个类加载器自己扩展）来加载
                c = findClass(name);
				// 5. 记录耗时
                sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                sun.misc.PerfCounter.getFindClasses().increment();
            }
        } i
        f (resolve) {
            resolveClass(c);
        } r
        eturn c;
    }
}
```

例如：  

```java
package cn.itcast.jvm.t3.load;

import java.util.ServiceLoader;

public class Load5_3 {
    public static void main(String[] args) throws ClassNotFoundException {
        System.out.println(Load5_3.class.getClassLoader());
        Class<?> aClass = Load5_3.class.getClassLoader().loadClass("cn.itcast.jvm.t3.load.H");
        System.out.println(aClass.getClassLoader());

    }
}
```

执行流程为：  

1. `sun.misc.Launcher$AppClassLoader` //1 处， 开始查看已加载的类，结果没有
2. `sun.misc.Launcher$AppClassLoader` // 2 处，委派上级`sun.misc.Launcher$ExtClassLoader.loadClass`()
3. `sun.misc.Launcher$ExtClassLoader` // 1 处，查看已加载的类，结果没有  
4. `sun.misc.Launcher$ExtClassLoader` // 3 处，没有上级了，则委派 `BootstrapClassLoader` 查找
5. `BootstrapClassLoader` 是在 JAVA_HOME/jre/lib 下找 H 这个类，显然没有
6. `sun.misc.Launcher$ExtClassLoader` // 4 处，调用自己的 findClass 方法，是在JAVA_HOME/jre/lib/ext 下找 H 这个类，显然没有，回到 `sun.misc.Launcher$AppClassLoader`的 // 2 处
7. 继续执行到 `sun.misc.Launcher$AppClassLoader` // 4 处，调用它自己的 findClass 方法，在classpath 下查找，找到了 

总之就是，先查找自己已加载的类，没有则委派上级查找已加载的类，递归去查找（套娃），没有再去各自类加载器的类路径查找未加载的类，有则把它加载，没有（解娃），让下级继续递归

#### 4) 线程上下文类加载器

我们在使用 JDBC 时，都需要加载 Driver 驱动，不知道你注意到没有，不写  

```java
Class.forName("com.mysql.jdbc.Driver")
```

也是可以让 `com.mysql.jdbc.Driver` 正确加载的，你知道是怎么做的吗？
让我们追踪一下源码：  

```java
public class DriverManager {
    // 注册驱动的集合
    private final static CopyOnWriteArrayList<DriverInfo> registeredDrivers
            = new CopyOnWriteArrayList<>();
    // 初始化驱动
    static {
        loadInitialDrivers();
        println("JDBC DriverManager initialized");
    }
}
```

先不看别的，看看 DriverManager 的类加载器：  

```java
System.out.println(DriverManager.class.getClassLoader());	
```

打印 null，表示它的类加载器是 `Bootstrap ClassLoader`，会到 JAVA_HOME/jre/lib 下搜索类，但JAVA_HOME/jre/lib 下显然没有 `mysql-connector-java-5.1.47.jar` 包，这样问题来了，在`DriverManager` 的静态代码块中，怎么能正确加载 `com.mysql.jdbc.Driver` 呢？

继续看 loadInitialDrivers() 方法：  

```java
private static void loadInitialDrivers() {
    String drivers;
    try {
        drivers = AccessController.doPrivileged(new PrivilegedAction<String>() {
            public String run() {
                return System.getProperty("jdbc.drivers");
            }
        });
    } catch (Exception ex) {
        drivers = null;
    }
    // 1）使用 ServiceLoader 机制加载驱动，即 SPI
    AccessController.doPrivileged(new PrivilegedAction<Void>() {
        public Void run() {
            ServiceLoader<Driver> loadedDrivers = ServiceLoader.load(Driver.class);
            Iterator<Driver> driversIterator = loadedDrivers.iterator();
            try{
                while(driversIterator.hasNext()) {
                    driversIterator.next();
                }
            } catch(Throwable t) {
                // Do nothing
            }
            return null;
        }
    });
    println("DriverManager.initialize: jdbc.drivers = " + drivers);

    // 2）使用 jdbc.drivers 定义的驱动名加载驱动
    if (drivers == null || drivers.equals("")) {
        return;
    }
    String[] driversList = drivers.split(":");
    println("number of Drivers:" + driversList.length);
    for (String aDriver : driversList) {
        try {
            println("DriverManager.Initialize: loading " + aDriver);
            // 这里的 ClassLoader.getSystemClassLoader() 就是应用程序类加载器
            Class.forName(aDriver, true, ClassLoader.getSystemClassLoader());
        } catch (Exception ex) {
            println("DriverManager.Initialize: load failed: " + ex);
        }
    }
}
```

先看 2）发现它最后是使用 Class.forName 完成类的加载和初始化，关联的是应用程序类加载器，因此可以顺利完成类加载

再看 1）它就是大名鼎鼎的 Service Provider Interface （SPI）

约定如下，在 jar 包的 META-INF/services 包下，以接口全限定名名为文件，文件内容是实现类名  

![image-20220303132622797](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220303132622797.png)

这样就可以使用  

```java
ServiceLoader<接口类型> allImpls = ServiceLoader.load(接口类型.class);
Iterator<接口类型> iter = allImpls.iterator();
while(iter.hasNext()) {
    iter.next();
}
```

来得到实现类，体现的是【面向接口编程+解耦】的思想，在下面一些框架中都运用了此思想：

- JDBC
- Servlet 初始化器
- Spring 容器
- Dubbo（对 SPI 进行了扩展）

接着看 ServiceLoader.load 方法：  

```java
public static <S> ServiceLoader<S> load(Class<S> service) {
    // 获取线程上下文类加载器
    ClassLoader cl = Thread.currentThread().getContextClassLoader();
    return ServiceLoader.load(service, cl);
}
```

线程上下文类加载器是当前线程使用的类加载器，默认就是应用程序类加载器，它内部又是由Class.forName 调用了线程上下文类加载器完成类加载，具体代码在 ServiceLoader 的内部类LazyIterator 中：  

```java
private S nextService() {
    if (!hasNextService())
        throw new NoSuchElementException();
    String cn = nextName;
    nextName = null;
    Class<?> c = null;
    try {
        c = Class.forName(cn, false, loader);
    } catch (ClassNotFoundException x) {
        fail(service,
                "Provider " + cn + " not found");
    }
    if (!service.isAssignableFrom(c)) {
        fail(service,
                "Provider " + cn  + " not a subtype");
    }
    try {
        S p = service.cast(c.newInstance());
        providers.put(cn, p);
        return p;
    } catch (Throwable x) {
        fail(service,
                "Provider " + cn + " could not be instantiated",
                x);
    }
    throw new Error();          // This cannot happen
}
```

#### 5) 自定义类加载器

问问自己，什么时候需要自定义类加载器

- 想加载非 classpath 随意路径中的类文件
- 都是通过接口来使用实现，希望解耦时，常用在框架设计
- 这些类希望予以隔离，不同应用的同名类都可以加载，不冲突，常见于 tomcat 容器

步骤：

1. 继承 ClassLoader 父类
2. 要遵从双亲委派机制，重写 findClass 方法
   - 注意不是重写 loadClass 方法，否则不会走双亲委派机制
3. 读取类文件的字节码
4. 调用父类的 defineClass 方法来加载类
5. 使用者调用该类加载器的 loadClass 方法

示例：

在当前类的父目录下，创建TestCass类 

NIO方式

```java
class MyClassLoader extends ClassLoader {

    @Override // name 就是类名称
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        
        String path = "D:\\Users\\fyp01\\IdeaProjects\\jvm\\jvm\\src\\cn\\itcast\\jvm\\t3\\load\\" + name + ".class";

        try {
            ByteArrayOutputStream os = new ByteArrayOutputStream();
            Files.copy(Paths.get(path), os);

            // 得到字节数组
            byte[] bytes = os.toByteArray();

            // byte[] -> *.class
            return defineClass(name, bytes, 0, bytes.length);

        } catch (IOException e) {
            e.printStackTrace();
            throw new ClassNotFoundException("类文件未找到", e);
        }
    }
}
```

传统IO方式

```java
public class MyClassLoder1 extends ClassLoader {

    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        String path = "D:\\Users\\fyp01\\IdeaProjects\\jvm\\jvm\\src\\cn\\itcast\\jvm\\t3\\load\\" + name + ".class";
        try {
            FileInputStream in = new FileInputStream(new File(path));
            ByteArrayOutputStream baos = new ByteArrayOutputStream();
            int len = -1;
            byte[] b = new byte[1024];
            while ((len = in.read(b)) != -1) {
                baos.write(b, 0, len);
            }
            byte[] bytes = baos.toByteArray();
            in.close();
            baos.close();
            return defineClass(name, bytes, 0, bytes.length);
        } catch (IOException e) {
            e.printStackTrace();
            return null;
        }
    }

    public static void main(String[] args) throws Exception {
        MyClassLoder1 myClassLoder1 = new MyClassLoder1();
        Class<?> demo = myClassLoder1.loadClass("com.itheima.Demo");
        Object o = demo.newInstance();
        System.out.println(o);
    }
}
```

#### 6) 类加载器的命名空间
**概念：**

- 每个类加载器都有各自的命名空间，命名空间由该加载器及所有父加载器所加载的类组成
- 在同一个命名空间中，不会出现全限定类名相同的两个Class对象
- 在不同的命名空间中，可以出现全限定类名相同的两个Class对象
- 父加载器加载的类对其子加载器可见，子加载器加载的类对其父加载器不可见
- 如果两个加载器之间没有直接或间接父子的关系，那么它们各自加载的类相互不可见

如何通过案例佐证？

**验证：**

我们可以自定义一个类加载器`MyClassLoader`去继承`ClassLoader`，重写父类的 `findClass`方法	

为什么要重写 `findClass`方法？

首先我们是使用双亲委派的方式 来实现类加载，`findClass`方法 在	调用 `ClassLoader`的 `loadClass`方法后，会在该方法体 调用 `findClass`方法，`findClass`方法是 从 自身加载器所在的 类路径下 加载 `class` 文件

我们先 用 `ClassLoader` 去加载 我们自定义的 `Student` 类，然后再去用自定义的 类加载器去 加载

Studeng类：

```java
/**
 * @Auther: fyp
 * @Date: 2022/3/3
 * @Description:
 * @Package: PACKAGE_NAME
 * @Version: 1.0
 */
public class Student{
    private Student student;
    //setter方法的参数是Object类型
    public void setStudent(Object object) {
        this.student = (Student) object;
    }
}
```

Demo演示：

```java
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

/**
 * @Auther: fyp
 * @Date: 2022/3/3
 * @Description:
 * @Package: PACKAGE_NAME
 * @Version: 1.0
 */
public class Demo {
   public static void main(String[] args) throws ClassNotFoundException, IllegalAccessException, InstantiationException, InvocationTargetException, NoSuchMethodException, InvocationTargetException {
      //通过线程获取线程上下文类加载器,默认是Application ClassLoader
      ClassLoader classLoader = Thread.currentThread().getContextClassLoader();
      /*第二种方式：使用 类.class.getClassLoader 获取 类加载器
     	 ClassLoader classLoader = Demo.class.getClassLoader();
      第三种方式：使用 Class.forName("全限定类名") 来获取类加载器
      	Class<?> sclass = Class.forName("Student");
      	ClassLoader classLoader = sclass.getClassLoader();
      */
      //使用类加载器加载两个Class对象
      Class c1 = classLoader.loadClass("Student");
      Class c2 = classLoader.loadClass("Student");
      System.out.println(c1 == c2);
      //使用两个Class对象分别创建一个新的实例(不使用强行转换)
      Object o1 = c1.newInstance();
      Object o2 = c2.newInstance();
      //获取setStudent方法
      Method m1 = c1.getMethod("setStudent",Object.class);
      //使用o1对象调用方法，o2对象作为参数传入
      m1.invoke(o1,o2);
      System.out.println(o1);
   }
}
```

输出：

```shell
true
Student@677327b6
```

c1 == c2 返回的结果是true，说明在堆内存中同一个类的Class对象有且仅有一个，第二条得以佐证

接下来通过自定义类加载器 来佐证第三条

自定义类加载器如下：

```java
class MyClassLoader extends ClassLoader{
   @Override
   protected Class<?> findClass(String name) throws ClassNotFoundException {
      String path = "e:\\myclasspath\\" + name + ".class";
      try {
         //创建byte数组输出流
         ByteArrayOutputStream os = new ByteArrayOutputStream();
         //把字节码文件复制到流中
         Files.copy(Paths.get(path),os);
         //得到字节数组
         byte[] bytes = os.toByteArray();
         //调用父类的defineClass方法加载类
         return defineClass(name,bytes,0,bytes.length);
      } catch (Exception e) {
         e.printStackTrace();
         throw new ClassNotFoundException("找不到类文件");
      }
   }
}
```

**注意：**

之前加载的 `Student`类的 `class`字节码文件 是 关联了 `Student`的 java类动态生成的，上面的 `path`路径下对应的 字节码文件暂时不管

Demo1演示：

```java
import java.io.ByteArrayOutputStream;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.nio.file.Files;
import java.nio.file.Paths;

/**
 * @Auther: fyp
 * @Date: 2022/3/3
 * @Description:
 * @Package: PACKAGE_NAME
 * @Version: 1.0
 */
public class Demo1 {
   public static void main(String[] args) throws ClassNotFoundException, IllegalAccessException, InstantiationException, InvocationTargetException, NoSuchMethodException, InvocationTargetException {
      //创建两个自定义类加载器的对线下
      MyClassLoader c1 = new MyClassLoader();
      MyClassLoader c2 = new MyClassLoader();
      //分别使用两个类加载器加载Student类
      Class s1 = c1.loadClass("Student");
      Class s2 = c2.loadClass("Student");
      System.out.println(s1 == s2);
      Object o1 = s1.newInstance();
      Object o2 = s2.newInstance();
      Method m1 = s1.getMethod("setStudent", Object.class);
      m1.invoke(o1,o2);
      System.out.println(o1);
   }
}
```

输出：

```shell
true
Student@14ae5a5
```

我们结合双亲委派来看两个Demo的输出，结果都为true

现在我想最大的疑问就是，为什么我把 类加载器 加载的 字节码 路径 转移到 `e:\\myclasspath\\`路径下，而且该路径没有 `Student`的字节码文件，为什么还可以 加载？

那是因为我们是通过 `ClassLoader`的双亲委派 来加载的 类加载器，它 加载的类有 一个机制，加载的优先顺序如下：

**自定义类加载器 < 应用类加载器 < 拓展类加载器 < 启动类加载器**

它会先去上级 加载 `Student`的 字节码文件，即从 启动类加载器 依次到 自定义类加载器，我们第一次执行程序时，就动态生成了`Student`字节码，我们 不妨 删除 之前的字节码文件

![image-20220303163045941](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220303163045941.png)

输出：

```shell\
Exception in thread "main" java.lang.ClassNotFoundException: 找不到类文件
	at MyClassLoader.findClass(Demo1.java:47)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	at Demo1.main(Demo1.java:20)
```

说明 我们 是去 加载了 之前的字节码文件，我们把之前的字节码文件 Student.class放到  `e:\\myclasspath\\`目录中去，再次执行

输出：

```shell
false
Exception in thread "main" java.lang.reflect.InvocationTargetException
...
Caused by: java.lang.ClassCastException: Student cannot be cast to Student
	at Student.setStudent(Student.java:13)
	... 5 more
```

`false`说明了 这两个类对象 是不同的

`Student cannot be cast to Student` 因为我们使用的是 两个 自定义的类加载器，而且两个 类加载后 不在 同一个 命名空间，为什么不在同一个命名空间，根据双亲委派的机制，只有上级的类加载器对下级的类加载器可见，所以之前的自定义类加载器 对 应用类加载器已加载的`Student.class`可见，后来删了该字节码文件，应用类加载器就不会去加载了，所以两个类相对来说是 不可见的，是两个不同的命名空间，而且两个命名空间可以存在相同全限定类名的对象，佐证了第3、4和5点。

![image-20220303165500230](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220303165500230.png)

#### 7) 即时编译

##### 分层编译

（TieredCompilation）
先来个例子  

```java
package cn.itcast.jvm.t3.jit;

public class JIT1 {

    // -XX:+PrintCompilation 指打印编译信息 -XX:-DoEscapeAnalysis 第二个指 关闭 C2 编译器的优化阶段
    public static void main(String[] args) {
        for (int i = 0; i < 200; i++) {
            long start = System.nanoTime();
            for (int j = 0; j < 1000; j++) {
                new Object();
            }
            long end = System.nanoTime();
            System.out.printf("%d\t%d\n",i,(end - start));
        }
    }
}
```

输出

```
0	247200
1	93200
2	112900
3	82400
4	86199
5	88200
6	92999
7	85199
8	90200
9	177200
10	92601
11	211300
12	228600
13	185600
14	162999
15	147199
16	122399
17	91799
18	85599
19	88999
20	211400
21	198900
22	90300
23	88500
24	251500
25	185801
26	83799
27	93700
28	96300
29	275501
30	213200
31	183200
32	91400
33	93301
34	135401
35	89900
36	89600
37	96899
38	90400
39	96501
40	127100
41	150199
42	91200
43	87600
44	84500
45	90801
46	89600
47	91700
48	84300
49	78300
50	91600
51	87200
52	87200
53	82300
54	86199
55	86200
56	86600
57	88201
58	91601
59	88000
60	115400
61	89901
62	84000
63	82700
64	109401
65	87300
66	74000
67	34499
68	38500
69	37900
70	30300
71	30700
72	30400
73	27899
74	27501
75	27899
76	30001
77	57100
78	32300
79	29800
80	25800
81	30299
82	30999
83	38001
84	26800
85	27900
86	32901
87	29900
88	25500
89	32099
90	39300
91	35800
92	26500
93	30601
94	37400
95	31600
96	59600
97	34101
98	31199
99	31300
100	31399
101	31399
102	90401
103	32400
104	33100
105	31400
106	30900
107	29001
108	31900
109	34600
110	37401
111	27201
112	32000
113	30500
114	31400
115	27501
116	32300
117	31200
118	36399
119	26001
120	30900
121	31000
122	34701
123	30700
124	28299
125	32500
126	32600
127	37601
128	31100
129	31400
130	31801
131	31400
132	32399
133	31800
134	30600
135	36101
136	30900
137	30901
138	25499
139	30201
140	31801
141	29999
142	28200
143	35000
144	31500
145	32000
146	33000
147	32400
148	29700
149	30200
150	29399
151	206900
152	70300
153	1500
154	1500
155	1499
156	1700
157	1399
158	1499
159	1401
160	1501
161	1499
162	1401
163	1700
164	1401
165	1400
166	1400
167	1500
168	1500
169	1600
170	1400
171	1800
172	1400
173	1501
174	1500
175	1600
176	1499
177	1500
178	1500
179	1601
180	1400
181	1399
182	1399
183	1500
184	1400
185	1500
186	1399
187	1500
188	1500
189	1499
190	1500
191	1501
192	1600
193	1500
194	1599
195	1499
196	1400
197	1600
198	1600
199	1599
```

原因是什么呢？

JVM 将执行状态分成了 5 个层次：

- 0 层，解释执行（Interpreter）
- 1 层，使用 C1 即时编译器编译执行（不带 profiling）
- 2 层，使用 C1 即时编译器编译执行（带基本的 profiling）
- 3 层，使用 C1 即时编译器编译执行（带完全的 profiling）
- 4 层，使用 C2 即时编译器编译执行  

>  profiling 是指在运行过程中收集一些程序执行状态的数据，例如【方法的调用次数】，【循环的
> 回边次数】等  

即时编译器（JIT）与解释器的区别

- 解释器是将字节码解释为机器码，下次即使遇到相同的字节码，仍会执行重复的解释
- JIT 是将一些字节码编译为机器码，并存入 Code Cache，下次遇到相同的代码，直接执行，无需再编译
- 解释器是将字节码解释为针对所有平台都通用的机器码
- JIT 会根据平台类型，生成平台特定的机器码  

对于占据大部分的不常用的代码，我们无需耗费时间将其编译成机器码，而是采取解释执行的方式运
行；另一方面，对于仅占据小部分的热点代码，我们则可以将其编译成机器码，以达到理想的运行速
度。 执行效率上简单比较一下 Interpreter < C1 < C2，总的目标是发现热点代码（hotspot名称的由
来），优化之  

刚才的一种优化手段称之为【逃逸分析】，发现新建的对象是否逃逸。可以使用 -XX:-
DoEscapeAnalysis 关闭逃逸分析，再运行刚才的示例观察结果  

参考资料：https://docs.oracle.com/en/java/javase/12/vm/java-hotspot-virtual-machineperformance-enhancements.html#GUID-D2E3DC58-D18B-4A6C-8167-4A1DFB4888E4  

##### 方法内联

（Inlining）  

```java
private static int square(final int i) {
	return i * i;
}
```

```java
System.out.println(square(9));
```

如果发现 square 是热点方法，并且长度不太长时，会进行内联，所谓的内联就是把方法内代码拷贝、
粘贴到调用者的位置：  

```java
System.out.println(9 * 9);
```

还能够进行常量折叠（constant folding）的优化  

```java
System.out.println(81);
```

实验

```java
public class JIT2 { 
  // -XX:+UnlockDiagnosticVMOptions -XX:+PrintInlining （解锁隐藏参数）打印 inlining 信息 
  // -XX:CompileCommand=dontinline,*JIT2.square 禁止某个方法 inlining 
  // -XX:+PrintCompilation 打印编译信息 
  public static void main(String[] args) { 
    int x = 0; 
    for (int i = 0; i < 500; i++) { 
      long start = System.nanoTime(); 
      for (int j = 0; j < 1000; j++) { 
        x = square(9); 
      }
      long end = System.nanoTime(); 
      System.out.printf("%d\t%d\t%d\n",i,x,(end - start)); 
    } 
  }
  
  private static int square(final int i) { 
    return i * i; 
  } 
}
```

##### 字段优化

JMH 基准测试请参考：http://openjdk.java.net/projects/code-tools/jmh/
创建 maven 工程，添加依赖如下  

```xml
<dependency>
    <groupId>org.openjdk.jmh</groupId>
    <artifactId>jmh-core</artifactId>
    <version>${jmh.version}</version>
</dependency>
<dependency>
    <groupId>org.openjdk.jmh</groupId>
    <artifactId>jmh-generator-annprocess</artifactId>
    <version>${jmh.version}</version>
    <scope>provided</scope>
</dependency>
```

编写基准测试代码：  

```java
package test;

import org.openjdk.jmh.annotations.*;
import org.openjdk.jmh.runner.Runner;
import org.openjdk.jmh.runner.RunnerException;
import org.openjdk.jmh.runner.options.Options;
import org.openjdk.jmh.runner.options.OptionsBuilder;

import java.util.Random;
import java.util.concurrent.ThreadLocalRandom;

@Warmup(iterations = 2, time = 1)
@Measurement(iterations = 5, time = 1)
@State(Scope.Benchmark)
public class Benchmark1 {

    int[] elements = randomInts(1_000);

    private static int[] randomInts(int size) {
        Random random = ThreadLocalRandom.current();
        int[] values = new int[size];
        for (int i = 0; i < size; i++) {
            values[i] = random.nextInt();
        }
        return values;
    }

    @Benchmark
    public void test1() {
        for (int i = 0; i < elements.length; i++) {
            doSum(elements[i]);
        }
    }

    @Benchmark
    public void test2() {
        int[] local = this.elements;
        for (int i = 0; i < local.length; i++) {
            doSum(local[i]);
        }
    }

    @Benchmark
    public void test3() {
        for (int element : elements) {
            doSum(element);
        }
    }

    static int sum = 0;

    @CompilerControl(CompilerControl.Mode.DONT_INLINE)
    static void doSum(int x) {
        sum += x;
    }


    public static void main(String[] args) throws RunnerException {
        Options opt = new OptionsBuilder()
                .include(Benchmark1.class.getSimpleName())
                .forks(1)
                .build();

        new Runner(opt).run();
    }
}
```

首先启用 doSum 的方法内联，测试结果如下（每秒吞吐量，分数越高的更好）：  

```
Benchmark Mode Samples Score Score error Units
t.Benchmark1.test1 thrpt 5 2420286.539 390747.467 ops/s
t.Benchmark1.test2 thrpt 5 2544313.594 91304.136 ops/s
t.Benchmark1.test3 thrpt 5 2469176.697 450570.647 ops/s
```

接下来禁用 doSum 方法内联  

```java
@CompilerControl(CompilerControl.Mode.DONT_INLINE) 
static void doSum(int x) { 
  sum += x; 
}
```

测试结果如下：  

```
Benchmark Mode Samples Score Score error Units
t.Benchmark1.test1 thrpt 5 296141.478 63649.220 ops/s
t.Benchmark1.test2 thrpt 5 371262.351 83890.984 ops/s
t.Benchmark1.test3 thrpt 5 368960.847 60163.391 ops/s
```

分析：

在刚才的示例中，doSum 方法是否内联会影响 elements 成员变量读取的优化：
如果 doSum 方法内联了，刚才的 test1 方法会被优化成下面的样子（伪代码）：  

```java
@Benchmark public void test1() { 
  // elements.length 首次读取会缓存起来 -> int[] local 
  for (int i = 0; i < elements.length; i++) { 
    // 后续 999 次 求长度 <- local 
    sum += elements[i]; 
    // 1000 次取下标 i 的元素 <- local 
  } 
}
```

可以节省 1999 次 Field 读取操作
但如果 doSum 方法没有内联，则不会进行上面的优化

练习：在内联情况下将 elements 添加 volatile 修饰符，观察测试结果  

#### 8) 反射优化

```java
package cn.itcast.jvm.t3.reflect; 
import java.io.IOException; 
import java.lang.reflect.InvocationTargetException; 
import java.lang.reflect.Method; 

public class Reflect1 { 
  public static void foo() { 
    System.out.println("foo..."); 
  }
  
  public static void main(String[] args) throws Exception { 
    Method foo = Reflect1.class.getMethod("foo"); 
    for (int i = 0; i <= 16; i++) { 
      System.out.printf("%d\t", i); 
      foo.invoke(null); 
    }
    System.in.read(); 
  } 
}
```

foo.invoke 前面 0 ~ 15 次调用使用的是 MethodAccessor 的 NativeMethodAccessorImpl 实现  

```java
package sun.reflect; 
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method; 
import sun.reflect.misc.ReflectUtil; 

class NativeMethodAccessorImpl extends MethodAccessorImpl { 
  private final Method method; 
  private DelegatingMethodAccessorImpl parent; 
  private int numInvocations; 
  
  NativeMethodAccessorImpl(Method method) { 
    this.method = method; 
  }
  
  public Object invoke(Object target, Object[] args) throws IllegalArgumentException, InvocationTargetException { 
    // inflationThreshold 膨胀阈值，默认 15 
    if (++this.numInvocations > ReflectionFactory.inflationThreshold() && !ReflectUtil.isVMAnonymousClass(this.method.getDeclaringClass())) { 
      // 使用 ASM 动态生成的新实现代替本地实现，速度较本地实现快 20 倍左右 
      MethodAccessorImpl generatedMethodAccessor = (MethodAccessorImpl) (new MethodAccessorGenerator()) 
        .generateMethod( 
        this.method.getDeclaringClass(),
        this.method.getName(),
        this.method.getParameterTypes(), 
        this.method.getReturnType(), 
        this.method.getExceptionTypes(), 
        this.method.getModifiers() ); 
      this.parent.setDelegate(generatedMethodAccessor); 
    }
    
    // 调用本地实现 
    return invoke0(this.method, target, args); 
  }
  
  void setParent(DelegatingMethodAccessorImpl parent) { 
    this.parent = parent; 
  }
  
  private static native Object invoke0(Method method, Object target, Object[] args); 
  
}
```

当调用到第 16 次（从0开始算）时，会采用运行时生成的类代替掉最初的实现，可以通过 debug 得到类名为 sun.reflect.GeneratedMethodAccessor1

可以使用阿里的 arthas 工具：  

```
java -jar arthas-boot.jar
[INFO] arthas-boot version: 3.1.1
[INFO] Found existing java process, please choose one and hit RETURN.
* [1]: 13065 cn.itcast.jvm.t3.reflect.Reflect1
```

选择 1 回车表示分析该进程  

```
[INFO] arthas home: C:\Users\fyp01\.arthas\lib\3.5.6\arthas
[INFO] Try to attach process 13012
[INFO] Attach process 13012 success.
[INFO] arthas-client connect 127.0.0.1 3658
  ,---.  ,------. ,--------.,--.  ,--.  ,---.   ,---.
 /  O  \ |  .--. ''--.  .--'|  '--'  | /  O  \ '   .-'
|  .-.  ||  '--'.'   |  |   |  .--.  ||  .-.  |`.  `-.
|  | |  ||  |\  \    |  |   |  |  |  ||  | |  |.-'    |
`--' `--'`--' '--'   `--'   `--'  `--'`--' `--'`-----'

wiki       https://arthas.aliyun.com/doc
tutorials  https://arthas.aliyun.com/doc/arthas-tutorials.html
version    3.5.6
main_class
pid        13012
time       2022-03-05 13:31:26
```

再输入【jad + 类名】来进行反编译  

```java
[arthas@13012]$ jad sun.reflect.GeneratedMethodAccessor1

ClassLoader:
+-sun.reflect.DelegatingClassLoader@1d44bcfa
  +-sun.misc.Launcher$AppClassLoader@18b4aac2
    +-sun.misc.Launcher$ExtClassLoader@5cad8086

Location:

/*
 * Decompiled with CFR.
 *
 * Could not load the following classes:
 *  cn.itcast.jvm.t3.reflect.Reflect1
 */
package sun.reflect;

import cn.itcast.jvm.t3.reflect.Reflect1;
import java.lang.reflect.InvocationTargetException;
import sun.reflect.MethodAccessorImpl;

public class GeneratedMethodAccessor1
extends MethodAccessorImpl {
    /*
     * Loose catch block
     */
    public Object invoke(Object object, Object[] objectArray) throws InvocationTargetException {
        // 比较奇葩的做法，如果有参数，那么抛非法参数异常
        block4: {
            if (objectArray == null || objectArray.length == 0) break block4;
            throw new IllegalArgumentException();
        }
        try {
            // 可以看到，已经是直接调用了
            Reflect1.foo();
            // 因为没有返回值
            return null;
        }
        catch (Throwable throwable) {
            throw new InvocationTargetException(throwable);
        }
        catch (ClassCastException | NullPointerException runtimeException) {
            throw new IllegalArgumentException(super.toString());
        }
    }
}

Affect(row-cnt:1) cost in 609 ms.
[arthas@13012]$
```

> **注意**
> 通过查看 ReflectionFactory 源码可知
>
> - sun.reflect.noInflation 可以用来禁用膨胀（直接生成 GeneratedMethodAccessor1，但首次生成比较耗时，如果仅反射调用一次，不划算）
> - sun.reflect.inflationThreshold 可以修改膨胀阈值  

# 五、内存模型

## 1. java内存模型

### 1) 原子性

原子性在学习线程时讲过，下面来个例子简单回顾一下：

问题提出，两个线程对初始值为 0 的静态变量一个做自增，一个做自减，各做 5000 次，结果是 0 吗？  

### 2) 问题分析

以上的结果可能是正数、负数、零。为什么呢？因为 Java 中对静态变量的自增，自减并不是原子操作。

例如对于 i++ 而言（i 为静态变量），实际会产生如下的 JVM 字节码指令  

```
getstatic i // 获取静态变量i的值
iconst_1 // 准备常量1
iadd // 加法
putstatic i // 将修改后的值存入静态变量i
```

而对应 i-- 也是类似：  

```
getstatic i // 获取静态变量i的值
iconst_1 // 准备常量1
isub // 减法
putstatic i // 将修改后的值存入静态变量
```

而Java的内存模型如下，完成静态变量的自增，自减需要在主存和工作内存中进行数据交换 ：

![image-20220305140454401](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220305140454401.png)

如果是单线程以上 8 行代码是顺序执行（不会交错）没有问题：  

```
// 假设i的初始值为0
getstatic i // 线程1-获取静态变量i的值 线程内i=0
iconst_1 // 线程1-准备常量1
iadd // 线程1-自增 线程内i=1
putstatic i // 线程1-将修改后的值存入静态变量i 静态变量i=1
getstatic i // 线程1-获取静态变量i的值 线程内i=1
iconst_1 // 线程1-准备常量1
isub // 线程1-自减 线程内i=0
putstatic i // 线程1-将修改后的值存入静态变量i 静态变量i=0
```

但多线程下这 8 行代码可能交错运行（为什么会交错？思考一下）： 

出现负数的情况：  

```
// 假设i的初始值为0
getstatic i // 线程1-获取静态变量i的值 线程内i=0
getstatic i // 线程2-获取静态变量i的值 线程内i=0
iconst_1 // 线程1-准备常量1
iadd // 线程1-自增 线程内i=1
putstatic i // 线程1-将修改后的值存入静态变量i 静态变量i=1
iconst_1 // 线程2-准备常量1
isub // 线程2-自减 线程内i=-1
putstatic i // 线程2-将修改后的值存入静态变量i 静态变量i=-1
```

出现正数的情况：  

```
/ 假设i的初始值为0
getstatic i // 线程1-获取静态变量i的值 线程内i=0
getstatic i // 线程2-获取静态变量i的值 线程内i=0
iconst_1 // 线程1-准备常量1
iadd // 线程1-自增 线程内i=1
iconst_1 // 线程2-准备常量1
isub // 线程2-自减 线程内i=-1
putstatic i // 线程2-将修改后的值存入静态变量i 静态变量i=-1
putstatic i // 线程1-将修改后的值存入静态变量i 静态变量i=1
```

### 3) 解决方法

语法  

```java
synchronized( 对象 ) {
	//要作为原子操作代码
}
```

用 `synchronized` 解决并发问题：  

```java
static int i = 0;
static Object obj = new Object();
public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            for (int j = 0; j < 5000; j++) {
                synchronized (obj) {
                    i++;
                }
            }
        });
        Thread t2 = new Thread(() -> {
            for (int j = 0; j < 5000; j++) {
                synchronized (obj) {
                    i--;
                }
            }
        });
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i);
}
```

如何理解呢：你可以把 obj 想象成一个房间，线程 t1，t2 想象成两个人。

当线程 t1 执行到 synchronized(obj) 时就好比 t1 进入了这个房间，并反手锁住了门，在门内执行count++ 代码。

这时候如果 t2 也运行到了 synchronized(obj) 时，它发现门被锁住了，只能在门外等待。

当 t1 执行完 synchronized{} 块内的代码，这时候才会解开门上的锁，从 obj 房间出来。t2 线程这时才可以进入 obj 房间，反锁住门，执行它的 count-- 代码。

> **注意：**上例中 t1 和 t2 线程必须用 synchronized 锁住同一个 obj 对象，如果 t1 锁住的是 m1 对
> 象，t2 锁住的是 m2 对象，就好比两个人分别进入了两个不同的房间，没法起到同步的效果。  

Java并发之线程八锁链接：https://blog.csdn.net/F15217283411/article/details/122752255?spm=1001.2014.3001.5501

## 2. 可见性

### 1) 退不出的循环

先来看一个现象，main 线程对 run 变量的修改对于 t 线程不可见，导致了 t 线程无法停止：  

```java
package cn.itcast.jvm.t4.avo;

public class Demo4_2 {

    static boolean run = true;

    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(()->{
            while(run){
                // ....
                //System.out.println(1);
            }
        });
        t.start();

        Thread.sleep(1000);
        run = false; // 线程t不会如预想的停下来
    }
}
```

为什么呢？分析一下：

1. 初始状态， t 线程刚开始从主内存读取了 run 的值到工作内存。  

![image-20220305143653158](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220305143653158.png)

2. 因为 t 线程要频繁从主内存中读取 run 的值，JIT 编译器会将 run 的值缓存至自己工作内存中的高速缓存中，减少对主存中 run 的访问，提高效率  

![image-20220305143725552](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220305143725552.png)

3. 1 秒之后，main 线程修改了 run 的值，并同步至主存，而 t 是从自己工作内存中的高速缓存中读取这个变量的值，结果永远是旧值  

![image-20220305143744621](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/image-20220305143744621.png)

### 2) 解决方法

volatile（易变关键字）

它可以用来修饰成员变量和静态成员变量，他可以避免线程从自己的工作缓存中查找变量的值，必须到主存中获取它的值，线程操作 volatile 变量都是直接操作主存  

### 3) 可见性

前面例子体现的实际就是可见性，它保证的是在多个线程之间，一个线程对 volatile 变量的修改对另一个线程可见， 不能保证原子性，仅用在一个写线程，多个读线程的情况： 上例从字节码理解是这样的：  

```
getstatic run // 线程 t 获取 run true
getstatic run // 线程 t 获取 run true
getstatic run // 线程 t 获取 run true
getstatic run // 线程 t 获取 run true
putstatic run // 线程 main 修改 run 为 false， 仅此一次
getstatic run // 线程 t 获取 run false
```

比较一下之前我们将线程安全时举的例子：两个线程一个 i++ 一个 i-- ，只能保证看到最新值，不能解决指令交错  

```
getstatic i // 线程1-获取静态变量i的值 线程内i=0
getstatic i // 线程2-获取静态变量i的值 线程内i=0
iconst_1 // 线程1-准备常量1
iadd // 线程1-自增 线程内i=1
putstatic i // 线程1-将修改后的值存入静态变量i 静态变量i=1
iconst_1 // 线程2-准备常量1
isub // 线程2-自减 线程内i=-1
putstatic i //线程2-将修改后的值存入静态变量i 静态变量i=-1
```

> **注意：** 
>
> - synchronized 语句块既可以保证代码块的原子性，也同时保证代码块内变量的可见性。但缺点是synchronized是属于重量级操作，性能相对更低
> - 如果在前面示例的死循环中加入 System.out.println() 会发现即使不加 volatile 修饰符，线程 t 也能正确看到对 run 变量的修改了，想一想为什么？  

## 3. 有序性

### 1) 诡异的结果

```java
int num = 0;
boolean ready = false;

// 线程1 执行此方法
public void actor1(I_Result r) {
	if(ready) {
		r.r1 = num + num;
	} else {
		r.r1 = 1;
	}
} 

// 线程2 执行此方法
public void actor2(I_Result r) {
	num = 2;
	ready = true;
}
```

I_Result 是一个对象，有一个属性 r1 用来保存结果，问，可能的结果有几种？

有同学这么分析

情况1：线程1 先执行，这时 ready = false，所以进入 else 分支结果为 1
情况2：线程2 先执行 num = 2，但没来得及执行 ready = true，线程1 执行，还是进入 else 分支，结果为1
情况3：线程2 执行到 ready = true，线程1 执行，这回进入 if 分支，结果为 4（因为 num 已经执行过了）

但我告诉你，结果还有可能是 0 ，信不信吧！  

相信很多人已经晕了 

这种现象叫做指令重排，是 JIT 编译器在运行时的一些优化，这个现象需要通过大量测试才能复现：

借助 java 并发压测工具 jcstress：https://hg.openjdk.java.net/code-tools/jcstress/

创建 maven 项目，提供如下测试类  

```java
package test;

import org.openjdk.jcstress.annotations.*;
import org.openjdk.jcstress.infra.results.I_Result;

//@JCStressTest
@Outcome(id = {"1", "4"}, expect = Expect.ACCEPTABLE, desc = "ok")
@Outcome(id = "0", expect = Expect.ACCEPTABLE_INTERESTING, desc = "!!!!")
@State
public class ConcurrencyTest {

    int num = 0;
    volatile boolean ready = false;
    @Actor
    public void actor1(I_Result r) {
        if(ready) {
            r.r1 = num + num;
        } else {
            r.r1 = 1;
        }
    }

    @Actor
    public void actor2(I_Result r) {
        num = 2;
        ready = true;
    }

}
```

执行  

```
mvn clean install
java -jar target/jcstress.jar
```

会输出我们感兴趣的结果，摘录其中一次结果：  

```
*** INTERESTING tests
	Some interesting behaviors observed. This is for the plain curiosity.

	2 matching test results.
	[OK] test.ConcurrencyTest
	(JVM args: [-XX:-TieredCompilation])
    Observed state 	Occurrences 				Expectation 	Interpretation
    			 0		  1,729      ACCEPTABLE_INTERESTING               !!!!
                 1 	 42,617,915                  ACCEPTABLE 				ok
				 4 	  5,146,627 			     ACCEPTABLE 				ok
	[OK] test.ConcurrencyTest
	(JVM args: [])
	Observed state 	Occurrences 				Expectation 	Interpretation
				 0		  1,652      ACCEPTABLE_INTERESTING               !!!!
				 0	 46,460,657      			 ACCEPTABLE               !!!!
				 0	  4,571,072      			 ACCEPTABLE               !!!!
```

可以看到，出现结果为 0 的情况有 638 次，虽然次数相对很少，但毕竟是出现了。  

### 2) 解决方法

volatile 修饰的变量，可以禁用指令重排  

```java
package test;

import org.openjdk.jcstress.annotations.*;
import org.openjdk.jcstress.infra.results.I_Result;

//@JCStressTest
@Outcome(id = {"1", "4"}, expect = Expect.ACCEPTABLE, desc = "ok")
@Outcome(id = "0", expect = Expect.ACCEPTABLE_INTERESTING, desc = "!!!!")
@State
public class ConcurrencyTest {

    int num = 0;
    boolean ready = false;
    @Actor
    public void actor1(I_Result r) {
        if(ready) {
            r.r1 = num + num;
        } else {
            r.r1 = 1;
        }
    }

    @Actor
    public void actor2(I_Result r) {
        num = 2;
        ready = true;
    }

}
```

结果为：  

```
*** INTERESTING tests
Some interesting behaviors observed. This is for the plain curiosity.
0 matching test results.
```

### 3) 有序性理解

JVM 会在不影响正确性的前提下，可以调整语句的执行顺序，思考下面一段代码

```java
static int i;
static int j;

// 在某个线程内执行如下赋值操作
i = ...; // 较为耗时的操作
j = ...;  
```

可以看到，至于是先执行 i 还是 先执行 j ，对最终的结果不会产生影响。所以，上面代码真正执行时，
既可以是

```java
i = ...; // 较为耗时的操作
j = ...
```

也可以是

```java
j = ...;
i = ...; // 较为耗时的操作
```

这种特性称之为『指令重排』，多线程下『指令重排』会影响正确性，例如著名的 double-checked locking 模式实现单例

```java
public final class Singleton {
    private Singleton() { }
    private static Singleton INSTANCE = null;
    public static Singleton getInstance() {
        // 实例没创建，才会进入内部的 synchronized代码块
        if (INSTANCE == null) {
            synchronized (Singleton.class) {
                // 也许有其它线程已经创建实例，所以再判断一次
                if (INSTANCE == null) {
                    INSTANCE = new Singleton();
                }
            }
        } 
        return INSTANCE;
    }
}
```

以上的实现特点是：

- 懒惰实例化
- 首次使用 getInstance() 才使用 synchronized 加锁，后续使用时无需加锁

但在多线程环境下，上面的代码是有问题的， `INSTANCE = new Singleton()` 对应的字节码为：

```
0: new #2 // class cn/itcast/jvm/t4/Singleton
3: dup
4: invokespecial #3 // Method "<init>":()V
7: putstatic #4 // Field
INSTANCE:Lcn/itcast/jvm/t4/Singleton;
```

其中 4 7 两步的顺序不是固定的，也许 jvm 会优化为：先将引用地址赋值给 INSTANCE 变量后，再执行构造方法，如果两个线程 t1，t2 按如下时间序列执行：

```
时间1 t1 线程执行到 INSTANCE = new Singleton();
时间2 t1 线程分配空间，为Singleton对象生成了引用地址（0 处）
时间3 t1 线程将引用地址赋值给 INSTANCE，这时 INSTANCE != null（7 处）
时间4 t2 线程进入getInstance() 方法，发现 INSTANCE != null（synchronized块外），直接
返回 INSTANCE
时间5 t1 线程执行Singleton的构造方法（4 处）
```

这时 t1 还未完全将构造方法执行完毕，如果在构造方法中要执行很多初始化操作，那么 t2 拿到的是将是一个未初始化完毕的单例

对 INSTANCE 使用 volatile 修饰即可，可以禁用指令重排，但要注意在 JDK 5 以上的版本的 volatile 才会真正有效  

### 4) happens-before

happens-before 规定了哪些写操作对其它线程的读操作可见，它是可见性与有序性的一套规则总结，抛开以下 happens-before 规则，JMM 并不能保证一个线程对共享变量的写，对于其它线程对该共享变量的读可见

- 线程解锁 m 之前对变量的写，对于接下来对 m 加锁的其它线程对该变量的读可见  

```java
static int x;
static Object m = new Object();

new Thread(()->{
    	synchronized(m) {
    x = 10;
    }
},"t1").start();

new Thread(()->{
    synchronized(m) {
    	System.out.println(x);
    }
},"t2").start();
```

- 线程对 volatile 变量的写，对接下来其它线程对该变量的读可见  

```java
volatile static int x;

new Thread(()->{
    x = 10;
},"t1").start();

new Thread(()->{
	System.out.println(x);
},"t2").start();
```

- 线程 start 前对变量的写，对该线程开始后对该变量的读可见

```java
static int x;

x = 10;

new Thread(()->{
	System.out.println(x);
},"t2").start();
```

- 线程结束前对变量的写，对其它线程得知它结束后的读可见（比如其它线程调用 t1.isAlive() 或t1.join()等待它结束）  

```java
static int x;

Thread t1 = new Thread(()->{
	x = 10;
},"t1");
t1.start();
t1.join();
System.out.println(x);
```

- 线程 t1 打断 t2（interrupt）前对变量的写，对于其他线程得知 t2 被打断后对变量的读可见（通过t2.interrupted 或 t2.isInterrupted）  

```java
static int x;
public static void main(String[] args) {
    Thread t2 = new Thread(()->{
        while(true) {
            if(Thread.currentThread().isInterrupted()) {
                System.out.println(x);
                break;
            }
        }
    },"t2");
    t2.start();
    new Thread(()->{
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } 
        x = 10;
        t2.interrupt();
    },"t1").start();
    while(!t2.isInterrupted()) {
        Thread.yield();
    }
    System.out.println(x);
}
```

- 对变量默认值（0，false，null）的写，对其它线程对该变量的读可见  
- 具有传递性，如果 x hb-> y 并且 y hb-> z 那么有 x hb-> z  

> 变量都是指成员变量或静态成员变量  

## 4. CAS 与 原子类

### CAS

CAS 即 `Compare` and Swap ，它体现的一种乐观锁的思想，比如多个线程要对一个共享的整型变量执行 +1 操作：  

```java
// 需要不断尝试
while(true) {
    int 旧值 = 共享变量 ; // 比如拿到了当前值 0
    int 结果 = 旧值 + 1; // 在旧值 0 的基础上增加 1 ，正确结果是 1

    /*
    这时候如果别的线程把共享变量改成了 5，本线程的正确结果 1 就作废了，这时候
    compareAndSwap 返回 false，重新尝试，直到：
    compareAndSwap 返回 true，表示我本线程做修改的同时，别的线程没有干扰
    */
    if( compareAndSwap ( 旧值, 结果 )) {
        // 成功，退出循环
    }
}
```

获取共享变量时，为了保证该变量的可见性，需要使用 volatile 修饰。结合 CAS 和 volatile 可以实现无锁并发，适用于竞争不激烈、多核 CPU 的场景下。

- 因为没有使用 synchronized，所以线程不会陷入阻塞，这是效率提升的因素之一
- 但如果竞争激烈，可以想到重试必然频繁发生，反而效率会受影响

CAS 底层依赖于一个 Unsafe 类来直接调用操作系统底层的 CAS 指令，下面是直接使用 Unsafe 对象进行线程安全保护的一个例子  

```java
import sun.misc.Unsafe;
import java.lang.reflect.Field;
public class TestCAS {
    public static void main(String[] args) throws InterruptedException {
        DataContainer dc = new DataContainer();
        int count = 5;
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < count; i++) {
                dc.increase();
            }
        });
        t1.start();
        t1.join();
        System.out.println(dc.getData());
    }
}

class DataContainer {
    private volatile int data;
    static final Unsafe unsafe;
    static final long DATA_OFFSET;
    static {
        try {
            // Unsafe 对象不能直接调用，只能通过反射获得
            Field theUnsafe = Unsafe.class.getDeclaredField("theUnsafe");
            theUnsafe.setAccessible(true);
            unsafe = (Unsafe) theUnsafe.get(null);
        } catch (NoSuchFieldException | IllegalAccessException e) {
            throw new Error(e);
        } try {
            // data 属性在 DataContainer 对象中的偏移量，用于 Unsafe 直接访问该属性
            DATA_OFFSET = unsafe.objectFieldOffset(DataContainer.class.getDeclaredField("data"));
        } catch (NoSuchFieldException e) {
            throw new Error(e);
        }
    }
    public void increase() {
        int oldValue;
        while(true) {
            // 获取共享变量旧值，可以在这一行加入断点，修改 data 调试来加深理解
            oldValue = data;
            // cas 尝试修改 data 为 旧值 + 1，如果期间旧值被别的线程改了，返回 false
            if (unsafe.compareAndSwapInt(this, DATA_OFFSET, oldValue, oldValue + 1)) {
                return;
            }
        }
    }
    public void decrease() {
        int oldValue;
        while(true) {
            oldValue = data;
            if (unsafe.compareAndSwapInt(this, DATA_OFFSET, oldValue, oldValue - 1)) {
                return;
            }
        }
    }
    synchronized public int getData() {
        return data;
    }
}
```

### 乐观锁与悲观锁

- CAS 是基于乐观锁的思想：最乐观的估计，不怕别的线程来修改共享变量，就算改了也没关系，我吃亏点再重试呗。
- synchronized 是基于悲观锁的思想：最悲观的估计，得防着其它线程来修改共享变量，我上了锁你们都别想改，我改完了解开锁，你们才有机会。  

### 原子操作类

juc（java.util.concurrent）中提供了原子操作类，可以提供线程安全的操作，例如：AtomicInteger、AtomicBoolean等，它们底层就是采用 CAS 技术 + volatile 来实现的。

可以使用 AtomicInteger 改写之前的例子：  

```java
// 创建原子整数对象
private static AtomicInteger i = new AtomicInteger(0);
public static void main(String[] args) throws InterruptedException {
    Thread t1 = new Thread(() -> {
        for (int j = 0; j < 5000; j++) {
            i.getAndIncrement(); // 获取并且自增 i++
			// i.incrementAndGet(); // 自增并且获取 ++i
        }
    });
    Thread t2 = new Thread(() -> {
        for (int j = 0; j < 5000; j++) {
            i.getAndDecrement(); // 获取并且自减 i--
        }
    });
    t1.start();
    t2.start();
    t1.join();
    t2.join();
    System.out.println(i);
}
```

## 5. synchronized 优化

Java HotSpot 虚拟机中，每个对象都有对象头（包括 class 指针和 Mark Word）。Mark Word 平时存储这个对象的 `哈希码` 、 `分代年龄` ，当加锁时，这些信息就根据情况被替换为 `标记位` 、 `线程锁记录指针` 、 `重量级锁指针` 、 `线程ID` 等内容  

### 轻量级锁

如果一个对象虽然有多线程访问，但多线程访问的时间是错开的（也就是没有竞争），那么可以使用轻
量级锁来优化。这就好比：

学生（线程 A）用课本占座，上了半节课，出门了（CPU时间到），回来一看，发现课本没变，说明没
有竞争，继续上他的课。 如果这期间有其它学生（线程 B）来了，会告知（线程A）有并发访问，线程
A 随即升级为重量级锁，进入重量级锁的流程。

而重量级锁就不是那么用课本占座那么简单了，可以想象线程 A 走之前，把座位用一个铁栅栏围起来
假设有两个方法同步块，利用同一个对象加锁  

```java
static Object obj = new Object();
public static void method1() {
    synchronized( obj ) {
        // 同步块 A
        method2();
    }
} 
public static void method2() {
    synchronized( obj ) {
   		// 同步块 B
    }
}
```

每个线程都的栈帧都会包含一个锁记录的结构，内部可以存储锁定对象的 Mark Word  

| 线程 1                                       | 对象 Mark Word                | 线程 2                                       |
| -------------------------------------------- | ----------------------------- | -------------------------------------------- |
| 访问同步块 A，把 Mark 复制到 线程 1 的锁记录 | 01（无锁）                    | -                                            |
| CAS 修改 Mark 为线程 1 锁记录 地址           | 01（无锁）                    | -                                            |
| 成功（加锁）                                 | 00（轻量锁）线程 1 锁记录地址 | -                                            |
| 执行同步块 A                                 | 00（轻量锁）线程 1 锁记录地址 | -                                            |
| 访问同步块 B，把 Mark 复制到 线程 1 的锁记录 | 00（轻量锁）线程 1 锁记录地址 | -                                            |
| CAS 修改 Mark 为线程 1 锁记录 地址           | 00（轻量锁）线程 1 锁记录地址 | -                                            |
| 失败（发现是自己的锁）                       | 00（轻量锁）线程 1 锁记录地址 | -                                            |
| 锁重入                                       | 00（轻量锁）线程 1 锁记录地址 | -                                            |
| 执行同步块 B                                 | 00（轻量锁）线程 1 锁记录地址 | -                                            |
| 同步块 B 执行完毕                            | 00（轻量锁）线程 1 锁记录地址 | -                                            |
| 同步块 A 执行完毕                            | 00（轻量锁）线程 1 锁记录地址 | -                                            |
| 成功（解锁）                                 | 01（无锁）                    | -                                            |
| -                                            | 01（无锁）                    | 访问同步块 A，把 Mark 复制到 线程 2 的锁记录 |
| -                                            | 01（无锁）                    | CAS 修改 Mark 为线程 2 锁记录 地址           |
| -                                            | 00（轻量锁）线程 2 锁记录地址 | 成功（加锁）                                 |
| -                                            | ...                           | ...                                          |

### 锁膨胀

如果在尝试加轻量级锁的过程中，CAS 操作无法成功，这时一种情况就是有其它线程为此对象加上了轻量级锁（有竞争），这时需要进行锁膨胀，将轻量级锁变为重量级锁。  

```java
static Object obj = new Object();
public static void method1() {
	synchronized( obj ) {
	// 同步块
	}
}
```

| 线程 1                                    | 对象 Mark                      | 线程 2                             |
| ----------------------------------------- | ------------------------------ | ---------------------------------- |
| 访问同步块，把 Mark 复制到线程 1 的锁记录 | 01（无锁）                     | -                                  |
| CAS 修改 Mark 为线程 1 锁记录地 址        | 01（无锁）                     | -                                  |
| 成功（加锁）                              | 00（轻量锁）线程 1 锁 记录地址 | -                                  |
| 执行同步块                                | 00（轻量锁）线程 1 锁 记录地址 | -                                  |
| 执行同步块                                | 00（轻量锁）线程 1 锁 记录地址 | 访问同步块，把 Mark 复制 到线程 2  |
| 执行同步块                                | 00（轻量锁）线程 1 锁 记录地址 | CAS 修改 Mark 为线程 2 锁 记录地址 |
| 执行同步块                                | 00（轻量锁）线程 1 锁 记录地址 | 失败（发现别人已经占了 锁）        |
| 执行同步块                                | 00（轻量锁）线程 1 锁 记录地址 | CAS 修改 Mark 为重量锁             |
| 执行同步块                                | 10（重量锁）重量锁指 针        | 阻塞中                             |
| 执行完毕                                  | 10（重量锁）重量锁指 针        | 阻塞中                             |
| 失败（解锁）                              | 10（重量锁）重量锁指 针        | 阻塞中                             |
| 释放重量锁，唤起阻塞线程竞争              | 01（无锁）                     | 阻塞中                             |
| -                                         | 10（重量锁）                   | 竞争重量锁                         |
| -                                         | 10（重量锁）                   | 成功（加锁）                       |
| -                                         | ...                            | ...                                |

### 重量级锁

重量级锁竞争的时候，还可以使用自旋来进行优化，如果当前线程自旋成功（即这时候持锁线程已经退出了同步块，释放了锁），这时当前线程就可以避免阻塞。

在 Java 6 之后自旋锁是自适应的，比如对象刚刚的一次自旋操作成功过，那么认为这次自旋成功的可能性会高，就多自旋几次；反之，就少自旋甚至不自旋，总之，比较智能。  

自旋会占用 CPU 时间，单核 CPU 自旋就是浪费，多核 CPU 自旋才能发挥优势。

好比等红灯时汽车是不是熄火，不熄火相当于自旋（等待时间短了划算），熄火了相当于阻塞（等待时间长了划算）

- Java 7 之后不能控制是否开启自旋功能

自旋重试成功的情况  

| 线程 1 （cpu 1 上）      | 对象 Mark              | 线程 2 （cpu 2 上）      |
| ------------------------ | ---------------------- | ------------------------ |
| -                        | 10（重量锁）           | -                        |
| 访问同步块，获取 monitor | 10（重量锁）重量锁指针 | -                        |
| 成功（加锁）             | 10（重量锁）重量锁指针 | -                        |
| 执行同步块               | 10（重量锁）重量锁指针 | -                        |
| 执行同步块               | 10（重量锁）重量锁指针 | 访问同步块，获取 monitor |
| 执行同步块               | 10（重量锁）重量锁指针 | 自旋重试                 |
| 执行完毕                 | 10（重量锁）重量锁指针 | 自旋重试                 |
| 成功（解锁）             | 01（无锁）             | 自旋重试                 |
| -                        | 10（重量锁）重量锁指针 | 成功（加锁）             |
| -                        | 10（重量锁）重量锁指针 | 执行同步块               |
| -                        | ...                    | ...                      |

自旋重试失败的情况  

| 线程 1（cpu 1 上）       | 对象 Mark              | 线程 2（cpu 2 上）       |
| ------------------------ | ---------------------- | ------------------------ |
| -                        | 10（重量锁）           | -                        |
| 访问同步块，获取 monitor | 10（重量锁）重量锁指针 | -                        |
| 成功（加锁）             | 10（重量锁）重量锁指针 | -                        |
| 执行同步块               | 10（重量锁）重量锁指针 | -                        |
| 执行同步块               | 10（重量锁）重量锁指针 | 访问同步块，获取 monitor |
| 执行同步块               | 10（重量锁）重量锁指针 | 自旋重试                 |
| 执行同步块               | 10（重量锁）重量锁指针 | 自旋重试                 |
| 执行同步块               | 10（重量锁）重量锁指针 | 自旋重试                 |
| 执行同步块               | 10（重量锁）重量锁指针 | 阻塞                     |
| -                        | ...                    | ...                      |

### 偏向锁

量级锁在没有竞争时（就自己这个线程），每次重入仍然需要执行 CAS 操作。Java 6 中引入了偏向锁来做进一步优化：只有第一次使用 CAS 将线程 ID 设置到对象的 Mark Word 头，之后发现这个线程 ID是自己的就表示没有竞争，不用重新 CAS.

- 撤销偏向需要将持锁线程升级为轻量级锁，这个过程中所有线程需要暂停（STW）
- 访问对象的 hashCode 也会撤销偏向锁
- 如果对象虽然被多个线程访问，但没有竞争，这时偏向了线程 T1 的对象仍有机会重新偏向 T2，
- 重偏向会重置对象的 Thread ID
- 撤销偏向和重偏向都是批量进行的，以类为单位
- 如果撤销偏向到达某个阈值，整个类的所有对象都会变为不可偏向的
- 可以主动使用 -XX:-UseBiasedLocking 禁用偏向锁

可以参考这篇论文：https://www.oracle.com/technetwork/java/biasedlocking-oopsla2006-wp-149958.pdf

假设有两个方法同步块，利用同一个对象加锁  

```java
static Object obj = new Object();
public static void method1() {
    synchronized( obj ) {
        // 同步块 A
        method2();
    }
} 

public static void method2() {
    synchronized( obj ) {
    	// 同步块 B
    }
}
```

| 线程 1                                      | 对象 Mark                      |
| ------------------------------------------- | ------------------------------ |
| 访问同步块 A，检查 Mark 中是否有线程 ID     | 101（无锁可偏向）              |
| 尝试加偏向锁                                | 101（无锁可偏向）对象 hashCode |
| 成功                                        | 101（无锁可偏向）线程ID        |
| 执行同步块 A                                | 101（无锁可偏向）线程ID        |
| 访问同步块 B，检查 Mark 中是否有线程 ID     | 101（无锁可偏向）线程ID        |
| 是自己的线程 ID，锁是自己的，无需做更多操作 | 101（无锁可偏向）线程ID        |
| 执行同步块 B                                | 101（无锁可偏向）线程ID        |
| 执行完毕                                    | 101（无锁可偏向）对象 hashCode |

### 其他优化

### 1) 减少上锁时间

同步代码块中尽量短  （有针对性的保护）

### 2) 减少锁的粒度

将一个锁拆分为多个锁提高并发度，例如：

- ConcurrentHashMap
- LongAdder 分为 base 和 cells 两部分。没有并发争用的时候或者是 cells 数组正在初始化的时候，会使用 CAS 来累加值到 base，有并发争用，会初始化 cells 数组，数组有多少个 cell，就允许有多少线程并行修改，最后将数组中每个 cell 累加，再加上 base 就是最终的值
- LinkedBlockingQueue 入队和出队使用不同的锁，相对于LinkedBlockingArray只有一个锁效率要高  

### 3) 锁粗化

多次循环进入同步块不如同步块内多次循环 另外 JVM 可能会做如下优化，把多次 append 的加锁操作粗化为一次（因为都是对同一个对象加锁，没必要重入多次）  

```java
new StringBuffer().append("a").append("b").append("c");
```

### 4) 锁消除

JVM 会进行代码的逃逸分析，例如某个加锁对象是方法内局部变量，不会被其它线程所访问到，这时候就会被即时编译器忽略掉所有同步操作。  

### 5) 读写分离

CopyOnWriteArrayList 

ConyOnWriteSet

参考：
[Oralce官方 Synchronization](https://wiki.openjdk.java.net/display/HotSpot/Synchronization)

[java锁优化/](http://luojinping.com/2015/07/09/java%E9%94%81%E4%BC%98%E5%8C%96/)

[聊聊并发（二）——Java SE1.6 中的 Synchronized](https://www.infoq.cn/article/java-se-16-synchronized)

[Java锁优化--JVM锁降级](https://www.jianshu.com/p/9932047a89be)

[从jvm的角度来看java的多线程](https://www.cnblogs.com/sheeva/p/6366782.html)

[Java 是否曾经重新调整单个锁](https://stackoverflow.com/questions/46312817/does-java-ever-rebias-an-individual-lock  )

