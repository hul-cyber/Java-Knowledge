# Java中的内部类
下面的内容主要来自于博文:https://www.cnblogs.com/dolphin0520/p/3811445.html
我们可以将一个类定义在另一个类或者方法里面,叫做内部类。
内部类是一种编译器现象,与虚拟机无关,编译器会将内部类转化为常规的类文件,也就是同样会产生`.class`的字节码文件,用$来分割外部类名与内部类名。

## 成员内部类
成员内部类是最普通的内部类，它的定义为位于另一个类的内部，形如下面的形式:
```java
class Circle {
    double radius = 0;
     
    public Circle(double radius) {
        this.radius = radius;
    }
     
    class Draw {     //内部类
        public void drawShape() {
            System.out.println("drawShape");
        }
    }
}
```
这样看起来，类Draw像是类Circle的一个成员，Circle称为外部类。成员内部类可以无条件访问外部类的所有成员属性和成员方法（包括private成员和静态成员）。
```java
class Circle {
    private double radius = 0;
    public static int count =1;
    public Circle(double radius) {
        this.radius = radius;
    }
     
    class Draw {     //内部类
        public void drawShape() {
            System.out.println(radius);  //外部类的private成员
            System.out.println(count);   //外部类的静态成员
        }
    }
}
```
不过要注意的是，当成员内部类拥有和外部类同名的成员变量或者方法时，会发生隐藏现象，即默认情况下访问的是成员内部类的成员。如果要访问外部类的同名成员，需要以下面的形式进行访问:
> 外部类.this.成员变量
> 外部类.this.成员方法

虽然成员内部类可以无条件地访问外部类的成员，而外部类想访问成员内部类的成员却不是这么随心所欲了。在外部类中如果要访问成员内部类的成员，必须先创建一个成员内部类的对象，再通过指向这个对象的引用来访问:
```java
class Circle {
    private double radius = 0;
 
    public Circle(double radius) {
        this.radius = radius;
        getDrawInstance().drawShape();   //必须先创建成员内部类的对象，再进行访问
    }
     
    private Draw getDrawInstance() {
        return new Draw();
    }
     
    class Draw {     //内部类
        public void drawShape() {
            System.out.println(radius);  //外部类的private成员
        }
    }
}
```
成员内部类是依附外部类而存在的，也就是说，如果要创建成员内部类的对象，前提是必须存在一个外部类的对象。创建成员内部类对象的一般方式如下:
```java
public class Test {
    public static void main(String[] args)  {
        //第一种方式：
        Outter outter = new Outter();
        Outter.Inner inner = outter.new Inner();  //必须通过Outter对象来创建
         
        //第二种方式：
        Outter.Inner inner1 = outter.getInnerInstance();
    }
}
 
class Outter {
    private Inner inner = null;
    public Outter() {
         
    }
     
    public Inner getInnerInstance() {
        if(inner == null)
            inner = new Inner();
        return inner;
    }
      
    class Inner {
        public Inner() {
             
        }
    }
}
```
内部类可以拥有private访问权限、protected访问权限、public访问权限及包访问权限。比如上面的例子，如果成员内部类Inner用private修饰，则只能在外部类的内部访问，如果用public修饰，则任何地方都能访问；如果用protected修饰，则只能在同一个包下或者继承外部类的情况下访问；如果是默认访问权限，则只能在同一个包下访问。这一点和外部类有一点不一样，外部类只能被public和包访问两种权限修饰。我个人是这么理解的，由于成员内部类看起来像是外部类的一个成员，所以可以像类的成员一样拥有多种权限修饰。
**这里要补充一点知识,对于Java的外部类来说,有两种修饰符,一种叫做`public`,另外一个是包访问权限,其实就是没有修饰符,并且只能够在一个包里面访问,但是对于`public`来说,在任何地方都能够使用。**

除了上面提到的那些之外,还有就是要明白:成员内部类中不能够有静态的定义出现。

## 局部内部类
局部内部类是定义在一个方法或者一个作用域里面的类，它和成员内部类的区别在于局部内部类的访问仅限于方法内或者该作用域内。
```java
class People{
    public People() {
         
    }
}
 
class Man{
    public Man(){
         
    }
     
    public People getWoman(){
        class Woman extends People{   //局部内部类
            int age =0;
        }
        return new Woman();
    }
}
```
注意，局部内部类就像是方法里面的一个局部变量一样，是不能有`public`、`protected`、`private`以及`static`修饰符的。

## 匿名内部类
在局部类上更加更近一步,我们都可以将局部类的名字去掉,比如我们可以将上面的代码改成:
```java
public People getWoman(){
    return new People {
        int age = 0;
    }
}
```
就是匿名内部类的使用。
一般格式如下所示:
```java
new SuperType(construction parameters) {
    //inner class methods and data
}
```
上面的`SuperType`可以是类,或者接口。匿名类就是为了扩展类或者实现接口,但是要注意的是:扩展类的时候,可以有构造参数`construction parameters`,但是在实现接口的时候,就不能有`construction parameters`,但是()必须要保留下来。
使用匿名内部类能够在实现父类或者接口中的方法情况下同时产生一个相应的对象，但是前提是这个父类或者接口必须先存在才能这样使用。匿名内部类也是不能有访问修饰符和static修饰符的,匿名内部类是唯一一种没有构造器的类。正因为其没有构造器，所以匿名内部类的使用范围非常有限，大部分匿名内部类用于接口回调。匿名内部类在编译的时候由系统自动起名为Outter$1.class。一般来说，匿名内部类用于继承其他类或是实现接口，并不需要增加额外的方法，只是对继承方法的实现或是重写。

## 静态内部类
静态内部类也是定义在另一个类里面的类，只不过在类的前面多了一个关键字static。静态内部类是不需要依赖于外部类的，这点和类的静态成员属性有点类似，并且它不能使用外部类的非static成员变量或者方法，这点很好理解，因为在没有外部类的对象的情况下，可以创建静态内部类的对象，如果允许访问外部类的非static成员就会产生矛盾，因为外部类的非static成员必须依附于具体的对象。
```java
public class Test {
    public static void main(String[] args)  {
        Outter.Inner inner = new Outter.Inner();
    }
}
 
class Outter {
    public Outter() {
         
    }
     
    static class Inner {
        public Inner() {
             
        }
    }
}
```
