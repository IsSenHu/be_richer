## 分析Java字节码中的实例方法的this参数

​		编译后每一个实例方法的第一个参数都是this，方法参数的数量总是会比源代码中方法参数的数量多一个，所以在实例方法的局部变量表中，至少会有一个指向当前对象的局部变量。

​		我们MyTest3来做实验，代码如下：

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

​		使用javap -verbose MyTest3得到如下信息，明明源代码中方法的参数为0个，但是编译后显示参数数量为1：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/WX20200413-171605.png)

​	 	在局部变量表中，也可以看到this，如下所示：

![](http://studysssmd.oss-cn-chengdu.aliyuncs.com/jvm/byte_code/WX20200413-171849.png)

​		这说明编译期在编译的时候，会自动的将当前对象this作为实例方法的第一个参数，并且在局部变量表中进行记录。