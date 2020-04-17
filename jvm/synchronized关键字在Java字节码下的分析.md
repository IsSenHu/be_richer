## 在Java字节码下的synchronized关键字

写3个被synchronized修饰的方法如下：

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

