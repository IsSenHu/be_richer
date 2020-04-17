## Java字节码的魔数以及版本信息

[TOC]

### 一、魔数

​		所有的.class字节码文件的前4个字节都是魔数，魔数值为固定值：0xCAFEBABE，CAFE BABE（咖啡宝贝）😂，如图所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/QQ20200417-110825.png)

### 二、版本信息

​		魔数之后的4个字节为版本信息，前两个字节表示minor version（次版本号），后两个字节表示major version（主版本号）。这里的版本号为00 00 00 34，换算成十进制，表示次版本号为0，主版本号为52（对应jdk1.8）。所以，该文件的版本号为：1.8.0。可以通过java -version命令来验证这一点。

```bash
macos@macosdeiMac-Pro classes % java -version
# 1.8是主版本，0是次版本
java version "1.8.0_241"
Java(TM) SE Runtime Environment (build 1.8.0_241-b07)
Java HotSpot(TM) 64-Bit Server VM (build 25.241-b07, mixed mode)
```

