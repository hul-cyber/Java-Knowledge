# Java代码块和执行顺序
该篇文章的主要内容来源于:
https://how2playlife.com/2019/09/07/7%E4%BB%A3%E7%A0%81%E5%9D%97%E5%92%8C%E4%BB%A3%E7%A0%81%E6%89%A7%E8%A1%8C%E9%A1%BA%E5%BA%8F/
https://juejin.im/entry/6844903460119248910
https://blog.csdn.net/Dustin_CDS/article/details/79143760
https://www.cnblogs.com/twoheads/p/10244017.html
如有侵权,请转告作者。
## Java中的构造方法
```java
class class_name
{
    public class_name(){}    //默认无参构造方法
    public class_name([paramList]){}    //定义构造方法
    …
    //类主体
}
```
无参构造方法和有参构造方法
```java
public class MyClass
{
    private int m;    //定义私有变量
    MyClass()
    {
        //定义无参的构造方法
        m=0;
    }
    MyClass(int m)
    {
        //定义有参的构造方法
        this.m=m;
    }
}
```
**类的构造方法不是要求必须定义的。如果在类中没有定义任何一个构造方法，则 Java 会自动为该类生成一个默认的构造方法。默认的构造方法不包含任何参数，并且方法体为空。如果类中显式地定义了一个或多个构造方法，则 Java 不再提供默认构造方法。**

### Java中的几种构造方法

#### 普通构造方法
方法名与类名相同
无返回类型
子类不能继承父类的构造方法
不能被static、final、abstract修饰（有final和static修饰的是不能被子类继承的，abstract修饰的是抽象类，抽象类是不能实例化的，也就是不能new）

（类的静态变量和静态方法能否被子类继承？）

结论：java中静态属性和静态方法可以被继承，但是没有被重写(overwrite)而是被隐藏.
原因：
1). 静态方法和属性是属于类的，调用的时候直接通过类名.方法名完成对，不需要继承机制及可以调用。如果子类里面定义了静态方法和属性，那么这时候父类的静态方法或属性称之为"隐藏"。如果你想要调用父类的静态方法和属性，直接通过父类名.方法或变量名完成，至于是否继承一说，子类是有继承静态方法和属性，但是跟实例方法和属性不太一样，存在"隐藏"的这种情况。
2). 多态之所以能够实现依赖于继承、接口和重写、重载（继承和重写最为关键）。有了继承和重写就可以实现父类的引用指向不同子类的对象。重写的功能是："重写"后子类的优先级要高于父类的优先级，但是“隐藏”是没有这个优先级之分的。
3). 静态属性、静态方法和非静态的属性都可以被继承和隐藏而不能被重写，因此不能实现多态，不能实现父类的引用可以指向不同子类的对象。非静态方法可以被继承和重写，因此可以实现多态。
```java
package com.study.test;
 
public class StaticExtendsTest {
    public static void main(String[] args) {
        C c = new C();
        System.out.println(c.nonStaticStr);
        System.out.println(c.staticStr);
        c.staticMethod();//输出的结果都是父类中的非静态属性、静态属性和静态方法,推出静态属性和静态方法可以被继承
        
        System.out.println("-------------------------------");
        
        A c1 = new C();
        System.out.println(c1.nonStaticStr);
        System.out.println(c1.staticStr);
        c1.staticMethod();//结果同上，输出的结果都是父类中的非静态属性、静态属性和静态方法,推出静态属性和静态方法可以被继承
    
        System.out.println("-------------------------------");
        B b = new B();
        System.out.println(b.nonStaticStr);
        System.out.println(b.staticStr);
        b.staticMethod();
        
        System.out.println("-------------------------------");
        A b1 = new B();
        System.out.println(b1.nonStaticStr);
        System.out.println(b1.staticStr);
        b1.staticMethod();//结果都是父类的静态方法，说明静态方法不可以被重写，不能实现多态
    }
}
```
输出结果为:
```
测试结果如下：A非静态属性
A静态属性
A静态方法
-------------------------------
A非静态属性
A静态属性
A静态方法
-------------------------------
B改写后的非静态属性
B改写后的静态属性
B改写后的静态方法
-------------------------------
A非静态属性
A静态属性
A静态方法
```
#### 默认构造方法
如果没有任何的构造方法，编译时系统会自动添加一个默认无参构造方法.

#### 重载构造方法
```java
public class A{
    private int age;
    private String name;
    public A(int age,String name){
        this.age=age;
        this.name=name;
    }
    public A(int age){
        this(age,"无名氏");//调用 A(int age,String name)构造方法
    }
    public A(){
        this(1);//调用 A(int age)构造方法
    }
    public void setName(String name) {this.name=name;}
    public String getName() {return name;}
    public void setAge(int age) {this.age=age;}
    public int getAge() {return age;}
}

A a=new A(20,"周一");
A b=new A(20);
A c=new A();
String name = a.getName();
String name1 = b.getName();
int age = c.getAge();
System.out.println(name);
System.out.println(name1);
System.out.println(age);
```

#### java子类构造方法调用父类构造方法
首先父类构造方法是绝对不能被子类继承的。

子类构造方法调用父类的构造方法重点是：子类构造方法无论如何都要调用父类的构造方法。

子类构造方法要么调用父类无参构造方法（包括当父类没有构造方法时。系统默认给的无参构造方法），要么调用父类有参构造方法。当子类构造方法调用父类无参构造方法，一般都是默认不写的，要写的话就是super（），且要放在构造方法的第一句。当子类构造方法要调用父类有参数的构造方法，那么子类的构造方法中必须要用super（参数）调用父类构造方法，且要放在构造方法的第一句。

当子类的构造方法是无参构造方法时，必须调用父类无参构造方法。因为系统会自动找父类有没有无参构造方法，如果没有的话系统会报错：说父类没有定义无参构造方法。

当子类构造方法是有参构造方法时，这时就会有两种情况。
第一种：子类构造方法没有写super，也就是说你默认调用父类无参构造方法，这样的话就和子类是无参构造方法一样。

第二种：子类构造方法有super（参数）时，就是调用父类有参构造方法，系统会找父类有没有参数一致（参数数量，且类型顺序要相同）的有参构造方法，如果没有的话，同样也会报错。

但是这里会遇到和重载构造方法this一样问题，一个参数的构造方法可以调用多个参数构造方法，没有的参数给一个自己定义值也是可以的。

## Java代码块

### 简介
在java中用{}括起来的称为代码块,代码块可分为以下四种:
1. 普通代码块
   在方法或语句中出现的{}里面的内容就被称为普通代码块
2. 构造代码块
   构造块会在创建对象时被调用,每次创建时都会被调用,优先于类构造函数执行。
3. 静态代码块
   用static{}包裹起来的代码片段,只会执行一次。静态代码块优先于构造块执行。
4. 同步代码块
   使用`synchronized（）{}`包裹起来的代码块,在多线程环境下,对共享数据的读写操作是需要互斥进行的,否则会导致数据的不一致性。同步代码块需要写在方法中。

### 静态代码块和构造代码块的异同点
相同点：都是JVM加载类后且在构造函数执行之前执行，在类中可定义多个，一般在代码块中对一些static变量进行赋值。

不同点：静态代码块在非静态代码块之前执行。静态代码块只在第一次new时执行一次，之后不在执行。而非静态代码块每new一次就执行一次。

### Java代码块的使用

#### 普通代码块(局部代码块)
    在方法或语句中出现的{}里面的内容就被称为普通代码块，普通代码
块和一般的语句执行顺序一样，由他们在代码中出现的次序决定，即——“先出现先执行”。
　　但是不同的普通代码块即使在同一个方法里面定义，里面存储的方法和变量都有自己独立的作用域，但是都可以访问在自己所在花括号之外，所在方法或语句之内的内容。
    之所以出现局部代码块,是因为有的变量在使用过后,接下来不想再用
它,就需要将其释放掉,不想让其占用过多的空间。
```java
public void show(){
    {
        System.out.println("局部代码块运行！");
    }
}
```

### 构造代码块(实例代码块)
直接在类中定义的而且没有加static的代码块被称为构造代码块，构造代
码块在创建对象的时候被调用，每次创建对象的时候都会被调用，并且构
造代码块的执行次序是优于构造函数的。
**构造代码块在每次创建对象的时候被调用;**
```java
public class CodeBlock02{
    {
      //这里写在构造函数的前面，但是这并不是在构造函数之前执行的原因
      System.out.println("第一代码块");    
    }  

    public CodeBlock02(){
        System.out.println("构造方法");
    } 

    {
        //这里写在构造函数的后面，可以在结果上看到依旧是在构造函数之前执行的
        System.out.println("第二构造块");
    }

    public static void main(String[] args){
          new CodeBlock02();
          new CodeBlock02();
          // 在结果中可以看到，每次生成新对象的时候        
          new CodeBlock02(); 
    }
}
```
执行结果:
```
第一代码块
第二构造块
构造方法
第一代码块
第二构造块
构造方法
第一代码块
第二构造块
构造方法
```

### 静态代码块
    在类中定义的有`static`关键词的代码块被称为静态代码块，静态类
被用于初始化类，为类的属性初始化，对于一个类，其对应的静态代码只会执行一次，由于JVM在加载类时会执行静态代码块，所以静态代码由于主方法执行（意思是，如果main函数定义在该类中，静态代码块也会先执行）;

    如果类中包含有多个静态代码块，那么将按照“先定义的代码先执行，
后定义的代码后执行”；
    静态代码块不能存在于任何的方法体内，因为本身是静态的，所以也
只能访问对应类中的静态成员。

```java
class Code{
    {
      System.out.println("Code的构造代码块");
    } 

    static {
        System.out.println("Code的静态代码块");
    }   

    public Code() {
        System.out.println("Code的构造方法");
    }
}    
public class CodeBlock03{
    {
        System.out.println("CodeBlock03的构造代码块");    
    }
     static{
        System.out.println("CodeBlock03的静态代码块");
    }
        
    public CodeBlock03() {
        System.out.println("CodeBlock03的构造方法");
    }

    public static void main(String[] args) {
        System.out.println("CodeBlock03的主方法");
        new Code();
        new Code();
        new CodeBlock03();
        new CodeBlock03();
    }
}
```
执行结果:
```
CodeBlock03的静态代码块
CodeBlock03的主方法
Code的静态代码块
Code的构造代码块
Code的构造方法
Code的构造代码块
Code的构造方法
CodeBlock03的构造代码块
CodeBlock03的构造方法
CodeBlock03的构造代码块
CodeBlock03的构造方法
```

### 同步代码块
这里的同步代码块指的是被Java中Synchronized关键词修饰的代码块，在
Java中，`Synchronized`关键词不仅仅可以用来修饰代码块，与此同时也
可以用来修饰类、方法，是一种线程同步机制，被Synchronized关键词修
饰的代码块会被加上内置锁。显然，因为是同步代码块，因此比被
`Synchronized`关键词修饰的方法更加颗粒化（实际上因为本身同步是一
种高开销的操作，因此我们应该尽量减少被同步的内容，很很多场景，我
们没有必要去同步整个方法，而只需要同步部分代码即可，也就是使用同
步代码块，因此在很多场景，自己更加推崇使用同步代码块），被
`Synchronized`关键词修饰的代码块如下:
```java
public class CodeBlock04 implements Runnable{
    public void method(){  
        synchronized(this){  
            System.out.print("这里是同步代码块");  
        }  
    } 
    public static void main(String[] args){
        CodeBlock04 a=new CodeBlock04();
        CodeBlock04 b=new CodeBlock04();
    }
}
```
当两个并发线程访问同一个对象object中的Synchronized(this)同步代码块时（不仅仅限于同一个代码块，只要是从属于同一个object），只有一个线程能够得以执行，另一个线程则处于阻塞态，只有在一个线程执行完该同步代码块之后，另外一个线程才能得以访问该同步代码块，但是对于非同步代码块，则完全不受影响。事实上，以上规则对于Java中的其他对象锁也是同样适用的。
当然，要针对于同一个对象：
最后提一点，对于Synchronized关键词修饰静态方法和对象，可以很容易推断的是，因为静态方法是属于类而不是属于对象的，因此使用Synchronized来修饰静态方法和静态对象的时候，类下的所有对象都会被锁定。 

## Java代码块、构造方法（包含继承关系）的执行顺序
A类:
```java
public class A {
    static {
        Log.i("HIDETAG", "A静态代码块");
    }

    private static C c = new C("A静态成员");
    private  C c1 = new C("A成员");

    {
        Log.i("HIDETAG", "A代码块");
    }

    static {
        Log.i("HIDETAG", "A静态代码块2");
    }

    public A() {
        Log.i("HIDETAG", "A构造方法");
    }
}
```
B类
```java
public class B extends A {
    private static C c1 = new C("B静态成员");

    {
        Log.i("HIDETAG", "B代码块");
    }

    private C c = new C("B成员");

    static {
        Log.i("HIDETAG", "B静态代码块2");
    }

    static {
        Log.i("HIDETAG", "B静态代码块");
    }

    public B() {
        Log.i("HIDETAG", "B构造方法");
    }

}
```
C类
```java
public class C {
    public C(String str) {
        Log.i("c", str + "构造方法");
    }
}
```
执行语句:`new B();`
输出结果如下:
```
HIDETAG: A静态代码块
c: A静态成员构造方法
HIDETAG: A静态代码块2
c: B静态成员构造方法
HIDETAG: B静态代码块2
HIDETAG: B静态代码块
c: A成员构造方法
HIDETAG: A代码块
HIDETAG: A构造方法
HIDETAG: B代码块
c: B成员构造方法
HIDETAG: B构造方法
```
```
I/HIDETAG: A静态代码块
I/HIDETAG: A静态成员构造方法
I/HIDETAG: A静态代码块2
I/HIDETAG: B静态成员构造方法
I/HIDETAG: B静态代码块2
I/HIDETAG: B静态代码块
I/HIDETAG: A成员构造方法
I/HIDETAG: A代码块
I/HIDETAG: A构造方法
I/HIDETAG: B代码块
I/HIDETAG: B成员构造方法
I/HIDETAG: B构造方法
```

结论:
执行顺序依次为:
父类的静态成员和代码块
子类静态成员和代码块
父类成员初始化和代码块
父类构造方法
子类成员初始化和代码块
子类构造方法
下一道题:
```java
class A {
    public A() {
        System.out.println("1A类的构造方法");
    }
    {
        System.out.println("2A类的构造快");
    }
    static {
        System.out.println("3A类的静态块");
    }
}

public class B extends A {
    public B() {
        System.out.println("4B类的构造方法");
    }
    {
        System.out.println("5B类的构造快");
    }
    static {
        System.out.println("6B类的静态块");
    }
    public static void main(String[] args) {
        System.out.println("7");
        new B();
        new B();
        System.out.println("8");
    }
}
```
367215421548