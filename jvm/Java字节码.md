## Java字节码

[TOC]

### 一、命令和工具准备

#### 1.1 javap命令

​		使用javap命令可以用来分析字节码（.class） 文件

- javap com.gapache.commons.jvm.bytecode.MyTest1
- javap -c com.gapache.commons.jvm.bytecode.MyTest1
- javap -verbose com.gapache.commons.jvm.bytecode.MyTest1

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

​		将MyTest1.java文件编译成class文件，运行javap -verbose com.gapache.commons.jvm.bytecode.MyTest1，控制台输出如下所示：

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

#### 1.2  Hex Fiend工具

​		Hex Fiend是十六进制编辑器，可以用来显示.class文件的十六进制内容，我们先用Hex Fiend打开MyTest1.class文件，并在Views下将Byte Grouping设置为Single，如下图所示：

![](https://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-19%E4%B8%8B%E5%8D%882.56.47.png)

### 二、魔数以及版本信息

#### 2.1 魔数

​		所有的.class字节码文件的前4个字节都是魔数，魔数值为固定值：0xCAFEBABE，CAFE BABE（咖啡宝贝）😂。

#### 2.2 版本信息

​		魔数之后的4个字节为版本信息，前两个字节表示minor version（次版本号），后两个字节表示major version（主版本号）。这里的版本号为00 00 00 34，换算成十进制，表示次版本号为0，主版本号为52（对应jdk1.8）。所以，该文件的版本号为：1.8.0。可以通过java -version命令来验证这一点。

```bash
macos@macosdeiMac-Pro classes % java -version
# 1.8是主版本，0是次版本
java version "1.8.0_241"
Java(TM) SE Runtime Environment (build 1.8.0_241-b07)
Java HotSpot(TM) 64-Bit Server VM (build 25.241-b07, mixed mode)
```

### 三、常量池（Constant pool）

​		紧接着主版本号之后的就是常量池入口。一个Java类中定义的很多信息都是由常量池来维护和描述的，可以将常量池看作是Class文件的资源仓库，比如说Java类中定义的方法与变量信息，都是存储在常量池中。常量池中主要存储两类常量：

1. 字面量：字面量如文本字符串，Java中声明为final的常量值等
2. 符号引用：如类和接口的全局限定名，字段的名称和描述符，方法的名称和描述符等

#### 3.1 常量池结构

##### 3.1.1 常量池计数器

​		常量池是class文件中非常重要的结构，它描述着整个class文件的字面量信息。 常量池是由一组constant_pool结构体数组组成的，而数组的大小则由常量池计数器指定。

​		值得注意的是，常量池数组中元素的个数 = 常量池数 - 1（其中0暂时不使用），目的是满足某些常量池索引值的数据在特定情况下需要表达「不引用任何一个常量池的」含义。根本原因在于，索引为0也是一个常量（保留常量），只不过它不位于常量表中，这个常量就对应null值，所以常量池的索引从1而非0开始。

​		constant_pool表的索引值只有在大于 0 且小于constant_pool_count时才会被认为是有效的。常量池数量紧跟在主版本号后面，占据2个字节，常量池数组则紧跟在常量池数量之后。

##### 3.1.2 常量池

​		constant_pool是一种表结构,它包含 Class 文件结构及其子结构中引用的所有字符串常量、 类或接口名、字段名和其它常量。常量池数组与一般的数组不同的是，常量池数组中不同的元素（常量池项目cp_info）的类型、结构都是不同的，长度当然也就不同。常量池中的每一项都具备相同的格式特征——第一个字节作为类型标记用于识别该项是哪种类型的常量，称为 “tag byte” 。

##### 3.1.3 常量池项（cp_info）的结构是什么？

​		每个常量池项(cp_info) 都会对应记录着class文件中的某种类型的字面量。让我们先来了解一下常量池项(cp_info)的结构：

``` java
cp_info {
  u1 tag;
  u1[] info;
}
```

#### 3.2  描述

##### **3.2.1 描述信息**

​		**在JVM规范中，每个变量/字段都有描述信息，描述信息主要的作用是描述字段的数据类型、方法的参数列表（包括数量、类型与顺序）与返回值。根据描述符规则，基本数据类型和代表无返回值的void类型都用一个大写字符来表示，对象类型则使用字符L加对象的全限定名称来表示。如下所示：**

|   **类型**   |        **表示**         |
| :----------: | :---------------------: |
|   **Byte**   |          **B**          |
|   **Char**   |          **C**          |
|  **Double**  |          **D**          |
|  **float**   |          **F**          |
|   **Int**    |          **I**          |
|   **long**   |          **J**          |
|  **short**   |          **S**          |
| **boolean**  |          **Z**          |
|   **void**   |          **V**          |
| **对象类型** | **Ljava/lang/string；** |

​		对于数组类型来说，每一个维度使用一个前置的[来表示，如int[]被记录为[I，String[][]被记录为[[Ljava/lang/string;

#### 3.2 常量池中数据类型结构及描述

##### 3.2.1 JVM虚拟机规定了不同的tag值和不同类型的字面量对应关系如下：

| **Tag值** |                       **表示的字面量**                       |           **更细化的结构**           |
| :-------: | :----------------------------------------------------------: | :----------------------------------: |
|   **1**   |                  **用于表示字符串常量的值**                  |        **CONSTANT_Utf8_info**        |
|   **3**   |                **表示4字节（int）的数值常量**                |      **CONSTANT_Integer_info**       |
|   **4**   |               **表示4字节（float）的数值常量**               |       **CONSTANT_Float_INFO**        |
|   **5**   |               **表示8字节（long）的数值常量**                |        **CONSTANT_Long_info**        |
|   **6**   |              **表示8字节（double）的数值常量**               |       **CONSTANT_Double_info**       |
|   **7**   |                 **表示类或接口的完全限定名**                 |       **CONSTANT_Class_info**        |
|   **8**   |            **表示java.lang.String类型的常量对象**            |       **CONSTANT_String_info**       |
|   **9**   |                      **表示类中的字段**                      |      **CONSTANT_Fieldref_info**      |
|  **10**   |                      **表示类中的方法**                      |     **CONSTANT_Methodref_info**      |
|  **11**   |                 **表示类所实现的接口的方法**                 | **CONSTANT_InterfaceMethodref_info** |
|  **12**   |                **表示字段或方法的名称和类型**                |    **CONSTANT_NameAndType_info**     |
|  **15**   |                       **表示方法句柄**                       |    **CONSTANT_MethodHandle_info**    |
|  **16**   |                       **表示方法类型**                       |     **CONSTANT_MethodType_info**     |
|  **18**   | **表示invokedynamic指令所使用到的引导方法（Bootstrap Method）、引导方法使用到动态调用名称（Dynamic Invocation Name）、参数和请求返回类型、以及可以选择性的附加被称为静态参数（Static Arguments）的常量序列** |   **CONSTANT_InvokeDynamic_info**    |

##### 3.2.2 int和float数据类型的常量在常量池中是怎样表示和存储的？(CONSTANT_Integer_info, CONSTANT_Float_info)

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/20141011132721395)

​		举例：建下面的类 IntAndFloatTest.java，在这个类中，我们声明了五个变量，但是取值就两种int类型的10和Float类型的11f。

```java
public class IntAndFloatTest {
    private final int a = 10;
    private final int b = 10;
    private float c = 11F;
    private float d = 11F;
    private float e = 11F;
}
```

​		我们通过javap -verbose com.gapache.commons.jvm.bytecode.IntAndFloatTest 指令来看一下其常量池中的信息，可以看到虽然我们在代码中写了两次10 和三次11f，但是常量池中，就只有一个常量10 和一个常量11f，如下所示：

Constant pool:

  \#1 = Methodref     #9.#28     // java/lang/Object."<init>":()V

  \#2 = Fieldref      #8.#29     // com/gapache/commons/jvm/bytecode/IntAndFloatTest.a:I

  \#3 = Fieldref      #8.#30     // com/gapache/commons/jvm/bytecode/IntAndFloatTest.b:I

  \#4 = Float       11.0f

  \#5 = Fieldref      #8.#31     // com/gapache/commons/jvm/bytecode/IntAndFloatTest.c:F

  \#6 = Fieldref      #8.#32     // com/gapache/commons/jvm/bytecode/IntAndFloatTest.d:F

  \#7 = Fieldref      #8.#33     // com/gapache/commons/jvm/bytecode/IntAndFloatTest.e:F

  \#8 = Class       #34      // com/gapache/commons/jvm/bytecode/IntAndFloatTest

  \#9 = Class       #35      // java/lang/Object

 \#10 = Utf8        a

 \#11 = Utf8        I

 \#12 = Utf8        ConstantValue

 \#13 = Integer      10

 \#14 = Utf8        b

 \#15 = Utf8        c

 \#16 = Utf8        F

 \#17 = Utf8        d

 \#18 = Utf8        e

 \#19 = Utf8        <init>

 \#20 = Utf8        ()V

 \#21 = Utf8        Code

 \#22 = Utf8        LineNumberTable

 \#23 = Utf8        LocalVariableTable

 \#24 = Utf8        this

 \#25 = Utf8        Lcom/gapache/commons/jvm/bytecode/IntAndFloatTest;

 \#26 = Utf8        SourceFile

 \#27 = Utf8        IntAndFloatTest.java

 \#28 = NameAndType    #19:#20    // "<init>":()V

 \#29 = NameAndType    #10:#11    // a:I

 \#30 = NameAndType    #14:#11    // b:I

 \#31 = NameAndType    #15:#16    // c:F

 \#32 = NameAndType    #17:#16    // d:F

 \#33 = NameAndType    #18:#16    // e:F

 \#34 = Utf8        com/gapache/commons/jvm/bytecode/IntAndFloatTest

 \#35 = Utf8        java/lang/Object

​		从结果上可以看到常量池第#13个常量池项(cp_info) 就是CONSTANT_Integer_info,值为10；第#4个常量池项(cp_info) 就是CONSTANT_Float_info,值为11f。

　　代码中所有用到 int 类型 10 的地方，会使用指向常量池的指针值#13定位到第#13个常量池项(cp_info)，即值为 10的结构体CONSTANT_Integer_info。

　　而用到float类型的11f时，也会指向常量池的指针值#4来定位到第#4个常量池项(cp_info) 即值为11f的结构体CONSTANT_Float_info。

##### 3.2.3 long和 double数据类型的常量在常量池中是怎样表示和存储的？(CONSTANT_Long_info、CONSTANT_Double_info )

​		Java语言规范规定了 long 类型和 double类型的数据类型占用8 个字节的空间。相应地，在常量池中，将long和double类型的常量分别使用CONSTANT_Long_info和Constant_Double_info表示，他们的结构如下所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310095427781-359284857.png)

​		举例：建下面的类 LongAndDoubleTest.java，在这个类中，我们声明了六个变量，但是取值就两种Long 类型的-6076574518398440533L 和Double 类型的10.1234567890D。

```java
public class LongAndDoubleTest {
    private long a = -6076574518398440533L;
    private long b = -6076574518398440533L;
    private long c = -6076574518398440533L;
    private double d = 10.1234567890D;
    private double e = 10.1234567890D;
    private double f = 10.1234567890D;
}
```

​		我们通过javap -verbose com.gapache.commons.jvm.bytecode.LongAndDoubleTest指令来看一下其常量池中的信息，可以看到虽然我们在代码中写了三次-6076574518398440533L 和三次10.1234567890D，但是常量池中，就只有一个常量-6076574518398440533L和一个常量10.1234567890D，如下所示：

Constant pool:

  \#1 = Methodref     #13.#31    // java/lang/Object."<init>":()V

  \#2 = Long        -6076574518398440533l

  \#4 = Fieldref      #12.#32    // com/gapache/commons/jvm/bytecode/LongAndDoubleTest.a:J

  \#5 = Fieldref      #12.#33    // com/gapache/commons/jvm/bytecode/LongAndDoubleTest.b:J

  \#6 = Fieldref      #12.#34    // com/gapache/commons/jvm/bytecode/LongAndDoubleTest.c:J

  \#7 = Double       10.123456789d

  \#9 = Fieldref      #12.#35    // com/gapache/commons/jvm/bytecode/LongAndDoubleTest.d:D

 \#10 = Fieldref      #12.#36    // com/gapache/commons/jvm/bytecode/LongAndDoubleTest.e:D

 \#11 = Fieldref      #12.#37    // com/gapache/commons/jvm/bytecode/LongAndDoubleTest.f:D

 \#12 = Class       #38      // com/gapache/commons/jvm/bytecode/LongAndDoubleTest

 \#13 = Class       #39      // java/lang/Object

 \#14 = Utf8        a

 \#15 = Utf8        J

 \#16 = Utf8        b

 \#17 = Utf8        c

 \#18 = Utf8        d

 \#19 = Utf8        D

 \#20 = Utf8        e

 \#21 = Utf8        f

 \#22 = Utf8        <init>

 \#23 = Utf8        ()V

 \#24 = Utf8        Code

 \#25 = Utf8        LineNumberTable

 \#26 = Utf8        LocalVariableTable

 \#27 = Utf8        this

 \#28 = Utf8        Lcom/gapache/commons/jvm/bytecode/LongAndDoubleTest;

 \#29 = Utf8        SourceFile

 \#30 = Utf8        LongAndDoubleTest.java

 \#31 = NameAndType    #22:#23    // "<init>":()V

 \#32 = NameAndType    #14:#15    // a:J

 \#33 = NameAndType    #16:#15    // b:J

 \#34 = NameAndType    #17:#15    // c:J

 \#35 = NameAndType    #18:#19    // d:D

 \#36 = NameAndType    #20:#19    // e:D

 \#37 = NameAndType    #21:#19    // f:D

 \#38 = Utf8        com/gapache/commons/jvm/bytecode/LongAndDoubleTest

 \#39 = Utf8        java/lang/Object

​		常量池第#2个常量池项(cp_info) 就是CONSTANT_Long_info，值为-6076574518398440533L ；第#7个常量池项(cp_info) 就是CONSTANT_Double_info，值为10.1234567890D。

　　代码中所有用到long类型-6076574518398440533L 的地方，会使用指向常量池的指针值#2定位到第#2个常量池项(cp_info)，即值为-6076574518398440533L 的结构体CONSTANT_Long_info。

　　而用到double类型的10.1234567890D时，也会指向常量池的指针值#7来定位到第#7个常量池项(cp_info) 即值为10.1234567890D的结构体CONSTANT_Double_info。

##### 3.2.4 String类型的字符串常量在常量池中是怎样表示和存储的？（CONSTANT_String_info、CONSTANT_Utf8_info）

​		对于字符串而言，JVM会将字符串类型的字面量以UTF-8编码格式存储到在class字节码文件中。

​		我们先从直观的Java源码中中出现的用双引号""括起来的字符串来看，在编译器编译的时候，都会将这些字符串转换成CONSTANT_String_info结构体，然后放置于常量池中。其结构如下所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310100107438-1284587273.png)

​		举例，定义一个简单的StringTest.java类，然后在这个类里加一个"JVM原理" 字符串，然后，我们来看看它在class文件中是怎样组织的。

```java
public class StringTest {
    private String s1 = "JVM原理";
    private String s2 = "JVM原理";
    private String s3 = "JVM原理";
    private String s4 = "JVM原理";
}
```

​		我们通过javap -verbose com.gapache.commons.jvm.bytecode.StringTest指令来看一下其常量池中的信息，可以看到CONSTANT_String_info结构体位于常量池的第#2个索引位置。而存放"Java虚拟机原理" 字符串的UTF-8编码格式的字节数组被放到CONSTANT_Utf8_info结构体中，该结构体位于常量池的第#24个索引位置。

Constant pool:

  \#1 = Methodref     #8.#23     // java/lang/Object."<init>":()V

  \#2 = String       #24      // JVM原理

  \#3 = Fieldref      #7.#25     // com/gapache/commons/jvm/bytecode/StringTest.s1:Ljava/lang/String;

  \#4 = Fieldref      #7.#26     // com/gapache/commons/jvm/bytecode/StringTest.s2:Ljava/lang/String;

  \#5 = Fieldref      #7.#27     // com/gapache/commons/jvm/bytecode/StringTest.s3:Ljava/lang/String;

  \#6 = Fieldref      #7.#28     // com/gapache/commons/jvm/bytecode/StringTest.s4:Ljava/lang/String;

  \#7 = Class       #29      // com/gapache/commons/jvm/bytecode/StringTest

  \#8 = Class       #30      // java/lang/Object

  \#9 = Utf8        s1

 \#10 = Utf8        Ljava/lang/String;

 \#11 = Utf8        s2

 \#12 = Utf8        s3

 \#13 = Utf8        s4

 \#14 = Utf8        <init>

 \#15 = Utf8        ()V

 \#16 = Utf8        Code

 \#17 = Utf8        LineNumberTable

 \#18 = Utf8        LocalVariableTable

 \#19 = Utf8        this

 \#20 = Utf8        Lcom/gapache/commons/jvm/bytecode/StringTest;

 \#21 = Utf8        SourceFile

 \#22 = Utf8        StringTest.java

 \#23 = NameAndType    #14:#15    // "<init>":()V

 \#24 = Utf8        JVM原理

 \#25 = NameAndType    #9:#10     // s1:Ljava/lang/String;

 \#26 = NameAndType    #11:#10    // s2:Ljava/lang/String;

 \#27 = NameAndType    #12:#10    // s3:Ljava/lang/String;

 \#28 = NameAndType    #13:#10    // s4:Ljava/lang/String;

 \#29 = Utf8        com/gapache/commons/jvm/bytecode/StringTest

 \#30 = Utf8        java/lang/Object

##### 3.2.4 类文件中定义的类名和类中使用到的类在常量池中是怎样被组织和存储的？(CONSTANT_Class_info)

​		JVM会将某个Java类中所有使用到了的类的完全限定名以二进制形式的完全限定名封装成CONSTANT_Class_info结构体中，然后将其放置到常量池里。CONSTANT_Class_info 的tag值为 7 。其结构如下：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310100611078-1830534162.png)

​		(PS：在某个Java源码中，我们会使用很多个类，比如我们定义了一个ClassTest的类，并把它放到com.louis.jvm 包下，则 ClassTest类的完全限定名为com.louis.jvm.ClassTest，将JVM编译器将类编译成class文件后，此完全限定名在class文件中，是以二进制形式的完全限定名存储的，即它会把完全限定符的"."换成"/" ，即在class文件中存储的 ClassTest类的完全限定名称是"com/louis/jvm/ClassTest"。因为这种形式的完全限定名是放在了class二进制形式的字节码文件中，所以就称之为 二进制形式的完全限定名。)

​		举例，我们定义一个很简单的ClassTest类，来看一下常量池是怎么对类的完全限定名进行存储的。

```java
public class ClassTest {
    private Date date = new Date();
}
```

​		我们通过javap -verbose com.gapache.commons.jvm.bytecode.ClassTest指令来看一下其常量池中的信息，

Constant pool:

  \#1 = Methodref     #6.#18     // java/lang/Object."<init>":()V

  \#2 = Class       #19      // java/util/Date

  \#3 = Methodref     #2.#18     // java/util/Date."<init>":()V

  \#4 = Fieldref      #5.#20     // com/gapache/commons/jvm/bytecode/ClassTest.date:Ljava/util/Date;

  \#5 = Class       #21      // com/gapache/commons/jvm/bytecode/ClassTest

  \#6 = Class       #22      // java/lang/Object

  \#7 = Utf8        date

  \#8 = Utf8        Ljava/util/Date;

  \#9 = Utf8        <init>

 \#10 = Utf8        ()V

 \#11 = Utf8        Code

 \#12 = Utf8        LineNumberTable

 \#13 = Utf8        LocalVariableTable

 \#14 = Utf8        this

 \#15 = Utf8        Lcom/gapache/commons/jvm/bytecode/ClassTest;

 \#16 = Utf8        SourceFile

 \#17 = Utf8        ClassTest.java

 \#18 = NameAndType    #9:#10     // "<init>":()V

 \#19 = Utf8        java/util/Date

 \#20 = NameAndType    #7:#8     // date:Ljava/util/Date;

 \#21 = Utf8        com/gapache/commons/jvm/bytecode/ClassTest

 \#22 = Utf8        java/lang/Object