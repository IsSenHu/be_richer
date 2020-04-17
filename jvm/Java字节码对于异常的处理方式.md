## Java字节码对于异常的处理方式

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

