> 本文的主要内容来自
> https://how2playlife.com/2019/09/04/4final%E5%85%B3%E9%94%AE%E5%AD%97%E7%89%B9%E6%80%A7/
以及
> https://www.cnblogs.com/dolphin0520/p/3736238.html
如有侵权,请联系作者
# final关键字
final关键字在java中使用非常广泛，可以申明成员变量、方法、类、本地变量。一旦将引用声明为final，将无法再改变这个引用。final关键字还能保证内存同步，本博客将会从final关键字的特性到从java内存层面保证同步讲解。这个内容在面试中也有可能会出现。

## final使用

### final变量
final变量有成员变量或者是本地变量(方法内的局部变量)，在类成员中final经常和static一起使用，作为类常量使用。其中类常量必须在声明时初始化，final成员常量可以在构造函数初始化。
```java
    public static final int i; //报错，必须初始化 因为常量在常量池中就存在了，调用时不需要类的初始化，所以必须在声明时初始化
    public static final int j;
    Main() {
        i = 2;
        j = 3;
    }
```
```java
public class Main {
    public static final int i = 2;
    Main() {
        System.out.println("调用构造函数"); // 该方法不会调用
    }
    public static void main(String[] args) {
        System.out.println(Main.i);
    }
}
```

### final修饰基本数据类型变量和引用
```java
@Test
public void final修饰基本类型变量和引用() {
    final int a = 1;
    final int[] b = {1};
    final int[] c = {1};
//  b = c;报错
    b[0] = 1;
    final String aa = "a";
    final Fi f = new Fi();
    //aa = "b";报错
    // f = null;//报错
    f.a = 1;
}
```
**对于一个final变量，如果是基本数据类型的变量，则其数值一旦在初始化之后便不能更改；如果是引用类型的变量，则在对其初始化之后便不能再让其指向另一个对象**。
final方法表示该方法不能被子类的方法重写，将方法声明为final，在编译的时候就已经静态绑定了，不需要在运行时动态绑定。final方法调用时使用的是invokespecial指令。
```java
class PersonalLoan{
    public final String getName(){
        return"personal loan”;
    }
}
```
```java
class CheapPersonalLoan extends PersonalLoan{
    @Override
    public final String getName(){
        return"cheap personal loan";//编译错误，无法被重载
    }

    public String test() {
        return getName(); //可以调用，因为是public方法
    }
}
```
### final 修饰类
当用final修饰一个类时，表明这个类不能被继承。也就是说，如果一个类你永远不会让他被继承，就可以用final进行修饰。final类中的成员变量可以根据需要设为final，但是要注意final类中的所有成员方法都会被隐式地指定为final方法。
**在使用final修饰类的时候，要注意谨慎选择，除非这个类真的在以后不会用来继承或者出于安全的考虑，尽量不要将类设计为final类。**
当用final修饰一个类时，表明这个类不能被继承。也就是说，如果一个类你永远不会让他被继承，就可以用final进行修饰。final类中的成员变量可以根据需要设为final，但是要注意final类中的所有成员方法都会被隐式地指定为final方法。

### final修饰方法
final修饰的方法不可以被重写
final修饰的方法仅仅是不能重写，但它完全可以被重载
父类中private final方法，子类可以重新定义，这种情况不是重写
```java
规则1代码

public class FinalMethodTest {
	public final void test() {

    }
}
class Sub extends FinalMethodTest {
	// 下面方法定义将出现编译错误，不能重写final方法
	public void test() {

    }
}

规则2代码

public class FinalOverload {
	//final 修饰的方法只是不能重写，完全可以重载
	public final void test() {

    }
	public final void test(String arg) {

    }
}

规则3代码

public class PrivateFinalMethodTest {
	private final void test() {

    }
}
class Sub extends PrivateFinalMethodTest {
	// 下面方法定义将不会出现问题
	public void test() {

    }
}
```
对于父类中的`private`方法,子类完全不会被继承下来,所以说子类中的`test()`和父类中的`test()`没有任何关系。

### 对于final关键字知识点的总结
1. final成员变量必须在声明的时候初始化或者在构造器中初始化，否则就会报编译错误。final变量一旦被初始化后不能再次赋值。
2. 局部变量在使用前初始化即可.
3. 匿名内部类和局部内部类里面的变量全都是final变量
4. 接口中声明的变量必须是final变量
5. final和abstract这两个关键字是反相关的，final类就不可能是abstract的。
6. final方法在编译阶段绑定，称为静态绑定(static binding)
7. 将类、方法、变量声明为final能够提高性能，这样JVM就有机会进行估计，然后优化。
8. final类不能够被继承,final方法不能够被重写

### final的好处
1. 提高了性能，JVM在常量池中会缓存final变量
2. final变量在多线程中并发安全，无需额外的同步开销
3. final方法是静态编译的，提高了调用速度
4. final类创建的对象是只可读的，在多线程可以安全共享

### final的内存分配
刚提到了内嵌机制，现在详细展开。 要知道调用一个函数除了函数本身的执行时间之外，还需要额外的时间去寻找这个函数（类内部有一个函数签名和函数地址的映射表）。所以减少函数调用次数就等于降低了性能消耗。
final修饰的函数会被编译器优化，优化的结果是减少了函数调用的次数。如何实现的，举个例子给你看：
```java
public class Test{ 
    final void func() {
        System.out.println("g");
    };

    public void main(String[] args) { 
        for(int j=0;j<1000;j++)   
            func(); 
        }
    } 
}
经过编译器优化之后，这个类变成了相当于这样写： 
public class Test{ 
    final void func() {
        System.out.println("g");
    }; 

    public void main(String[] args) { 
        for(int j=0;j<1000;j++) {
            System.out.println("g");
        } 
    }
}
```

看出来区别了吧？编译器直接将func的函数体内嵌到了调用函数的地方，这样的结果是节省了1000次函数调用，当然编译器处理成字节码，只是我们可以想象成这样，看个明白。

不过，当函数体太长的话，用final可能适得其反，因为经过编译器内嵌之后代码长度大大增加，于是就增加了jvm解释字节码的时间。

在使用final修饰方法的时候，编译器会将被final修饰过的方法插入到调用者代码处，提高运行速度和效率，但被final修饰的方法体不能过大，编译器可能会放弃内联，但究竟多大的方法会放弃，我还没有做测试来计算过。

下面这些内容是通过两个疑问来继续阐述的
### 使用final修饰方法会提高速度和效率吗?
```java
public class Test   
{   
    public static void getJava()   
    {   
        String str1 = "Java ";   
        String str2 = "final ";   
        for (int i = 0; i < 10000; i++)   
        {   
            str1 += str2;   
        }   
    }   
    public static final void getJava_Final()   
    {   
        String str1 = "Java ";   
        String str2 = "final ";   
        for (int i = 0; i < 10000; i++)   
        {   
            str1 += str2;   
        }   
    }   
    public static void main(String[] args)   
    {   
        long start = System.currentTimeMillis();   
        getJava();   
        System.out.println("调用不带final修饰的方法执行时间为:" + (System.currentTimeMillis() - start) + "毫秒时间");   
        start = System.currentTimeMillis();   
        String str1 = "Java ";   
        String str2 = "final ";   
        for (int i = 0; i < 10000; i++)   
        {   
            str1 += str2;   
        }   
        System.out.println("正常的执行时间为:" + (System.currentTimeMillis() - start) + "毫秒时间");   
        start = System.currentTimeMillis();   
        getJava_Final();   
        System.out.println("调用final修饰的方法执行时间为:" + (System.currentTimeMillis() - start) + "毫秒时间");   
    }   
}  
```
上面的代码执行五次,得出五个执行结果:
```
调用不带final修饰的方法执行时间为:380毫秒时间
正常的执行时间为:392毫秒时间
调用final修饰的方法执行时间为:322毫秒时间

调用不带final修饰的方法执行时间为:406毫秒时间
正常的执行时间为:384毫秒时间
调用final修饰的方法执行时间为:342毫秒时间

调用不带final修饰的方法执行时间为:385毫秒时间
正常的执行时间为:363毫秒时间
调用final修饰的方法执行时间为:342毫秒时间

调用不带final修饰的方法执行时间为:400毫秒时间
正常的执行时间为:361毫秒时间
调用final修饰的方法执行时间为:344毫秒时间

调用不带final修饰的方法执行时间为:391毫秒时间
正常的执行时间为:360毫秒时间
调用final修饰的方法执行时间为:343毫秒时间
```
加上了final之后速度的确与正常执行的速度很接近,并且快于不加final的方法调用。

### 关于final的参数问题
"当你在方法中不需要改变作为参数的对象变量时，明确使用final进行声明，会防止你无意的修改而影响到调用方法外的变量",上面这句话是错误的,参数无论是基本数据类型还是引用类型的变量,都不能够达到这样的结果。
首先因为java是值传递的原因,对于基本变量来说,作为参数传递到别的方法体之后根本得不到修改。比如下面的这个例子:
```java
void changeValue(final int i) {
    i++;
}
```
对于上面的这个例子,编译器会报错,但是即使你不带final关键字,上面的i也不会变,因为形参这个变量和之前作为实参的这个变量根本就是两个不同的变量,两个变量产生不了影响.
对于参数是引用类型,final关键字也完全起不到作用:
```java
public class Test {
    public static void main(String[] args)  {
        MyClass myClass = new MyClass();
        StringBuffer buffer = new StringBuffer("hello");
        myClass.changeValue(buffer);
        System.out.println(buffer.toString());
    }
}
 
class MyClass {
     
    void changeValue(final StringBuffer buffer) {
        buffer.append("world");
    }
}
```
上面这个代码,运行起来是正常的,因为final修饰引用类型变量,是保证其里面的内容不可变,里面存储的内容是地址,也就是说不能够引用其它的变量,也就是说在里面`buffer = new BuilderBuffer();`,会出现错误,但是你可以改变其指向对象里面的内容。

事实上,即使你不加关键字final,你使用上面的代码`buffer = new BuilderBuffer();`,也不会起到任何作用,原来的实参也不会发生任何变化,就如我们之前提到过的那样,因为`Java`里面是值传递,实参和形参自身值的改变根本不会对另外一方产生任何影响。

## final与jvm的关系
