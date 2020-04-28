## jcmd命令的使用

[TOC]

### 一、命令的介绍与说明

jcm从JDK 1.7开始增加的命令，首先要准备我们进行实验所需的程序代码：

```java
public class MyTest5 {

    public static void main(String[] args) throws InterruptedException {
        while (true) {
            System.out.println("hello world");
            Thread.sleep(100);
        }
    }
}
```

我们运行这个程序，然后输入jcmd pid help就可以列出当前的java进程可以执行的操作：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-28%E4%B8%8B%E5%8D%884.42.37.png)

运行jcmd pid help operation就可以查看具体命令的选项：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-28%E4%B8%8B%E5%8D%884.45.52.png)

### 二、查看VM启动参数

jcmd pid VM.flags 查看VM启动参数：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-28%E4%B8%8B%E5%8D%884.51.18.png)

### 三、查看JVM性能相关的一些参数

jcmd pid PerfCounter.print 查看JVM性能相关的一些参数：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-28%E4%B8%8B%E5%8D%884.52.49.png)

### 四、查看JVM的运行时长

Jcmd pid VM.uptime 查看JVM的运行时长：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-28%E4%B8%8B%E5%8D%884.54.26.png)

### 五、查看系统中类的统计信息

jcmd pid GC.class_histogram 查看系统中类的统计信息：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-28%E4%B8%8B%E5%8D%884.57.03.png)

### 六、查看线程的堆栈信息

jcmd pid Thread.print 查看线程的堆栈信息：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-28%E4%B8%8B%E5%8D%884.58.58.png)

### 七、导出Heap dump文件

jcmd pid GC.heap_dump filename 导出Heap dump文件，导出的文件可以通过jvisualvm查看：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-28%E4%B8%8B%E5%8D%885.12.16.png)

### 八、查看JVM的属性信息

jcmd pid VM.system_properties 查看JVM的属性信息：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-28%E4%B8%8B%E5%8D%885.15.56.png)

### 九、查看目标JVM进程的版本信息

jcmd pid VM.version 查看目标JVM进程的版本信息：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-28%E4%B8%8B%E5%8D%885.17.28.png)

### 十、查看JVM启动的命令行参数信息

jcmd pid VM.command_line 查看JVM启动的命令行参数信息：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-28%E4%B8%8B%E5%8D%885.19.00.png)

