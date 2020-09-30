# Java的异常

程序中出现了不正常的情况。

![image-20200826181110169](D:\编辑器\Typora\Typora Files\image-20200826181110169.png)

`Throwable`的下一层分为`Error`和`Exception`，其中`Error`是严重的错误，基本上无能为力。

`Exception`往下分为`RuntimeException`以及非`RuntimeException`。简单来说`RuntimeException`是因为你的问题，就是程序问题。包括下面的问题：

1. 错误的强制类型转换。
2. 数组访问越界。
3. 访问null指针。

上面的问题自己可以通过修正自己的代码来解决，比如，在使用一个变量的时候，可以来检查它是不是为空来避免空指针异常，但是非`RuntimeException`则包括下面问题：

1. 试图超过文件末尾来读取数据。
2. 试图打开一个不存在的文件。
3. 试图根据指定的字符串来查找Class对象，但这个字符串代表的类并不存在。

上面的问题可能不能够仅仅依靠修正程序来解决，因为如果你想检查文件存不存在再打开它，但是检查完之后可能会被消除。或者检查完还未超过文件末尾，但是随后文件内容被删除到读取位置之前。这些全都很难预料。

> Java将派生于Error类或RuntimeException类(以及其子类)的所有异常成为非检查型异常，所有其它的异常称为检查型异常。

上面的这句话是我从Java核心技术上面摘抄出来的，我觉得他这段话有点小问题，派生于Error类，我觉得不能够被称为异常，关于`Throwable`的定义：

> The `Throwable` class is the superclass of all errors and exceptions in the Java language.

很明显errors跟exceptions是不属于一类的。除此之外，我们还需要注意的是：这里说的检查型异常，指的并不是通不过编译，需要在编译的时候就必须要检查出来，现在我们正在讨论的异常以及错误全部都发生在运行中。`RuntimeException`这个名字很容易让人混淆，以为只有这个才是在运行中出现，其实这种异常是由编程导致的，不需要在编译期进行处理(这里的处理指的是不需要提供异常处理器)，所以叫做非检查型异常，而检查型在编译期就会检查是否有异常处理器。

总结上面的话，异常可以被分为两类：1.编译时异常 2.运行时异常 编译时异常也被叫做检查型异常，也就是必须要被显式处理，否则的话通不过编译，而运行时异常也被叫做非检查型异常，不需要显式处理，可以通过编译期。

## 1.JVM的默认处理

JVM默认处理：

1. 把异常的名称，异常的原因以及异常出现的位置全部都显示出来。
2. 异常之后的代码不予处理。

## 2.关于抛出的异常

1. 对于我们要抛出的异常，我们应当尽量抛出检查型异常，事实上，如果一个方法首部不能够列出所有的异常，通不过编译。

   ```java
   void drawImage(int i) throws ArrayIndexOutOfBoundsException {
       
   }
   ```

   上面的就是一种比较坏的风格，因为`ArrayIndexOutOfBoundsException`是一种运行时错误，应该从一开始就将其避免。

2. 异常的“继承”，对于子类覆盖了父类的方法，子类方法中声明的异常不能够比父类方法中声明的异常更通用，但是可以相同，或者更特定。另外还需要说明的是，如果父类方法中没有抛出检查型异常，子类也不能抛出任何检查型异常。

3. 对于捕获的异常，也就是可以通过一个该异常的父类引用来指向。

   异常类`SubException`继承于`Exception`:

   ```java
   public class SubException extends Exception{
       public ParentException() {}
       public ParentException(String gripe) {
           super(gripe);
       }
   }
   ```

   对于`CheckScore`来说，抛出了`SubException`异常：

   ```java
   public void CheckScore(int score) throws SubException {
   
   }
   ```

   那么我们捕获可以使用`Exception`来捕获：

   ```java
   try {
       t.CheckScore(score);
   } catch (Exception e) {
   
   }
   ```

4. A调用B，A抛出的异常可以是B抛出异常的父类

   ```java
   public void Method() throws Exception {
       CheckScore(50);
   }
   
   public void CheckScore(int score) throws SubException {
   
   }
   ```

   

## 3. 如何抛出异常(关于throws与throw)

`throw` 和`throws`全部都是抛出的意思，但是两者的使用不相同。对于`throws`来说。他是用来说明该方法可能会抛出哪些异常的。

```java
public FileInputStream(String name) throws FileNoFoundException
```

上面表明该方法体可能会抛出`FileNoFoundException`这样的异常。个人认为方法体应该这样：

```java
{
    throw new FileNoFoundException();
}
```

或者应该调用可能会抛出该异常的方法：

```java
{
    Method();    //public void Method() throws FileNoFoundException
}
```

上面是我可以想到的能够抛出异常的两种情况，但是对第二种情况，Method方法会调用其它方法，就这样一直回溯下去，总会找到一个`throw new FileNoFoundException()`这样的语句。但实际上：

```java
public int CheckScore(int score) throws SubException {
    return 0;
}
```

但实际上上面这种情况也不会报错，我个人认为如果一个方法在不显式抛出异常的情况下，也可能抛出异常,见如下情况,但这种属于运行时异常,使用`throws`语句并不应该被建议：

```java
int a = 0;
System.out.println(5 / a);
```

上面的方法会出现`java.lang.ArithmeticException`的异常，但这种情况一般都是因为运行时异常，属于非检查型异常，对于我最初说的那种情况，即方法体内未显式抛出异常或调用抛出异常的函数，而方法首部可以抛出异常，一般指的是检查型异常。非检查型异常一般自己可以通过修正代码来解决，而不是通过抛出异常。尽管非检查型异常可以抛出。

还有就是，如果方法体内显式抛出异常或调用抛出异常的函数，那么方法首部必须抛出异常。

```java
public void Method() throws Exception {
    CheckScore(50);
}

public int CheckScore(int score) throws SubException {
    throw new SubException();
}
```

上面的代码很好说明了这种情况，事实上，这也是非常合理的。

## 4.如何处理异常

对于抛出的异常，通常有两种解决方案，一个是对于抛出的异常，利用`try-catch`将其捕捉，另外一种是将其抛出去。如果将异常捕获了，那么就没有必要往上抛了(比如A调用了B，如果B将异常捕获了，那么A就完全看不到B中的异常)

`try-catch`的用法：

```java
try {
    //可能出现异常的代码
} catch(异常类名 对象名) {
    //异常的处理代码
}
```

如果`try`里面的代码出现了异常，那么抛出的异常会被`catch`捕获到，`catch(异常类名 对象名)`这里的对象就指向该异常，我们可以进行相应的处理，这里需要注意的是：程序并不会因为抛出了异常而中断，因为异常已经被捕获了，会继续往下执行。如果我们使用的是向上抛出异常的方式，我们程序在抛出异常的地方就会中断，不会继续往下执行。但是这里需要注意的事情为:
```java
    public static void main(String[] args) {
        System.out.println("开始");
        try {
            int[] a = {1, 2, 3};
            System.out.println(a[3]);
            System.out.println("99999999");
        } catch (ArrayIndexOutOfBoundsException e) {
            e.printStackTrace();
        }
        System.out.println("结束");
    }
```
对于上面的例子,我们可以知道,在`try`下面的代码块中,在语句`System.out.println(a[3])`会抛出异常`ArrayIndexOutOfBoundsException`,事实上,抛出该异常之后,`try`代码块中的剩余内容就不会再执行了,也就是不会再执行`System.out.println("99999999");`语句。上面提到的会继续往下执行,指的是`try-catch`代码块下面的语句`System.out.println("结束");`会继续执行。下面引出关键字`finally`,首先有一个比较大的疑问,就是如果我们使用`catch`捕获了异常之后,下面的程序依旧还会执行,而关键字`finally`的作用的确保在`finally`代码块中的代码必须执行。那么我可以给出这样的解释,就是我们有可能不能够将代码中出现的所有异常全部`catch`到,或者我们并不想处理,这样的情况下,的确会出现后面代码执行的情况。这样的话,我们给出下面的分析:
如果我们可以捕获到所有的异常的话,那么我们就应该能够完全省略掉下面的`finally`关键字,因为下面的异常肯定会执行,所以我们完全没有必要放到`finally`代码块里。
但是我们没有抛出所有异常的情况下,`finally`子句肯定会保证代码的执行。
还有一点我们需要注意的是:
**try语句可以没有catch子句,只有finally子句,当然,也可以只有catch子句,而没有finally子句。**
根据我们之前的描述，我们应该注意的是：上面的异常我们应该成为检查型异常，对于错误和运行时异常，我们往往不会将它们抛出来，错误，我们控制不了，但是对于运行时异常来说，我们完全可以控制，我们可以通过修正代码的方式来避免异常的存在。事实上，对于运行时异常来说，因为我们一般不捕获以及不显式抛出，最后他们会隐式地从`main`方法中抛给JVM。

## 5.创建自己的异常类

如果任何标准异常类都无法描述清楚的问题，那么我们应该创建自己的异常类。我们可以定义一个派生于`Exception`的类，或者派生于`Exception`的子类。一般习惯的做法是这样的：

```java
class XXXException extends Exception {
    public XXXException() {} //默认构造函数
    public XXXException(String gripe) {  //包含详细描述信息的构造器
        super(gripe);
    }
}
```

所有的异常类的父类是`Throwable`，所以我们可以在初始化异常类的时候，也会将`Throwable`初始化。

```java
public Throwable() {} //默认构造函数
```

```java
public Throwable(String message) {	//构造一个新的Throwable对象，带有指定的详细描述信息。
    fillInStackTrace();
    detailMessage = message;
}
```

其中`getMessage`方法获取`Throwable`对象的详细信息：

```java
public String getMessage() {
    return detailMessage;
}
```

如果我们定义的异常类，有包含详细描述信息的构造器，那么也会调用`Throwable`包含详细描述信息的构造器。之后配合`Throwable`的`toString`方法，将会返回详细信息的字符串。

```java
public String toString() {
    String s = getClass().getName();
    String message = getLocalizedMessage();
    return (message != null) ? (s + ": " + message) : s;
}
```

除了上面的`getMessage()`和`toString()`以外，还有一个方法`printStackTrace()`也可以打印异常的信息。但是`getMessage()`最简略，`toString()`居中，`printStackTrace()`最详细。

`getMessage()`只输出异常出现的原因：

![image-20200829093520018](D:\编辑器\Typora\Typora Files\image-20200829093520018.png)

`toString()`还输出异常类名：

![image-20200829093610849](D:\编辑器\Typora\Typora Files\image-20200829093610849.png)

`printStackTrace()`还输出出错位置：

![image-20200829093703338](D:\编辑器\Typora\Typora Files\image-20200829093703338.png)