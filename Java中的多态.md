# Java中的多态

这里只简单地分析类层次结构中静态字段，实例字段，静态方法，非静态方法。我们给出下面的例子：

Fu.java

```java
public class Fu {
    static int variable = 1;
    int num = 5;
    static void show()
    {
        System.out.println("fu show()");
    }
    void print()
    {
        System.out.println("fu print()");
    }
}
```

Zi.java

```java
public class Zi extends Fu {
    static int variable = 5;
    int num = 8;
    static void show()
    {
        System.out.println("zi show()");
    }
    void print()
    {
        System.out.println("zi print()");
    }
    void hello()
    {
        System.out.println("zi hello");
    }
}
```

可以用一句话来交代类里面成员的使用：

> 静态字段与非静态字段，静态方法看左边；非静态方法：编译看左边，运行看右边。

我们使用下面的程序来验证：

```java
public class Demo {
    public static void main(String[] args) {
        Fu f = new Zi();
        System.out.println(Fu.variable);//与父类一致
        System.out.println(f.variable);//与父类一致
        System.out.println(f.num);//与父类一致
        f.show();//与父类一致
        f.print();//编译时与父类一致，运行时与子类一致
        //f.hello();//这条语句会报错
        Zi z = new Zi();
        System.out.println(z.num);
        z.show();
        z.print();
        z.hello();
    }
}
```

用父类引用指向子类对象，`Fu f = new Zi();`此时引用f主要将其作为Fu类的对象来看待，`f.num`是实例变量，`Fu.variable`或者`f.variable`是静态字段，`f.show()`是静态方法，当编译的时候，会看父类中使用，并且在运行，也按照父类的运行。所以叫做**看左边**，但是对于非静态方法`f.print()`，当编译的时候，会看父类中是否存在，若不存在，则会报错，若存在，因为被子类覆盖，所以会按照子类中定义的那样运行，所以叫做**编译看左边，运行看右边**。

