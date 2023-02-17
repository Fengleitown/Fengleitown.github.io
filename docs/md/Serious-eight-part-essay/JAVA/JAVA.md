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

*[更形象的图示演示请参考 👉 演示动画](binary_search.html)*

**算法实现和测试：**

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


