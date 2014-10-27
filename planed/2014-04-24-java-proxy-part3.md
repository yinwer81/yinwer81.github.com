---
layout: post
title: "Java代理(3)"
---

续上一篇: [Java代理(2)]()

先看cglib动态代理实例。</br>
统一接口:  
{% highlight java %}
/**
 * Copyright (c) 2005-2013 dunkcoder.com
 * Licensed under (CC BY-NC-SA 3.0)
 */
package com.dunkcoder.modules.blog.buinese;

/**
 * @author yin@dunkcoder
 */
public interface ManagerIntf {
  public void approve();
}
{% endhighlight %}
实现委托类target: 
{% highlight java %}
/**
 * Copyright (c) 2005-2013 dunkcoder.com
 * Licensed under (CC BY-NC-SA 3.0)
 */
package com.dunkcoder.modules.blog.buinese;

/**
 * @author yin@dunkcoder
 */
public class ManagerImpl implements ManagerIntf {
  public void approve() {
    doApproveJob();
  }
  private void doApproveJob(){
    System.out.println("approve");
  }
}
{% endhighlight %}
实现代理类proxy: 
{% highlight java %}
/**
 * Copyright (c) 2005-2013 dunkcoder.com
 * Licensed under (CC BY-NC-SA 3.0)
 */
package com.dunkcoder.modules.blog.proxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

/**
 * @author yin@dunkcoder
 */
public class PerformanceHandler implements InvocationHandler {
  private Object proxy;
  public Object bind(Object proxy) {
    this.proxy = proxy;
    ClassLoader loader = proxy.getClass().getClassLoader();
    Class<?>[] interfaces = proxy.getClass().getInterfaces();
    return Proxy.newProxyInstance(loader, interfaces, this);
  }
  public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    Object result = null;
    try {
      System.out.println(method.getName() + " invoked before");
      result = method.invoke(this.proxy, args);
      System.out.println(method.getName() + " invoked after");
    } catch (Exception ex) {
      ex.printStackTrace();
      throw ex;
    }
    return result;
  }
}
{% endhighlight %}
调用方法:
{% highlight java %}
/**
 * Copyright (c) 2005-2013 dunkcoder.com
 * Licensed under (CC BY-NC-SA 3.0)
 */
package com.dunkcoder.modules.blog.demo;

import com.dunkcoder.modules.blog.buinese.ManagerImpl;
import com.dunkcoder.modules.blog.buinese.ManagerIntf;
import com.dunkcoder.modules.blog.proxy.PerformanceHandler;

/**
 * @author yin@dunkcoder
 */
public class PerformanceHandlerDemo {
  public static void main(String[] args) {
    ManagerIntf manager = new ManagerImpl();
    PerformanceHandler handler = new PerformanceHandler();
    ManagerIntf managerProxy = (ManagerIntf) handler.bind(manager);
    managerProxy.approve();
  }
}
{% endhighlight %}

OK, 有人说了: 静态代理和动态代理差在于代理类实现不同, 都要实现接口(静态代理中代理类proxy
实现和委托类target相同接口, 动态代理中代理类proxy实现InvocationHandler接口), 动态代理的好处
到底体现在哪里? 或者说, 动态代理到底如何用好?

在统一接口和委托类中增加一个方法试试:
统一接口中增加方法:
{% highlight java %}
public void reject();
{% endhighlight %}
委托类target中实现:
{% highlight java %}
public void reject() {
  doRejectJob();
}
private void doRejectJob(){
  System.out.println("reject");
}
{% endhighlight %}
好嘛, 静态代理中代理类proxy报编译错了吧, 动态代理中代理类proxy不需要修改吧。</br>
接下来在调用方法中调用reject方法:
{% highlight java %}
managerProxy.reject();
{% endhighlight %}
Run as->Java Application, OK, approve()和reject()都被拦截了吧！试想:</br>
1. 在委托类target方法调用前后记录时间, 时间差不就是方法执行时间吗?</br>
2. 将args记录下来, 不就是委托类target方法入参吗?</br>
3. 在委托类target方法前后执行其它方法, 不就是面向切面吗? </br>
   这样一来, 你可以做任何想做的事: 如日志, 权限检查, 事务等等。

jdk动态代理入门了, 那么cglib动态代理不需要统一接口是如何做呢? 请看[Java代理3]()

以上。