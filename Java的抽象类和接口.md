# Java的抽象类和接口
本文主要内容来自
https://how2playlife.com/2019/09/06/6%E6%8A%BD%E8%B1%A1%E7%B1%BB%E5%92%8C%E6%8E%A5%E5%8F%A3/
如有侵权,请联系作者

## 抽象类
这种只给出方法定义而不具体实现的方法被称为抽象方法，抽象方法是没有方法体的，在代码的表达上就是没有“{}”。
包含一个或多个抽象方法的类也必须被声明为抽象类,事实上,一个没有抽象方法的类也能够是抽象类。
使用`abstract`修饰符来表示抽象方法以及抽象类。
```java
//有抽象方法的类也必须被声明为abstract
public abstract class Test1 {

    //抽象方法，不能有“{}”
    public abstract void f();

}
```
抽象类除了包含抽象方法外，还可以包含具体的变量和具体的方法。类即使不包含抽象方法，也可以被声明为抽象类，防止被实例化。
抽象类不能被实例化，也就是不能使用new关键字来得到一个抽象类的实例，抽象方法必须在子类中被实现。
抽象方法必须为public或者protected（因为如果为private，则不能被子类继承，子类便无法实现该方法），缺省情况下默认为public。
```java
//有抽象方法的类也必须被声明为abstract
public class Test1 {

    public static void main(String[] args) {
        Teacher teacher=new Teacher("教师");
        teacher.work();

        Driver driver=new Driver("驾驶员");
        driver.work();
    }

}

//一个抽象类
abstract class People{
    //抽象方法
    public abstract void work();
}

class Teacher extends People{
    private String work;
    public Teacher(String work) {
        this.work=work;
    }
    @Override
    public void work() {
        System.out.println("我的职业是"+this.work);
    }

}
class Driver extends People{
    private String work;
    public Driver(String work) {
        this.work=work;
    }
    @Override
    public void work() {
        System.out.println("我的职业是"+this.work);
    }

}
```
抽象类不能直接使用，需要子类去实现抽象类，然后使用其子类的实例。然而可以创建一个变量，其类型也是一个抽象类，并让他指向具体子类的一个实例，也就是可以使用抽象类来充当形参，实际实现类为实参，也就是多态的应用。
```java
People people=new Teacher("教师");
people.work();
```
不能有抽象构造方法或抽象静态方法。
当一个类的一个或多个方法是抽象方法时。
当类是一个抽象类的子类，并且不能实现父类的所有抽象方法时。
当一个类实现一个接口，并且不能实现接口的所有抽象方法时。
注意：
上面说的是这些情况下一个类将称为抽象类，没有说抽象类就一定会是这些情况。
抽象类可以不包含抽象方法，包含抽象方法的类就一定是抽象类。
事实上，抽象类可以是一个完全正常实现的类。

## 接口
接口（英文：Interface），在JAVA编程语言中是一个抽象类型，是抽象方法的集合，接口通常以interface来声明。一个类通过继承接口的方式，从而来继承接口的抽象方法。
接口并不是类，编写接口的方式和类很相似，但是它们属于不同的概念。类描述对象的属性和方法。接口则包含类要实现的方法。
除非实现接口的类是抽象类，否则该类要定义接口中的所有方法。
接口无法被实例化，但是可以被实现。一个实现接口的类，必须实现接口内所描述的所有方法，否则就必须声明为抽象类。另外，在 Java 中，接口类型可用来声明一个变量，他们可以成为一个空指针，或是被绑定在一个以此接口实现的对象。
接口中可以含有 变量和方法。但是要注意，接口中的变量会被隐式地指定为public static final变量（并且只能是public static final变量，用private修饰会报编译错误），而方法会被隐式地指定为public abstract方法且只能是public abstract方法（用其他关键字，比如private、protected、static、 final等修饰会报编译错误），并且接口中所有的方法不能有具体的实现，也就是说，接口中的方法必须都是抽象方法。从这里可以隐约看出接口和抽象类的区别，接口是一种极度抽象的类型，它比抽象类更加“抽象”，并且一般情况下不在接口中定义变量。

### 接口与类的相似点
一个接口可以有多个方法。
接口文件保存在 .java 结尾的文件中，文件名使用接口名。
接口的字节码文件保存在 .class 结尾的文件中。
接口相应的字节码文件必须在与包名称相匹配的目录结构中。

### 接口与类的区别
接口不能用于实例化对象。
接口没有构造方法。
接口中所有的方法必须是抽象方法。
接口不能包含成员变量，除了 static 和 final 变量。
接口不是被类继承了，而是要被类实现。
接口支持多继承(多实现比较合适)。
> 在JDK 9中，接口中的方法就可以是`private`, `private`方法可以是静态方法或实例方法。因为权限为`private`,
> 在Java 8中,允许在接口中添加静态方法,并且静态方法可以有方法体,事实上,必须有方法体。



### 抽象类与接口的区别
抽象类可以提供成员方法的实现细节，而接口中只能存在public abstract 方法；
抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是public static final类型的；
接口中不能含有静态代码块以及静态方法，而抽象类可以有静态代码块和静态方法；
一个类只能继承一个抽象类，而一个类却可以实现多个接口。

设计层面上的区别
1. 抽象类是对一种事物的抽象，即对类抽象，而接口是对行为的抽象。抽象类是对整个类整体进行抽象，包括属性、行为，但是接口却是对类局部（行为）进行抽象。举个简单的例子，飞机和鸟是不同类的事物，但是它们都有一个共性，就是都会飞。那么在设计的时候，可以将飞机设计为一个类Airplane，将鸟设计为一个类Bird，但是不能将 飞行 这个特性也设计为类，因此它只是一个行为特性，并不是对一类事物的抽象描述。此时可以将 飞行 设计为一个 接口Fly，包含方法fly( )，然后Airplane和Bird分别根据自己的需要实现Fly这个接口。然后至于有不同种类的飞机，比如战斗机、民用飞机等直接继承Airplane即可，对于鸟也是类似的，不同种类的鸟直接继承Bird类即可。从这里可以看出，继承是一个 "是不是"的关系，而 接口 实现则是 "有没有"的关系。如果一个类继承了某个抽象类，则子类必定是抽象类的种类，而接口实现则是有没有、具备不具备的关系，比如鸟是否能飞（或者是否具备飞行这个特点），能飞行则可以实现这个接口，不能飞行就不实现这个接口。
   
2. 设计层面不同，抽象类作为很多子类的父类，它是一种模板式设计。而接口是一种行为规范，它是一种辐射式设计。什么是模板式设计？最简单例子，大家都用过ppt里面的模板，如果用模板A设计了ppt B和ppt C，ppt B和ppt C公共的部分就是模板A了，如果它们的公共部分需要改动，则只需要改动模板A就可以了，不需要重新对ppt B和ppt C进行改动。而辐射式设计，比如某个电梯都装了某种报警器，一旦要更新报警器，就必须全部更新。也就是说对于抽象类，如果需要添加新的方法，可以直接在抽象类中添加具体的实现，子类可以不进行变更；而对于接口则不行，如果接口进行了变更，则所有实现这个接口的类都必须进行相应的改动。

### 接口特性
接口中每一个方法也是隐式抽象的,接口中的方法会被隐式的指定为 public abstract（只能是 public abstract，其他修饰符都会报错）。
接口中可以含有变量，但是接口中的变量会被隐式的指定为 public static final 变量（并且只能是 public，用 private 修饰会报编译错误）。
接口中的方法是不能在接口中实现的，只能由实现接口的类来实现接口中的方法。

下面看一个网上流传最广泛的例子：门和警报的例子：门都有open( )和close( )两个动作，此时我们可以定义通过抽象类和接口来定义这个抽象概念：
```java
abstract class Door {
    public abstract void open();
    public abstract void close();
}
interface Door {
    public abstract void open();
    public abstract void close();
}
```
　　但是现在如果我们需要门具有报警alarm( )的功能，那么该如何实现？下面提供两种思路：

　　1）将这三个功能都放在抽象类里面，但是这样一来所有继承于这个抽象类的子类都具备了报警功能，但是有的门并不一定具备报警功能；

　　2）将这三个功能都放在接口里面，需要用到报警功能的类就需要实现这个接口中的open( )和close( )，也许这个类根本就不具备open( )和close( )这两个功能，比如火灾报警器。

　　从这里可以看出， Door的open() 、close()和alarm()根本就属于两个不同范畴内的行为，open()和close()属于门本身固有的行为特性，而alarm()属于延伸的附加行为。ar因此最好的解决办法是单独将报警设计为一个接口，包含alarm()行为,Door设计为单独的一个抽象类，包含open和close两种行为。再设计一个报警门继承Door类和实现Alarm接口。
```java
interface Alarm {
    void alarm();
}
 
abstract class Door {
    void open();
    void close();
}
 
class AlarmDoor extends Door implements Alarm {
    void open() {
      //....
    }
    void close() {
      //....
    }
    void alarm() {
      //....
    }
}
```
### 接口的使用
```java
package demo;
//构建一个抽象类People
abstract class People{
    //父类属性私有化
    private String name;
    private int age;
    //提供父类的构造器
    public People(String name,int age){
        this.name = name;
        this.age = age;
    }
    //提供获取和设置属性的getter()/setter()方法
    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    //提供一个抽象方法
    public abstract void talk();    
}

//定义一个接口
interface Study{
    //设置课程数量为3
    int COURSENUM = 3;
    //构建一个默认方法
    default void stu(){
        System.out.println("学生需要学习"+COURSENUM+"门课程");
    }
}

//再定义一个接口
interface Write{
    //定义一个抽象方法
    void print();
}

//子类继承People,实现接口Study,Write
class Student extends People implements Study, Write {
    //通过super关键字调用父类的构造器
    public Student(String name, int age) {
        super(name, age);
    }
    //实现父类的抽象方法
    public void talk() {
        System.out.println("我的名字叫" + this.getName() + ",今年" + this.getAge() + "岁");
    }
    //实现Write接口的抽象方法
    public void print() {
        System.out.println("学生会写作业");
    }
}

public class InterfaceDemo{
    public static void main(String[] args) {
        //构建student对象
        Student student = new Student("dodo", 22);
        //调用父类的抽象方法
        student.talk();
        //调用接口Write中的抽象方法
        student.print();
        //调用接口Study中的默认方法
        student.stu();
    }
}
```
接口的作用---制定标准
接口是标准，所谓的标准，指的是各方共同遵守一个守则，只有操作标准统一了，所有的参与者才可以按照统一的规则操作。 
```java
//如电脑可以和各个设备连接，提供统一的USB接口，其他设备只能通过USB接口和电脑相连
//代码实现：
package demo;

interface USB {
    public void work() ;    // 拿到USB设备就表示要进行工作
}

//实现类（接口类）
class Print implements USB             
{                               
    // 打印机实现了USB接口标准（对接口的方法实现）
    public void work() {
        System.out.println("打印机用USB接口，连接,开始工作。");
    }
}
class Flash implements USB         //实现类（接口类）                  
{                                  // U盘实现了USB接口标准（对接口的方法实现）
    public void work() 
    {
        System.out.println("U盘使用USB接口，连接,开始工作。") ;
    }
}

class Computer 
{
    public void plugin(USB usb)             //plugin的意思是插件，参数为接收接口类
    {
        usb.work() ;    // 按照固定的方式进行工作
    }
}
public class InterfaceStandards { 
    public static void main(String args[]) { 
        Computer computer = new Computer() ; 
        computer.plugin(new Print()) ; 
        //实例化接口类， 在电脑上使用打印机 
        computer.plugin(new Flash()) ; 
        //实例化接口类， 在电脑上使用U盘
    }
}
```
```java
class Computer 
{
    public void plugin(USB usb)             //plugin的意思是插件，参数为接收接口类
    {
        usb.work() ;    // 按照固定的方式进行工作
    }
}
```
### 接口的最佳实践:设计模式中的工厂模式
```
首先我们来认识一下什么是工厂模式？工厂模式是为了解耦：把对象的创建和使用的过程分开。就是Class A 想调用 Class B ，那么A只是调用B的方法，而至于B的实例化，就交给工厂类。

其次，工厂模式可以降低代码重复。如果创建对象B的过程都很复杂，需要一定的代码量，而且很多地方都要用到，那么就会有很多的重复代码。我们可以这些创建对象B的代码放到工厂里统一管理。既减少了重复代码，也方便以后对B的创建过程的修改维护。

由于创建过程都由工厂统一管理，所以发生业务逻辑变化，不需要找到所有需要创建B的地方去逐个修正，只需要在工厂里修改即可，降低维护成本。同理，想把所有调用B的地方改成B的子类C，只需要在对应生产B的工厂中或者工厂的方法中修改其生产的对象为C即可，而不需要找到所有的new B() 改为newC ()。
```
代码演示:
```java
package demo;

import java.util.Scanner;

interface Fruit                        //定义一个水果标准
{
    public abstract void eat();
}

class Apple implements Fruit
{
    public void eat()
    {
        System.out.println("吃苹果");
    }
}
class Orange implements Fruit
{
    public void eat()
    {
        System.out.println("吃橘子");
    }
}

class factory
{
    public static Fruit getInstance(String className)  //返回值是Fruit的子类
    {
        if("apple".equals(className))
        {
            return new Apple();
        }
        else if("orange".equals(className))
        {
            return new Orange();
        }
        else
        {
            return null;
        }
    }
}

public class ComplexFactory {
    public static void main(String[] args)
    {    
        System.out.println("请输入水果的英文名:");
        Scanner sc = new Scanner(System.in);
        String ans = sc.nextLine();
        Fruit f = factory.getInstance(ans);   //初始化参数
        f.eat();
        sc.close();
    }
}
```
代码讲解：上述代码部分我们讲一下factory这个类，类中有一个getInstance方法，我们用了static关键字修饰，在使用的时候我们就在main中使用类名.方法名调用。
Fruit f = factory.getInstance(ans); //初始化参数
在Factory的getInstance()方法中，我们就可以通过逻辑的实现，将对象的创建和使用的过程分开了。
> 总结点评：在接口的学习中，大家可以理解接口是特殊的抽象类，java中类可以实现多个接口，接口中成员属性默认是public static final修饰，可以省略；成员方法默认是public abstract修饰，同样可以省略，接口中还可定义带方法体的默认方法，需要使用default修饰。利用接口我们还可以制定标准，还能够使用工厂模式，将对象的创建和使用过程分开。

