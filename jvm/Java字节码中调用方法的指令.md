## Java字节码中调用方法的指令

### 一、调用方法的指令：

1. invokeinterface：调用接口中的方法，实际上是在运行期决定的，决定到底调用实现改接口的哪个对象的特定方法。针对于invokeinterface指令来说，虚拟机会建立一个叫做接口方法表的数据结构（interface method table，itable）。
2. invokestatic：调用静态方法。
3. invokespecial：调用自己的私有方法、构造方法以及父类的方法。
4. invokevirtual：调用虚方法，运行期动态查找的过程。
5. invokedynamic：动态调用方法。

静态解析的4种情形：

1. 静态方法
2. 父类方法
3. 构造方法
4. 私有方法

以上4类方法称作非虚方法，他们是在类加载阶段就可以将符号引用转换为直接引用的。

### 二、静态分配

方法重载，是一种静态的行为，编译期就可以完全确定。我们使用方法重载来说明静态分配，代码如下：

```java
public class MyTest5 {

    public void test(Grandpa grandpa) {
        System.out.println("grandpa");
    }

    public void test(Father father) {
        System.out.println("father");
    }

    public void test(Son son) {
        System.out.println("son");
    }

    public static void main(String[] args) {
        Grandpa g1 = new Father();
        Grandpa g2 = new Son();

        MyTest5 myTest5 = new MyTest5();
        myTest5.test(g1);
      	// 输出结果 grandpa
        myTest5.test(g2);
      	// 输出结果 grandpa
    }
}

class Grandpa {

}

class Father extends Grandpa {

}

class Son extends Father {

}
```

​		在这里，g1和g2的静态类型都是Grandpa，而实际类型分别是Father和Son。所以我们可以得出一个结论：变量的静态类型是不会发生变化的，而变量的实际类型则是可以发生变化的（多态的一种体现），实际类型是在运行期方可确定的。

​		而上面的这种方法重载，是一种静态的行为，编译期就可以完全确定，都会调用参数的类型为Grandpa的方法。

### 三、动态分配

方法重写是动态的，是运行期行为，方法的动态分派涉及到一个重要概念：方法接收者。我们用下面的代码来演示一下：

```java
public class MyTest6 {

    public static void main(String[] args) {
        Fruit apple = new Apple();
        Fruit orange = new Orange();

        apple.test();
      	// Apple
        orange.test();
      	// Orange

        apple = new Orange();
        apple.test();
      	// Orange
    }

}

class Fruit {

    public void test() {
        System.out.println("Fruit");
    }
}

class Apple extends Fruit {

    @Override
    public void test() {
        System.out.println("Apple");
    }
}

class Orange extends Fruit {

    @Override
    public void test() {
        System.out.println("Orange");
    }
}
```

​		可以看到输出结果却是和我们想的一样，查看方法的字节码指令，看看调用方法的指令为什么，如图所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/QQ20200417-151620.png)

可以看到都是invokevirtual，所以我们来了解一下invokevirtual字节码指令的动态查找流程。

​		找到操作数栈顶的第一个元素它所指向对象的实际类型，在这个类型里边，然后查找和常量里边Fruit的方法描述符和方法名称都一致的方法，如果在这个类型下，常量池里边找到了就会返回实际对象方法的直接用。

​		如果找不到，就会按照继承体系由下往上(Apple–>Fruit–>Object)查找，查找匹配的方式就是上面描述的方式，一直找到位为止。如果一直找不到就会抛出异常。

​		针对于方法调用动态分派的过程，虚拟机会在类的方法区建立一个虚方法表的数据结构（virtual method table，vtable）。



