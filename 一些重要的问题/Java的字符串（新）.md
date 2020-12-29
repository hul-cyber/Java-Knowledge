# String
在Java中，`String`是一个引用类型，它本身也是一个`class`。但是，Java编译器对`String`有特殊处理，即可以直接用`"..."`来表示一个字符串：
```java
String s1 = "Hello!";
```
实际上字符串在`String`内部是通过一个`char[]`数组表示的，因此，按下面的写法也是可以的：
```java
String s2 = new String(new char[] {'H', 'e', 'l', 'l', 'o', '!'});
```
因为`String`太常用了，所以Java提供了`"..."`这种字符串字面量表示方法。
Java字符串的一个重要特点就是字符串不可变。这种不可变性是通过内部的`private final char[]`字段，以及没有任何修改`char[]`的方法实现的。
```java
public class Main {
    public static void main(String[] args) {
        String s = "Hello";
        System.out.println(s);
        s = s.toUpperCase();
        System.out.println(s);
    }
}
```

## 字符串的比较
当我们想要比较两个字符串是否相同时，要特别注意，我们实际上是想比较字符串的内容是否相同。必须使用`equals()`方法而不能用`==`。
我们看下面的例子：
```java
public class Main {
    public static void main(String[] args) {
        String s1 = "hello";
        String s2 = "hello";
        System.out.println(s1 == s2);
        System.out.println(s1.equals(s2));
    }
}
```
从表面上看，两个字符串用`==`和`equals()`比较都为`true`，但实际上那只是Java编译器在编译期，会自动把所有相同的字符串当作一个对象放入常量池，自然`s1`和`s2`的引用就是相同的。
上面的这段的代码可以这样解释:直接使用双引号声明出来的`String`对象会直接存储在常量池中,所以我们的`s1`指向常量池上的`"hello"`,我们使用`""`来创建字符串对象的时候,**先检查常量池上有没有这个字符串,如果有这个字符串,那么我们就直接引用,如果没有,那么我们就会创建它,然后再引用它。**所以说上面的这个例子中`s1`和`s2`的引用是相同的。但是如果我们向下面这样的话,他们就不会相等了。
```java
public static void main(String[] args) throws IOException {
    String s2 = new String("abc");
    String s1 = "abc";
    System.out.println(s1 == s2);
}
```
这是因为我们使用`new`创建的字符串不会放到常量池中,所以后面的`s2`引用与它不相同。

这里我们补充一个知识点:**对象变量并没有实际包含一个对象,它只是引用一个对象,在Java中,任何对象变量的值都是对存储在另一个地方的某个对象的引用,new操作符的返回值也是一个引用。**

所以，这种`==`比较返回`true`纯属巧合。换一种写法，`==`比较就会失败：
```java
public class Main {
    public static void main(String[] args) {
        String s1 = "hello";
        String s2 = "HELLO".toLowerCase();
        System.out.println(s1 == s2);
        System.out.println(s1.equals(s2));
    }
}
```
也就是说,**字符串操作不改变原字符串内容，而是返回新字符串；**

结论：两个字符串比较，必须总是使用`equals()`方法。
要忽略大小写比较，使用`equalsIgnoreCase()`方法。
`String`类还提供了多种方法来搜索子串、提取子串。常用的方法有：
```java
// 是否包含子串:
"Hello".contains("ll"); // true
```
注意到`contains()`方法的参数是`CharSequence`而不是`String`，因为`CharSequence`是`String`的父类。
搜索子串的更多的例子：
```java
"Hello".indexOf("l"); // 2
"Hello".lastIndexOf("l"); // 3
"Hello".startsWith("He"); // true
"Hello".endsWith("lo"); // true
```
提取子串的例子：
```java
"Hello".substring(2); // "llo"
"Hello".substring(2, 4); "ll"
```
注意索引号是从`0`开始的。

# StringBuilder
Java编译器对`String`做了特殊处理，使得我们可以直接用`+`拼接字符串。
考察下面的循环代码：
```java
String s = "";
for (int i = 0; i < 1000; i++) {
    s = s + "," + i;
}
```
虽然可以直接拼接字符串，但是，在循环中，每次循环都会创建新的字符串对象，然后扔掉旧的字符串。这样，绝大部分字符串都是临时对象，不但浪费内存，还会影响GC效率。上面的代码会转化成`s = (new StringBuilder()).append(s).append(",").append(i).toString();`运行,在某种程度上可以说是节约了内存,因为使用`StringBuilder`来创建字符串的话,不会每拼接一次就创建一个字符串,但即使是这样的话,每次循环都必须要创建`StringBuilder`,因此我们可以将上面的代码修改为:
```java
StringBuilder sb = new StringBuilder(1024);
for (int i = 0; i < 1000; i++) {
    sb.append(',');
    sb.append(i);
}
String s = sb.toString();
```
这样的话,只创建一个`StringBuilder`就可以解决了。

对比`StringBuilder`与`String`不相同的例子:
比如我们想要递归并且按层次打印一个目录下面的文件夹以及文件名,正如下面的这种形式:
```
Documents/
  word/
    1.docx
    2.docx
    work/
      abc.doc
  ppt/
  other/
```
比如我们打印当前目录下的文件以及文件夹名,使用`String`:
```java
public static void main(String[] args) throws IOException {
    File targetFolder=new File(".");
    String indentation="";
    listDir(targetFolder.getCanonicalFile(), indentation);//传入当前路径的规范路径和空字符串
}

static void listDir(File dir, String indentation) {
    System.out.println(indentation + dir.getAbsolutePath());
    if (dir.isDirectory()) {
        File[] files = dir.listFiles();
        for (File file : files) {
            listDir(file, indentation + "    ");
        }
    }
}
```
上面的打印结果没有问题.
使用`StringBuilder`的情况:
```java
public static void main(String[] args) throws IOException {
    File file = new File(".");
    listDir(file.getCanonicalFile(), new StringBuilder(""));
}

static void listDir(File dir, StringBuilder s) {
    if (dir.isDirectory()) {
        System.out.println(s + dir.getAbsoluteFile().toString());
        File[] files = dir.listFiles();
        for (File file : files) {
            listDir(file, s.append("    "));
        }
    } else {
        System.out.println(s + dir.getAbsoluteFile().toString());
    }
}
```
如果使用`StringBuilder`的情况,会出现这样的情况:
```
Documents/
  word/
    1.docx
        2.docx
            work/
                abc.doc
                    ppt/
                        other/
```
即现实下一个目录或者文件的时候,不论是不是下一级目录,都会缩进。
出现上面情况的原因:因为传进去的参数是`StringBuilder()`,并且之后我们传进去的参数都是`s.append("    ")`,也就是每次返回的是同一个对象,当我们对同级的文件进行处理的时候:
```java
for (File file : files) {
    listDir(file, s.append("    "));
}
```
每次执行一个循环,`s`就会增加一个空格,在一次执行循环的时候,必然会比之前多一个空格.事实上,如果两个同级的文件`A`和`B`,如果`A`下面有两个文件的话,那么每遍历一个文件都会给`s`添加一个空格,所以`B`前面应该比`A`多三个空格。
```
D:\learn\notebooks\JavaDemo\JavaBaseDemo\io-file\.idea\dictionaries
    D:\learn\notebooks\JavaDemo\JavaBaseDemo\io-file\.idea\dictionaries\21973.xml
        D:\learn\notebooks\JavaDemo\JavaBaseDemo\io-file\.idea\dictionaries\no
            D:\learn\notebooks\JavaDemo\JavaBaseDemo\io-file\.idea\misc.xml
```
上面的例子中`dictionaries`和`misc.xml`是同级目录。但是`String`不会出现这种情况,因为**字符串操作不改变原字符串内容，而是返回新字符串；**:
```java
for (File file : files) {
    listDir(file, s + "    ");
}
```
`s + "    "`会产生一个新对象传进去,而并不是`s`,并且`s`也没有发生改变,所以当执行下一个循环的时候,`s + "    "`和之前的`s + "    "`是一样的。
所以我们能够看见下面的结果:
```
D:\learn\notebooks\JavaDemo\JavaBaseDemo\io-file\.idea\dictionaries
    D:\learn\notebooks\JavaDemo\JavaBaseDemo\io-file\.idea\dictionaries\21973.xml
    D:\learn\notebooks\JavaDemo\JavaBaseDemo\io-file\.idea\dictionaries\no
D:\learn\notebooks\JavaDemo\JavaBaseDemo\io-file\.idea\misc.xml
```