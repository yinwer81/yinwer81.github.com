---
layout: post
title: "Leetcode twoSum问题"
categories: programming leetcode
---

本篇记录leetcode的twoSum问题。

twoSum问题官方描述在[这里](https://oj.leetcode.com/problems/two-sum/)，翻译过来大致是：<br />
给定一个整数数组input和数字target，从整数数组中找出两个数，使得两个数字之和为target，打印这两个数在数组中的下标，数组下标从1开始。

问题分析：<br />
问题很简单，但是可能需要考虑数组中数字重复，两个整数和溢出整数范围，数组中找不到两个整数的和为target等情况。

简化问题，也就是说：<br />
>* 数组中数字重复只考虑下标靠前的
>* 找不到两个整数和为target时，返回两个下表都是0

下面是OJ Accepted的代码：
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
