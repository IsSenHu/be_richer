## Java字节码的常量池（Constant pool）

[TOC]

### 一、常量池结构

​		常量池是由常量池计数器与常量池组成。

#### 1.1 常量池计数器

​		常量池是class文件中非常重要的结构，它描述着整个class文件的字面量信息。 常量池是由一组constant_pool结构体数组组成的，而数组的大小则由常量池计数器指定。

​		值得注意的是，常量池数组中元素的个数 = 常量池数 - 1（其中0暂时不使用），目的是满足某些常量池索引值的数据在特定情况下需要表达「不引用任何一个常量池的」含义。根本原因在于，索引为0也是一个常量（保留常量），只不过它不位于常量表中，这个常量就对应null值，所以常量池的索引从1而非0开始。

​		constant_pool表的索引值只有在大于 0 且小于constant_pool_count时才会被认为是有效的。常量池数量紧跟在主版本号后面，占据2个字节，常量池数组则紧跟在常量池数量之后。

#### 1.2 常量池

​		constant_pool是一种表结构，它包含 Class 文件结构及其子结构中引用的所有字符串常量、 类或接口名、字段名和其它常量。常量池数组与一般的数组不同的是，常量池数组中不同的元素（常量池项目cp_info）的类型、结构都是不同的，长度当然也就不同。常量池中的每一项都具备相同的格式特征——第一个字节作为类型标记用于识别该项是哪种类型的常量，称为 “tag byte” 。

#### 1.3 常量池项（cp_info）的结构是什么？

​		每个常量池项(cp_info) 都会对应记录着class文件中的某种类型的字面量。让我们先来了解一下常量池项(cp_info)的结构：

``` javascript
cp_info {
  u1 tag;
  u1[] info;
}
```

### 二、描述

​		在JVM规范中，每个变量/字段都有描述信息，描述信息主要的作用是描述字段的数据类型、方法的参数列表（包括数量、类型与顺序）与返回值。根据描述符规则，基本数据类型和代表无返回值的void类型都用一个大写字符来表示，对象类型则使用字符L加对象的全限定名称来表示。如下所示：

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

### 三、常量池中数据类型结构及描述

#### 3.1 tag值对应的字面量

​		JVM虚拟机规定了不同的tag值和不同类型的字面量对应关系如下：

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

#### 3.2 int和float数据类型的常量的存储

​		Java语言规范规定了int 类型和float类型的数据类型占用4 个字节的空间。相应地，在常量池中，将int和float类型的常量分别使用CONSTANT_Integer_info和Constant_Float_info表示，他们的结构如下所示：

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

#### 3.3 long和 double数据类型的常量在常量池中的存储

​		Java语言规范规定了long类型和double类型的数据类型占用8 个字节的空间。相应地，在常量池中，将long和double类型的常量分别使用CONSTANT_Long_info和Constant_Double_info表示，他们的结构如下所示：

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

#### 3.4 String类型的字符串常量在常量池中的存储

​		对于字符串而言，JVM会将字符串类型的字面量以UTF-8编码格式存储到在class字节码文件中。

```javascript
CONSTANT_Utf8_info {
		u2	length;
		u1[length] info;
}
```

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

#### 3.5 类文件中定义的类名和类中使用到的类在常量池中是怎样被组织和存储的？

​		JVM会将某个Java类中所有使用到了的类的完全限定名以二进制形式的完全限定名封装成CONSTANT_Class_info结构体中，然后将其放置到常量池里。CONSTANT_Class_info 的tag值为 7 。其结构如下：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310100611078-1830534162.png)

​		(PS：在某个Java源码中，我们会使用很多个类，比如我们定义了一个ClassTest的类，并把它放到com.louis.jvm 包下，则 ClassTest类的完全限定名为com.louis.jvm.ClassTest，将JVM编译器将类编译成class文件后，此完全限定名在class文件中，是以二进制形式的完全限定名存储的，即它会把完全限定符的"."换成"/" ，即在class文件中存储的 ClassTest类的完全限定名称是"com/louis/jvm/ClassTest"。因为这种形式的完全限定名是放在了class二进制形式的字节码文件中，所以就称之为 二进制形式的完全限定名。)		举例，我们定义一个很简单的ClassTest类，来看一下常量池是怎么对类的完全限定名进行存储的。

```java
public class ClassTest {
    private Date date = new Date();
}
```

​		我们通过javap -verbose com.gapache.commons.jvm.bytecode.ClassTest指令来看一下其常量池中的信息，可以看到，在ClassTest.class文件的常量池中共有3个CONSTANT_Class_info结构体，分别表示ClassTest中用到的Class信息。我们就看其中一个表示com/gapache/commons/jvm/bytecode/ClassTest的CONSTANT_Class_info结构体。它在常量池中的位置是#5，它指向了常量池的第#21个常量池项。

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-20%E4%B8%8B%E5%8D%881.05.31.png)

​		对于某个类而言，其class文件中至少要有两个CONSTANT_Class_info常量池项，用来表示自己的类信息和其父类信息。（除了java.lang.Object类除外，其他的任何类都会默认继承自java.lang.Object），如果类声明实现了某些接口，那么接口的信息也会生成对应的

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

#### 3.6 类中引用到的field字段在常量池中是怎样描述的？

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

​		使用javap -verbose com.gapache.commons.jvm.bytecode.Person，查看class文件的信息，你会看到，在Person类中引用到age和name字段的地方，都是指向了常量池中age和name字段对应的常量池项中。表示field字段的常量池项叫做CONSTANT_Field_info。如下所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-20%E4%B8%8B%E5%8D%883.00.19.png)

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/%E6%88%AA%E5%B1%8F2020-03-20%E4%B8%8B%E5%8D%883.00.32.png)

​		怎样描述一个field字段的引用？

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310102335297-130978447.png)

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310102613000-1211659752.png)

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310102623141-1625084358.png)

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/753271-20170310102722578-1808156508.png)

​		（PS：如果我们在类中定义了field字段，但是没有在类中的其他地方用到这些字段，它是不会被编译器放到常量池中的，读者可以自己试一下。）

#### 3.7 类中引用到的method方法在常量池中是怎样描述的？

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

#### 3.8 类中引用到某个接口中定义的method方法在常量池中是怎样描述的？

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

#### 3.9 CONSTANT_MethodHandle_info

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

#### 3.10 CONSTANT_MethodType_info

```java
CONSTANT_MethodType_info {
  u1 tag;
  u2 descriptor_index;
}
```

#### 3.11 CONSTANT_InvokeDynamic_info

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