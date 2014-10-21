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

如何优化时间复杂度？我们再回头分析一下排序数组A,B和Median值：

我们一定是找到n+m其中的某个具体位置k，使得当n+m为奇数时k=(n+m-1)/2
假设数组A长度n较小，那么不管数组B长度m多大，Median的值仅取决于数组A长度n，举极端例子：数组A长度m=1，数组B长度n=100，那么Median值一定是A[0],B[49],B[50]三元素中，值的大小在中间的，即：如果A[0]<B[49]，那么B[49]即为所求Median值，如果B[49]<A[0]<B[50]，那么A[0]即为所求Median值。


如果数组A长度m=1，数组B长度n=7，那么Median值一定是A[0]
{% highlight java %}
public static int[] twoSum(final int[] input, final long target) {
	int[] result = new int[2];
	Map<Integer, Integer> map = new HashMap<Integer, Integer>();
	for (int i = 0; i < input.length; i++) {
		int diffVal = (int) (target - input[i]);
		if (map.containsKey(diffVal)) {
			result[1] = i + 1;
			result[0] = map.get(diffVal);
			break;
		}
		map.put(input[i], i + 1);
	}
	return result;
}
{% endhighlight %}

以上。您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
