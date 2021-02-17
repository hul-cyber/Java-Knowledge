# Lambda表达式

对于Java来,我们有时候想要将一个有一个可传递的代码块,但是对于Java来说,我们一般传递的是基本类型的数据或者一个类的对象,所以Java实现了Lambda表达式,来表示一个可传递的代码块。
我们看下面的一个例子:
```java
public class TimerTest {
    public static void main(String[] args) {
        var listener = new TimerPrinter();
        var timer = new Timer(1000, listener);

        timer.start();

        JOptionPane.showMessageDialog(null, "Quit Program?");
        System.exit(0);
    }
}

class TimerPrinter implements ActionListener {
    @Override
    public void actionPerformed(ActionEvent event) {
        //System.out.println("At the tone, the time is " + Instant.ofEpochMilli(event.getWhen()));
        //Toolkit.getDefaultToolkit().beep();
        System.out.println("Hello World ");
    }
}
```
在这个例子中我们要每间隔1s就要打印一个"Hello World",但是我们不想这么干,因为我们每次都要传一个`TimerPrinter`对象进去。于是就有了`Lambda`表达式。

## Lambda表达式语法
![](2020-10-05-11-25-37.png)
事实上,包括这个格式针对不同的情况有不同的变形,比如说我们要把上面的`TimerPrinter`改造成`Lambda`表达式。
ActionListener = (ActionEvent event) -> System.out.println("Hello World ");
事实上,我们可以将上面的`(ActionEvent event)`改造成`event`,但我们并不做过多的论述,我们采取最原始的方式来写:
```java
(参数类型 参数名) -> {代码块}
```

## Lambda表达式的使用
根据上面的论述,我们发现当一个接口只有一个方法的时候我们才能使用(只包含**一个抽象方法**的接口叫做函数式接口),并且这个方法还得是抽象方法,也就是说我们实际是把这个方法实现之后就这个方法作为参数传递过去。下面我们给出一个示例,分别来表示原始方法、匿名内部类、Lambda表达式三种情况。
```java
@FunctionalInterface
public interface Eatable {
    void eat();
    default void hello() {
        System.out.println("hello");
    }
}
```
```java
public class EatableImpl implements Eatable {

    @Override
    public void eat() {
        System.out.println("eat");
    }
}
```
```java
public class EatableDemo {
    public static void main(String[] args) {
        //原始方式
        useEatable(new EatableImpl());
        //匿名内部类
        useEatable(new Eatable() {
            @Override
            public void eat() {
                System.out.println("eat");
            }
        });
        //Lambda表达式
        useEatable(() -> {
            System.out.println("eat");
        });
    }

    public static void useEatable(Eatable e) {
        e.eat();
    }
}
```

## Lambda表达式的省略模式:
![](2020-10-05-15-02-55.png)

## Lambda表达式与函数式接口
这里的观点基本上都来自这篇博客:
关于Lambda表达式的参考博客:https://objcoding.com/2019/03/04/lambda/
首先,可以简单地理解为**Lambda表达式是为了简化匿名类**,当然这种理解有点错误,但是如果不深究在JVM里面的实现的话,可以简单地这么认为。
事实上,Lambda表达式并不能够取代所有的匿名内部类,只能够取代**函数式接口**的简写。
函数式接口:就是**包含且只包含一个==抽象方法==**的接口,我们可以用（非必要）注解`@FunctionalInterface`来标注,其实也可以不用,像下面这样:
```java
@FunctionalInterface
public interface Eatable {
    void eat();
    //void hit();
    default void hello() {
        System.out.println("hello");
    }
}
```
如果我们再添加一个抽象方法:`hit()`,就会出现`Multiple non-overriding abstract methods found in interface com.demo1.Eatable`的错误。
至于会出现这样的情况,其实很容易理解,因为如果你有多个方法,它不会知道Lambda表达式中到底是哪个方法,但是,我个人认为如果方法参数个数不同,完全有可能将他们区分开,但是,即使这样Lambda表达式也不支持。

## Lambda表达式与匿名内部类的区别
![](2020-10-05-15-38-12.png)
这里还有关于`this`引用的推论（这里的观点也来自于博客:https://objcoding.com/2019/03/04/lambda/）:

```java
public class Hello {
	Runnable r1 = () -> { System.out.println(this); };
	Runnable r2 = () -> { System.out.println(toString()); };
	public static void main(String[] args) {
		new Hello().r1.run();
		new Hello().r2.run();
        new Hello().outThis();
	}
	public String toString() { 
        return "Hello"; 
    }
    public void outThis() {
        System.out.println(this);
    }
}
```
> 既然Lambda表达式不是内部类的简写，那么Lambda内部的this引用也就跟内部类对象没什么关系了。在Lambda表达式中this的意义跟在表达式外部完全一样。因此下列代码将输出三遍Hello，而不是两个引用地址。

在这里再阐明一个小知识点:
在我们使用`System.out.println()`输出对象的时候,我们会调用`toString()`方法,正如下面所示:
```java
public void println(Object x) {
    String s = String.valueOf(x);
    if (getClass() == PrintStream.class) {
        // need to apply String.valueOf again since first invocation
        // might return null
        writeln(String.valueOf(s));
    } else {
        synchronized (this) {
            print(s);
            newLine();
        }
    }
}

public static String valueOf(Object obj) {
    return (obj == null) ? "null" : obj.toString();
}
```

## Lambda表达式的注意事项
1. 接口中有且只有一个抽象方法。
2. 必须有上下文环境,才能够推导出Lambda表达式对应的接口。
![](2020-10-05-15-12-09.png)
