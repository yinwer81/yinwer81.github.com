---
layout: post
title: "找到两个单链表的第一个相交点"
categories: programming
---

昨天在同济大学校招，计算机及相关专业本科/硕士，笔试30分钟，其中有两个代码算法题，任选其一：
>* 给定降序整数组(intArray)和长整数(target)，检查整数组中是否存在两个数，其和为target，如果有则找出其在数组中的位置
>* 给定两个单链表(list1,list2)，检查两个链表是否有交点，如果有返回第一个交点

第一题其实就是我在前面介绍过的[twoSum问题](/20141007/leetcode-twosum/)。另外因为数组已降序排列，可考虑使用头尾指针来解题，效率可能会比使用HashMap要好。此处略去不提。

至于第二题，先要搞清楚的是：如果两个单链表相交，那么单链表(list1,list2)的末尾节点一定会相同。因为单链表**只可能为Y/V型相交，不可能为X型，否则会出现某一节点存在两个next节点的情况**。

复用前面[移除单链表中间元素及反转](/20141102/remove-the-middle-of-singlylist-and-reverse/)中定义的单链表SinglyList，解题思路和步骤为：

	1. 定义函数findTheFirstCross(SinglyList<T> list1, SinglyList<T> list2)，head1为长度较短的链表
	2. 取单链表list2减去单链表list1的长度差shortOfList1，如果list1比list2长，recall以保证按短，长的方式传参
	3. 使用指针point2从list2头部开始，位移到shortOfList1位置
	4. 此时，使用指针point1从list1头部开始，同时移动，当point1指向的元素与point2指向的元素相同时，即为第一个交点

复用SinglyList类并增加实现size()方法：
{% highlight java %}
package com.dunkcoder;

import java.util.ArrayList;
import java.util.List;

public class SinglyList<T> {

    private Node<T> head;
    private Node<T> tail;
    private List<T> tList;
    
    public SinglyList(Node<T> head) {
        this.head = head;
        this.tail = head;
        tList = new ArrayList<T>();
    }
    
    // 复用前篇的SinglyList并增加size()
    public int size() {
    	return tList.size();
	}
    
    public Node<T> head() {
        return this.head;
    }

    @SuppressWarnings("unchecked")
	public void add(Node<T> node) {
    	tList.add((T) node);
        this.tail.setNext(node);
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

下面是算法和测试代码：
{% highlight java %}
package com.dunkcoder;

public class TestSinglyList {

	public static <T> SinglyList.Node<T> findTheFirstCross(SinglyList<T> list1, SinglyList<T> list2) {

		int shortOfList1 = list2.size() - list1.size();

		// recall step2
		if (shortOfList1 < 0)
			return findTheFirstCross(list2, list1);

		SinglyList.Node<T> head1 = list1.head();
		SinglyList.Node<T> head2 = list2.head();

		// step3
		while (shortOfList1-- > 0) {
			head2 = head2.next();
		}

		// step4
		SinglyList.Node<T> node = null;
		while (head1.getObject() != null && head2.getObject() != null) {
			if (head1.getObject().equals(head2.getObject())) {
				node = head1;
				break;
			} else {
				head1 = head1.next();
				head2 = head2.next();
			}
		}
		return node;
	}

	public static void main(String[] args) {

		SinglyList.Node<String> head1 = new SinglyList.Node<String>("head1");
		SinglyList<String> list1 = new SinglyList<String>(head1);
		list1.add(new SinglyList.Node<String>("1"));
		list1.add(new SinglyList.Node<String>("2"));
		list1.add(new SinglyList.Node<String>("3"));
		list1.add(new SinglyList.Node<String>("4"));
		list1.add(new SinglyList.Node<String>("5"));
		SinglyList.Node<String> head2 = new SinglyList.Node<String>("head2");
		SinglyList<String> list2 = new SinglyList<String>(head2);
		SinglyList.Node<String> node6 = new SinglyList.Node<String>("6");
		list1.add(node6);
		list2.add(node6);
		SinglyList.Node<String> node7 = new SinglyList.Node<String>("7");
		list1.add(node7);
		list2.add(node7);
		System.out.println(findTheFirstCross(list1, list2).getObject());
	}

}
{% endhighlight %}

就酱，嗯。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
