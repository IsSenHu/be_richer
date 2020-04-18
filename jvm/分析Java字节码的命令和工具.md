## 分析Java字节码的命令和工具

[TOC]

### 一、Javap命令

​			使用javap命令可以用来分析字节码（.class） 文件

- javap：打印出类的信息，字段和方法信息

- javap -c：加上-c可以看到方法的code信息

- javap -verbose

- javap -verbose -p：加上-p可以看到私有的信息

		​		这3条命令都可以用来分析字节码文件，使用javap -verbose命令分析一个字节码文件时，将会分析该字节码文件的魔数、版本号、常量池、类信息、类的构造方法、类中的方法信息、类变量与成员变量等信息。我们先准备下面的一个Java类：

```java
public class MyTest1 {

    private int a = 1;

    public int getA() {
        return a;
    }

    public void setA(int a) {
        this.a = a;
    }
}
```

​		将MyTest1.java文件编译成class文件，进入MyTest1.class所在的目录，运行javap -verbose MyTest1，控制台输出如下所示：

```java
Classfile /Users/macos/Documents/codes/mine/big-plan/base/commons/target/classes/com/gapache/commons/jvm/bytecode/MyTest1.class
  Last modified 2020-3-18; size 541 bytes
  MD5 checksum d49f7672a012f838d88fa0df4d3b1cdf
  Compiled from "MyTest1.java"
public class com.gapache.commons.jvm.bytecode.MyTest1
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #4.#21         // java/lang/Object."<init>":()V
   #2 = Fieldref           #3.#22         // com/gapache/commons/jvm/bytecode/MyTest1.a:I
   #3 = Class              #23            // com/gapache/commons/jvm/bytecode/MyTest1
   #4 = Class              #24            // java/lang/Object
   #5 = Utf8               a
   #6 = Utf8               I
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               LocalVariableTable
  #12 = Utf8               this
  #13 = Utf8               Lcom/gapache/commons/jvm/bytecode/MyTest1;
  #14 = Utf8               getA
  #15 = Utf8               ()I
  #16 = Utf8               setA
  #17 = Utf8               (I)V
  #18 = Utf8               MethodParameters
  #19 = Utf8               SourceFile
  #20 = Utf8               MyTest1.java
  #21 = NameAndType        #7:#8          // "<init>":()V
  #22 = NameAndType        #5:#6          // a:I
  #23 = Utf8               com/gapache/commons/jvm/bytecode/MyTest1
  #24 = Utf8               java/lang/Object
{
  public com.gapache.commons.jvm.bytecode.MyTest1();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: aload_0
         5: iconst_1
         6: putfield      #2                  // Field a:I
         9: return
      LineNumberTable:
        line 7: 0
        line 9: 4
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      10     0  this   Lcom/gapache/commons/jvm/bytecode/MyTest1;

  public int getA();
    descriptor: ()I
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: getfield      #2                  // Field a:I
         4: ireturn
      LineNumberTable:
        line 12: 0
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       5     0  this   Lcom/gapache/commons/jvm/bytecode/MyTest1;

  public void setA(int);
    descriptor: (I)V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=2, args_size=2
         0: aload_0
         1: iload_1
         2: putfield      #2                  // Field a:I
         5: return
      LineNumberTable:
        line 16: 0
        line 17: 5
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       6     0  this   Lcom/gapache/commons/jvm/bytecode/MyTest1;
            0       6     1     a   I
    MethodParameters:
      Name                           Flags
      a
}
SourceFile: "MyTest1.java"
```

### 二、Hex Fiend工具

​		Hex Fiend是十六进制编辑器，可以用来显示.class文件的十六进制内容，我们先用Hex Fiend打开MyTest1.class文件，并在Views下将Byte Grouping设置为Single，如下图所示：

![](https://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-19%E4%B8%8B%E5%8D%882.56.47.png)

### 三、Jclasslib工具

​		Jclasslib可以直接让我们看到.class的所有详细的信息，提供了独立的程序可直接使用。同时在Idea中也提供了插件，插件的名称叫jclasslib Bytecode viewer，下面我们将演示如何在Idea中使用这个插件。

​		首先直接打开需要查看的.java文件或者.class文件，然后按如图所示进行操作：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/QQ20200417-102448.png)

​		然后就能看到下图所展示出的详细信息：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/QQ20200417-102836.png)

