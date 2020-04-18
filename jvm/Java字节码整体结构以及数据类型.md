## Java字节码整体结构以及数据类型

[TOC]

### 一、Java字节码整体结构

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

### 二、Class字节码中有两种数据类型

- 字节数据直接量：这是基本的数据类型。共细分为u1、u2、u4、u8四种，分别代表连续的1个字节、2个字节、4个字节、8个字节组成的整体数据。
- 表（数组）：表是由多个基本数据或其他表，按照既定顺序组成的大的数据集合。表是有结构的，它的结构体现在：组成表的成分所在的位置和顺序都是已经严格定义好的。