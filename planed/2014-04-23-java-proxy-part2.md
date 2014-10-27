---
layout: post
title: "Java代理(2)"
---

续上一篇: [Java代理(1)]()

先看jdk动态代理实例。</br>
接口:
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

动态代理方法：
定义接口;
定义委托target, 委托target实现该接口;
实现代理proxy, 代理proxy实现InvocationHandler接口, 覆盖invoke方法。

调用时：
声明接口引用, 使用委托实现, 此为委托实例。
声明接口引用, 使用代理实现, 此为代理实例, 并传入委托实例。
使用代理实例。

OK, 聪明人说: 静态和动态代理差在于代理类实现不同, 都要实现接口(静态代理中代理proxy
实现和委托target相同接口, 动态代理中代理proxy实现InvocationHandler接口), 动态代理的好处
到底体现在哪里? 或者说, 动态代理到底如何用好?

在接口中增加一个方法试试:
接口中增加方法:
{% highlight java %}
public void reject();
{% endhighlight %}
委托target中实现:
{% highlight java %}
public void reject() {
  doRejectJob();
}
private void doRejectJob(){
  System.out.println("reject");
}
{% endhighlight %}
好嘛, 静态代理中代理proxy报编译错了吧, 动态代理中代理proxy不需要修改吧。</br>
接下来在调用方法中调用reject方法:
{% highlight java %}
managerProxy.reject();
{% endhighlight %}
Run as->Java Application, OK, approve()和reject()都被拦截了吧！试想:</br>
1. 在委托target方法调用前后记录时间, 时间差不就是方法执行时间吗?</br>
2. 将args记录下来, 不就是委托target方法入参吗?</br>
3. 在委托target方法前后执行其它方法, 不就是面向切面吗? </br>
   这样一来, 你可以做任何想做的事: 如日志, 权限检查, 事务等等。

嗯, 好用吧, 简单吧。总结一下jdk动态代理过程: 
1. 实例化委托target对象(manager), 实例化代理proxy对象(handler)
2. 传入委托target对象(manager)生成代理对象managerProxy
3. 通过代理对象managerProxy调用委托target对象(manager)的方法

接下来, 我们看看jdk动态代理到底是如何生成代理对象managerProxy的:
java.lang.reflect.Proxy类有四个静态方法:
getProxyClass(ClassLoader, Class<?>...)
newProxyInstance(ClassLoader, Class<?>[], InvocationHandler)
isProxyClass(Class<?>)
getInvocationHandler(Object)

通过Proxy.newProxyInstance生成代理对象managerProxy, 更深一步:
newProxyInstance方法中调用getProxyClass取得代理proxy Class, 匹配构造函数, 取得实例
在getProxyClass方法中调用ProxyGenerator.generateProxyClass生成代理proxy对象字节码和实例。
反编译sun.misc.ProxyGenerator, generateProxyClass静态方法可看到jdk是如何生成字节码的。


接下来, 我们看看jdk生成的代理proxy, 以搞清楚InvocationHandler.invoke方法是如何被调用的。
{% highlight java %}
/**
 * Copyright (c) 2005-2013 dunkcoder.com
 * Licensed under (CC BY-NC-SA 3.0)
 */
package com.dunkcoder.modules.blog.proxy;

import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import sun.misc.ProxyGenerator;

/**
 * @author yin@dunkcoder
 */
@SuppressWarnings("restriction")
public class ProxyGeneratorUtil {
  public static void writeProxyClassToHD(String path, Class<?>[] clazz) 
        throws FileNotFoundException, IOException {

    // String propKey = "sun.misc.ProxyGenerator.saveGeneratedFiles";
    // System.getProperties().put(propKey, true);

    byte[] classFile = ProxyGenerator.generateProxyClass("$Proxy0", clazz);
    FileOutputStream out = new FileOutputStream(path);
    if (out != null) {
      out.write(classFile);
      out.flush();
      out.close();
    }
  }
}
{% endhighlight %}

{% highlight java %}
Class<?>[] clazz = ManagerImpl.class.getInterfaces();
ProxyGeneratorUtil.writeProxyClassToHD("c:/$Proxy0.class", clazz);
{% endhighlight %}

将c:/$Proxy0.class反编译, 整理如下:
{% highlight java %}
import com.dunkcoder.modules.blog.buinese.ManagerIntf;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.lang.reflect.UndeclaredThrowableException;

public final class $Proxy0 extends Proxy implements ManagerIntf {
  private static Method m4;
  private static Method m1;
  private static Method m3;
  private static Method m0;
  private static Method m2;

  public $Proxy0(InvocationHandler paramInvocationHandler) throws {
    super(paramInvocationHandler);
  }

  public final void reject() throws {
    try {
      this.h.invoke(this, m4, null);
      return;
    }catch (Error|RuntimeException localError){
      throw localError;
    }catch (Throwable localThrowable){
      throw new UndeclaredThrowableException(localThrowable);
    }
  }

  public final boolean equals(Object paramObject) throws {
    try{
      return ((Boolean)this.h.invoke(this, m1, new Object[] { paramObject })).booleanValue();
    }catch (Error|RuntimeException localError){
      throw localError;
    }catch (Throwable localThrowable){
      throw new UndeclaredThrowableException(localThrowable);
    }
  }

  public final void approve() throws {
    try{
      this.h.invoke(this, m3, null);
      return;
    }catch (Error|RuntimeException localError){
      throw localError;
    }catch (Throwable localThrowable){
      throw new UndeclaredThrowableException(localThrowable);
    }
  }

  public final int hashCode() throws {
    try{
      return ((Integer)this.h.invoke(this, m0, null)).intValue();
    }catch (Error|RuntimeException localError){
      throw localError;
    }catch (Throwable localThrowable){
      throw new UndeclaredThrowableException(localThrowable);
    }
  }

  public final String toString() throws {
    try{
      return (String)this.h.invoke(this, m2, null);
    }catch (Error|RuntimeException localError){
      throw localError;
    }catch (Throwable localThrowable){
      throw new UndeclaredThrowableException(localThrowable);
    }
  }

  static{
    try{
      m4 = Class.forName("com.dunkcoder.modules.blog.buinese.ManagerIntf").getMethod("reject", new Class[0]);
      m1 = Class.forName("java.lang.Object").getMethod("equals", new Class[] { Class.forName("java.lang.Object") });
      m3 = Class.forName("com.dunkcoder.modules.blog.buinese.ManagerIntf").getMethod("approve", new Class[0]);
      m0 = Class.forName("java.lang.Object").getMethod("hashCode", new Class[0]);
      m2 = Class.forName("java.lang.Object").getMethod("toString", new Class[0]);
      return;
    }catch (NoSuchMethodException localNoSuchMethodException){
      throw new NoSuchMethodError(localNoSuchMethodException.getMessage());
    }catch (ClassNotFoundException localClassNotFoundException){
      throw new NoClassDefFoundError(localClassNotFoundException.getMessage());
    }
  }
}
{% endhighlight %}

到这里, 了解了jdk动态代理的过程和机制, 我们看看委托target又是谁呢?
看代码：动态代理时如何取得委托target?
{% highlight java %}
/**
 * Copyright (c) 2005-2013 dunkcoder.com
 * Licensed under (CC BY-NC-SA 3.0)
 */
package com.dunkcoder.modules.blog.proxy;

import java.lang.reflect.Field;
import org.springframework.aop.framework.AdvisedSupport;
import org.springframework.aop.framework.AopProxy;
import org.springframework.aop.support.AopUtils;

/**
 * @author yin@dunkcoder
 */
public class ProxyTargetUtil {
  public static Object getProxyTarget(Object proxy) throws Exception {
    Object result = null;
    if (!AopUtils.isAopProxy(proxy)) {
      result = proxy;
    } else {
      if (AopUtils.isJdkDynamicProxy(proxy)) {
         result = getJdkProxyTarget(proxy);
      } else {
         result = getCglibProxyTarget(proxy);
      }
    }
    return result;
  }

  private static Object getCglibProxyTarget(Object proxy) throws Exception {
    Field h = proxy.getClass().getDeclaredField("CGLIB$CALLBACK_0");
    h.setAccessible(true);
    Object interceptor = h.get(proxy);

    Field advised = interceptor.getClass().getDeclaredField("advised");
    advised.setAccessible(true);

    AdvisedSupport support = (AdvisedSupport) advised.get(interceptor);
    Object target = support.getTargetSource().getTarget();

    return target;
  }

  private static Object getJdkProxyTarget(Object proxy) throws Exception {
    Field h = proxy.getClass().getSuperclass().getDeclaredField("h");
    h.setAccessible(true);
    AopProxy aopProxy = (AopProxy) h.get(proxy);

    Field advised = aopProxy.getClass().getDeclaredField("advised");
    advised.setAccessible(true);

    AdvisedSupport support = (AdvisedSupport) advised.get(aopProxy);
    Object target = support.getTargetSource().getTarget();

    return target;
  }

}
{% endhighlight %}
调用方法:
{% highlight java %}
System.out.println(ProxyTargetUtil.getProxyTarget(managerProxy));
{% endhighlight %}
OK, 终于只剩下cglib动态代理了(不需要统一接口), 看代码:
实现委托target: 
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
装饰者代理proxy:
{% highlight java %}
/**
 * Copyright (c) 2005-2013 dunkcoder.com
 * Licensed under (CC BY-NC-SA 3.0)
 */
package com.dunkcoder.modules.blog.proxy;

import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;

/**
 * @author yin@dunkcoder
 */
public class ManagerDecorator implements MethodInterceptor {
  public Object invoke(MethodInvocation invocation) throws Throwable {
    System.out.println("proceed before");
    Object result = invocation.proceed();
    System.out.println("proceed after");
    return result;
  }
}
{% endhighlight %}
组装调用:
{% highlight java %}
/**
 * Copyright (c) 2005-2013 dunkcoder.com
 * Licensed under (CC BY-NC-SA 3.0)
 */
package com.dunkcoder.modules.blog.demo;

import org.springframework.aop.framework.ProxyFactory;
import com.dunkcoder.modules.blog.buinese.ManagerImpl;
import com.dunkcoder.modules.blog.proxy.ManagerDecorator;
import com.dunkcoder.modules.blog.proxy.ProxyGeneratorUtil;
import com.dunkcoder.modules.blog.proxy.ProxyTargetUtil;

/**
 * @author yin@dunkcoder
 */
public class ManagerWeaver {
  public static void main(String[] args) throws Exception {
    ManagerImpl target = new ManagerImpl();
        
    ProxyFactory proxyFactory = new ProxyFactory();
    proxyFactory.addAdvice(new ManagerDecorator());
    proxyFactory.setTarget(target);

    ManagerImpl managerProxy = (ManagerImpl) proxyFactory.getProxy();
    System.out.println(ProxyTargetUtil.getProxyTarget(managerProxy));
    managerProxy.approve();
    managerProxy.reject();
        
    Class<?>[] clazz = ManagerImpl.class.getInterfaces();
    ProxyGeneratorUtil.writeProxyClassToHD("c:/$Proxy0.class", clazz);  
  }
}
{% endhighlight %}

已经很清楚了, 举例3个我们平时快速代码实现代理方式:
{% highlight java %}
/**
 * Copyright (c) 2005-2013 dunkcoder.com
 * Licensed under (CC BY-NC-SA 3.0)
 */
package com.dunkcoder.modules.blog.demo;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.ArrayList;
import java.util.Collection;
import java.util.List;

/**
 * @author yin@dunkcoder
 */
public class ProxyTest {
  public static void main(String[] args) {
    test1();
    test2();
    test3();
  }

  // 匿名类
  private static void test1() {
    @SuppressWarnings({ "unchecked", "rawtypes" })
    Collection<String> proxy = (Collection<String>) Proxy.newProxyInstance(ProxyTest.class.getClassLoader(),
        new Class[] { Collection.class }, new InvocationHandler() {
          List target = new ArrayList();

          public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            System.out.println("begin " + method.getName());
            Object result = method.invoke(target, args);
            System.out.println("end " + method.getName());
            return result;
          }
        });
    System.out.println(proxy.getClass().getName());
    proxy.add("xyz");
    System.out.println(proxy.size());
  }

  // 静态方法中定义内部类，接收外面传入的目标
  @SuppressWarnings({ "rawtypes", "unchecked" })
  private static void test2() {
    List list = new ArrayList();
    class MyInvocationHandler implements InvocationHandler {
      Collection target = null;

      public MyInvocationHandler(Collection target) {
        this.target = target;
      }

      public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("begin " + method.getName());
        Object result = method.invoke(target, args);
        System.out.println("end " + method.getName());
        return result;
      }
    }

    Collection<String> proxy = (Collection<String>) Proxy.newProxyInstance(ProxyTest.class.getClassLoader(),
        new Class[] { Collection.class }, new MyInvocationHandler(list));

    System.out.println(proxy.getClass().getName());
    proxy.add("xyz");
    System.out.println(proxy.size());
  }

  // 静态方法中定义内部类，接收目标同时返回代理
  @SuppressWarnings({ "rawtypes", "unchecked" })
  private static void test3() {
    List list = new ArrayList();
    class MyInvocationHandler implements InvocationHandler {
      Collection<String> target = null;

      public Collection<String> bind(Collection<String> target) {
        this.target = target;
        Collection<String> proxy = (Collection<String>) Proxy.newProxyInstance(ProxyTest.class.getClassLoader(),
            new Class[] { Collection.class }, this);
        return proxy;
      }

      public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("begin " + method.getName());
        Object result = method.invoke(target, args);
        System.out.println("end" + method.getName());
        return result;
      }

    }
    MyInvocationHandler handler = new MyInvocationHandler();
    Collection<String> proxy = handler.bind(list);

    System.out.println(proxy.getClass().getName());
    proxy.add("xyz");
    System.out.println(proxy.size());
  }
}
{% endhighlight %}
以上。