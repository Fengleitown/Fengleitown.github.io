# 基础篇

> ***注重：算法、数据结构、常见的设计模式***

## 1.二分查找

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



##  7.ArrayList

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


