# IO简介
IO是指Input/Output，即输入和输出。**以内存为中心**(也就是说输入输出是针对内存来说的):
1. Input指从外部读入数据到内存，例如，把文件从磁盘读取到内存，从网络读取数据到内存等等。
2. Output指把数据从内存输出到外部，例如，把数据从内存写入到文件，把数据从内存输出到网络等等。

为什么要把数据读到内存才能处理这些数据？因为代码是在内存中运行的，数据也必须读到内存，最终的表示方式无非是byte数组，字符串等，都必须存放在内存里。

从Java代码来看，输入实际上就是从外部，例如，硬盘上的某个文件，把内容读到内存，并且以Java提供的某种数据类型表示，例如，byte[]，String，这样，后续代码才能处理这些数据。

因为内存有“易失性”的特点，所以必须把处理后的数据以某种方式输出，例如，写入到文件。Output实际上就是把Java表示的数据格式，例如，`byte[]`，`String`等输出到某个地方。

IO流是一种顺序读写数据的模式，它的特点是单向流动。数据类似自来水一样在水管中流动，所以我们把它称为IO流。

## **InputStream / OutputStream**
IO流以byte（字节）为最小单位，因此也称为字节流。例如，我们要从磁盘读入一个文件，包含6个字节，就相当于读入了6个字节的数据：
```
╔════════════╗
║   Memory   ║
╚════════════╝
       ▲
       │0x48
       │0x65
       │0x6c
       │0x6c
       │0x6f
       │0x21
 ╔═══════════╗
 ║ Hard Disk ║
 ╚═══════════╝
```
这6个字节是按顺序读入的，所以是输入字节流。

反过来，我们把6个字节从内存写入磁盘文件，就是输出字节流：
```
╔════════════╗
║   Memory   ║
╚════════════╝
       │0x21
       │0x6f
       │0x6c
       │0x6c
       │0x65
       │0x48
       ▼
 ╔═══════════╗
 ║ Hard Disk ║
 ╚═══════════╝
```
在Java中，`InputStream`代表输入字节流，`OutputStream`代表输出字节流，这是最基本的两种IO流。

## **Reader / Writer**
如果我们需要读写的是字符，并且字符不全是单字节表示的ASCII字符，那么，按照`char`来读写显然更方便，这种流称为**字符流**。
Java提供了`Reader`和`Writer`表示字符流，字符流传输的最小数据单位是`char`。
例如，我们把`char[]`数组`Hi你好`这4个字符用`Writer`字符流写入文件，并且使用UTF-8编码，得到的最终文件内容是8个字节，英文字符`H`和`i`各占一个字节，中文字符`你好`各占3个字节：
```
0x48
0x69
0xe4bda0
0xe5a5bd
```
反过来，我们用`Reader`读取以UTF-8编码的这8个字节，会从`Reader`中得到`Hi你好`这4个字符。
因此，`Reader`和`Writer`本质上是一个能自动编解码的`InputStream`和`OutputStream`。
使用`Reader`，数据源虽然是字节，但我们读入的数据都是`char`类型的字符，原因是`Reader`内部把读入的`byte`做了解码，转换成了`char`。使用`InputStream`，我们读入的数据和原始二进制数据一模一样，是`byte[]`数组，但是我们可以自己把二进制`byte[]`数组按照某种编码转换为字符串。究竟使用`Reader`还是`InputStream`，要取决于具体的使用场景。如果数据源不是文本，就只能使用`InputStream`，如果数据源是文本，使用`Reader`更方便一些。`Writer`和`OutputStream`是类似的。

## 同步和异步
同步IO是指，读写IO时代码必须等待数据返回后才继续执行后续代码，它的优点是代码编写简单，缺点是CPU执行效率低。

而异步IO是指，读写IO时仅发出请求，然后立刻执行后续代码，它的优点是CPU执行效率高，缺点是代码编写复杂。

Java标准库的包`java.io`提供了同步IO，而`java.nio`则是异步IO。上面我们讨论的`InputStream`、`OutputStream`、`Reader`和`Writer`都是同步IO的抽象类，对应的具体实现类，以文件为例，有`FileInputStream`、`FileOutputStream`、`FileReader`和`FileWriter`。

本节我们只讨论Java的同步IO，即输入/输出流的IO模型。

## 小结
IO流是一种流式的数据输入/输出模型：

1. 二进制数据以byte为最小单位在InputStream/OutputStream中单向流动；

2. 字符数据以char为最小单位在Reader/Writer中单向流动。

Java标准库的java.io包提供了同步IO功能：

1. 字节流接口：InputStream/OutputStream；

2. 字符流接口：Reader/Writer。

# File对象
在计算机系统中，文件是非常重要的存储方式。Java的标准库java.io提供了File对象来操作文件和目录。
要构造一个File对象，需要传入文件路径：
```java
import java.io.*;
public class Main {
    public static void main(String[] args) {
        File f = new File("C:\\Windows\\notepad.exe");
        System.out.println(f);
    }
}
```
构造File对象时，既可以传入绝对路径，也可以传入相对路径。绝对路径是以根目录开头的完整路径，例如：
```java
File f = new File("C:\\Windows\\notepad.exe");
```
注意Windows平台使用`\`作为路径分隔符，在Java字符串中需要用`\\`表示一个`\`。Linux平台使用`/`作为路径分隔符：
传入相对路径时，相对路径前面加上当前目录就是绝对路径：
```java
// 假设当前目录是C:\Docs
File f1 = new File("sub\\javac"); // 绝对路径是C:\Docs\sub\javac
File f3 = new File(".\\sub\\javac"); // 绝对路径是C:\Docs\sub\javac
File f3 = new File("..\\sub\\javac"); // 绝对路径是C:\sub\javac
```
可以用`.`表示当前目录，`..`表示上级目录。
File对象有3种形式表示的路径，一种是`getPath()`，返回构造方法传入的路径，一种是`getAbsolutePath()`，返回绝对路径，一种是`getCanonicalPath`，它和绝对路径类似，但是返回的是规范路径。

## `getPath()`、`getAbsolutePath()`与`getCanonicalPath`
1. getPath()
   `getPath()`会将传进来来的参数直接返回。
2. getAbsolutePath()
   该函数返回给定文件对象的绝对路径名，如果文件对象的路径名是绝对的，那么它只是返回当前文件对象的路径。
   如果我们使用路径为 "program.txt "创建一个文件对象，它将指向保存可执行程序的同一目录下的文件（如果你使用的是IDE，它将指向你保存程序的文件）。这里上面提到的文件路径是 "program.txt"，但这个路径不是绝对的（即不完整）。函数getAbsolutePath()将从根目录返回绝对（完整）路径。如果文件对象是用绝对路径创建的，那么getPath()和getAbsolutePath()将给出相同的结果。
   例子1：
   在目前的工作目录中，有一个名为 "program.txt "的文件。工作目录:
   `C:\Users\pc\eclipse-workspace1\arnab`
   ```java
    import java.io.*; 
  
    public class solution { 
        public static void main(String args[]) 
        { 
    
            // try-catch block to handle exceptions 
            try { 
    
                // Create a file object 
                File f = new File("program.txt"); 
    
                // Get the absolute path of file f 
                String absolute = f.getAbsolutePath(); 
    
                // Display the file path of the file object 
                // and also the file path of absolute file 
                System.out.println("Original  path: "
                                + f.getPath()); 
                System.out.println("Absolute  path: "
                                + absolute); 
            } 
            catch (Exception e) { 
                System.err.println(e.getMessage()); 
            } 
        } 
    } 
   ```
   输出:
   ```
   Original Path: program.txt
   Absolute Path: C:\Users\pc\eclipse-workspace1\arnab\program.txt
   ```
   例子3:
   ```java
    // Java program to demonstrate the 
    // use of getAbsolutePath() function 

    import java.io.*; 

    public class solution { 
        public static void main(String args[]) 
        { 

            // try catch block to handle exceptions 
            try { 

                // Create a file object 
                File f = new File("f:\\program.txt"); 

                // get the absolute path 
                // of file f 
                String absolute = f.getAbsolutePath(); 

                // display the file path of the file object 
                // and also the file path of absolute file 
                System.out.println("Original path: "
                                + f.getPath()); 
                System.out.println("Absolute path: "
                                + absolute); 
            } 
            catch (Exception e) { 
                System.err.println(e.getMessage()); 
            } 
        } 
    } 
   ```
    输出
    ```
    Original file path: f:\program.txt
    Absolute file path: f:\program.txt
    ```
3. getCanonicalPath()
   
   这个函数返回给定文件对象的`Canonical`路径名，如果文件对象的路径名是`Canonical`的，那么它只是返回当前文件对象的路径。`Canonical`路径总是绝对和唯一的，如果路径中存在`'.''...'`，则函数将其删除。
   如果我们使用路径为 "program.txt "创建一个文件对象，它将指向保存可执行程序的同一目录下的文件（如果你使用的是IDE，它将指向你保存程序的文件）。这里上面提到的文件路径是 "program.txt"，但这个路径不是绝对的（即不完整）。函数`getCanonicalPath()`将返回一个路径，这个路径将是根目录的绝对和唯一路径。一个现有文件的规范形式可能与同一个非现有文件的规范形式不同，一个现有文件的规范形式可能与同一个文件被删除时的规范形式不同。
   例子1：
   ```java
    import java.io.*; 
  
    public class solution { 
        public static void main(String args[]) 
        { 
            // try-catch block to handle exceptions 
            try { 
    
                // Create a file object 
                File f = new File("c:\\program"); 
    
                // Get the Canonical path of file f 
                String canonical = f.getCanonicalPath(); 
    
                // Display the file path of the file object 
                // and also the file path of Canonical file 
                System.out.println("Original file path : "
                                + f.getPath()); 
                System.out.println("Canonical file path : "
                                + canonical); 
            } 
            catch (Exception e) { 
                System.err.println(e.getMessage()); 
            } 
        }
    } 
   ```
   输出
   ```
   Original file path : c:\program
   Canonical file path : C:\program
   ```
   例子2：
   ```java
    // Java program to demonstrate the 
    // use of getCanonicalPath() function 

    import java.io.*; 
    public class solution { 
        public static void main(String args[]) 
        { 
            // try-catch block to handle exceptions 
            try { 

                // Create a file object 
                File f = new File("c:\\users\\..\\program"); 

                // Get the Canonical path of file f 
                String canonical = f.getCanonicalPath(); 

                // Display the file path of the file object 
                // and also the file path of Canonical file 
                System.out.println("Original file path : "
                                + f.getPath()); 
                System.out.println("Canonical file path : "
                                + canonical); 
            } 
            catch (Exception e) { 
                System.err.println(e.getMessage()); 
            } 
        } 
    } 
   ```
   输出：
   ```
   Original file path : c:\users\..\program
   Canonical file path : C:\program
   ```
上面的三个参数,完全不需要关心传进来的参数代表的目录存不存在。
我们来讨论一下`getCanonicalPath()`与`getAbsolutePath()`,`getCanonicalPath()`会对参数路径进行很细致的处理,比方说,我们传进来的是一个绝对路径`c:\\program`,会整理成`C:\\program`,但是`getAbsolutePath()`不会进行这样的处理。对于`c:\\users\\..\\program`的处理,`getCanonicalPath()`会将`..`转化为上层目录,也就是说到达`users`之后,之后到达`..`之后,会转到上一层`c`,之后继续向下,最终结果为:`c:\\program`.但是`getAbsolutePath()`不会进行转义,就当成一个普通的文件夹的名字处理,如果出现在最后,会当成文件名处理。也就是说`getCanonicalPath()`会将我们真正想要的路径解析出来。
什么是规范路径？我们看以下代码：
```java
import java.io.*;
public class Main {
    public static void main(String[] args) throws IOException {
        File f = new File("..");
        System.out.println(f.getPath());
        System.out.println(f.getAbsolutePath());
        System.out.println(f.getCanonicalPath());
    }
}
```
绝对路径可以表示成`C:\Windows\System32\..\notepad.exe`，而规范路径就是把`.`和`..`转换成标准的绝对路径后的路径：`C:\Windows\notepad.exe`。

因为Windows和Linux的路径分隔符不同，File对象有一个静态变量用于表示当前平台的系统分隔符：
```java
System.out.println(File.separator); // 根据当前平台打印"\"或"/"
```

## 文件和目录
`File`对象既可以表示文件，也可以表示目录。特别要注意的是，构造一个`File`对象，即使传入的文件或目录不存在，代码也不会出错，因为构造一个`File`对象，并不会导致任何磁盘操作。只**有当我们调用`File`对象的某些方法的时候，才真正进行磁盘操作**。
例如，调用`isFile()`，判断该`File`对象是否是一个已存在的文件，调用`isDirectory()`，判断该`File`对象是否是一个已存在的目录：
```java
import java.io.*;
public class Main {
    public static void main(String[] args) throws IOException {
        File f1 = new File("C:\\Windows");
        File f2 = new File("C:\\Windows\\notepad.exe");
        File f3 = new File("C:\\Windows\\nothing");
        System.out.println(f1.isFile());
        System.out.println(f1.isDirectory());
        System.out.println(f2.isFile());
        System.out.println(f2.isDirectory());
        System.out.println(f3.isFile());
        System.out.println(f3.isDirectory());
    }
}
```
上面的操作全部都是针对已经存在的文件,如果文件不存在的话,就会返回`false`.

用`File`对象获取到一个文件时，还可以进一步判断文件的权限和大小：

+ `boolean canRead()`：是否可读；
+ `boolean canWrite()`：是否可写；
+ `boolean canExecute()`：是否可执行；
+ `long length()`：文件字节大小。

对目录而言，是否可执行表示能否列出它包含的文件和子目录。

## 创建和删除文件
当File对象表示一个文件时，可以通过`createNewFile()`创建一个新文件，用`delete()`删除该文件：
```java
File file = new File("/path/to/file");
if (file.createNewFile()) {
    // 文件创建成功:
    // TODO:
    if (file.delete()) {
        // 删除文件成功:
    }
}
```
有些时候，程序需要读写一些临时文件，File对象提供了createTempFile()来创建一个临时文件，以及deleteOnExit()在JVM退出时自动删除该文件。
```java
import java.io.*;
public class Main {
    public static void main(String[] args) throws IOException {
        File f = File.createTempFile("tmp-", ".txt"); // 提供临时文件的前缀和后缀
        f.deleteOnExit(); // JVM退出时自动删除
        System.out.println(f.isFile());
        System.out.println(f.getAbsolutePath());
    }
}
```

## 遍历文件和目录
当File对象表示一个目录时，可以使用`list()`和`listFiles()`列出目录下的文件和子目录名。`listFiles()`提供了一系列重载方法，可以过滤不想要的文件和目录：
```java
import java.io.*;
public class Main {
    public static void main(String[] args) throws IOException {
        File f = new File("C:\\Windows");
        File[] fs1 = f.listFiles(); // 列出所有文件和子目录
        printFiles(fs1);
        File[] fs2 = f.listFiles(new FilenameFilter() { // 仅列出.exe文件
            public boolean accept(File dir, String name) {
                return name.endsWith(".exe"); // 返回true表示接受该文件
            }
        });
        printFiles(fs2);
    }

    static void printFiles(File[] files) {
        System.out.println("==========");
        if (files != null) {
            for (File f : files) {
                System.out.println(f);
            }
        }
        System.out.println("==========");
    }
}
```
和文件操作类似，File对象如果表示一个目录，可以通过以下方法创建和删除目录：
+ `boolean mkdir()`：创建当前File对象表示的目录；
+ `boolean mkdirs()`：创建当前File对象表示的目录，并在必要时将不存在的父目录也创建出来；
+ `boolean delete()`：删除当前File对象表示的目录，当前目录必须为空才能删除成功。

## Path
Java标准库还提供了一个`Path`对象，它位于`java.nio.file`包。`Path`对象和`File`对象类似，但操作更加简单：
```java
import java.io.*;
import java.nio.file.*;

public class Main {
    public static void main(String[] args) throws IOException {
        Path p1 = Paths.get(".", "project", "study"); // 构造一个Path对象
        System.out.println(p1);
        Path p2 = p1.toAbsolutePath(); // 转换为绝对路径
        System.out.println(p2);
        Path p3 = p2.normalize(); // 转换为规范路径
        System.out.println(p3);
        File f = p3.toFile(); // 转换为File对象
        System.out.println(f);
        for (Path p : Paths.get("..").toAbsolutePath()) { // 可以直接遍历Path
            System.out.println("  " + p);
        }
    }
}
```
其中最后的遍历
```java
for (Path p : Paths.get("..").toAbsolutePath()) { // 可以直接遍历Path
    System.out.println("  " + p);
}
```
就是将整个路径上的各级文件/文件夹的名称打印出来