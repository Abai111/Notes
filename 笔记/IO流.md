# IO流编程

## IO流的释义

IO流是数据传输的通道/管道，是实现数据输入和输出的基础，通过IO流可以完成硬盘文件的读和写

## IO流的分类

**按照流的方向进行分类（以内存作为参照物）：**

输入流：数据从硬盘到内存里去叫作输入或读，可理解为读进去
输出流：数据从内存中出来到硬盘叫作输出或写，可理解为写出来

**按照数据的读取方式进行分类：**

字节流：按照字节的方式读取数据，一次读取一个字节byte，等同于一次读取8个二进制位，这种流是万能的，什么类型的文件都可以读取，包括：文本文件、图片、声音文件、视频文件
字符流：按照字符的方式读取数据，一次读取一个字符，这种流是为了方便读取普通文本文件而存在的，但这种流不能读取：图片、声音、视频等文件，只能读取纯文本文件，也读不了word文件

总结：在Java中只要类名以“Stream”结尾的都是字节流，以“Reader/Writer”结尾的都是字符流

- 字节输入流：InputStream
- 字节输出流：OutputStream
- 字符输入流：Reader
- 字符输出流：Writer

注：1、以上所有的流都实现了Closeable接口，表明所有的流都可以关闭，都有close()方法，切记用完流之后一定要关闭流。2、所有的输出流都实现了Flushable接口，表明所有的输出流都是可以刷新的，都有flush()方法，输出流在最终输出完后一定要记得flush()方法刷新一下，这个刷新表示将通道当中剩余未输出的数据强行输出完（清空通道），如果没有flush()方法可能为导致数据丢失



## File类的释义

java.io.File类是文件和目录路径名的抽象表示。java把电脑的文件和文件夹（文件目录）封装成了一个File类用于文件和文件夹（文件目录的创建）的创建，查找和删除等操作。File类与操作系统无关，任何操作系统都可以使用该类中的方法

## File类书写文件路径

文件夹路径分隔符：File.pathSeparator

文件路径分隔符：File.separator

```java
//路径分隔符符号：window系统：;  linux是：
String pathSeparator = File.pathSeparator;
//文件名称的分隔符 window系统：反斜杠\ linux是正斜杠/
String separator = File.separator;
//路径不要写死，E:\gok\javaSe E:/gok/javaSe
//拼接："E："+File.separator+"gok"+File.separator+"javaSe"
```

绝对路径：绝对路径是一个完整的路径，以盘符为开始

==e.g.==

E：\Aba1\example\a.txt

相对路径：相对路径是简化路径，相对指相当于当前目录的根目录

==e.g.==

a.txt（可以省略项目的根目录）

## File类构造方法

```java
//绝对路径
File file = new File("E:\\xxx\\xxx\\xxx");
System.out.println("file="+file);
//相对路径
File file1 = new File("xxx.txt");
System.out.println("file1="+file1);
//父子类构造方法
File file2 = new File("E:\\xxx\\xxx\\xxx", "a.txt");
System.out.println("file2="+file2);
```



## File类常用方法

#### 获取绝对路径：getAbsolutePath()

```java
File file1= new File("xxxx");
String absolutePath1 = file1.getAbsolutePath();
```

注：无论传入的参数是相对路径还是绝对路径，传出的都是绝对路径

#### 获取路径：getPath()

```java
File file1= new File("xxxx");
String path1 = file1.getPath();
```

注：传入的是绝对路径，返回的也是绝对路径；传入的是相对路径，返回的也是相对路径

#### 获取路径结尾(可以是文件，也可以是文件夹)：getName()

```java
File file1= new File("xxxx");
String name1 = file1.getName();
```

#### 获取文件长度：length()

```java
File file1= new File("xxxx");
long length = file1.length();
```

注：length()文件夹是没有大小的概念，如果构造函数的路径是不存在话，它的长度就是为0

#### 判断文件或文件夹是否存在：exists()

```java
File file1= new File("xxxx");
System.out.println(file1.exists());//true or false
```

#### isDirectory()是否以文件夹为结尾 **,**isFile() 是否是以文件为结尾

```java
File file1= new File("xxxx");
System.out.println(file1.isDirectory());//是否是文件夹true
System.out.println(file1.isFile());//是否是文件 false
if(file1.exists()){
	System.out.println(file1.isDirectory());//是否是文件夹true
	System.out.println(file1.isFile());//是否是文件 false
}
```

注：在电脑里面，只有文件/文件夹 isDirectory和isFile是互斥，这两个方法使用前构造函数传递的路径必须是存在，否则返回都是false

#### 创建文件：createNewfile()

```java
File file1= new File("xxxx");
boolean b1 = file1.createNewFile();//true or false
System.out.println(b1);//1 or 0;
```

注：路径传的是一个目录不存在second  抛异常IOException: 系统找不到指定的路径

#### 创建文件夹：mkdir()/mkdirs()

```java
File file1= new File("xxxx\\1");
System.out.println(file1.mkdir());//true or false
File file4= new File("xxxx\\1\\2\\3");
System.out.println(file4.mkdir());//false
//mkdirs是创建多级目录的时候使用
System.out.println(file4.mkdirs());//true
```

#### 删除文件或文件夹：delete()

```java
File file2= new File("xxxx");
System.out.println(file2.delete());//true or false
```

注：删除是直接走硬盘，不走回收站，==此操作是不可逆的==

#### 用String数组遍历当前文件夹目录底下的所有文件或文件夹：list()

```java
File file2= new File("xxxx");
String[] list = file2.list();
for (String s:list){
	System.out.println(s);
}
```

注：此方法不会展示出已罗列文件夹中的文件且传递出的是相对路径

#### 所以增加以下方法，用listFiles()方法把文件/文件夹封装为File对象

```java
File file2= new File("xxxx");
File[] files = file2.listFiles();
for (File file:files){
    System.out.println(file);
}
```

注：此方法传出的是绝对路径，依然无法解决无法展示文件夹里面的子文件，所以引出以下方法，递归思想

## 递归的释义及分类

**释义：**指当前方法内部调用自己

**分类：**

直接递归：方法自身调用自己

间接递归：A方法调用B方法，B方法调用C方法，C方法调用A方法

==注意事项：==

1.递归一定要有条件限定的，保证递归能够停止下来，否则会发生栈内存溢出

2.在递归中虽然有限定条件，但是递归次数不能太多，否则也会发生栈内存溢出

3.构造方法是禁止递归

**递归调用的前提：**当调用方法的时候，方法的主题不变，每次调用的参数不同，可以使用递归

```java
 //递归一定要有条件限定的，保证递归能够停止下来，否则会发生栈内存溢出
    public  static  void a(){
        System.out.println("a!!!");
        a();
    }
```

```java
public  static  void b(int n){
        //11109java.lang.StackOverflowError
        //在递归中虽然有限定条件，但是递归次数不能太多，否则也会发生栈内存溢出
        System.out.println(n);
        if(n==20000){
            return;
        }
        b(++n);
    }
```

```java
	//构造方法是禁止递归
    //编译报错：构造方法是创建对象的使用，一直递归的话，会导致无数个对象，直接编译报错
    public  Demo01DiGui(){
       Demo01DiGui();
    }
```

## 递归的调用

### e.g.

```java
/**
 * @ClassName: Demo02DiGui
 * @Description:使用递归求和  比如：n=3  实现是3+2+1=6
 * @Date: 2023/9/15
 * @Version: V1.0
 */
public class Demo02DiGui {
    public static void main(String[] args) {
        int sum = sum(3);
        System.out.println(sum);

    }
   //求和递归的方法
    public static  int sum(int n){
        if(n==1){
            return 1;
        }
        return n+sum(n-1);
    }
}
```

### **用递归思想使文件全部遍历**

```java
public class Demo03Digui {
    public static void main(String[] args) {
        File file = new File("xxxx");
        getFileAll(file);

    }
    public  static  void getFileAll(File fir){
        File[] files = fir.listFiles();
        for (File file:files){
            //file有可能是文件夹，若是，则继续遍历
            if(file.isDirectory()){
                //发现getFileAll方法传递的就是文件夹，遍历文件的方法，体现递归
                getFileAll(file);
            }
            //新增需求：查找.java为后缀的文件
            //拿文件的名字，去取后缀.java
            if(file.getName().endsWith(".java")){
                System.out.println(file);
            }
        }
    }
}
```

## 文件过滤器

### 文件过滤器常用接口：FileFilter(用于抽象路径的名(File对象)的过滤器)

```java
public class FilterImpl implements FileFilter {
    @Override
    public boolean accept(File pathname) {
        //添加一层判断
//        if(pathname.isDirectory()){
//            return true;
//        }
//        return pathname.getName().endsWith(".java");
        return pathname.isDirectory()||pathname.getName().endsWith(".java");
    }
}
```

```java
public class Demo01Filter {
    public static void main(String[] args) {
        File file = new File("E:\\gok\\javaSe2301\\first");
        getFileAll(file);

    }

    public  static  void getFileAll(File fir){
        /**一、listFiles方法一共做了3件事情:
        l.listFiles方法会对构造方法中传递的目录进行遍历,获取目录中的文件/文件夹封装为File对象
         2.listFiles方法会调用参数传递的过滤器中的方法accept
         3.listFiles方法会把遍历得到的每一个File对象传递accept方法的参数（File pathName）
         二、accept方法返回值是一个布尔值
         true:把传递过去的File对象(也就是accept方法实参)保存到File数组中
         false:不会把传递过去的File对象保存到File数组中，也是files是空数组（没有元素）
        */
        //new FilterImpl()过滤器对象
        File[] files = fir.listFiles(new FilterImpl());
        for (File file:files){
            //file有可能是文件夹，若是，则继续遍历
            if(file.isDirectory()){
                //发现getFileAll方法传递的就是文件夹，遍历文件的方法，体现递归
                getFileAll(file);
            }else{
                //新增需求：查找.java为后缀的文件
                System.out.println(file);
            }
        }

    }
}
```

## 文件字节输入流

构造方法的作用：
创建的是FileInputStream对象，会把这个指向构造方法中的要读的文件
步骤：
 1、创建FileInputStream对象，绑定要读取文件目的
 2、调用FileInputStream的read方法读取文件
 3、关闭资源
 read()从输入类读取一个字节数据，读取的文件末尾标识是为-1

```java
public class Demo01InputStream {
    public static void main(String[] args) throws IOException {
//        创建FileInputStream对象，绑定要读取文件目的
        FileInputStream fileInputStream = new FileInputStream("a .txt");
        //调用FileInputStream的read方法读取文件
//        int len=fileInputStream.read();
//        System.out.println((char) len);
//         len=fileInputStream.read();
//        System.out.println((char) len);
//        len=fileInputStream.read();
//        System.out.println((char) len);
//         len=fileInputStream.read();
//        System.out.println((char) len);
//        len=fileInputStream.read();
//        System.out.println((char) len);
//        len=fileInputStream.read();
//        System.out.println(len);
        //发现以上的动作内容是重复，--循环 读取文件不知道有多少字节，while
        int len=0;
        while ((len=fileInputStream.read())!=-1){
            System.out.println((char) len);
        }
        //关闭资源
        fileInputStream.close();
    }
}
```

**多个字符读取储存**

```java
public class Demo02InputStream {
    public static void main(String[] args) throws IOException {
        FileInputStream fileInputStream = new FileInputStream("a.txt");
//        byte[] by=new byte[2];//存储每次读取的个数
//        int len=fileInputStream.read(by);
//        System.out.println(len);//读取的是有效字节个数
//        System.out.println(new String(by));
        //while
        byte[] bys=new byte[1024];
        int len=0;
        while ((len=fileInputStream.read(bys))!=-1){
            System.out.println(len);
            System.out.println(new String(bys));
        }
        fileInputStream.close();
    }
}
```

## 文件字节输出流

作用：从内存中的数据写入到硬盘的文件

**构造方法：**

* FileOutputStream(File file)

* FileOutputStream(String name)

  

  **构造方法的作用：**

  

* 1、创建一个FileOutputStream

* 2、会根据传递的参数文件/文件夹，创建一个空的文件

* 3、会把FileOutputStream对象指向创建好的文件

  

  **写入的原理：**

  

* java程序---java虚拟机（JVM）--操作系统---调用数据的方法---数据写入到文件

  

  **字节输出流的使用步骤**

  

* 1、创建一个FileOutputStream对象，传参就是写入数据的目的

* 2、调用FileOutputStream中的write方法

* 3、释放资源（流的使用会占用一定的资源，不需要的话一定要进行关闭）

```java
public class Demo01FileOutputStream {
    public static void main(String[] args) throws IOException {
//        1、创建一个FileOutputStream对象，传参就是写入数据的目的
        FileOutputStream fileOutputStream = new FileOutputStream("a.txt");
        //调用FileOutputStream中的write方法
        fileOutputStream.write(97);
//        //关闭资源
       fileOutputStream.close();
    }
}
```

```java
public class Demo02FileOutputStream {
    public static void main(String[] args) throws IOException {
        FileOutputStream fileOutputStream = new FileOutputStream("b.txt");
        //写入a,b,c
//        byte[] bytes={97,98,99};
        //一次写多个
//        fileOutputStream.write(bytes);
        //如果写的第一个字节是负数，那么第一个字节会合第二字节组成一个中文显示
        //默认回去查询系统的编码（GBK）
        byte[] bytes={-65,-66,-67,68,69};
        fileOutputStream.write(bytes);
        fileOutputStream.close();

    }
}
```

#### 追加/续写

 FileOutputStream(String name, boolean append)
* name：数据输入的目的地

* append：是否进行追加  

*  true：源数据不会被覆盖，继续在源文件末尾进行追加

* false：创建一个新的文件，会取覆盖原来的文件内容

  **换行：**

* wind："\r\n"

* linux:"\n"

*  mac:".\r"

```java
public class Demo03FileOutputStream {
    public static void main(String[] args) throws IOException {
        FileOutputStream fileOutputStream = new FileOutputStream("c.txt",true);
        fileOutputStream.write("星期五".getBytes());
        //换行
        fileOutputStream.write("\r\n".getBytes());
        fileOutputStream.write("您好".getBytes());
        fileOutputStream.close();
    }
}
```

## 字符输入流

* 字符输入流的步骤：

  1、创建FileReader对象，绑定要读取的数据源

  2、使用FileReader对象的read()方法读取文件

  3、关闭资源

```java
public static void main(String[] args) throws IOException {
    FileReader fileReader = new FileReader("2.txt");
    int len=0;
    while ((len=fileReader.read())!=-1){
        System.out.println((char) len);
    }
    fileReader.close();
}
```

## 字符输出流

**构造函数：**
FileWriter(File file) 给一个File对象构造一个FileWriter对象。
FileWriter(String fileName)构造一个给定文件名的FileWriter对象。
file/fileName：写入数据的目的地

**构造方法的作用：**

 * 1、创建一个FileWriter对象

 * 2、会根据传递的参数文件/文件夹，创建一个空的文件

 * 3、会把FileWriter对象指向创建好的文件

   

   **字符输出流的步骤：**

   

 * 1、创建一个FileWriter对象，绑定要写入数据的目的地

 * 2、使用FileWriter对象里面write，把数据写到内存缓冲区（字符转为字节过程）

 * 3、调用FileWriter中flush方法，把内存缓冲的数据刷新到文件当中

 * 4、关闭资源(内存缓冲的数据刷新到文件当中)

```java
public static void main(String[] args) throws IOException {
        //创建一个FileWriter对象，绑定要写入数据的目的地
        FileWriter fileWriter = new FileWriter("d.txt");
        //使用FileWriter对象里面write，把数据写到内存缓冲区（字符转为字节过程）
        fileWriter.write(98);
        //调用FileWriter中flush方法，把内存缓冲的数据刷新到文件当中
        fileWriter.flush();
        //资源的关闭
        fileWriter.close();

    }
```

注：flush:刷新缓冲区，流对象是可以继续使用的；close:先刷新缓冲区，然后进行系统释放资源，内存消失，流对象不能被使用

**写入字符数组**

```java
public static void main(String[] args) throws IOException {
        FileWriter fileWriter = new FileWriter("f.txt");
        //写数据
        char[] chars={'a','b','c','d','f'};
        fileWriter.write(chars,1,3);
        fileWriter.write("goktech");
        fileWriter.write("今天是星期日",1,3);
        //刷新缓冲数据到文件
        fileWriter.flush();
        //关闭资源
        fileWriter.close();
    }
```

**追写/续写**

```java
public static void main(String[] args) throws IOException {
        FileWriter fileWriter = new FileWriter("h.txt",false);
        for (int i=0;i<10;i++){
            fileWriter.write("星期五"+i+"\r\n");
        }
        fileWriter.write("上的内容是IO流");
        fileWriter.close();
    }
```

## 字符输入缓冲流

```java
public static void main(String[] args) throws IOException {
        //创建了BufferedReader对象，提高FileReader读取效率
        BufferedReader br = new BufferedReader(new FileReader("a2.txt"));
        //调用readline方法
        String len=null;
        while((len=br.readLine())!=null){
            System.out.println(len);
        }
        //释放资源
        br.close();
    }
```

注：readLine：读一行文字。 一行被视为由换行符（'\ n'），回车符（'\ r'）中的任何一个或随后的换行符终止。
 返回的结果标识：包含行的内容的字符串，不包括任何行终止字符，如果已达到流的末尾，则为null

## 字符输出缓冲流

BufferedWriter(Writer out)创建使用默认大小的输出缓冲区的缓冲字符输出流。
BufferedWriter(Writer out, int sz)创建一个新的缓冲字符输出流，使用给定大小的输出缓冲区。
还有自己的特殊方法：newLine() 写一行行分隔符。写一行行分隔符。 行分隔符字符串由系统属性line.separator定义，并不一定是单个换行符（'\ n'）字符。
 **步骤：**
1、创建FileWriter对象，构造函数的参数写输入的目的地
 2、创建BufferedWriter对象，提高字符写的效率
 3、调用BufferedWriter的write方法，把数据写入到缓冲区里面
 4、刷新flush把缓冲区的数据写到文件当中
 5、释放资源

```java
public static void main(String[] args) throws IOException {
        //创建FileWriter对象，构造函数的参数写输入的目的地
        FileWriter fileWriter = new FileWriter("a2.txt");
        //创建BufferedWriter对象，提高字符写的效率
        BufferedWriter bw = new BufferedWriter(fileWriter);
        //调用BufferedWriter的write方法，把数据写入到缓冲区里面
        for (int i=0;i<3;i++){
            bw .write("国科科技"+i);
        //bw.write("\r\n");
            bw.newLine();
        }
        //刷新flush把缓冲区的数据写到文件当中
        bw.flush();
        //释放资源
        System.out.println();
    }
```

## 字节输出缓冲流

该类实现缓冲输出流。 通过设置这样的输出流，应用程序可以向底层输出流写入字节，
* 而不必为写入的每个字节导致底层系统的调用。
* BufferedOutputStream(OutputStream out)创建一个新的缓冲输出流，以将数据写入指定的底层输出流。
* BufferedOutputStream(OutputStream out, int size)创建一个新的缓冲输出流，以便以指定的缓冲区大小将数据写入指定的底层输出流。
**使用的步骤：**
* 1、创建FileOuputStream对象，构造方法就是绑定输出流的目的地
* 2、创建BufferedOutputStream对象，提高FileOuputStream对象效率
* 3、使用BufferedOutputStream调用write方法，把数据写到缓冲区
* 4、调用flush的把缓冲区的数据刷新到文件当中
* 5、关闭资源（close方法：先调用flush的方法，释放资源，所以第4步是可以省略）

```java
public static void main(String[] args) throws IOException {
//        1、创建FileOuputStream对象，构造方法就是绑定输出流的目的地
        FileOutputStream fileOutputStream = new FileOutputStream("a1.txt");
        //2、创建BufferedOutputStream对象，提高FileOuputStream对象效率
        BufferedOutputStream bo = new BufferedOutputStream(fileOutputStream);
        //3、使用BufferedOutputStream调用write方法，把数据写到缓冲区
        bo.write("这是字节缓冲流".getBytes());
        //调用flush的把缓冲区的数据刷新到文件当中
        bo.flush();
//      关闭资源
        bo.close();
    }
```

## 字节输入缓冲流

BufferedInputStream(InputStream in)创建一个 BufferedInputStream并保存其参数，输入流 in ，供以后使用。
BufferedInputStream(InputStream in, int size)创建 BufferedInputStream具有指定缓冲区大小，并保存其参数，输入流 in ，供以后使用。
**使用步骤：**
 1、创建FileInputStream对象，构造方法绑定的参数是告诉数据源
 2、创建BufferedInputStream对象，构造方法里面传递的是FileInputStream对象，提高它的读取效率
 3、使用BufferedInputStreamread对象调用read方法
 4、关闭资源

```java
public static void main(String[] args) throws IOException {
        //创建FileInputStream对象，构造方法绑定的参数是告诉数据源
        FileInputStream fileInputStream = new FileInputStream("a1.txt");
        //创建BufferedInputStream对象，构造方法里面传递的是FileInputStream对象，提高它的读取效率
        BufferedInputStream bi = new BufferedInputStream(fileInputStream);
        //使用BufferedInputStreamread对象调用read方法
        int len=0;
                while((len=bi.read())!=-1){
                    System.out.println(len);
                }
                bi.close();
    }
```

