---
layout: post
title: "How to break out of a resursion"
categories: programming
---
#### Java中，我们用break跳出循环，那跳出递归怎么办？举例如下：
<br />
需求1：给定一整数数组input，打印其所有可能的组合sub，用递归来实现的代码如下：
{% highlight java %}
//递归函数
public static void recursion(int[] input, int pick, int start, int count, int[] sub) {
    if (count == pick) {
        System.out.print(Arrays.toString(sub));
        return;
    }
    for (int i = start; i < input.length; ++i) {
        sub[count++] = input[i];
        recursion(input, pick, ++start, count, sub);
        --count;
    }
}

public static void main(String[] args) {
    int[] inputArray = new int[] { 9, 7, 2 };
    for (int i = 1; i <= inputArray.length; i++) {
        int[] sub = new int[i];
        recursion(inputArray, i, 0, 0, sub);
    }
}
{% endhighlight %}
打印结果如下，共7个组合：

    [9],[7],[2],[9, 7],[9, 2],[7, 2],[9, 7, 2]

需求2：找出是否存在一组合sub，使得等式2 * sum(sub) = sum(input)成立，打印该组合（用"+"号连接）。

即在需求1的基础上，一旦递归到满足条件的组合，即返回结果同时结束递归。改进代码如下：
{% highlight java %}
//递归函数
public static boolean recursion(Integer[] input, int pick, int start, int count, Integer[] sub) {
    if (count == pick) {
        if (2 * sum(sub) == sum(input)) {
            return true;
        }
        return false;
    }
    boolean found = false;
    for (int i = start; i < input.length; ++i) {
        sub[count++] = input[i];
        if (recursion(input, pick, ++start, count, sub)) {
            found = true;
            break;
        }
        --count;
    }
    return found;
}

// 求和
public static int sum(Integer[] input) {
    Integer total = 0;
    for (Integer each : input) {
        total += each;
    }
    return total;
}

// 重写数组差 by index
public static Integer[] subtraction(Integer[] full, Integer[] sub) {
    List<Integer> fullList = new ArrayList<Integer>(Arrays.asList(full));
    for (int i = 0; i < sub.length; i++) {
        for (int j = 0; j < fullList.size(); j++) {
            if (sub[i].equals(fullList.get(j))) {
                fullList.remove(j);
                break;
            }
        }
    }
    return fullList.toArray(new Integer[]{});
}

// 使用"+"连接数组元素
public static String array2expression(Integer[] arr) {
    String expression = "";
    for (Integer str : arr) {
        expression += str + "+";
    }
    return expression.substring(0, expression.length() - 1);
}

public static void main(String[] args) {
    Integer[] inputArray = new Integer[] { 9, 7, 2 };
    for (int i = 1; i <= inputArray.length / 2; i++) {
        Integer[] branch = new Integer[i];
        if (recursion(inputArray, i, 0, 0, branch)){
            Integer[] sub = subtraction(inputArray, branch);
            print(String.format("%s = %s", array2expression(branch), array2expression(sub)));
            break;
        }
    }
}
{% endhighlight %}
打印结果:

    9 = 7+2

#### 总结：通过给递归函数定义返回值，在递归过程中判断该返回值可结束递归。
<br />
以上。

您有任何问题或建议, 请给我写[邮件](mailto:yinwer81@gmail.com)。

