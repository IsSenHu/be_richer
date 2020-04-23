## jvisualvm工具的使用

[TOC]

### 一、jvisualvm工具介绍

VisualVM是一个集成命令行JDK工具和轻量级分析功能的可视化工具。为开发和生产时间的使用而设计。安装jdk后直接在终端输入

`jvisualvm`

就可以启动如图所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8A%E5%8D%8811.35.35.png)

### 二、使用jvisualvm分析堆栈溢出

我们先准备好代码如下所示：

```java
public class MyTest2 {

    private int length;

    public int getLength() {
        return length;
    }

    public void test() {
        this.length++;
        try {
            Thread.sleep(300);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        test();
    }

    public static void main(String[] args) {
        // -Xss160k
        MyTest2 myTest2 = new MyTest2();
        try {
            myTest2.test();
        } catch (Throwable ex) {
            System.out.println(myTest2.getLength());
            ex.printStackTrace();
        }
    }
}
```

使用JVM参数-Xss160k指定堆栈的大小为160k并运行MyTest2，因为我们这里睡眠了300眠，所以不会立刻发生堆栈溢出，以便于我们能够使用jvisualvm进行监控。启动后我们在jvisualvm的应用程序窗口的本地中可以看到刚运行的MyTest2，我们双击打开监控视图。

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8A%E5%8D%8811.44.11.png)

打开后得到如下所示的概述视图：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8A%E5%8D%8811.50.50.png)

可以看到一些基本的信息例如：PID、主机、主类、JVM版本、Java版本、JVM参数等。并且还有概述、监视、线程、抽样器以及Profiler面板。

我们点击监视打开监视的视图如图所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8A%E5%8D%8811.56.56.png)

可以实时的监控程序正常运行时间、CPU使用情况和垃圾回收活动、堆空间和元空间的使用情况、类的加载和卸载信息以及线程的运行情况。

我们点击线程打开线程的视图如图所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8B%E5%8D%883.17.08.png)

可以实时的看到每个线程的运行时长和状态，点击线程 Dump还可以看到每个线程的详细运行情况，如下所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8B%E5%8D%883.19.17.png)

这里我们查看main线程的运行情况，正处于休眠的状态，这是因为MyTest2.test()方法正在无限进行递归，在代码的25行一直递归调用MyTest2.test()方法。不久后出现了java.lang.StackOverflowError堆栈溢出错误，导致程序结束。

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8B%E5%8D%883.23.16.png)

出现这个错误的原因是因为JVM堆栈的容量不是无限的，有一个最大值。每次调用方法都会将对应的一个栈帧压入堆栈的栈顶，当超过堆栈容量的最大值的时候，就会发生java.lang.StackOverflowError堆栈溢出错误。

### 三、使用jvisualvm打开转储文件

我们先写一个程序来让他生成转储文件，代码如下所示：

```java
public class MyTest1 {

    public static void main(String[] args) {
        // -Xms5m -Xmx5m -XX:+HeapDumpOnOutOfMemoryError
        // 堆内存溢出
        List<MyTest1> list = new ArrayList<>();
        for (;;) {
            list.add(new MyTest1());
        }
    }

}
```

这个程序不断的生成MyTest1的实例并将其放入的list集合中，直到超过最大的堆容量就是发生堆内存溢出的错误。我们通过加上JVM参数-XX:+HeapDumpOnOutOfMemoryError，一旦程序发生了堆内存溢出，就会在本地生成转储文件，我们就可以使用jvisualvm打开这个转储文件来进行分析。

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8B%E5%8D%883.33.47.png)

我们点击文件然后点击装入：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8B%E5%8D%883.37.00.png)

选择转储文件：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8B%E5%8D%883.39.08.png)

打开后就可以看到如下所示的面板：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8B%E5%8D%883.43.30.png)

红色圈起来的地方显示了发生的具体异常是什么以及异常错误的线程。此外我们还可以看到转储文件生成的时间、环境、系统属性、堆转储上的线程等等信息。我们还可以在右边的检查窗口下点击查找，查看到最大的对象。

我们点击类得到如下所示的信息：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8B%E5%8D%883.51.21.png)

可以看到所有的类的实例数，大小，保留的大小。MyTest1确实生成了很多实例并且都保留在了堆内存中。

我们点击实例数或者双击类下的MyTest1可以查看每个实例的具体信息，如下所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8B%E5%8D%883.56.16.png)

至于OQL控制台，就是执行类似于SQL语句的对象查找，后面我们再讲这个讲。

### 四、使用jvisualvm检查和分析死锁

jvisualvm既然可以实时监测到线程的运行情况，那我们可以使用它来检查和分析死锁，我们首先要做的事情就是要准备能产生死锁的代码，如下所示：

```java
public class MyTest3 {

    public static void main(String[] args) {
        new Thread(A::method, "Thread-A").start();
        new Thread(B::method, "Thread-B").start();
    }
}

class A {

    public static synchronized void method() {
      	// 进入这个方法后线程Thread-A将会获得class A的锁
        System.out.println("method from A");
        System.out.println(A.class);

        try {
          	// 睡眠5秒然后尝试获取class B的锁，但是这里获取不到，因为线程Thread-B已经获取
          	// 到了class B的锁并且未释放
            Thread.sleep(5000);
            B.method();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

class B {

    public static synchronized void method() {
      	// 同理class A
        System.out.println("method from B");
        System.out.println(B.class);

        try {
          	// 同理class A
            Thread.sleep(5000);
            A.method();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

```bash
# 我们运行这个程序后，果然发生了死锁。输出结果也是和我们预期的相同
method from A
class com.gapache.commons.jvm.memory.A
method from B
class com.gapache.commons.jvm.memory.B
```

然后我们用jvisualvm打开MyTest3查看实时的运行情况，如下所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8B%E5%8D%884.06.33.png)

然后我们立即就看了线程报红色警告，点进线程面膜就看到提示检测到死锁！然后再看线程Thread-A和线程Thread-B都处于监视状态。我们点击线程 Dump并找到线程Thread-A和线程Thread-B来看看具体的运行情况如下所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8B%E5%8D%884.10.01.png)

其中线程Thread-B处于阻塞状态，在MyTest3的第20行等待锁定class A，在第40行已锁定class B。线程Thread-A也同理。可以看到jvisualvm可一立即检测到死锁，并且能够查看发生死锁的相关线程的具体情况。

###  五、使用jvisualvm分析方法区溢出

这里我们要是方法区溢出主要是通过指定元空间的最大大小来产生这个错误。关于为什么要指定元空间的最大大小我们这里先不说。

大家都知道元空间我们会用来存类的信息，所以我们使用cglib来不断的动态生成类，并存放在元空间，至少元空间的大小不断的变大，直到超过我们设置的最大值二而发生溢出的错误，先上代码，如下所示：

```java
public class MyTest4 {

    public static void main(String[] args) {
        // -XX:MaxMetaspaceSize=200m
        while (true) {
            Enhancer enhancer = new Enhancer();
            enhancer.setSuperclass(MyTest4.class);
            enhancer.setUseCache(false);
            enhancer.setCallback((MethoInterceptor) (obj, method, args1, proxy) -> proxy.invokeSuper(obj, args1));

            System.outprintln("hello world");

            enhancer.create();
        
    }
}
```

设置JVM参数-XX:MaxMetaspaceSize=200m指定元空间的最大大小为200m，然后运行此程序。用jvisualvm进行监控如下所示：

[演示视频](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E5%B1%8F%E5%B9%95%E5%BD%95%E5%88%B62020-04-22%E4%B8%8B%E5%8D%884.29.30.mov)

默认显示的是堆内存的实时用情况，我们可以手动点击Metaspace切换元空间的实时使用情况。我们可以看到元空间的使用内存不断地再增加，这是因为我们利用了cglib在不断的动态生成类。一段时间后到达元空间的最大大小，这时候就出现了内存溢出，如图所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-22%E4%B8%8B%E5%8D%884.35.38.png)

关于元空间的说明我们会在后面单独进行说明。