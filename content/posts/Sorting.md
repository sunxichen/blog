+++
date = "2018-04-10"
title = "Sorting"
tags = [
    "Algorithms",
    "Java",
    "Data Structure",
]
categories = [
    "Algorithms",
]
+++

> **Sorting cost model**
>
> 在研究排序算法时，我们计算compares 和 exchanges的次数。对于那些没有exchanges的排序算法，我们统计array accesses.

## Selection sort
```java
public class Selection{
    public static void sort(Comparable[] a){
        int N = a.length
        for(int i=0;i<N;i++){
            int min = i;
            for(int j = i+1; j<N;j++){
                if(less(a[j],a[i])) min = j;
                exch(a, i, min);
            }
        }
    }
}
```

算法思想：
首先，找到数组中最小的item, 然后让他与数组第一个元素交换。接下来，找到下一个最小的item然后让它与第二个元素交换……

![](/images/Sorting/screenshot1.png)

Selection sort 并没有利用数组的原始order. 他没有有关下次排序时最小item可能的位置的信息。所以一个任意顺序的数组与一个已经排好序的或者一个item全部相等的数组在用selection sort时的performance相同。但是selection sort 的exchange (data movement)是linear的。这是其他sort算法所没有的。

## Insertion sort

![](/images/Sorting/screenshot2.png)

```java
public class Insertion{
    public static void sort(Comparable[] a){
        int N = a.length
        for(int i=1; i<N; i++){
            for(int j = i; j>0 && less(a[j],a[j-1]); j--){
                exch(a,j,j-1) //交换数组中a[j]与a[j-1]
            }
        }
    }
}
```

算法思想：
就像打扑克牌时对牌的排序一样，将起到的牌插入适当的位置。
每次插入时，我们需要将较大的item向右移动一位，以给当前要插入的item腾出一个空位。
The item to the left of the current index are in sorted order during the sort, but they are not in their final position, as they may have to be moved to make room for smaller items encountered later. The array is, however, fully sorted when the index reaches the right end.

![](/images/Sorting/screenshot3.png)

最坏的情况是待排序的数组是从高到低的顺序（我们输出的是从低到高）

Insertion sort 在非随机生成的数组(大部分已经in order 的）表现优秀。

## Shellsort

Insertion sort在数量很大的unordered数组上很慢，因为它只涉及相邻的元素。

算法思想：
Shellsort是建立在Insertion sort之上的，它每次移动h个位置，而不再是一个位置。

![](/images/Sorting/screenshot4.png)

H的数值逐渐减小，当h=1时，即完成排序。

```java
public class Shell{
    public static void sort(Comparable[] a){
        int N = a.length;
        int h = 1;
        while(h<N/3) h = 3*h + 1; //1, 4, 13, 40, 121, 364, 1093, ...
        while(h>=1){
            for (int i=h; i<N; i++){
                for (int j = i; j>=h && less(a[j], a[j-h]); j-=h){
                    exch(a,j,j-h);
                }
            }
            h = h/3
        }
    }
}
```

对于取不同的h序列，算法的performance不同。但是目前还没有找到一个最好的h序列。上面算法所选择的序列（1, 4, 13, 40, 121, 364, 1093, …）is easy to use and compute.

![](/images/Sorting/screenshot5.png)

H很大时，形成的subarray大小就小。h较小时，subarray is nearly in order. 因为有了前面的big increments时已经形成的partially ordered array.再进行smaller value时的insertion sort时便会更快。

## Mergesort

```java
public static void merge(Comparable[] a, int lo, int mid, int hi){
    int i = lo;
    int j = mid + 1;
    for (int k = lo; k<=hi; k++){   //Copy a[lo...hi] to aux[lo...hi]
        aux[k] = a[k];
    }
    for (int k = lo; k<=hi; k++){
        if(i>mid)   a[k] = aux[j++];
        else if(j>hi)   a[k] = aux[i++];
        else if(less(aux[j],aux[i]))    a[k] = aux[j++];
        else    a[k] = aux[i++];
    }
}
```

This method merges by first copying into the auxiliary array `aux[]` then merging back to `a[]`. In the merge (the second `for` loop), there are four conditions: left half exhausted (take from the right), right half exhausted (take from left), current key on right less than current key on left (take from the right), and current key on right greater than or equal to current key on left (take from the left).

### Top - down mergesort

```java
public class Merge{
    public static Comparable[] aux;
    public static void sort(Comparable[] a){
        aux = new Comparable[a.length]
        sort(a, 0, a.length-1)
    }
    public static void sort(Comparable[] a, int lo, int hi){
        if(hi<=lo)  return;
        int mid = lo+(hi-lo)/2;
        sort(a, lo, mid);
        sort(a, mid+1, hi);
        merge(a,lo,mid,hi);
    }
}
```

算法思想：
分治、递归。 If it sorts the two subarrays, it sorts the whole array, by merging together the subarrays.

![](/images/Sorting/screenshot6.png)

[Improvements:](https://algs4.cs.princeton.edu/22mergesort/MergeX.java.html)

![](/images/Sorting/screenshot7.png)

### Bottom-up mergesort

```java
public class Merge{
    public static Comparable[] aux;

    public static void sort(Comparable[] a, int lo, int hi){
        int N = a.length;
        aux = new Comparable[N]
        for (int sz = 1; sz<N; sz = sz+sz){ //sz: subarray size
            for(int lo = 0; lo<N-sz; lo += sz+sz){  //lo: subarray index
                merge(a, lo, lo+sz-1, Math.min(lo+sz+sz-1, N-1));
            }
        }
    }
}
```

算法思想：
先将长度为一的subarrays两两merge, 再将sz double, 将长度为2的subarrays两两merge…直至将整个array合并。

![](/images/Sorting/screenshot8.png)

我们可以使用mergesort来sort成百上千万的item. Mergesort首要缺点是 it requires extra space proportional to N, for the auxiliary array for merging.

## Quicksort

Quicksort可能是应用最广泛的sort. The quicksort’s desirable features are that it is in-place and that it requires time proportional to NlogN on the average to sort an array of length N. Its primary drawback is that it is fragile in the sense that some care is involved in the implementation to be sure to avoid bad performance.

算法思想：
Quicksort is a divide-and-conquer method for sorting. It works by **partitioning** an array into two parts, then sorting the parts independently.

![](/images/Sorting/screenshot9.png)

```java
public class Quick{
    public static void sort(Comparable[] a){
        StdRandom.shuffle(a);    //Eliminate dependence on input
        sort(a, 0, a.length-1);
    }

    public static int partition(Comparable[] a, int lo, int hi){
        int i = lo;
        int j = hi+1;   //left and right scan indices
        Comparable v = a[lo];   //partitioning item
        while(True){
            while(less(a[++i], v)   if(i==hi) break;
            while(less(v, a[--j]))  if(j==lo)   break;
            if(i>=j)    break;
            exch(a, i, j)
        }
        exch(a, lo, j)  //put v = a[j] into position
        return j;
    }

    public static void sort(Comparable[] a, int lo, int hi){
        if(hi<=lo)  return;
        int j = partition(a, lo, hi);
        sort(a, lo,j-1);    //sort left part a[lo..j-1]
        sort(a, j+1, hi);   //sort right part a[j+1..hi]
    }
}
```

![](/images/Sorting/screenshot10.png)

![](/images/Sorting/screenshot11.png)

**Quicksort with 3-way partitioning**:

![](/images/Sorting/screenshot12.png)

```java
public class Quick3way{
    private static void sort(Comparable[] a, int lo, int hi){
        if(hi <= lo)    return;
        int lt = lo, i = lo+1, gt = hi;
        Comparable v = a[lo];
        while (i<= gt){
            int cmp = a[i].comapreTo(v);
            if (cmp < 0)    exch(a, lt++, i++);
            else if (cmp > 0)   exch(a, i, gt--);
            else    i++;
        }   //Now a[lo...lt-1] < v = a[lt..gt] < a[gt+1..hi]
        sort(a, lo, lt-1);
        sort(a, gt + 1, hi);
    }
}
```

## Priority Queues

API:
![](/images/Sorting/screenshot13.png)

[Heap and heapsort](https://algs4.cs.princeton.edu/24pq/)

两个应用情景：

1. You have a huge input stream of N strings and associated integer values, and your task is to find the largest or smallest M integers.

2. <u>Multiway merge</u> problem: it merges together several sorted input streams into one sorted output stream.



