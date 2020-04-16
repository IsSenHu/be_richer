## Java字节码

[TOC]

### 一、命令和工具准备

#### 1.1 javap命令

​		使用javap命令可以用来分析字节码（.class） 文件

- javap com.gapache.commons.jvm.bytecode.MyTest1
- javap -c com.gapache.commons.jvm.bytecode.MyTest1
- javap -verbose com.gapache.commons.jvm.bytecode.MyTest1
- javap -verbose -p 可以打印出私有的信息 

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



#### 1.2  Hex Fiend工具

​		Hex Fiend是十六进制编辑器，可以用来显示.class文件的十六进制内容，我们先用Hex Fiend打开MyTest1.class文件，并在Views下将Byte Grouping设置为Single，如下图所示：

![](https://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-19%E4%B8%8B%E5%8D%882.56.47.png)

#### 1.3 Java字节码整体结构

|      类型      |                名称                |          数量           |
| :------------: | :--------------------------------: | :---------------------: |
|       u4       |           magic（魔数）            |            1            |
|       u2       |     minor_version（次版本号）      |            1            |
|       u2       |     major_version（主版本号）      |            1            |
|       u2       |  constant_pool_count（常量个数）   |            1            |
|    cp_info     |     constant_pool（常量池表）      | constant_pool_count - 1 |
|       u2       |  access_flags（类的访问控制权限）  |            1            |
|       u2       |         this_class（类名）         |            1            |
|       u2       |       super_class（父类名）        |            1            |
|       u2       |    interfaces_count（接口个数）    |            1            |
|       u2       |        interfaces（接口名）        |    interfaces_count     |
|       u2       |       fields_count（域个数）       |            1            |
|   field_info   |          fields（域的表）          |      fields_count       |
|       u2       |    methods_conut（方法的个数）     |            1            |
|  method_info   |         methods（方法表）          |      methods_conut      |
|       u2       | attributes_count（附加属性的个数） |            1            |
| attribute_info |     attributes（附加属性的表）     |    attributes_count     |

#### 1.4 Class字节码中有两种数据类型

- 字节数据直接量：这是基本的数据类型。共细分为u1、u2、u4、u8四种，分别代表连续的1个字节、2个字节、4个字节、8个字节组成的整体数据。
- 表（数组）：表是由多个基本数据或其他表，按照既定顺序组成的大的数据集合。表是有结构的，它的结构体现在：组成表的成分所在的位置和顺序都是已经严格定义好的。

#### 1.5 字节码查看工具jclasslib

​		idea可以直接安装插件来使用。

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

``` javascript
cp_info {
  u1 tag;
  u1[] info;
}
```

#### 3.2  描述

##### 3.2.1 描述信息

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

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-20%E4%B8%8B%E5%8D%8812.58.54.png)

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

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-20%E4%B8%8B%E5%8D%8812.53.27.png)

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

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-20%E4%B8%8B%E5%8D%881.03.15.png)

##### 3.2.5 类文件中定义的类名和类中使用到的类在常量池中是怎样被组织和存储的？(CONSTANT_Class_info)

​		JVM会将某个Java类中所有使用到了的类的完全限定名以二进制形式的完全限定名封装成CONSTANT_Class_info结构体中，然后将其放置到常量池里。CONSTANT_Class_info 的tag值为 7 。其结构如下：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310100611078-1830534162.png)

​		(PS：在某个Java源码中，我们会使用很多个类，比如我们定义了一个ClassTest的类，并把它放到com.louis.jvm 包下，则 ClassTest类的完全限定名为com.louis.jvm.ClassTest，将JVM编译器将类编译成class文件后，此完全限定名在class文件中，是以二进制形式的完全限定名存储的，即它会把完全限定符的"."换成"/" ，即在class文件中存储的 ClassTest类的完全限定名称是"com/louis/jvm/ClassTest"。因为这种形式的完全限定名是放在了class二进制形式的字节码文件中，所以就称之为 二进制形式的完全限定名。)

​		举例，我们定义一个很简单的ClassTest类，来看一下常量池是怎么对类的完全限定名进行存储的。

```java
public class ClassTest {
    private Date date = new Date();
}
```

​		我们通过javap -verbose com.gapache.commons.jvm.bytecode.ClassTest指令来看一下其常量池中的信息，可以看到，在ClassTest.class文件的常量池中共有3个CONSTANT_Class_info结构体，分别表示ClassTest中用到的Class信息。我们就看其中一个表示com/gapache/commons/jvm/bytecode/ClassTest的CONSTANT_Class_info结构体。它在常量池中的位置是#5，它指向了常量池的第#21个常量池项。

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-20%E4%B8%8B%E5%8D%881.05.31.png)

​		对于某个类而言，其class文件中至少要有两个CONSTANT_Class_info常量池项，用来表示自己的类信息和其父类信息。（除了

java.lang.Object类除外，其他的任何类都会默认继承自java.lang.Object），如果类声明实现了某些接口，那么接口的信息也会生成对应的

CONSTANT_Class_info常量池项。

​		除此之外，如果在类中使用其他的类，只有真正使用到了相应的类，JDK编译器才会将类的信息组成CONSTANT_Class_info常量池项中。如下图：

```java
public class Other {

    private Date date;

    public Other() {
        Date da;
    }
}
```

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-20%E4%B8%8B%E5%8D%882.12.25.png)

 		将类信息放到常量池中的目的，是为了在后续的代码中有可能会反复用到它。很显然，JDK在编译Other类的时候，会解析到Date类有没有用到，发现该类在代码中就没有用到过，所以就认为没有必要将它的信息放到常量池中了。

​		将上述的Other改写一下，仅使用new Date()，如下所示：

```java
public class Other {

    public Other() {
        new Date();
    }
}
```

​		这时候就可以看到常量池中有表示java/util/Date的常量池项：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-20%E4%B8%8B%E5%8D%882.20.05.png)

##### 3.2.6 类中引用到的field字段在常量池中是怎样描述的？(CONSTANT_Fieldref_info, CONSTANT_Name_Type_info)

​		一般而言，我们在定义类的过程中会定义一些 field 字段，然后会在这个类的其他地方（如方法中）使用到它。有可能我们在类的方法中只使用field字段一次，也有可能我们会在类定义的方法中使用它很多很多次。

​		举一个简单的例子，我们定一个叫Person的简单java bean，它有name和age两个field字段，如下所示：

```java
public class Person {

    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

​		在上面定义的类中，我们在Person类中的一系列方法里，多次引用到name字段和age字段。对于JVM编译器而言，name和age只是一个符号而已，并且由于它可能会在此类中重复出现多次，所以JVM把它当作常量来看待，将name和age以field字段常量的形式保存到常量池中。

​		将它name和age封装成CONSTANT_Fieldref_info常量池项，放到常量池中，在类中引用到它的地方，直接放置一个指向field字段所在常量池的索引。

​		  使用javap -verbose com.gapache.commons.jvm.bytecode.Person，查看class文件的信息，你会看到，在Person类中引用到age和name字段的地方，都是指向了常量池中age和name字段对应的常量池项中。表示field字段的常量池项叫做CONSTANT_Field_info。如下所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-20%E4%B8%8B%E5%8D%883.00.19.png)

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-20%E4%B8%8B%E5%8D%883.00.32.png)

​		怎样描述一个field字段的引用？

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310102335297-130978447.png)

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310102613000-1211659752.png)

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310102623141-1625084358.png)

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310102722578-1808156508.png)

​		（PS：如果我们在类中定义了field字段，但是没有在类中的其他地方用到这些字段，它是不会被编译器放到常量池中的。读者可以字节试一下。）

##### 3.2.7 类中引用到的method方法在常量池中是怎样描述的？(CONSTANT_Methodref_info, CONSTANT_Name_Type_info)

​		还是以Person类为例。在Person中我们定义了SetName(String name)、getName()、setAge(int age)、getAge()这些方法。虽然我们定义了方法，但是这些方法没有在类中的其他地方被用到，所以它们的方法引用信息并不会放到常量中。现在我们在类中加一个方法getInfo()，调用了getName()和getAge()方法：

```java
public String getInfo() {
  	return getName() + "\t" + getAge();
}
```

​		使用javap -verbose com.gapache.commons.jvm.bytecode.Person，查看class文件的信息，我们会看到，这时候JVM编译器会将getName()和getAge()方法的引用信息包装成CONSTANT_Methodref_info结构体放入到常量池之中。

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-20%E4%B8%8B%E5%8D%883.37.05.png)

​		（PS： 这里的方法调用的方式牵涉到Java非常重要的一个术语和机制，叫动态绑定。这个动态绑定问题以后在单独谈谈。）

​		怎样表示一个方法引用？

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310103612875-1701964407.png)

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310103622641-1587115112.png)

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310103632078-1256968219.png)

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310103757438-189877104.png)

##### 3.2.8 类中引用到某个接口中定义的method方法在常量池中是怎样描述的？(CONSTANT_InterfaceMethodref_info，CONSTANT_Name_Type_info)

​		当我们在某个类中使用到了某个接口中的方法，JVM会将用到的接口中的方法信息放到这个类的常量池中。比如我们定义了一个Worker接口，和一个Boss类，在Boss类中调用了Worker接口中的方法，这时候在Boss类的常量池中会有Worker接口的方法的引用表示。

```java
public interface Worker {

    void work();
}
```

```java
public class Boss {

    public void makeMoney(Worker worker) {
        worker.work();
    }
}
```

​		使用javap -verbose com.gapache.commons.jvm.bytecode.Boss，然后会看到如下信息：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-20%E4%B8%8B%E5%8D%883.55.08.png)

​		如上图所示，在Boss类的makeMoney()方法中调用了Worker接口的work()方法，机器指令是通过invokeinterface指令完成的。

​		invokeinterface指令后面的操作数，是指向了Boss常量池中Worker接口的work()方法描述，表示的意思就是：“我要调用Worker接口的work()方法”。Worker接口的work()方法引用信息，JVM会使用CONSTANT_InterfaceMethodref_info结构体来描述，CONSTANT_InterfaceMethodref_info定义如下：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310104422734-406388717.png)

​		CONSTANT_InterfaceMethodref_info结构体和上面介绍的CONSTANT_Methodref_info 结构体很基本上相同，它们的不同点只有：

1. CONSTANT_InterfaceMethodref_info 的tag 值为11，而CONSTANT_Methodref_info的tag值为10；
2. CONSTANT_InterfaceMethodref_info 描述的是接口中定义的方法，而CONSTANT_Methodref_info描述的是实例类中的方法。

##### 3.2.9 CONSTANT_MethodHandle_info

```java
CONSTANT_MethodHandle_info {
  u1 tag;
  u1 reference_kind;
  u2 reference_index;
}
```

​		reference_kind是一个1到9之间的整数，具体含义可以参考 JVM规范。reference_index是常量池索引，但具体索引的是什么类型的常量，需要看re

REF：

|       constant_pool entry        |                        reference_kind                        |
| :------------------------------: | :----------------------------------------------------------: |
|      CONSTANT_Fieldref_info      | 1 (REF_getField), 2 (REF_getStatic), 3 (REF_putField), or 4 (REF_putStatic) |
|     CONSTANT_Methodref_info      | 5 (REF_invokeVirtual), 6 (REF_invokeStatic), 7 (REF_invokeSpecial), or 8 (REF_newInvokeSpecial) |
| CONSTANT_InterfaceMethodref_info |                   9 (REF_invokeInterface)                    |

##### 3.2.10 CONSTANT_MethodType_info

```java
CONSTANT_MethodType_info {
  u1 tag;
  u2 descriptor_index;
}
```

##### 3.2.11 CONSTANT_InvokeDynamic_info

```java
CONSTANT_InvokeDynamic_info {
	u1 tag;
  u2 bootstrap_method_attr_index;
  u2 name_and_type_index;
}
```

​		bootstrap_method_attr_index索引bootstrap_methods表，bootstrap_methods位于class文件的attributes表里。

​		JVM规范规定，如果类的常量池中存在CONSTANT_InvokeDynamic_info的话，那么attributes表中就必定**有且仅有一个**BootstrapMethods属性。BootstrapMethods属性是个变长的表，结构如下所示：

```java
BootstrapMethods_attribute {
	u2 attribute_name_index;
  u4 attribute_length;
  u2 num_bootstrap_methods;
  {   u2 bootstrap_method_ref;
      u2 num_bootstrap_arguments;
      u2 bootstrap_arguments[num_bootstrap_arguments];
  } bootstrap_methods[num_bootstrap_methods];
}
```

​		每一个BootstrapMethod都包含一个bootstrap_method_ref和n个bootstrap_arguments。bootstrap_method_ref是个常量池索引，指向一个 **CONSTANT_MethodHandle_info**。而每一个bootstrap_argument也都是常量池索引，可以指向下面这些结构：

- CONSTANT_String_info
- CONSTANT_Class_info
- CONSTANT_Integer_info
- CONSTANT_Long_info
- CONSTANT_Float_info
- CONSTANT_Double_info
- CONSTANT_MethodHandle_info
- CONSTANT_MethodType_info

###   三、字节码访问标志与字段表

#### 3.1 Access_Flag 访问标志

|   Flag Name    | Value  |                        Interpretation                        |
| :------------: | :----: | :----------------------------------------------------------: |
|   ACC_PUBLIC   | 0x0001 |  Declared public; may be accessed from outside its package.  |
|   ACC_FINAL    | 0x0010 |            Declared final;no subclasses allowed.             |
|   ACC_SUPER    | 0x0020 | Treat superclass methods specially when invoked by the invokespecial instruction. |
| ACC_INTERFACE  | 0x0200 |                Is an interface, not a class.                 |
|  ACC_ABSTRACT  | 0x0400 |         Declared abstract; must not be instantiated.         |
| ACC_SYNTHETIC  | 0x1000 |      Declared synthetic;not present in the source code.      |
| ACC_ANNOTATION | 0x2000 |               Declared as an annotation type.                |
|    ACC_ENUM    | 0x4000 |                  Declared as an enum type.                   |

#### 3.2 字段表集合

​		字段表用于描述类和接口中声明的变量。这里的字段包含了类级别变量以及实例变量，但是不包括方法内部声明的局部变量。

```java
field_info {
  u2 access_flags;
  u2 name_index;
  u2 descriptor_index;
  u2 attributes_count;
  attribute_info attributes[attributes_count];
}
```

### 四、方法表与属性表

#### 4.1  方法表

```java
method_info {
  u2 access_flags;
  u2 name_index;
  u2 descriptor_index;
  u2 attributes_count;
  attribute_info attributes[attributes_count];
}
```

​		  方法中的每个属性都是一个attribute_info结构

```java
attribute_info {
  u2 attribute_name_index;
  u4 attribute_length;
  u1 info[attribute_length];
}
```

- JVM预定义类部分attribute，但是编译器自己也可以实现自己的attribute写入class文件里，供运行时使用。

- 不同的attribute通过attribute_name_index来区分。

- Code attribute的作用是保存该方法的结构，如所对应的字节码：

  ```java
  Code_attribute {
    u2 attribute_name_index;
    u4 attribute_length;
    u2 max_stack;
    u2 max_locals;
    u4 code_length;
    u1 code[code_length];
    u2 exception_table_length;
    {
      u2 start_pc;
      u2 end_pc;
      u2 handler_pc;
      u2 catch_type;
    } exception_table[exception_table_length];
    u2 attributes_count;
    attribute_info attributes[attributes_count];
  }
  ```

- attribute_length表示attribute所包含的字节数，不包含attribute_name_index和attribute_length字段。

- max_stack表示这个方法运行的任何时刻所能达到的操作数栈的最大深度。

- max_locals表示方法执行期间创建的局部变量的数目，包含用来表示传入的参数的局部变量。

- code_length表示改方法所包含的字节码的字节数以及具体的指令码。

- 具体字节码即是该方法被调用时，虚拟机所执行的字节码。

- exception_table，这里存放的是处理异常的信息。

- 每个exception_table表项由start_pc，end_pc，handler_pc，catch_type组成。

- start_pc和end_pc表示在code数组中的从start_pc到end_pc处（包含start_pc，不包含end_pc）的指令抛出的异常会由这个表项来处理。

- handler_pc表示处理异常的代码的开始处。catch_type表示会被处理的异常类型，它指向常量池里的一个异常类。当catch_type为0时，表示处理所有的异常。

  接下来就是该方法的附加属性：

- LineNumberTable：这个属性用来表示code数组中的字节码和Java代码行数之间的关系。这个属性可以用来在调试的时候定位代码所执行的行数。

  ```java
  LineNumberTable_attribute {
  	u2 attribute_name_index;
    u4 attribute_length;
    u2 line_number_table_length;
    {
      u2 start_pc;
      u2 line_number;
    } line_number_table[line_number_table_length];
  }
  ```

  ### 五、synchronized关键字
  
  ​		写3个被synchronized修饰的方法如下：
  
  ```java
  public class MyTest2 {
    
  		String str = "Welcome";
  
      private int x = 5;
  
      public static Integer in = 10;
  
      private final Object object = new Object();
    
      private synchronized void setX(int x) {
          this.x = x;
      }
  
      private void test(String str) {
          synchronized (this.object) {
              System.out.println("hello world");
          }
      }
  
      private synchronized static void test2() {
  
      }
  }
  ```
  
  ​		然后我们运行javap -verbose -p 命令得到如下结果：
  
  ![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/WechatIMG1.png)
  
  ​		可以看到，如果synchronized修饰方法，code上没有任何变化。而如果是使用synchronized修饰代码块，则会看到monitorenter和monitorexit这两个助记符。
  
  ### 六、构造方法和静态代码块字节码指令详解
  
  ​		这里可以看到Methods中有<init>和<clinit>方法。
  
  ![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/WX20200413-164835.png)
  
  - 构造方法：<init>
  
    ​		非静态成员变量的初始化实际不是在声明的位置进行赋值的的，而是在编译器默认生成的构造方法中完成赋值的，就算自己编写了构造方法，那也会在那也会在每个构造方法中完成赋值。编译器会将这些成员变量赋值的字节码指令放到构造方法中。
  
    ![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/WX20200413-164108.png)
  
  - 静态属性初始化：<clinit>
  
    ​	所有的静态成员初始化都会被移到这个方法中，按照从上到下的顺序。
  
    ![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/WX20200413-165626.png)

### 七、Java字节码对于异常的处理方式

1. 统一采用异常表的方式来对异常进行处理。

2. 在jdk1.4.2之前的版本中，并是不使用异常表的方式来对异常进行处理的，而是采用特定的指令方式。

3. 当异常处理存在finally语句块时，现代化的JVM采取的处理方式是将finally语句块的字节码拼接到每一个catch块后面。

   ​	我们编写如下所示代码：

   ```java
   public class MyTest3 {
   
       public void test() throws IOException, FileNotFoundException, NullPointerException {
           try {
               InputStream is = new FileInputStream("test.txt");
               ServerSocket serverSocket = new ServerSocket(9999);
               serverSocket.accept();
           } catch (FileNotFoundException e) {
               System.err.println(e.getMessage());
           } catch (IOException e) {
               e.printStackTrace();
           } catch (Exception e) {
               System.err.println(e.getLocalizedMessage());
           } finally {
               System.out.println("finally");
           }
       }
   }
   ```

    然后我们运行javap -verbose，可以看到标红的部分就是finally语句块的被拼接到了每个catch块后面。

   ![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/WX20200413-170711.png)

   这是异常表。

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/WX20200413-170809.png)

 ### 八、编译后每一个实例方法的第一个参数都是this

​		编译后每一个实例方法的第一个参数都是this，方法参数的数量总是会比源代码中方法参数的数量多一个，所以在实例方法的局部变量表中，至少会有一个指向当前对象的局部变量。如下图所示，明明源代码中方法的参数为0个，但是编译后显示参数数量为1：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/WX20200413-171605.png)

​	 	在局部变量表中，也可以看到this，如下所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/WX20200413-171849.png)

### 九、栈帧、直接引用以及符号引用

- 栈帧（stack frame）：用于帮助虚拟机执行方法调用与方法执行的数据结构。封装了方法的局部变量表、动态链接信息、方法的返回地址以及操作数栈等信息。
- 直接引用：就是直接指向了地址

- 符号引用：通过将直接引用的赋值给符号，代码中可直接使用符号来代替直接引用。有些符号引用是在类加载阶段或事第一次使用时就会转换为直接引用，这种转换叫做静态解析；另外一些符号引用则是在每次运行期转换为直接引用，这种转换叫做动态链接，这体现为Java的多态性，如下面的伪代码：

  ```java
  Animal a = new Cat();
  a.sleep();
  a = new Dog();
  a.sleep();
  a = new Tiger();
  a.sleep();
  // 在程序的字节码中，我们能看到的都是调用的父类Animal的sleep()方法
  // 通过invokevirtual，下节会讲
  ```

  ### 十、方法重载与invokevirtual字节码指令

  我们先认识一下一些调用方法的指令：

  1. invokeinterface：调用接口中的方法，实际上是在运行期决定的，决定到底调用实现改接口的哪个对象的特定方法。

  2. invokestatic：调用静态方法。

  3. invokespecial：调用自己的私有方法、构造方法以及父类的方法。

  4. invokevirtual：调用虚方法，运行期动态查找的过程。

  5. invokedynamic：动态调用方法。

  ------

  静态解析的4种情形：

  1. 静态方法

       		2. 父类方法
       		3. 构造方法
       		4. 私有方法

  以上4类方法称作非虚方法，他们是在类加载阶段就可以将符号引用转换为直接引用的。

