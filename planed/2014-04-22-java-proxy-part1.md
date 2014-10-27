---
layout: post
title: "Java代理(1)"
---

代理包含两个对象(common sense): 代理proxy 和 被代理 即委托target。

代理有两种方式:
	
	静态代理(static proxy): 
		程序员创建好代理proxy, 编译时生成代理proxy的字节码文件。
		代理proxy和委托target须实现同一接口, 故代理proxy只能为特定的接口服务,	耦合度高;
    故若委托target数量较多, 逐个实现代理proxy, 工作量大。
		代理proxy和委托target实现了相同的方法, 故代码重复。
	动态代理(dynamic proxy)
		代理proxy一般在程序运行时, 通过反射机制动态生成。
		代理proxy实现InvocationHandler接口(自定义时), 与委托target各不相干。
		jdk的动态代理, cglib的动态代理, cglib不需要定义目标类的统一接口。
应用场景:

	代理的意义在于管理(控制|限制)对委托target的访问, 常用于日志, 过滤, 事务逻辑
	的拦截处理等; 不需要修改委托target代码(使得委托本身可以专注于业务), 即可在
	调用委托target前后, 通过代理增加其它处理逻辑, 比如权限控制, 统计, 过滤代码等。

	由静态代理和动态代理各自的特点可得出:
	静态代理较呆板, 用于委托较少时, 相对动态代理有一定性能优势;
	动态代理灵活, 应用广泛, 如：需对委托中方法进行批量处理(统计每个方法的执行时间,
	打印出每个方法的执行日志, 对以update开头的方法使用事务等等)。

以下为静态代理实例。</br>
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
委托target: 
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
实现代理proxy: 
{% highlight java %}
/**
 * Copyright (c) 2005-2013 dunkcoder.com
 * Licensed under (CC BY-NC-SA 3.0)
 */
package com.dunkcoder.modules.blog.proxy;

import com.dunkcoder.modules.blog.buinese.ManagerIntf;

/**
 * @author yin@dunkcoder
 */
public class ManagerStaticProxy implements ManagerIntf {
  private ManagerIntf manager;
  public ManagerStaticProxy(ManagerIntf manager) {
    super();
    this.manager = manager;
  }
  public void approve() {
    System.out.println("approve before");
    manager.approve();
    System.out.println("approve after");
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
import com.dunkcoder.modules.blog.proxy.ManagerStaticProxy;

/**
 * @author yin@dunkcoder
 */
public class ManagerStaticProxyDemo {
  public static void main(String[] args) {
    ManagerIntf target = new ManagerImpl();
    ManagerIntf managerProxy = new ManagerStaticProxy(target);
    managerProxy.approve();
  }
}
{% endhighlight %}

静态代理方法：
定义统一接口;
定义委托target, 委托target实现该接口;
实现代理proxy, 代理proxy实现该接口, 同时定义传入该接口的构造方法。

调用时：
声明接口引用, 使用委托实现, 此为委托实例。
声明接口引用, 使用代理实现, 此为代理实例, 并传入委托实例。
使用代理实例。

接下来排版到下一篇: [Java代理(2)]()

以上。