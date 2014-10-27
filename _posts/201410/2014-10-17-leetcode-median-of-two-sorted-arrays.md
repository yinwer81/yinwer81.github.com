---
layout: post
title: "Median of two sorted arrays问题"
categories: programming leetcode
---

本篇记录leetcode的Median of two sorted arrays问题。

Median of two sorted arrays问题官方描述在[这里](https://oj.leetcode.com/problems/median-of-two-sorted-arrays/)，翻译过来大致是：<br />
给定两个已排序好的整数型数组A(长度m)和B(长度n)，找出两个数组的Median值，时间复杂度为O(log (m+n))。Median值即排序后链表的中间值，举例：[1,2,3]的中间值为2，[1,2]的中间值为1.5。

问题分析：
<br />
直观上最容易想到的方法是保持排序的同时将A和B合并为数组C，然后再求该数组C的Median值，但时间复杂度是O(m+n)，下面是OJ Accepted代码：
{% highlight java %}
public double getMedianOfTwoSortedArrays(int A[], int B[]) {
    ArrayList<Integer> list = new ArrayList<Integer>();
    int m = A.length - 1;
    int n = B.length - 1;
    while (m >= 0 && n >= 0) {
        if (A[m] > B[n]) {
            list.add(A[m--]);
        } else {
            list.add(B[n--]);
        }
    }
    while (m >= 0) {
        list.add(A[m--]);
    }
    while (n >= 0) {
        list.add(B[n--]);
    }
    return (list.get(list.size() >> 1) + list.get((list.size() - 1) >> 1)) / 2.0;
}
{% endhighlight %}

如何优化时间复杂度？我们得回头再分析和思考：

假设整数型数组A(长度m)和B(长度n)中，长度较小的是整数数组A(长度m)，那么不管整数数组B(长度n)有多大，Median的值始终取决于整数数组A。这是make sense的，举两个极端例子如下：

如果数组A长度m=1，数组B长度n=100，那么Median值一定是A[0],B[49],B[50]三个数中，如果A[0]<B[49]，那么B[49]即为所求Median值，如果B[49]<A[0]<B[50]，那么A[0]即为所求Median值。

如果数组A长度m=2，数组B长度n=100，那么Median值一定是在A[0],A[1],B[49],B[50]四个数中比较得出。

那么简单了：二分法查找，这样一来，时间复杂度是O(log(min(m,n)))，OJ Accepted代码如下：
{% highlight java %}
public double getMedianOfTwoSortedArrays(int A[], int B[]) {
    int n = A.length;
    int m = B.length;

    // recall to change the reference of A and B
    if (n > m)
        return getMedianOfTwoSortedArrays(B, A);

    // do binary search, then the median should between these 4 numbers: A[l-1], A[l], B[k-l], and B[k-l+1]
    int k = (n + m - 1) / 2; // the middle of n+m
    int l = 0, r = Math.min(k, n); // the smaller of k and n
    while (l < r) {
        int midA = (l + r) / 2;
        int midB = k - midA; // reduce midA every time
        if (A[midA] < B[midB])
            l = midA + 1;
        else
            r = midA;
    }

    // if (n+m) is odd, the median is the larger one between A[l-1] and B[k-l]
    int a = Math.max(l > 0 ? A[l - 1] : Integer.MIN_VALUE, k - l >= 0 ? B[k - l] : Integer.MIN_VALUE);
    if (((n + m) & 1) == 1)
        return (double) a;

    // if (n+m) is even, the median can be calculated by (max(A[l-1], B[k-l]) + min(A[l], B[k-l+1]) / 2.0
    int b = Math.min(l < n ? A[l] : Integer.MAX_VALUE, k - l + 1 < m ? B[k - l + 1] : Integer.MAX_VALUE);
    return (a + b) / 2.0;
}
{% endhighlight %}

递归的逻辑更好理解，OJ Accepted代码如下：
{% highlight java %}
public static int getkth(int A[], int B[], int k, int left, int right) {
    int n = B.length;

    // recall to change the reference of A and B
    if (left > right)
        return getkth(B, A, k, 0, n - 1);

    int i = left + (right - left) / 2;
    int j = k - 1 - i - 1;

    if ((j < 0 || (j < n && A[i] >= B[j])) && (j + 1 >= n || (j + 1 >= 0 && A[i] <= B[j + 1]))) {
        return A[i];
    } else if (j < 0 || (j + 1 < n && A[i] > B[j + 1])) {
        // recursion
        return getkth(A, B, k, left, i - 1);
    } else {
        // recursion
        return getkth(A, B, k, i + 1, right);
    }
}

public static double getMedianOfTwoSortedArrays(int A[], int B[]) {
    int m = A.length;
    int n = B.length;
    if ((m + n) % 2 == 1) {
        return getkth(A, B, (m + n) / 2 + 1, 0, m - 1);
    } else {
        return (getkth(A, B, (m + n) / 2, 0, m - 1) + getkth(A, B, (m + n) / 2 + 1, 0, m - 1)) / 2.0;
    }
}
{% endhighlight %}

总结，我们能学到什么：
>* 认真分析需求，找出需求的关键所在，如以上例子中的Median值仅与长度较小的数组相关
>* 细节问题处理，如以上例子中的奇偶数和边界问题
>* 二分法查找的时间复杂度是O(log)

以上。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
