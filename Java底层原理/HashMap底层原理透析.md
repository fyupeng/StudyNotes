> 创作宗旨：化繁为简，绝不冗余，点到为止

直接开门见山，就事论事！

> 什么是 HashMap?

`HashMap`类继承了父类`AbstractMap<K,V>`，实现了接口`Map<K,V>, Cloneable, Serializable`,`AbstractMap<K,V>`实现了对`Map`的基本操作，`Serializable`是序列化接口，可实现序列化，`Map<K,V>`接口规范了`k-v`对操作的抽象方法，`Cloneable`规范了继承了`Object`后去使用`clone()`方法前必须得实现`Cloneable`接口，否则抛出`CloneNotSupportedException`错误。

## 一、介绍
### 1. HashMap 的线程不安全的场景

- 死锁

扩容产生死锁的原因关键有两点：
第一点：链表的头插法转移结点，`JDK8`版本就改成了尾插法；
第二点：多线程环境下，`CPU`的让出使得头结点的指针指向发生变化，但原线程重新占有`CPU`时仍然采取原先的做法；
**解读：**`JDK8`下使用尾插法，就算头结点已经转移了，但还是在头结点位置，也就是转移前后链表顺序不改变，只是多了一次重复的操作，结果不影响。
我不会用篇幅很长而做这么点事情，没必要，很简单的事 5 幅图就可以啦。
由于`HashMap`是`数组`+`链表`的数据结构，旧桶是指数组下标所在位置。
`线程 1` 扩容操作
![image.png](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/1658888533494624.png)

`线程 1` 转移结点`A`
![image.png](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/1658888733985797.png)
`线程1`结点转移完毕
![image.png](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/1658888829949728.png)
`线程2`开始转移结点`A`
因为是`线程2`首先要扩容的，不过`CPU`时间却让给了`线程2`,`线程2`也发觉需要扩容，等扩容完毕后再把`CPU`还给`线程1`
![image.png](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/1658888880999037.png)

`线程2`转移结点`A`结束，环形链产生，也就是产生死锁。
![image.png](https://yupeng-tuchuang.oss-cn-shenzhen.aliyuncs.com/1658889083366975.png)

所以原因的`头插法`得以佐证，`CPU`的轮询切换也演绎的很到位。
线程不安全讲完，下面就将原理，去潜移默化它。
### 2. HashMap 实现哈希命中均匀
哈希也就是`k`的`hashcode`计算后得到的下标，而`hashcode`是一个`32`位的`int`类型，要让分布均匀，即让不同的k不能出现有相同的效果，也就是得有唯一性，就要完全去利用高`16`位和低`16`位，而如果直接用`32`位去取模运算，高`16`就可能会用不到，导致`hashcode`高`16`位不同的`k`依旧命中同一个桶位。
所以JDK8采取的做法是对高`16`位与低`16`位进行异或运算，这样就变相保留了高位和低位的所有特征了。
哈希均匀，`JDK8`之后
```java
return (key == null) ? h = key.hashCode() ^ (h >>> 16); 
```
取模运算，`JDK7`之前
```java
h & (length - 1)
```
### 3. HashMap 参数以及扩容机制
初始容量`16`，达到`阈值`扩容，`阈值`等于`最大容量` * `负载因子`，扩容每次`2`倍，总是`2`的`n`次方。

- 扩容机制
使用一个容量更大的数组来代替已有的容量小的数组，`transfer()`方法将原有`Entry`数组元素拷贝到新的`Entry`数组里，`Java1.7`重新计算每个元素在数组中的位置。
而`Java1.8`它不需要重新计算每个元素，它会先比较元素的`hashcode & 原来数组的大小`的结果，如果为`0`，就以元素的原下标加入到新数组中，如果为`1`，就在原下标加上原数组的大小加入到新数组中，这样这两种情况就命中到新数组的`前一半`和`后一半`了，也就是将原本全在原数组大小的所有元素又均匀分布一些元素到`扩容`未使用的下标中。
那么就解释一下，假设加入的元素是随机的，也就是所有情况的`hash`值是均等出现的，而`&`原数组大小可以这样比如，有`64`个元素均等的从`0`到`63`，那么`0`到`15`分配给了扩容后数组大小为`32`的左半部分，`16-31`分配给了右半部分，`32-48`分配给了左半部分，`48-63`分配给了右半部分，就比较均匀分散在了两侧，而且机会均等，很巧妙！
```java
// 分布到左侧
if ((e.hash & oldCap) == 0) {
    if (loTail == null)
        loHead = e;
    else
        loTail.next = e;
    loTail = e;
}
// 分布到右侧
else {
    if (hiTail == null)
	// 初始化
        hiHead = e;
    else
	// 尾插法
        hiTail.next = e;
    // 自增，下次尾插法必要的变量
    hiTail = e;
}
```
### 4. HashMap 的 get 和 put 操作
- get
> 让我们见识下此时无图胜有图的效果吧！

`get`由源码解读是先获取`key`的`hashCode`，然后定位到对应的数组位置，再去遍历该元素处的链表
在这里才讲`equals`我觉得是最合适的，可以区分它与`hashCode`的区别，如果`hashCode`相同的两个元素在同一个数组下标，`equals`就开始发挥作用了，
不得不说，当然别说我是话痨，我觉得`HashMap`加入这两个的初衷是如果在一个庞大的`Map`中，要去`get`某一个`key`的`value`，有一种最直接的做法，就是直接整个`Map`去`equals`比较，这没错，而且还是有用的，但加入`hashCode`之后，`hashCode`能够给`equals`先筛选一番，因为不同的`key`，它的`hash`值一般是不同的，理解这里的一般，所以`hash`值不同的`equals`肯定就不同，就没必要再去对不同下标元素来`equals`比较了，好理解吧！
由于`HashMap`中新加入了红黑树，就多了一种`get`到`key`的方法，是一种二分法，对于大量数据的链表是不推荐的，所以采用红黑树（总是平衡的二叉树）更加地高效！

- put

`put`的实现主要针对两种`key`，一种是不存在的`key`，一种是已存在的`key`
**局部源码**
```java
public V put(K key, V value) {
    //采用hash(key)来计算key的hashCode值。
    return putVal(hash(key), key, value, false, true);
}
```
```java
static final int hash(Object key) {
    int h;
    //当key等于null的时候，不走hashCode()方法
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

如果为前者，而且`key`值为`null`，就在数组下标为`0`的链表中去寻找`key`为`null`的值，当然找不到，就创建`key`为`null`的结点。
如果为前者，而且`key`值不为`null`，就调用该元素hash计算具体的下标，在该下标的链表中寻找，当然也寻找不到，就尾插法加入该`k-v`结点。
如果为后者，即找到了，直接赋值就可以了，不需要考虑是否为`null`。


这里要分开来`put`是因为，在`HashTable`上，是不允许有`key`为`null`的情况出现，因为当`key`为`null`时，`key.hashCode()`会报空指针异常。
所以在`HashMap`做了特别的处理，对`key`为`null`做了单独处理，这样就解决了`key`为`null`的情况。
还有一点，`HashMap`的`value`如果设置为`null`，虽然你`put`了，但再去取`key`的时候，依旧是不存在！


## 二、附语

不足之处请大佬予以佐证，不希望哪个地方出现的错误误导同行，结果闹出笑话来，总之你们的阅读和评论是对作者最大的支持！
谢谢大家，我会继续努力，只为力争创作高质量的文章，分享给各位有需要的读者。
我的技术专栏：[https://github.com/fyupeng](https://github.com/fyupeng)
>专注品质，热爱生活。
>交流技术，寻求同志。