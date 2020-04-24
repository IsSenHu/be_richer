## jps命令的使用

[TOC]

jps(Java Virtual Machine Process Status Tool)，它的作用是显示当前系统的java进程情况及进程id。我们可以通过它来查看我们到底启动了几个java进程（因为每一个java程序都会独占一个java虚拟机实例），并可通过opt来查看这些进程的详细启动参数。

我们先准备一个用于测试的程序，并运行该程序：

```java
public class MyTest5 {

    public static void main(String[] args) {
        while (true) {
            System.out.println("hello world");
        }
    }
}
```

可以直接在命令终端输入jps就可以使用，如下所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-24%E4%B8%8A%E5%8D%8811.48.11.png)

### 一、jps -q

只显示pid

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-24%E4%B8%8A%E5%8D%8811.49.44.png)

### 二、jus -m

输出传递给main方法的参数，在嵌入式jvm上可能是null

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-24%E4%B8%8A%E5%8D%8811.51.17.png)

### 三、jus -l

输出应用程序main class的完整package名或者应用程序的jar文件完整路径名

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-24%E4%B8%8A%E5%8D%8811.53.58.png)

### 四、jps -v

输出传递给JVM的参数

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-24%E4%B8%8A%E5%8D%8811.55.21.png)

### 五、jps -V

隐藏输出传递给JVM的参数

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-24%E4%B8%8B%E5%8D%8812.00.55.png)

