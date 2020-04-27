## jmap命令的使用

[TOC]

### 一、命令的介绍与使用说明

以下是jmap命令的使用说明：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8A%E5%8D%889.51.08.png)

命令格式：

```bash
jmap [ option ] pid
jmap [ option ] executable core
jmap [ option ] [server-id@]remote-hostname-or-IP
```

参数：

- option：选项参数，不可同时使用多个选项参数
- pid：java进程id，使用jps相关命令可以选快速获取
- executable：产生核心dump的java可执行文件
- core：需要打印配置信息的核心文件
- remote-hostname-or-ip：远程调试的主机名或ip
- server-id：可选的唯一id，如果相同的远程主机上运行了多台调试服务器，用此选项参数标识服务器

option参数：

- heap：显示Java堆详细信息
- histo：显示堆中对象的统计信息
- clstats：显示类加载器的统计信息
- finalizerinfo：显示在F-Queue队列等待Finalizer线程执行finalizer方法的对象
- dump：生成堆转储快照
- F：当-dump没有响应时，强制生成dump快照

### 二、-dump

dump堆到文件，format指定输出格式，live指明是活着的对象，file指定文件名。

jmap -dump:live,format=b,file=dump.hprof pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8A%E5%8D%8810.10.42.png)

可以看到已经成功dump堆到文件dump.hprof中，我接下来就可以使用jvisualvm来打开这个文件并进行分析：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8A%E5%8D%8810.15.52.png)

### 三、-heap

打印heap的概要信息，GC使用的算法，heap的配置及使用情况，可以用此来判断内存目前的使用情况以及垃圾回收情况。

jmap -heap pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8A%E5%8D%8810.25.34.png)

### 三、-finalizerinfo

打印等待回收的对象信息。

jamp -finalizerinfo pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8A%E5%8D%8810.33.14.png)

Number of objects pending for finalization: 0 ，说明当前F-QUEUE队列中并没有等待Fializer线程执行finalizer方法的对象。

### 四、-histo

打印堆的对象的统计信息，包括对象数、内存大小等等。jmap -histo:live这个命令执行，JVM会先触发gc，然后再统计信息：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8A%E5%8D%8811.25.52.png)

可以看到每个类有多少个实例，占用了多少的内存等信息。

### 五、-clstats

打印Java的类加载器的智能统计信息。对于每个类加载器而言，它的名称、活跃度、地址、父类加载器、它所加载的类的数量和大小都会被打印。此外，包含的字符串数量和大小也会被打印。

jamp -clstats pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8A%E5%8D%8811.33.41.png)

