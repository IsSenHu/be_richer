## jstat命令的使用

[TOC]

### 一、命令的介绍与说明

以下是jstat命令的使用说明：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%882.22.19.png)

命令格式：

```bash
jstat [ option ] [-t] [-h<lines>] [vmid] [<interal> [<count>]]
```

参数：

- option：选项参数，不可同时使用多个选项参数
- vmid：虚拟机唯一id，可以是进程id，或者主机服务名或ip和rmi的端口
- lines：标头行之间的样本数
- interval：采样间隔，单位允许ms和s
- count：终止前要采集的样本数

option参数：

- class：类加载统计
- compiler：编译统计
- gc：垃圾回收统计
- gccapacity：堆内存统计
- gccause：gc原因统计
- gcmetacapacity：元数据空间统计
- gcnew：新生代垃圾回收统计
- gcnewcapacity：新生代内存空间统计
- gcold：老年代垃圾回收统计
- gcoldcapacity：老年代内存空间统计
- gcutil：总结垃圾回收统计
- printcompilation：jvm编译方法统计

### 二、-class

jstat -class pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%883.22.12.png)

- Loaded：加载class的数量
- Bytes：所占用空间大小
- Unloaded：未加载数量
- Bytes：未加载占用的空间
- Time：时间

### 三、-compiler

jstat -compiler pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%883.25.51.png)

- Compiled：编译数量
- Failed：失败数量
- Invalid：不可用数量
- Time：时间
- FailedType：失败类型
- FailedMethod：失败的方法

### 四、-gc

jstat -gc pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%883.30.35.png)

- S0C：第一个幸存区的大小
- S1C：第二个幸存区的大小
- S0U：第一个幸存区的使用大小
- S1U：第二个幸存区的使用大小
- EC：伊甸园区的大小
- EU：伊甸园区的使用大小
- OC：老年代大小
- OU：老年代使用大小
- MC：方法区大小
- MU：方法区使用大小
- CCSC：压缩类空间大小
- CCSU：压缩类空间使用大小
- YGC：年轻代垃圾回收次数
- YGCT：年轻代垃圾回收消耗时间
- FGC：老年代垃圾回收次数
- FGCT：老年代垃圾回收消耗时间
- GCT：垃圾回收消耗总时间

### 五、-gccapacity

jstat -gccapacity pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%883.36.00.png)

- NGCMN：新生代最小容量
- NGCMX：新生代最大容量
- NGC：当前新生代容量
- S0C：第一个幸存区大小
- S1C：第二个幸存区的大小
- EC：伊甸园区的大小
- OGCMN：老年代最小容量
- OGCMX：老年代最大容量
- OGC：当前老年代大小
- OC：当前老年代大小
- MCMN：最小元数据容量
- MCMX：最大元数据容量
- MC：当前元数据空间大小
- CCSMN：最小压缩类空间大小
- CCSMX：最大压缩类空间大小
- CCSC：当前压缩类空间大小
- YGC：年轻代gc次数
- FGC：老年代GC次数

### 六、-gccause

jstat -gccause pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%883.41.12.png)

- S0：年轻代中第一个survivor（幸存区）已使用的占当前容量百分比
- S1：年轻代中第二个survivor（幸存区）已使用的占当前容量百分比
- E：年轻代中Eden（伊甸园）已使用的占当前容量百分比
- O：old代已使用的占当前容量百分比
- M:元空间(Metaspace)已使用的占当前容量百分比,类似1.8之前的永久代(PermGen)
- CCS：CCS表示的是Metaspace的使用率，也就是CCSU/CCSC算出来的
- YGC：从应用程序启动到采样时年轻代中gc次数
- YGCT：从应用程序启动到采样时年轻代中gc所用时间(s)
- FGC：从应用程序启动到采样时old代(全gc)gc次数
- FGCT：从应用程序启动到采样时old代(全gc)gc所用时间(s)
- GCT：从应用程序启动到采样时gc用的总时间(s)
- LGCC：最近一次full gc的原因
- GCC：这次full gc的原因

### 七、-gcmetacapacity

jstat -gcmetacapacity pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%883.50.24.png)

- MCMN:最小元数据容量
- MCMX：最大元数据容量
- MC：当前元数据空间大小
- CCSMN：最小压缩类空间大小
- CCSMX：最大压缩类空间大小
- CCSC：当前压缩类空间大小
- YGC：年轻代垃圾回收次数
- FGC：老年代垃圾回收次数
- FGCT：老年代垃圾回收消耗时间
- GCT：垃圾回收消耗总时间

### 八、-gcnew

jstat -gcnew pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%883.52.37.png)

- S0C：第一个幸存区大小
- S1C：第二个幸存区的大小
- S0U：第一个幸存区的使用大小
- S1U：第二个幸存区的使用大小
- TT：对象在新生代存活的次数
- MTT：对象在新生代存活的最大次数
- DSS：期望的幸存区大小
- EC：伊甸园区的大小
- EU：伊甸园区的使用大小
- YGC：年轻代垃圾回收次数
- YGCT：年轻代垃圾回收消耗时间

### 十、-gcnewcapacity

jstat -gcnewcapacity pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%883.54.44.png)

- NGCMN：新生代最小容量
- NGCMX：新生代最大容量
- NGC：当前新生代容量
- S0CMX：最大幸存1区大小
- S0C：当前幸存1区大小
- S1CMX：最大幸存2区大小
- S1C：当前幸存2区大小
- ECMX：最大伊甸园区大小
- EC：当前伊甸园区大小
- YGC：年轻代垃圾回收次数
- FGC：老年代回收次数

### 十一、-gcold

jstat -gcold pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%883.57.12.png)

- MC：方法区大小
- MU：方法区使用大小
- CCSC:压缩类空间大小
- CCSU:压缩类空间使用大小
- OC：老年代大小
- OU：老年代使用大小
- YGC：年轻代垃圾回收次数
- FGC：老年代垃圾回收次数
- FGCT：老年代垃圾回收消耗时间
- GCT：垃圾回收消耗总时间

### 十二、-gcoldcapacity

jstat -gcoldcapacity pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%883.59.12.png)

- OGCMN：老年代最小容量
- OGCMX：老年代最大容量
- OGC：当前老年代大小
- OC：老年代大小
- YGC：年轻代垃圾回收次数
- FGC：老年代垃圾回收次数
- FGCT：老年代垃圾回收消耗时间
- GCT：垃圾回收消耗总时间

### 十三、-gcutil

jstat -gcutil pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%884.03.22.png)

- S0：幸存1区当前使用比例
- S1：幸存2区当前使用比例
- E：伊甸园区使用比例
- O：老年代使用比例
- M：元数据区使用比例
- CCS：压缩使用比例
- YGC：年轻代垃圾回收次数
- YGCT：年轻代垃圾回收消耗时间
- FGC：老年代垃圾回收次数
- FGCT：老年代垃圾回收消耗时间
- GCT：垃圾回收消耗总时间

### 十四、-printcompilation

jstat -printcompilation pid，运行实例程序，在命令终端输入该命令：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-27%E4%B8%8B%E5%8D%884.05.57.png)

- Compiled：最近编译方法的数量
- Size：最近编译方法的字节码大小
- Type：最近编译方法的编译类型
- Method：方法名标识