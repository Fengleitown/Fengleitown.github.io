# 基础篇

> ***注重：算法、数据结构、常见的设计模式***

## 算法部分

- 掌握常见排序算法（二分、冒泡、快排、选择、插入等）
- 手写冒泡、快排的代码
- 了解各个排序算法的特性，如时间复杂度、是否稳定

------



### 1.二分查找

**要求**

* 能够用自己语言描述二分查找算法

    - 答：`二分查找就是用中间值的数与目标数做对比，如果计算出目标数比中间数大，则说明目标数在中间数到最右端之间，反之则小。`
    - 算法思想`可见最左端和最右端是两个变量，需要通过这两个变量来控制整个算法进行，正常情况则是左端一定要小于等于右端才不断的进行循环，反正算法结束。`
* 能够手写二分查找代码
* 快速解答二分查找的选择题
* 能够解答一些变化后的考法



**算法描述**

1. 前提：有已排序数组 A（假设已经做好）

2. 定义左边界 L、右边界 R，确定搜索范围，循环执行二分查找（3、4两步）

3. 获取中间索引 M = Floor((L+R) /2)  `Floor是向下取整的意思`

4. 中间索引的值  A[M] 与待搜索的值 T 进行比较

   ① A[M] == T 表示找到，返回中间索引

   ② A[M] > T，中间值右侧的其它元素都大于 T，无需比较，中间索引左边去找，M - 1 设置为右边界，重新查找

   ③ A[M] < T，中间值左侧的其它元素都小于 T，无需比较，中间索引右边去找， M + 1 设置为左边界，重新查找

5. 当 L => R 时，表示没有找到，应结束循环

*[更形象的图示演示请参考 👉 演示动画](docs/md/Serious-eight-part-essay/JAVA/binary_search.html)*

**算法实现和测试：**（以jdk里的Arrays.binarySearch()方法来实现）

```java
public class BinarySearch {
    // 测试
    public static void main(String[] args) {
        BinarySearch binarySearch = new BinarySearch();
        int[] array = {1, 12, 23, 54, 55, 66, 67, 80, 99};
        int temp = 55;
        String shuo = binarySearch.binarySearch(array,temp);
        System.out.println(shuo);
    }
    // 算法实现
    public String binarySearch(int [] array,int temp) {
        int l =0;   int r =array.length-1;
        while (l<=r) {
            int model = (l+r) / 2;
            if (temp == array[model]) {
                return "是这个数    "+temp;
            } else if (temp > array[model]) {
               l=model+1;

            } else if(temp < array[model]){
                r=model-1;
            }
        }
        return "没找着";
    }
}

```

**解决整数溢出问题**

> *溢出过程详解*：

```java
public static void main(String[] args) {
        int left = 0;
        int right = Integer.MAX_VALUE - 1;
        int middle = (left + right) / 2;
        System.out.println(right);//2147483646
        System.out.println(middle);//1073741823
        // 得出中间值为 m = 10亿多，如果接下来再计算（int m =(l+r)/2 ），则会继续进行 l+r 运算，就等于30亿多了，超出了int的最大值。
//        举例子
//        在右侧
        left = middle + 1;
        middle = (left + right) / 2;
        System.out.println(middle);//-536870913
//        二进制运算，演示两个正整数相加变成负数，规则：逢 2 进 1，为 2 是 0.
//        比如 126 + 63 规定在一个byte（8个bit）内且有符号， 则结果不是 189，而是 -67
//         符号位，0代表正->   0 1 1 1  1 1 1 0      值为126
//                        +  0 0 1 1  1 1 1 1      值为63
//                           1 0 1 1  1 1 0 1      值为-67
//
//        方法一： (l + r)/2 ==> l - l/2 + r/2 ==> l + (r/2 - l/2) ==> l + (r-l)/2    利用大值先减去左边界后先除以2，这样即使再加上左边界也小了
        middle = left + (right - left) / 2;
        System.out.println(middle);//1610612735
//        方法二；  用无符号的右移运算代替掉除法。  向右移一位
        middle = (left + right) >>> 1;
        System.out.println(middle);
    }
```

当 l 和 r 都较大时，`l + r` 有可能超过整数范围，造成运算错误，解决方法有两种：

相关面试题
1 ．有一个有序表为 1 , 5 , 8 , 11 , 19 , 22 , 31 , 35 , 40 , 45 , 48 , 49 , 50 当二分查找值为 48 的结点时，查找成功需要比较的次数() 

2 ．使用二分法在序列 1 , 4 , 6 , 7 , 15 , 33 , 39 , 50 , 64 , 78 , 75 , 81 , 89 , 96 中查找元素 81 时，需要经过（）次比较 
> 口诀：
> 奇数二分取中间
> 偶数二分取中间靠左

3 ．在拥有一个 128 个元素的数组中二分查找一个数，需要比较的次数最多不超过多少次  
-  $2^n$   = 128 或 128 /2 /2....直到 1
-  问题转化为$log_2 128=7$, 计算器则是 $log_10 128/$log_10 2 = 7$  四舍五入取整。

   - 是整数，则整数即为最终结果
   
   - 是小数，则舍去小数部分，整数加一为最终结果
   

------

### 2. 冒泡排序

**要求**

* 能够用自己语言描述冒泡排序算法
* 能够手写冒泡排序代码
* 了解一些冒泡排序的优化手段

**算法描述**

1. 依次比较数组中相邻两个元素大小，若 a[j] > a[j+1]，则交换两个元素，两两都比较一遍称为一轮冒泡，结果是让最大的元素排至最后
2. 重复以上步骤，直到整个数组有序

**算法实现**（优化`外层`循环的循环比较次数——排好序的数组不需要再循环比较）

```java
public class Test {
    public static void main(String[] args) {
        int[] array = {5, 9, 7, 4, 1, 3, 2, 8};
        bubble(array);
    }

    public static void bubble(int[] array) {
        int k;
        for (int j = 0; j < array.length - 1 - j; j++) { // array.length- 1- j每轮少比一次
            boolean swapped = false;// 是否发生了交换，避免已排好序又做交换操作，提升算法效率
//        第一轮排序————挑出最大的排在最右边
            for (int i = 0; i < array.length - 1; i++) {
                 System.out.println("比较次数"+i);
                if (array[i] > array[i + 1]) {
                    swap(array, i, i + 1);
                }
            }
            k=j+1;
            System.out.println("第" + k + "轮" + Arrays.toString(array));
            if (!swapped) {
                break;
            }
        }
    }

    public static void swap(int[] array, int i, int j) {
        int t = array[i];
        array[i] = array[j];
        array[j] = t;
    }
}
```

**最优算法实现**（优化`内层`循环的循环比较次数——数组元素中后几位比较完的元素无需再比较）

- 优化方式：每轮冒泡时，最后一次交换索引可以作为下一轮冒泡的比较次数，如果这个值为零，表示整个数组有序，直接退出外层循环即可。

```java
 public static void bubble_2(int[] array) {
        int n = array.length - 1;//比较循环的次数
        while (true) {
            int last = 0;// 最后一次发生交换时的索引位置
            for (int i = 0; i < n; i++) {
                System.out.println("比较次数"+i);
                if (array[i] > array[i + 1]) {
                    swap(array, i, i + 1);
                    last = i;// 每次循环后记录最后一次交换的索引位置

                }
            }
            n = last;
            System.out.println("第"+n+"轮循环冒泡排序"+Arrays.toString(array));
            if (0 == n) {
                break;
            }
        }
    }
```

### 3. 选择排序

**要求**

* 能够用自己语言描述选择排序算法
* 能够比较选择排序与冒泡排序
* 理解非稳定排序与稳定排序

**算法描述**

1. 将数组分为两个子集，排序的和未排序的，每一轮从未排序的子集中选出最小的元素，放入排序子集

2. 重复以上步骤，直到整个数组有序

<!-- **算法思路解析：**

1.确定数组需要经过多少轮选择才能让数组有序 	eg:假设数组长度是8，则需要经过7轮选择，可以选出前7个数放入到有序部分，剩下的那个无需再选就是最大的了。

2.每轮循环应查出的数是什么

3.
-->

3. 优化方式

1.为减少交换次数，每一轮可以先找最小的索引，在每轮最后再交换元素。

4. 与冒泡排序比较

  - 1.二者平均时间复杂度都是O（n`2）.
  - 2.选择排序一般要快于冒泡，因为其交换次数少。
  - 但如果集合有序度高，冒泡优于选择
  - 冒泡属于稳定排序算法，而选择属于不稳定排序。

```java
public class Test {
    public static void main(String[] args) {
        int[] array = {5, 3, 7, 2, 1, 9, 8, 4};
        selection(array);
    }
    public static void selection(int[] array) {
        for (int i = 0; i < array.length - 1; i++) {
//                i 代表每轮选择最小元素要交换到的目标索引
            int s =i; // 代表最小元素的索引值
            for (int j = s+1; j < array.length; j++) {   // 此循环为了找出最小的那个索引，找出后放到最小的索引位置，
                if (array[s] > array[j]) { // j 元素比 s 元素还要小, 更新 s
                    s = j;   //  最小值放到s的索引上
                }
            }
            if (s!=i) { //  将当前最小数据值与此次循环的开始位置数据做交换。
                swap(array,s,i);
            }
            System.out.println(Arrays.toString(array));
        }
    }
    public static void swap(int[] array, int i, int j) {
//        i 最小元素的索引值 j
        int t = array[i];
        array[i] = array[j];
        array[j] = t;
    }
}
// 输出结果为
[1, 3, 7, 2, 5, 9, 8, 4]
[1, 2, 7, 3, 5, 9, 8, 4]
[1, 2, 3, 7, 5, 9, 8, 4]
[1, 2, 3, 4, 5, 9, 8, 7]
[1, 2, 3, 4, 5, 9, 8, 7]
[1, 2, 3, 4, 5, 7, 8, 9]
[1, 2, 3, 4, 5, 7, 8, 9]
```





------


## 集合部分
###  7.ArrayList

**要求**

* 掌握 ArrayList 扩容规则

无参ArrayList ，初始值是空，即为0，代码如下：

```java
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
```

有参ArrayList ,初始值是整数，初始值是那个整数参数的大小，默认是10。

```java
private static final Object[] EMPTY_ELEMENTDATA = {};

public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }
```

有参ArrayList ，初始值是集合，初始值是集合元素个数：

```java


public ArrayList(Collection<? extends E> c) {
        Object[] a = c.toArray();
        if ((size = a.length) != 0) {
            if (c.getClass() == ArrayList.class) {
                elementData = a;
            } else {
                elementData = Arrays.copyOf(a, size, Object[].class);
            }
        } else {
            // replace with empty array.
            elementData = EMPTY_ELEMENTDATA;
        }
    }
```

**扩容**：

 在 Java 中，ArrayList 内部采用一个数组来存储元素，当数组的长度不足以容纳新的元素时，需要进行扩容操作。ArrayList 的扩容机制是每次扩容容量变为原来的 1.5 倍，也就是说，每次扩容后，数组的长度会变为原来的 1.5 倍。 

**1.5 倍的来由：**

原数组的元素个数值，右移一位，在二进制中，右移一位相当于除以2，得出数字后与原数组元素个数相加，得出结果为新数组大小，eg:原数组大小是15,15>>1，得7，15+7=22，即新数组大小为22。

代码：

```java
private void ensureCapacityInternal(int minCapacity) {
    // 如果 ArrayList 的容量不足以容纳新的元素
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        // 如果 ArrayList 内部数组还没有被初始化，则初始化一个默认长度为 10 的数组
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    // 如果需要扩容
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

/**
 * 扩容方法
 */
private void grow(int minCapacity) {
    // 扩容前的数组长度
    int oldCapacity = elementData.length;
    // 扩容后的数组长度，每次扩容为原来的 1.5 倍
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    // 如果扩容后的数组长度依然无法容纳元素，则将数组长度设为 minCapacity(*要添加元素的个数，即新数组扩容后也无法满足大小，直接建要传入元素个数大小的数组*)
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    // 将原来的数组拷贝到新的数组中
    elementData = Arrays.copyOf(elementData, newCapacity);
}

```

**扩容规则总结**

1. ArrayList() 会使用长度为零的数组
2. ArrayList(int initialCapacity) 会使用指定容量的数组
3. public ArrayList(Collection<? extends E> c) 会使用 c 的大小作为数组容量
4. add(Object o) 首次扩容为 10，再次扩容为上次容量的 1.5 倍
5. addAll(Collection c) 没有元素时，扩容为 Math.max(10, 实际元素个数)取两者之间最大值，有元素时为 Math.max(原容量 1.5 倍, 实际元素个数)

------

### 8. Iterator

**要求**

* 掌握什么是 Fail-Fast、什么是 Fail-Safe

Fail-Fast 与 Fail-Safe

* ArrayList 是 fail-fast 的典型代表，遍历的同时不能修改，尽快失败

* CopyOnWriteArrayList 是 fail-safe 的典型代表，遍历的同时可以修改，原理是读写分离，修改后new建个集合，所以当时遍历的集合和修改后的集合不是一个集合。

**提示**

>[代码示例👈](https://github.com/Fengleitown/BLOG-code/tree/main/src/main/java/day01/list)中FailFastVsFailSafe


### 9.LinkedList

**要求**

* 能够说清楚 LinkedList 对比 ArrayList 的区别，并重视纠正部分错误的认知

**ArrayList与LinkedList区别**

- ArrayList

  - 基于数组，需要连续内存
  - 随机访问快（指根据下标访问）
  - 尾部插入、删除性能可以，其他部分插入、删除都会移动数据，因此性能会低
  - 可以利用cpu缓存，局部性能原理
    - 解释:
      -  1.cpu缓存的引入作用：比如cpu做运算操作，需要从内存中读取数字然后放到寄存器中，算出结果后，再将数值放到内存中，内存的读写效率没那么高会有几百纳秒，这样如果后续中每次操作都利用这个数字，效率太低，这样就引入了cpu缓存，类似缓存中间件，提升效率。
      - 2.局部性原理，将数据读取后放到cpu缓存中，操作系统会把相邻数据也放到cpu缓存中，这样的操作对数组很有利，但链表就不行，链表读取到相邻元素后，可能是没用的，因为他的内存地址是随机的,而且cpu缓存空间大小也有限，等下次根据局部性原理将相关数据放到cpu缓存中时，可能会将之前的数据覆盖掉。


![](/assets/img/ext-img/cpu缓存.jpg)

- LinkedList

  - 基于双向链表，无需连续内存
  - 随机访问慢（要沿着链表遍历）
  - 头尾插入删除性能高（在链表中间插入性能不高，因为要查找插入位置的元素，查找过程会很慢，找到之后插入性能会很高）
  - 占用内存多（有很多Node节点，节点有三个属性，分别是 上个元素下标、元素、下个元素下标，除此之外还有头尾结点等，所以占用内存较大。）
  
  **总结**：综合来看，平均使用ArrayList效率高，大多数情况都是用ArrayList。除非是头插，则用LinkedList。

### 10. HashMap

面试题：1.底层数据结构，1.7和1.8有什么不同？

* 1.7 数组 + 链表
* 1.8 数组 + （链表 | 红黑树）

2.为何要用红黑树，为何一上来不树化，树化阈值为何是8，何时会树化，何时会退化为链表？

- 为什么要用红黑树 🌴 ？
  - 因为1.7里面如果链表太长了，它其实是会影响我们整个hashMap的性能，那1.8以后引入了这个红黑树之后，在链表比较长的时候，会提升性能。
    这是这个问题啊。
  
- 何时会树化 🌴 ？
  
  - 要满足两个条件，第一个链表长度超过阈值8，第二个就是整个数字的长度要大于等于64，这两个条件都满足了，列表就可以变成树了。
  
- 为什么不是一上来就进行树化 🌴？ 
  
  - 这个呢，大家要想一想，如果大部分情况下，我这个链表中只有一个两个三个元素的时候，那它比较的次数最多也就是三次。那如果费了一大堆劲，把这个链表变成了一个短的链表，变成了树以后，它的性能就能够比我们这种短链表性能更高吗？显然不会对吧，哎，我们链表短的情况下，其实性能是比红黑数要好的，只有是链表比较长的情况下，它的性能才是远远不如红黑树，所以如果链表短没有必要进行书法。而且变成树以后，它占用的内存也多啊，因为链表它的一个数据结构是node，而红黑树呢，它的底层数据结构是一个treeNode,treeNode里面的成员变量要比链表多很多，因此呢，对内存的占用也更多，所以我们如非必要不要转化成红黑树。
  
- 为什么树化阈值选择了8，而不是选一个更小的数，让它在链表更短的一点的时候就变成树 🌴 呢？
  其实正常情况下，我们的链表长度绝不可能超过8
  
- 何时会退化为链表？
  
  - 扩容时树会拆分，当拆分后剩余元素个数<=6时，会退化成链表（元素是7时还是红黑树结构）。
  
- 举个例子 🌰 证明一下啊。

  - >[代码示例👈](https://github.com/Fengleitown/BLOG-code/tree/main/src/main/java/day01/map)中HashMapDistribution.java

    结果为： 👇 


```
class java.util.HashMap
总的桶个数[524288]
0个元素的桶个数[334855]
1个元素的桶个数[150323]
2个元素的桶个数[33401]
3个元素的桶个数[5090]
4个元素的桶个数[555]
5个元素的桶个数[62]
6个元素的桶个数[2]
```

可见链表长度为6的时候都比较少，只有两个，所以8就够用了，红黑树也是不正常的一种情况，主要目的为了防止 DoS 攻击。 💯 

------



**要求**

* 掌握 HashMap 的基本数据结构
* 掌握树化
* 理解索引计算方法、二次 hash 的意义、容量对索引计算的影响
* 掌握 put 流程、扩容、扩容因子
* 理解并发使用 HashMap 可能导致的问题
* 理解 key 的设计

### 1）基本数据结构

* 1.7 数组 + 链表
* 1.8 数组 + （链表 | 红黑树）

### 2）树化与退化

**树化意义**

* 红黑树用来避免 DoS 攻击，防止链表超长时性能下降，树化应当是偶然情况，是保底策略
  - hash 表的查找，更新的时间复杂度是 $O(1)$，而红黑树的查找，更新的时间复杂度是 $O(log_2⁡n )$，TreeNode 占用空间也比普通 Node 的大，如非必要，尽量还是使用链表
  - hash 值如果足够随机，则在 hash 表内按泊松分布，在负载因子 0.75 的情况下，长度超过 8 的链表出现概率是 0.00000006，树化阈值选择 8 就是为了让树化几率足够小

**树化规则**

- 树化两个条件：
  - ① 链表长度超过树化阈值 8 
  - ② 数组容量>=64

 💡 说明：**当链表长度超过树化阈值 8 时，先尝试扩容来减少链表长度，如果数组容量已经 >=64，才会进行树化**

**退化规则**

* 情况1：在扩容时如果拆分树时，树元素个数 <= 6 则会退化链表
* 情况2：remove 树节点时，若 root、root.left(左孩子)、root.right(右孩子)、root.left.left(左孙子) 有一个为 null ，也会退化为链表（移除之前上面几个元素都存在，不会退化成链表。移除之前上面几个元素有其中一个不存在，则会退化成链表）

### 3）索引计算

*面试题：索引如何计算的？hashcode都有了，为何还要提供hash()方法？数组容量为何是2的n次幂？*

**索引计算方法**

* ①计算`对象` 🐘 的 hashCode()，得到原始hash值。
* ②调用 `HashMap` 的 hash() 方法进行二次哈希。
  * 二次 hash() 是为了综合高位数据，让哈希分布更为均匀。
* ③最后 `二次哈希值 & (capacity – 1)，哈希值按位与容量-1运算 `得到索引，或`二次哈希值对map容量取模`运算，得到链表的索引。

**数组容量为何是 2 的 n 次幂**

1. 计算索引时效率更高：如果是 2 的 n 次幂可以使用位与运算代替取模
2. 扩容时重新计算索引效率更高： hash & oldCap == 0 的元素留在原来位置 ，否则新位置 = 旧位置 + oldCap
   - 解释：hash按位与运算旧capacity == 0的元素扩容后留在原来位置，不等于0时，新位置 = 旧位置（索引下标）+ 旧 capacity 。
    ![](/assets/img/ext-img/map扩容后的位置.jpg)

 ⚠️ 注意：

* 二次 hash 是为了配合 **容量是 2 的 n 次幂** 这一设计前提，如果 hash 表的容量不是 2 的 n 次幂，则不必二次 hash
* **容量是 2 的 n 次幂** 这一设计计算索引效率更好，但 hash 的分散性就不好，需要二次 hash 来作为补偿，没有采用这一设计的典型例子是 Hashtable

### 4）put 与扩容

*面试题：介绍一个put方法的流程，1.7与1.8有何不同？*

**put 流程**

1. HashMap 是懒惰创建数组的，首次使用才创建数组

2. 计算索引（桶下标）

3. 如果桶下标还没人占用，创建 Node 占位返回

4. 如果桶下标已经有人占用

   1. 已经是 TreeNode 走红黑树的添加或更新逻辑
   2. 是普通 Node，走链表的添加或更新逻辑，如果链表长度超过树化阈值，走树化逻辑

5. 返回前检查容量是否超过阈值，一旦超过进行扩容

   - 扩容过程

   ​    ①先把元素加进去原始map

   ​	②然后需要扩容的话，创建新数组扩容。

   ​	③一个链表一个链表把元素都迁移到新书组中

**1.7 与 1.8 的区别**

1. 链表插入节点时，1.7 是头插法，1.8 是尾插法
2. 1.7 是大于等于阈值且没有空位时才扩容，而 1.8 是大于阈值就扩容
3. 1.8 在扩容计算 Node 索引时，会优化

**扩容（加载）因子为何默认是 0.75f**？（加载因子就是3/4，16X3/4=13,大于这个数就扩容）

1. 在空间占用与查询时间之间取得较好的权衡
2. 大于这个值，空间节省了，但链表就会比较长影响性能
3. 小于这个值，冲突减少了，但扩容就会更频繁，空间占用也更多

### 5）并发问题

*面试题：多线程下操作hashMap会有啥问题？*

**扩容死链（1.7 会存在）**



**数据错乱（1.7，1.8 都会存在）**

存在线程之间会覆盖数据的问题。

> [代码示例👈](https://github.com/Fengleitown/BLOG-code/tree/main/src/main/java/day01/map)中	HashMapMissData.java

### 6）key 的设计

面试题：key能否为null,作为key的对象有什么要求？

- hashmap的key可以为Null,但其他map实现则不然。
- 作为 key 的对象，必须实现 hashCode （为了key在整个hashMap中有更好的分布性，提高查询性能）和 equals（万一两个key计算出来的结果一样，要比较看他们是不是相同的对象），并且 key 的内容不能修改（不可变）> [代码示例👈](https://github.com/Fengleitown/BLOG-code/tree/main/src/main/java/day01/map)中HashMapMutableKey

```
// 打印出的结果，可见改了map的属性后，需要重新计算hashcode值，原来的找不到了，查找的就是null.
java.lang.Object@3d71d552
null
```

**String 对象的 hashCode() 设计**

* 目标是达到较为均匀的散列效果，每个字符串的 hashCode 足够独特
* 字符串中的每个字符都可以表现为一个数字，称为 $S_i$，其中 i 的范围是 0 ~ n - 1 
* 散列公式为： $S_0∗31^{(n-1)}+ S_1∗31^{(n-2)}+ … S_i ∗ 31^{(n-1-i)}+ …S_{(n-1)}∗31^0$
* ①31 代入公式有较好的散列特性，②并且 31 * h 可以被优化为 
  * 即 $32 ∗h -h $
  * 即 $2^5  ∗h -h$
  * 即 $h≪5  -h$
可看到hashcode为31时，散列效果比较好
![](/assets/img/ext-img/hashmap-31.jpg)       
![](/assets/img/ext-img/hashmap-31-11.jpg)
