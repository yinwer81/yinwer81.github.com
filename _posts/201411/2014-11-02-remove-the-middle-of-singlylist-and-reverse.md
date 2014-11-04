---
layout: post
title: "移除单链表中间元素及反转"
categories: programming
---

这几天在成都校招，计算机及相关专业本科/硕士，笔试30分钟，两个代码算法题，任选其一：
>* 移除单链表的中间元素
>* 反转单链表，递归和非递归两种方式

这是很简单的题，网上一搜一把，目的是考察学生有基础编码能力，不要求编译测试通过，只要写清楚思路和注意EDGE CASE都算过，但很可惜，通过率很低。为什么呢？

如果自己构造数据结构，代码稍多一些，我们也当然希望看到学生能多表现自己：
{% highlight java %}
package com.dunkcoder;

public class SinglyList<T> {

	private Node<T> head;
	private Node<T> tail;

	public SinglyList(Node<T> head) {
		this.head = head;
		this.tail = head;
	}

	public Node<T> head() {
		return this.head;
	}

	public void add(Node<T> node) {
		this.tail.next = node;
		this.tail = node;
	}

	public void remove(Node<T> node) {
		// TODO:
	}

	public static class Node<T> {
		private Node<T> next;
		private T object;

		public Node(T object) {
			this.object = object;
		}

		// getter() setter()

		public Node<T> next() {
			return next;
		}

		public void setNext(Node<T> next) {
			this.next = next;
		}

		// toString() hashCode() equals() ...
	}
}
{% endhighlight %}

下面是算法和测试：
{% highlight java %}
package com.dunkcoder;

public class TestSinglyList {

	public static <T> SinglyList<T> removeMiddle(SinglyList<T> list) {
		SinglyList.Node<T> current = list.head();
		int index = 0;
		SinglyList.Node<T> middle = list.head();

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

