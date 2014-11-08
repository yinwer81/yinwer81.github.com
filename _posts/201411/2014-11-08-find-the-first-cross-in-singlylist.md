---
layout: post
title: "找到两个单链表的第一个相交点"
categories: programming
---

昨天继续校招，计算机及相关专业本科/硕士，笔试30分钟，两个算法题，任选其一：
>* 给定降序整数组(intArray)和长整数(target)，检查整数组中是否存在两个数，其和为target，如果有则找出其在数组中的位置
>* 给定两个单链表(list1,list2)，检查两个链表是否有交点，如果有返回第一个交点

第一题其实就是我在前面介绍过的[twoSum问题](/20141007/leetcode-twosum/)，另外因为数组已降序排列，可考虑使用头尾指针来解题，效率可能会比使用HashMap要好。此处略去不提。

至于第二题，先要搞清楚的是：如果两个单链表相交，那么单链表(list1,list2)的末尾节点一定会相同。因为单链表只可能为Y/V型相交，不可能为X型，否则会出现某一节点存在两个next节点的情况。

复用前面[移除单链表中间元素及反转]()中定义的单链表SinglyList，解题思路和步骤为：

	1. 定义函数findTheFirstCross(SinglyList<T> list1, SinglyList<T> list2)，head1为长度较短的链表
	2. 取链表list1和list2的长度，如果list1比list2长，recall以保证按短，长的方式传参
	3. 取得单链表list2减去单链表list1的长度差shortOfList1，并使用指针point2从list2头部开始，位移到shortOfList1位置
	4. 此时，使用指针point1从list1头部开始，同时移动，当point1指向的元素与point2指向的元素相同时，即为第一个交点

下面是算法和测试代码：
{% highlight java %}
package com.dunkcoder;

public class TestSinglyList {

	public static <T> SinglyList<T>.Node findTheFirstCross(SinglyList<T> list1, SinglyList<T> list2) {

		int shortOfList1 = list2.size()-list1.size();

		if (shortOfList1 < 0)
			return findTheFirstCross(list2, list1);

		SinglyList.Node<T> head1 = list1.head();
		int index = 0;
		SinglyList.Node<T> head2 = list2.head();

		// 定义2个指针：current和middle
		// current的步速是middle的2倍，当current走到尾巴的时候，middle刚好指在中间元素
		while (current.next() != null) {
			index++;
			if (index % 2 == 0) {
				middle = middle.next();
			}
			current = current.next();
		}

		// 考虑链表长度为偶数和奇数情况
		String stringTmpl = "middle:%s of length:%s removed";
		if (index % 2 == 1) {
			// 奇数个元素
			middle = middle.next();
			list.remove(middle);
			System.out.println(String.format(stringTmpl, middle, index));
		} else {
			// 偶数个元素，middle指向左边
			SinglyList.Node<T> middleRight = middle.next();
			list.remove(middle);
			System.out.println(String.format(stringTmpl, middle, index));
			list.remove(middleRight);
			System.out.println(String.format(stringTmpl, middleRight, index));
		}

		return list;
	}

	// 递归
	public static <T> SinglyList.Node<T> recursivelyReverse(
			SinglyList.Node<T> head) {
		SinglyList.Node<T> forwardNode = head.next();
		if (forwardNode == null)
			return head;
		SinglyList.Node<T> newHead = recursivelyReverse(forwardNode);
		forwardNode.setNext(head);
		head.setNext(null);
		return newHead;
	}

	// 循环迭代
	public static <T> SinglyList.Node<T> iterativelyReverse(
			SinglyList.Node<T> head) {
		SinglyList.Node<T> tempNode = null;
		SinglyList.Node<T> traversedNode = null;

		while (head != null) {
			tempNode = head.next();

			head.setNext(traversedNode);
			traversedNode = head;

			head = tempNode;
		}
		head = traversedNode;
		return head;
	}

	public static void main(String[] args) {

		SinglyList.Node<String> head = new SinglyList.Node<String>("head");
		SinglyList<String> list = new SinglyList<String>(head);
		list.add(new SinglyList.Node<String>("1"));
		list.add(new SinglyList.Node<String>("2"));
		list.add(new SinglyList.Node<String>("3"));
		list.add(new SinglyList.Node<String>("4"));
		// list.add(new SinglyList.Node<String>("5"));

		removeMiddle(list);

		SinglyList.Node<String> reversedHead = recursivelyReverse(head);
		// SinglyList.Node<String> reversedHead = iterativelyReverse(head);
		System.out.println(reversedHead);
	}

}

{% endhighlight %}

如果基于LinkedList或ArrayList写也是可以的，说明对JDK比较熟悉，只是LinkedList是双向链表：
{% highlight java %}
package com.dunkcoder;

import java.util.ArrayList;
import java.util.ListIterator;

public class TestArrayList {

	public static <T> ArrayList<T> removeMiddle(ArrayList<T> list) {
		if (list == null || (list != null && list.size() <= 2)) {
			return list;
		} else {
			ListIterator<T> middle = list.listIterator();
			ListIterator<T> current = list.listIterator();
			T t = null;
			int index = 0;
			while (true) {
				// 2步走
				t = current.next();
				if (t != null) {
					index++;
					if (index % 2 == 0) {
						// 1步走
						t = middle.next();
					}
					// 没有下一个元素
					if (!current.hasNext()) {
						if (index % 2 == 0) {
							// 偶数个元素,此时afterStepItr指向Middle的左边
							int pos = index / 2 - 1;
							list.remove(pos);// 删除Middle的左边
							list.remove(pos);// 删除Middle的右边
						} else {
							// 奇数个元素,此时afterStepItr少移一次
							int pos = index / 2 + 1 - 1;
							list.remove(pos);// 删除Middle
						}
						break;
					}
				}

			}
			return list;
		}
	}

	public static void main(String[] args) {
		ArrayList<String> list = new ArrayList<String>();
		list.add("1");
		list.add("2");
		list.add("3");
		list.add("4");
		// list.add("5");
		removeMiddle(list);
	}

	// LinkedList双向，ArrayList则可以考虑使用Collections.reverse()方法
	public static <T> LinkedList<T> iterativelyReverse(LinkedList<T> head) {
		LinkedList<T> reversedList = new LinkedList<T>();
		for( Iterator<T > itr = head.descendingIterator(); itr.hasNext(); ) {
		    T t = itr.next();
		    reversedList.add(t);
		}
		return reversedList;
	}
}
{% endhighlight %}
以上。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。

