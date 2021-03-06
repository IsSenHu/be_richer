## Java对象在内存中的布局

[TOC]

参考：[Java对象在内存中的布局](https://blog.csdn.net/ju_362204801/article/details/98802697)

在虚拟机中，对象在内存中的存储布局可分为三块：对象头、实例数据和对齐填充，如下图所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-24%E4%B8%8A%E5%8D%8810.19.28.png)

### 一、对象头

对象头用于存储对象的元数据信息，对象头又可以分为存储对象自身的运行时数据，如哈希码HashCode、GC分代年龄、锁状态标志、线程持有的锁、偏向线程ID、偏向时间戳等，这部分数据的长度在32位和64位的虚拟机中分别位32bit和64bit，官方称它为 Mark Word。对象头的另一部分是类型指针，指向它的类元数据的指针，用于判断对象属于哪个类的实例，另外，如果对像是一个数组，那在对象头中还必须有一块用于记录数组长度的数据，因为虚拟机可以通过普通Java对象的元数据信息确定Java对象的大小，但是从数组的元数据中却无法确定数组的大小。

### 二、实例数据

实例数据部分是对象真正存储的有效信息，也是在程序代码中所定义各种类型的字段内容。无论是从父类继承下来的，还是在子类中定义的，都需要记录下来。父类定义的变量会出现在子类定义的变量的前面。各字段的分配策略为longs/doubles、ints、shorts/chars、bytes/boolean、oops(ordinary object pointers)，相同宽度的字段总是被分配到一起，便于之后取数据。

### 三、对齐填充

对齐填充并不是必然存在的，也没有特别的含义，它仅仅起着占位符的作用。为什么需要有对齐填充呢？由于hotspot VM的自动内存管理系统要求对象起始地址必须是8字节的整数倍，换句话，就是对象的大小必须是8字节的整数倍。而对象头正好是8字节的倍数。因此，当对象实例数据部分没有对齐时，就需要通过对齐填充来补全。

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/memory/%E6%88%AA%E5%B1%8F2020-04-24%E4%B8%8A%E5%8D%8810.34.37.png)

