## Java字节码的方法表与属性表

[TOC]

### 一、方法表

​		紧跟着字段表后面就是方法的个数，由一个u2来表示，然后后面就是方法表。方法表的结构如下所示：

```java
method_info {
  u2 access_flags;
  u2 name_index;
  u2 descriptor_index;
  u2 attributes_count;
  attribute_info attributes[attributes_count];
}
```

​		方法中的每个属性都是一个attribute_info结构：

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

### 二、属性表

​		紧跟着方法表后面就是附加属性的个数，由一个u2表示，最后就是附加的属性表了。附加的属性表的结构可以用上面方法表中所说的方法的属性来表示。或者换句话说，所有的属性都可以用如下所示的结构来表示：

```java
attribute_info {
  u2 attribute_name_index;
  u4 attribute_length;
  u1 info[attribute_length];
}
```

