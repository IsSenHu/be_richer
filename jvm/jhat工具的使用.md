## jhat工具的使用

[TOC]

### 一、命令的介绍与说明

是用来分析java堆的命令，可以启动一个服务并将堆中的对象以html的形式显示出来，包括对象的数量，大小等等，并支持对象查询语言。

输入jhat -h|-help来查看这个命令的说明：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-29%E4%B8%8A%E5%8D%8810.57.40.png)

命令格式：

```bash
jhat [option ...] file
```

### 二、使用jhat

首先我们要先生成一个heap.map的堆转储文件，运行

`jmap -dump:file=heap.map pid`

即可生成这个文件，然后我们输入

`jhat -port 8888 heap.map`

就可以启动一个http服务了，端口号为我们指定的8888，如果不指定使用默认端口号7000：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-29%E4%B8%8A%E5%8D%8811.10.53.png)

可以看到服务已经成功，我们打开浏览器，访问[localhost:8888](localhost:8888)，就可以看到如图所示的网页：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-29%E4%B8%8A%E5%8D%8811.13.18.png)

### 三、查看类的信息

我们点击相应的类，例如点击[class com.gapache.commons.jvm.memory.MyTest5](http://localhost:8888/class/0x795719590)，就可以看到该类的详细信息：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-29%E4%B8%8A%E5%8D%8811.16.25.png)

### 四、查看堆统计信息

我们点击[Show heap histogram](http://localhost:8888/histo/)，可以看到堆的统计信息：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-29%E4%B8%8A%E5%8D%8811.19.51.png)

### 五、查看垃圾回收总结

我们点击[Show finalizer summary](http://localhost:8888/finalizerSummary/)，可以看到垃圾回收总结：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-29%E4%B8%8A%E5%8D%8811.21.50.png)

### 六、执行OQL语句查询对象

我们点击[Execute Object Query Language (OQL) query](http://localhost:8888/oql/)，就可以进入查询页面，然后我们输入查询语句：

`select s from java.lang.String s where s.value.length >= 100`

查询字符串长度大于100的字符串：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-29%E4%B8%8A%E5%8D%8811.34.41.png)

除此之外还有其它的功能，例如：

- 查看包括平台的所有类[All classes including platform](http://localhost:8888/allClassesWithPlatform/)

- 展示根集合的所有成员[Show all members of the rootset](http://localhost:8888/showRoots/)
- 展示所有的类的实例[Show instance counts for all classes (including platform)](http://localhost:8888/showInstanceCounts/includePlatform/)/[Show instance counts for all classes (excluding platform)](http://localhost:8888/showInstanceCounts/)

