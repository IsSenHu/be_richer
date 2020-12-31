## jstack命令的使用

[TOC]

### 一、命令的介绍与说明

以下是jstack的使用说明：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%884.55.49.png)

命令格式：

```bash
jstack [option] pid
jstack -F [option] pid
jstack [option] <executable> <core>
jstack [option] remote-hostname-or-ip
```

参数：

- option：选项参数
- pid：进程id
- executable：产生核心dump的java可执行文件
- core：需要打印配置信息的核心文件
- remote-hostname-or-ip：远程调试的主机名或ip

option参数：

- F：强制线程转储。当jstack pid不响应（进程已挂起）时使用
- m：打印Java和本机帧（混合模式）
- l：长清单，打印有关锁的其他

### 二、jstack查看输出

我们先准备好测试程序，这个程序会产生死锁的问题，如下所示：

```java
public class MyTest3 {

    public static void main(String[] args) {
        new Thread(A::method, "Thread-A").start();
        new Thread(B::method, "Thread-B").start();
    }
}

class A {

    public static synchronized void method() {
        System.out.println("method from A");
        System.out.println(A.class);

        try {
            Thread.sleep(5000);
            B.method();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

class B {

    public static synchronized void method() {
        System.out.println("method from B");
        System.out.println(B.class);

        try {
            Thread.sleep(5000);
            A.method();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

运行这个程序，然后输出：

```java
method from A
class com.gapache.commons.jvm.memory.A
method from B
class com.gapache.commons.jvm.memory.B
```

我们在命令终端执行jstack -l pid就可以看到这个程序的线程信息：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%885.12.47.png)

### 三、jstack统计线程数

jstack -l 12269 | grep 'java.lang.Thread.State' | wc -l，使用这个命令可以统计线程数：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%885.15.33.png)

### 四、jstack检测死锁	

我们在命令终端执行jstack -l pid就可以看到这个程序的线程信息：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%885.18.28.png)

可以看到打印出的信息自动检测到了死锁，关于死锁的分析我们前面已经讲过了，所以这里我们不用再讲了，忘记了的同学可以去复习一遍。

### 五、jstack检测cpu高

步骤一：查看cpu占用高进程

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%885.23.56.png)

步骤二：查看cpu占用高线程

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%885.25.35.png)

步骤三：转换线程ID

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%885.27.16.png)

步骤四：定位cpu占用线程

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%885.29.43.png)

