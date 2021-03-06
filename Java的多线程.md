## 多线程简介
多线程是Java最基本的一种并发模型

## 多线程基础
现代操作系统（Windows，macOS，Linux）都可以执行多任务。多任务就是同时运行多个任务，例如可以同时运行谷歌浏览器和QQ。
CPU执行代码都是一条一条顺序执行的，但是，即使是单核cpu，也可以同时运行多个任务。因为操作系统执行多任务实际上就是让CPU对多个任务轮流交替执行。
例如，假设我们有语文、数学、英语3门作业要做，每个作业需要30分钟。我们把这3门作业看成是3个任务，可以做1分钟语文作业，再做1分钟数学作业，再做1分钟英语作业：
这样轮流做下去，在某些人眼里看来，做作业的速度就非常快，看上去就像同时在做3门作业一样。
类似的，操作系统轮流让多个任务交替执行，例如，让浏览器执行0.001秒，让QQ执行0.001秒，再让音乐播放器执行0.001秒，在人看来，CPU就是在同时执行多个任务。
即使是多核CPU，因为通常任务的数量远远多于CPU的核数，所以任务也是交替执行的。

### 进程
在计算机中，我们把一个任务称为一个进程，浏览器就是一个进程，视频播放器是另一个进程，类似的，音乐播放器和Word都是进程。
某些进程内部还需要同时执行多个子任务。例如，我们在使用Word时，Word可以让我们一边打字，一边进行拼写检查，同时还可以在后台进行打印，我们把子任务称为线程。
进程和线程的关系就是：一个进程可以包含一个或多个线程，但至少会有一个线程。
```
                        ┌──────────┐
                        │Process   │
                        │┌────────┐│
            ┌──────────┐││ Thread ││┌──────────┐
            │Process   ││└────────┘││Process   │
            │┌────────┐││┌────────┐││┌────────┐│
┌──────────┐││ Thread ││││ Thread ││││ Thread ││
│Process   ││└────────┘││└────────┘││└────────┘│
│┌────────┐││┌────────┐││┌────────┐││┌────────┐│
││ Thread ││││ Thread ││││ Thread ││││ Thread ││
│└────────┘││└────────┘││└────────┘││└────────┘│
└──────────┘└──────────┘└──────────┘└──────────┘
┌──────────────────────────────────────────────┐
│               Operating System               │
└──────────────────────────────────────────────┘
```
操作系统调度的最小任务单位其实不是进程，而是线程。常用的Windows、Linux等操作系统都采用抢占式多任务，如何调度线程完全由操作系统决定，程序自己不能决定什么时候执行，以及执行多长时间。
因为同一个应用程序，既可以有多个进程，也可以有多个线程，因此，实现多任务的方法，有以下几种：
多进程模式（每个进程只有一个线程）：
```
┌──────────┐ ┌──────────┐ ┌──────────┐
│Process   │ │Process   │ │Process   │
│┌────────┐│ │┌────────┐│ │┌────────┐│
││ Thread ││ ││ Thread ││ ││ Thread ││
│└────────┘│ │└────────┘│ │└────────┘│
└──────────┘ └──────────┘ └──────────┘
```
多线程模式（一个进程有多个线程）：
```
┌────────────────────┐
│Process             │
│┌────────┐┌────────┐│
││ Thread ││ Thread ││
│└────────┘└────────┘│
│┌────────┐┌────────┐│
││ Thread ││ Thread ││
│└────────┘└────────┘│
└────────────────────┘
```
多进程＋多线程模式（复杂度最高）：
```
┌──────────┐┌──────────┐┌──────────┐
│Process   ││Process   ││Process   │
│┌────────┐││┌────────┐││┌────────┐│
││ Thread ││││ Thread ││││ Thread ││
│└────────┘││└────────┘││└────────┘│
│┌────────┐││┌────────┐││┌────────┐│
││ Thread ││││ Thread ││││ Thread ││
│└────────┘││└────────┘││└────────┘│
└──────────┘└──────────┘└──────────┘
```
### 进程VS线程
进程和线程是包含关系，但是多任务既可以由多进程实现，也可以由单进程内的多线程实现，还可以混合多进程＋多线程。
具体采用哪种方式，要考虑到进程和线程的特点。
和多线程相比，多进程的缺点在于：
+ 创建进程比创建线程开销大，尤其是在Windows系统上；
+ 进程间通信比线程间通信要慢，因为线程间通信就是读写同一个变量，速度很快。
而多进程的优点在于：多进程稳定性比多线程高，因为在多进程的情况下，一个进程崩溃不会影响其他进程，而在多线程的情况下，任何一个线程崩溃会直接导致整个进程崩溃。

### 多线程
Java语言内置了多线程支持：一个Java程序实际上是一个JVM进程，JVM进程用一个主线程来执行`main()`方法，在`main()`方法内部，我们又可以启动多个线程。此外，JVM还有负责垃圾回收的其他工作线程等。
因此，对于大多数Java程序来说，我们说多任务，实际上是说如何使用多线程实现多任务。
和单线程相比，多线程编程的特点在于：多线程经常需要读写共享数据，并且需要同步。例如，播放电影时，就必须由一个线程播放视频，另一个线程播放音频，两个线程需要协调运行，否则画面和声音就不同步。因此，多线程编程的复杂度高，调试更困难。
Java多线程编程的特点又在于：
+ 多线程模型是Java程序最基本的并发模型；
+ 后续读写网络、数据库、Web开发等都依赖Java多线程模型。

### 创建新线程
Java语言内置了多线程支持。当Java程序启动的时候，实际上是启动了一个JVM进程，然后，JVM启动主线程来执行`main()`方法。在`main()`方法中，我们又可以启动其他线程。
要创建一个新线程非常容易，我们需要实例化一个`Thread`实例，然后调用它的`start()`方法：
```java
// 多线程
public class Main {
    public static void main(String[] args) {
        Thread t = new Thread();
        t.start(); // 启动新线程
    }
}
```
但是这个线程启动后实际上什么也不做就立刻结束了。我们希望新线程能执行指定的代码，有以下几种方法：

方法一：从`Thread`派生一个自定义类，然后覆写`run()`方法：
```java
// 多线程
public class Main {
    public static void main(String[] args) {
        Thread t = new MyThread();
        t.start(); // 启动新线程
    }
}

class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("start new thread!");
    }
}
```
执行上述代码，注意到`start()`方法会在内部自动调用实例的`run()`方法。

方法二：创建`Thread`实例时，传入一个`Runnable`实例：
```java
// 多线程
public class Main {
    public static void main(String[] args) {
        Thread t = new Thread(new MyRunnable());
        t.start(); // 启动新线程
    }
}

class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("start new thread!");
    }
}
```
或者用Java8引入的lambda语法进一步简写为：
```java
// 多线程
public class Main {
    public static void main(String[] args) {
        Thread t = new Thread(() -> {
            System.out.println("start new thread!");
        });
        t.start(); // 启动新线程
    }
}
```
有童鞋会问，使用线程执行的打印语句，和直接在`main()`方法执行有区别吗？
区别大了去了。我们看以下代码：
```java
public class Main {
    public static void main(String[] args) {
        System.out.println("main start...");
        Thread t = new Thread() {
            public void run() {
                System.out.println("thread run...");
                System.out.println("thread end.");
            }
        };
        t.start();
        System.out.println("main end...");
    }
}
```
我们用蓝色表示主线程，也就是`main`线程，`main`线程执行的代码有4行，首先打印`main start`，然后创建`Thread`对象，紧接着调用`start()`启动新线程。当`start()`方法被调用时，JVM就创建了一个新线程，我们通过实例变量`t`来表示这个新线程对象，并开始执行。
接着，`main`线程继续执行打印`main end`语句，而`t`线程在`main`线程执行的同时会并发执行，打印`thread run`和`thread end`语句。
但是，除了可以肯定，`main start`会先打印外，`main end`打印在`thread run`之前、`thread end`之后或者之间，都无法确定。因为从`t`线程开始运行以后，两个线程就开始同时运行了，并且由操作系统调度，程序本身无法确定线程的调度顺序。
要模拟并发执行的效果，我们可以在线程中调用`Thread.sleep()`，强迫当前线程暂停一段时间：
```java
// 多线程
public class Main {
    public static void main(String[] args) {
        System.out.println("main start...");
        Thread t = new Thread() {
            public void run() {
                System.out.println("thread run...");
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {}
                System.out.println("thread end.");
            }
        };
        t.start();
        try {
            Thread.sleep(20);
        } catch (InterruptedException e) {}
        System.out.println("main end...");
    }
}
```
`sleep()`传入的参数是毫秒。调整暂停时间的大小，我们可以看到`main`线程和`t`线程执行的先后顺序。
要特别注意：直接调用`Thread`实例的`run()`方法是无效的：
```java
public class Main {
    public static void main(String[] args) {
        Thread t = new MyThread();
        t.run();
    }
}

class MyThread extends Thread {
    public void run() {
        System.out.println("hello");
    }
}
```
直接调用`run()`方法，相当于调用了一个普通的Java方法，当前线程并没有任何改变，也不会启动新线程。上述代码实际上是在`main()`方法内部又调用了`run()`方法，打印`hello`语句是在`main`线程中执行的，没有任何新线程被创建。
必须调用`Thread`实例的`start()`方法才能启动新线程，如果我们查看`Thread`类的源代码，会看到`start()`方法内部调用了一个`private native void start0()`方法，native修饰符表示这个方法是由JVM虚拟机内部的C代码实现的，不是由Java代码实现的。

#### 线程的优先级
可以对线程设定优先级，设定优先级的方法是：
```java
Thread.setPriority(int n) // 1~10, 默认值5
```
优先级高的线程被操作系统调度的优先级较高，操作系统对高优先级线程可能调度更频繁，但我们决不能通过设置优先级来确保高优先级的线程一定会先执行。

### 小结
Java用`Thread`对象表示一个线程，通过调用`start()`启动一个新线程；
一个线程对象只能调用一次`start()`方法；
线程的执行代码写在`run()`方法中；
线程调度由操作系统决定，程序本身无法决定调度顺序；
`Thread.sleep()`可以把当前线程暂停一段时间。

## 线程的状态
在Java程序中，一个线程对象只能调用一次`start()`方法启动新线程，并在新线程中执行`run()`方法。一旦`run()`方法执行完毕，线程就结束了。因此，Java线程的状态有以下几种：
+ New：新创建的线程，尚未执行；
  实现`Runnable`接口和继承`Thread`可以得到一个线程类，`new`一个实例出来，线程就进入了初始状态。
+ Runnable：运行中的线程，正在执行`run()`方法的Java代码；
  Runnable中的Ready:
  1. 就绪状态只是说你资格运行，调度程序没有挑选到你，你就永远是就绪状态。
  2. 就绪状态只是说你资格运行，调度程序没有挑选到你，你就永远是就绪状态。
  3. 当前线程`sleep()`方法结束，其他线程`join()`结束(等待其它线程执行结束)，等待用户输入完毕，某个线程拿到对象锁，这些线程也将进入就绪状态。
  4. 当前线程时间片用完了，调用当前线程的yield()方法，当前线程进入就绪状态。
  5. 锁池里的线程拿到对象锁后，进入就绪状态。
  Runnable中的Running:
  线程调度程序从可运行池中选择一个线程作为当前线程时线程所处的状态。**这也是线程进入运行状态的唯一的一种方式。**
+ Blocked：运行中的线程，因为某些操作被阻塞而挂起；
  阻塞状态是线程阻塞在进入`synchronized`关键字修饰的方法或代码块(获取锁)时的状态。
+ Waiting：运行中的线程，因为某些操作在等待中；
  处于这种状态的线程不会被分配CPU执行时间，它们要等待被显式地唤醒，否则会处于无限期等待的状态。
+ Timed Waiting：运行中的线程，因为执行`sleep()`方法正在计时等待；
  处于这种状态的线程不会被分配CPU执行时间，不过无须无限期等待被其他线程显示地唤醒，在达到一定时间后它们会自动唤醒。
+ Terminated：线程已终止，因为`run()`方法执行完毕。
  1. 当线程的`run()`方法完成时，或者主线程的`main()`方法完成时，我们就认为它终止了。这个线程对象也许是活的，但是它已经不是一个单独执行的线程。线程一旦终止了，就不能复生。
  2. 在一个终止的线程上调用`start()`方法，会抛出`java.lang.IllegalThreadStateException`异常。

用一个状态转移图表示如下：
```
         ┌─────────────┐
         │     New     │
         └─────────────┘
                │
                ▼
┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
 ┌─────────────┐ ┌─────────────┐
││  Runnable   │ │   Blocked   ││
 └─────────────┘ └─────────────┘
│┌─────────────┐ ┌─────────────┐│
 │   Waiting   │ │Timed Waiting│
│└─────────────┘ └─────────────┘│
 ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
                │
                ▼
         ┌─────────────┐
         │ Terminated  │
         └─────────────┘
```
当线程启动后，它可以在`Runnable`、`Blocked`、`Waiting`和`Timed Waiting`这几个状态之间切换，直到最后变成`Terminated`状态，线程终止。
线程终止的原因有:
+ 线程正常终止：`run()`方法执行到`return`语句返回；
+ 线程意外终止：`run()`方法因为未捕获的异常导致线程终止；
+ 对某个线程的Thread实例调用`stop()`方法强制终止（强烈不推荐使用）.
![](2020-12-22-20-08-19.png)
```java
// 多线程
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            System.out.println("hello");
        });
        System.out.println("start");
        t.start();
        t.join();
        System.out.println("end");
    }
}
```
当`main`线程对线程对象`t`调用`join()`方法时，主线程将等待变量`t`表示的线程运行结束，即`join`就是指等待该线程结束，然后才继续往下执行自身线程。所以，上述代码打印顺序可以肯定是`main`线程先打印`start`，`t`线程再打印`hello`，`main`线程最后再打印`end`。
如果`t`线程已经结束，对实例`t`调用`join()`会立刻返回。此外，`join(long)`的重载方法也可以指定一个等待时间，超过等待时间后就不再继续等待。


### 小结
Java线程对象`Thread`的状态包括：`New`、`Runnable`、`Blocked`、`Waiting`、`Timed Waiting`和`Terminated`；
通过对另一个线程对象调用`join()`方法可以等待其执行结束；
可以指定等待时间，超过等待时间线程仍然没有结束就不再等待；
对已经运行结束的线程调用`join()`方法会立刻返回。

## 中断线程
如果线程需要执行一个长时间任务，就可能需要能中断线程。中断线程就是其他线程给该线程发一个信号，该线程收到信号后结束执行`run()`方法，使得自身线程能立刻结束运行。
我们举个栗子：假设从网络下载一个100M的文件，如果网速很慢，用户等得不耐烦，就可能在下载过程中点“取消”，这时，程序就需要中断下载线程的执行。
中断一个线程非常简单，只需要在其他线程中对目标线程调用`interrupt()`方法，目标线程需要反复检测自身状态是否是`interrupted`状态，如果是，就立刻结束运行。
我们还是看示例代码：
```java
// 中断线程
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new MyThread();
        t.start();
        Thread.sleep(1); // 暂停1毫秒
        t.interrupt(); // 中断t线程
        t.join(); // 等待t线程结束
        System.out.println("end");
    }
}

class MyThread extends Thread {
    public void run() {
        int n = 0;
        while (! isInterrupted()) {
            n ++;
            System.out.println(n + " hello!");
        }
    }
}
```
仔细看上述代码，`main`线程通过调用`t.interrupt()`方法中断`t`线程，但是要注意，`interrupt()`方法仅仅向`t`线程发出了“中断请求”，至于`t`线程是否能立刻响应，要看具体代码。而`t`线程的`while`循环会检测`isInterrupted()`，所以上述代码能正确响应`interrupt()`请求，使得自身立刻结束运行`run()`方法。

如果线程处于等待状态，例如，`t.join()`会让`main`线程进入等待状态，此时，如果对`main`线程调用`interrupt()`，`join()`方法会立刻抛出`InterruptedException`，因此，目标线程只要捕获到`join()`方法抛出的`InterruptedException`，就说明有其他线程对其调用了`interrupt()`方法，通常情况下该线程应该立刻结束运行。
```java
// 中断线程
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new MyThread();
        t.start();
        Thread.sleep(1000);
        t.interrupt(); // 中断t线程
        t.join(); // 等待t线程结束
        System.out.println("end");
    }
}

class MyThread extends Thread {
    public void run() {
        Thread hello = new HelloThread();
        hello.start(); // 启动hello线程
        try {
            hello.join(); // 等待hello线程结束
        } catch (InterruptedException e) {
            System.out.println("interrupted!");
        }
        hello.interrupt();
    }
}

class HelloThread extends Thread {
    public void run() {
        int n = 0;
        while (!isInterrupted()) {
            n++;
            System.out.println(n + " hello!");
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                break;
            }
        }
    }
}
```
`main`线程通过调用`t.interrupt()`从而通知`t`线程中断，而此时`t`线程正位于`hello.join()`的等待中，此方法会立刻结束等待并抛出`InterruptedException`。由于我们在`t`线程中捕获了`InterruptedException`，因此，就可以准备结束该线程。在`t`线程结束前，对`hello`线程也进行了`interrupt()`调用通知其中断。如果去掉这一行代码，可以发现hello线程仍然会继续运行，且JVM不会退出。
另一个常用的中断线程的方法是设置标志位。我们通常会用一个`running`标志位来标识线程是否应该继续运行，在外部线程中，通过把`HelloThread.running`置为`false`，就可以让线程结束：
```java
// 中断线程
public class Main {
    public static void main(String[] args)  throws InterruptedException {
        HelloThread t = new HelloThread();
        t.start();
        Thread.sleep(1);
        t.running = false; // 标志位置为false
    }
}

class HelloThread extends Thread {
    public volatile boolean running = true;
    public void run() {
        int n = 0;
        while (running) {
            n ++;
            System.out.println(n + " hello!");
        }
        System.out.println("end!");
    }
}
```
注意到`HelloThread`的标志位`boolean running`是一个线程间共享的变量。线程间共享变量需要使用`volatile`关键字标记，确保每个线程都能读取到更新后的变量值。
为什么要对线程间共享的变量用关键字`volatile`声明？这涉及到Java的内存模型。在Java虚拟机中，变量的值保存在主内存中，但是，当线程访问变量时，它会先获取一个副本，并保存在自己的工作内存中。如果线程修改了变量的值，虚拟机会在某个时刻把修改后的值回写到主内存，但是，这个时间是不确定的！
```
┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
           Main Memory
│                               │
   ┌───────┐┌───────┐┌───────┐
│  │ var A ││ var B ││ var C │  │
   └───────┘└───────┘└───────┘
│     │ ▲               │ ▲     │
 ─ ─ ─│─│─ ─ ─ ─ ─ ─ ─ ─│─│─ ─ ─
      │ │               │ │
┌ ─ ─ ┼ ┼ ─ ─ ┐   ┌ ─ ─ ┼ ┼ ─ ─ ┐
      ▼ │               ▼ │
│  ┌───────┐  │   │  ┌───────┐  │
   │ var A │         │ var C │
│  └───────┘  │   │  └───────┘  │
   Thread 1          Thread 2
└ ─ ─ ─ ─ ─ ─ ┘   └ ─ ─ ─ ─ ─ ─ ┘
```
这会导致如果一个线程更新了某个变量，另一个线程读取的值可能还是更新前的.例如，主内存的变量`a = true`，线程1执行`a = false`时，它在此刻仅仅是把变量`a`的副本变成了`false`，主内存的变量`a`还是`true`，在JVM把修改后的`a`回写到主内存之前，其他线程读取到的`a`的值仍然是`true`，这就造成了多线程之间共享的变量不一致。
因此，`volatile`关键字的目的是告诉虚拟机：
+ 每次访问变量时，总是获取主内存的最新值；
+ 每次修改变量后，立刻回写到主内存。
`volatile`关键字解决的是可见性问题：当一个线程修改了某个共享变量的值，其他线程能够立刻看到修改后的值。
如果我们去掉`volatile`关键字，运行上述程序，发现效果和带`volatile`差不多，这是因为在x86的架构下，JVM回写主内存的速度非常快，但是，换成ARM的架构，就会有显著的延迟。

## 小结
对目标线程调用`interrupt()`方法可以请求中断一个线程，目标线程通过检测`isInterrupted()`标志获取自身是否已中断。如果目标线程处于等待状态，该线程会捕获到`InterruptedException`；
目标线程检测到`isInterrupted()`为`true`或者捕获了`InterruptedException`都应该立刻结束自身线程；
通过标志位判断需要正确使用`volatile`关键字；
`volatile`关键字解决了共享变量在线程间的可见性问题。

## 守护线程
Java程序入口就是由JVM启动`main`线程，`main`线程又可以启动其他线程。当所有线程都运行结束时，JVM退出，进程结束。
如果有一个线程没有退出，JVM进程就不会退出。所以，必须保证所有线程都能及时结束。
但是有一种线程的目的就是无限循环，例如，一个定时触发任务的线程：
```java
class TimerThread extends Thread {
    @Override
    public void run() {
        while (true) {
            System.out.println(LocalTime.now());
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                break;
            }
        }
    }
}
```
如果这个线程不结束，JVM进程就无法结束。问题是，由谁负责结束这个线程？
然而这类线程经常没有负责人来负责结束它们。但是，当其他线程结束时，JVM进程又必须要结束，怎么办？
答案是使用守护线程（Daemon Thread）。
守护线程是指为其他线程服务的线程。在JVM中，所有非守护线程都执行完毕后，无论有没有守护线程，虚拟机都会自动退出。
因此，JVM退出时，不必关心守护线程是否已结束。
如何创建守护线程呢？方法和普通线程一样，只是在调用`start()`方法前，调用`setDaemon(true)`把该线程标记为守护线程：
```java
Thread t = new MyThread();
t.setDaemon(true);
t.start();
```
在守护线程中，编写代码要注意：守护线程不能持有任何需要关闭的资源，例如打开文件等，因为虚拟机退出时，守护线程没有任何机会来关闭文件，这会导致数据丢失。

### 小结
守护线程是为其他线程服务的线程；
所有非守护线程都执行完毕后，虚拟机退出；
守护线程不能持有需要关闭的资源（如打开文件等）。

## 线程同步
当多个线程同时运行时，线程的调度由操作系统决定，程序本身无法决定。因此，任何一个线程都有可能在任何指令处被操作系统暂停，然后在某个时间段后继续执行。
这个时候，有个单线程模型下不存在的问题就来了：如果多个线程同时读写共享变量，会出现数据不一致的问题。
我们来看一个例子：
```java
// 多线程
public class Main {
    public static void main(String[] args) throws Exception {
        var add = new AddThread();
        var dec = new DecThread();
        add.start();
        dec.start();
        add.join();
        dec.join();
        System.out.println(Counter.count);
    }
}

class Counter {
    public static int count = 0;
}

class AddThread extends Thread {
    public void run() {
        for (int i=0; i<10000; i++) { Counter.count += 1; }
    }
}

class DecThread extends Thread {
    public void run() {
        for (int i=0; i<10000; i++) { Counter.count -= 1; }
    }
}
```
上面的代码很简单，两个线程同时对一个`int`变量进行操作，一个加10000次，一个减10000次，最后结果应该是0，但是，每次运行，结果实际上都是不一样的。
这是因为对变量进行读取和写入时，结果要正确，必须保证是原子操作。原子操作是指不能被中断的一个或一系列操作。
例如，对于语句：
```java
n = n + 1;
```
看上去是一行语句，实际上对应了3条指令：
```
ILOAD
IADD
ISTORE
```
我们假设`n`的值是`100`，如果两个线程同时执行`n = n + 1`，得到的结果很可能不是`102`，而是`101`，原因在于：
```
┌───────┐    ┌───────┐
│Thread1│    │Thread2│
└───┬───┘    └───┬───┘
    │            │
    │ILOAD (100) │
    │            │ILOAD (100)
    │            │IADD
    │            │ISTORE (101)
    │IADD        │
    │ISTORE (101)│
    ▼            ▼
```
如果线程1在执行`ILOAD`后被操作系统中断，此刻如果线程2被调度执行，它执行`ILOAD`后获取的值仍然是`100`，最终结果被两个线程的`ISTORE`写入后变成了`101`，而不是期待的`102`。
这说明多线程模型下，要保证逻辑正确，对共享变量进行读写时，必须保证一组指令以原子方式执行：即某一个线程执行时，其他线程必须等待：
```
┌───────┐     ┌───────┐
│Thread1│     │Thread2│
└───┬───┘     └───┬───┘
    │             │
    │-- lock --   │
    │ILOAD (100)  │
    │IADD         │
    │ISTORE (101) │
    │-- unlock -- │
    │             │-- lock --
    │             │ILOAD (101)
    │             │IADD
    │             │ISTORE (102)
    │             │-- unlock --
    ▼             ▼
```
通过加锁和解锁的操作，就能保证3条指令总是在一个线程执行期间，不会有其他线程会进入此指令区间。即使在执行期线程被操作系统中断执行，其他线程也会因为无法获得锁导致无法进入此指令区间。只有执行线程将锁释放后，其他线程才有机会获得锁并执行。这种加锁和解锁之间的代码块我们称之为临界区（`Critical Section`），任何时候临界区最多只有一个线程能执行。
可见，保证一段代码的原子性就是通过加锁和解锁实现的。Java程序使用`synchronized`关键字对一个对象进行加锁：
```java
synchronized(lock) {
    n = n + 1;
}
```
`synchronized`保证了代码块在任意时刻最多只有一个线程能执行。我们把上面的代码用`synchronized`改写如下：
```java
// 多线程
public class Main {
    public static void main(String[] args) throws Exception {
        var add = new AddThread();
        var dec = new DecThread();
        add.start();
        dec.start();
        add.join();
        dec.join();
        System.out.println(Counter.count);
    }
}

class Counter {
    public static final Object lock = new Object();
    public static int count = 0;
}

class AddThread extends Thread {
    public void run() {
        for (int i=0; i<10000; i++) {
            synchronized(Counter.lock) {
                Counter.count += 1;
            }
        }
    }
}

class DecThread extends Thread {
    public void run() {
        for (int i=0; i<10000; i++) {
            synchronized(Counter.lock) {
                Counter.count -= 1;
            }
        }
    }
}
```
注意到代码：
```java
synchronized(Counter.lock) { // 获取锁
    ...
} // 释放锁
```
它表示用`Counter.lock`实例作为锁，两个线程在执行各自的`synchronized(Counter.lock) { ... }`代码块时，必须先获得锁，才能进入代码块进行。执行结束后，在`synchronized`语句块结束会自动释放锁。这样一来，对`Counter.count`变量进行读写就不可能同时进行。上述代码无论运行多少次，最终结果都是0。
使用`synchronized`解决了多线程同步访问共享变量的正确性问题。但是，它的缺点是带来了性能下降。因为`synchronized`代码块无法并发执行。此外，加锁和解锁需要消耗一定的时间，所以，`synchronized`会降低程序的执行效率。
我们来概括一下如何使用`synchronized`：
1. 找出修改共享变量的线程代码块；
2. 选择一个共享实例作为锁；
3. 使用`synchronized(lockObject) { ... }`。
在使用`synchronized`的时候，不必担心抛出异常。因为无论是否有异常，都会在`synchronized`结束处正确释放锁：
```java
public void add(int m) {
    synchronized (obj) {
        if (m < 0) {
            throw new RuntimeException();
        }
        this.value += m;
    } // 无论有无异常，都会在此释放锁
}
```
我们再来看一个错误使用`synchronized`的例子：
```java
// 多线程
public class Main {
    public static void main(String[] args) throws Exception {
        var add = new AddThread();
        var dec = new DecThread();
        add.start();
        dec.start();
        add.join();
        dec.join();
        System.out.println(Counter.count);
    }
}

class Counter {
    public static final Object lock1 = new Object();
    public static final Object lock2 = new Object();
    public static int count = 0;
}

class AddThread extends Thread {
    public void run() {
        for (int i=0; i<10000; i++) {
            synchronized(Counter.lock1) {
                Counter.count += 1;
            }
        }
    }
}

class DecThread extends Thread {
    public void run() {
        for (int i=0; i<10000; i++) {
            synchronized(Counter.lock2) {
                Counter.count -= 1;
            }
        }
    }
}
```
结果并不是0，这是因为两个线程各自的`synchronized`锁住的**不是同一个对象**！这使得两个线程各自都可以同时获得锁：因为JVM只保证同一个锁在任意时刻只能被一个线程获取，但两个不同的锁在同一时刻可以被两个线程分别获取。
因此，使用`synchronized`的时候，获取到的是哪个锁非常重要。锁对象如果不对，代码逻辑就不对。
我们再看一个例子：
```java
// 多线程
public class Main {
    public static void main(String[] args) throws Exception {
        var ts = new Thread[] { new AddStudentThread(), new DecStudentThread(), new AddTeacherThread(), new DecTeacherThread() };
        for (var t : ts) {
            t.start();
        }
        for (var t : ts) {
            t.join();
        }
        System.out.println(Counter.studentCount);
        System.out.println(Counter.teacherCount);
    }
}

class Counter {
    public static final Object lock = new Object();
    public static int studentCount = 0;
    public static int teacherCount = 0;
}

class AddStudentThread extends Thread {
    public void run() {
        for (int i=0; i<10000; i++) {
            synchronized(Counter.lock) {
                Counter.studentCount += 1;
            }
        }
    }
}

class DecStudentThread extends Thread {
    public void run() {
        for (int i=0; i<10000; i++) {
            synchronized(Counter.lock) {
                Counter.studentCount -= 1;
            }
        }
    }
}

class AddTeacherThread extends Thread {
    public void run() {
        for (int i=0; i<10000; i++) {
            synchronized(Counter.lock) {
                Counter.teacherCount += 1;
            }
        }
    }
}

class DecTeacherThread extends Thread {
    public void run() {
        for (int i=0; i<10000; i++) {
            synchronized(Counter.lock) {
                Counter.teacherCount -= 1;
            }
        }
    }
}
```
上述代码的4个线程对两个共享变量分别进行读写操作，但是使用的锁都是`Counter.lock`这一个对象，这就造成了原本可以并发执行的`Counter.studentCount += 1`和`Counter.teacherCount += 1`，现在无法并发执行了，执行效率大大降低。实际上，需要同步的线程可以分成两组：`AddStudentThread`和`DecStudentThread`，`AddTeacherThread`和`DecTeacherThread`，组之间不存在竞争，因此，应该使用两个不同的锁，即：
`AddStudentThread`和`DecStudentThread`使用`lockStudent`锁：
```java
synchronized(Counter.lockStudent) {
    ...
}
```
`AddTeacherThread`和`DecTeacherThread`使用`lockTeacher`锁：
```java
synchronized(Counter.lockTeacher) {
    ...
}
```
这样才能最大化地提高执行效率。
### 不需要`synchronized`的操作
JVM规范定义了几种原子操作：、
> "原子操作(atomic operation)是不需要synchronized"，这是多线程编程的老生常谈了。所谓原子操作是指不会被线程调度机制打断的操作；这种操作一旦开始，就一直运行到结束，中间不会有任何 context switch （切 [1]  换到另一个线程）。
+ 基本类型（`long`和`double`除外）赋值，例如：`int n = m`；
+ 引用类型赋值，例如：`List<String> list = anotherList`。
`long`和`double`是64位数据，JVM没有明确规定64位赋值操作是不是一个原子操作，不过在x64平台的JVM是把`long`和`double`的赋值作为原子操作实现的。
单条原子操作的语句不需要同步。例如：
```java
public void set(int m) {
    synchronized(lock) {
        this.value = m;
    }
}
```
就不需要同步。
对引用也是类似。例如：
```java
public void set(String s) {
    this.value = s;
}
```
上述赋值语句并不需要同步。
但是，如果是多行赋值语句，就必须保证是同步操作，例如：
```java
class Pair {
    int first;
    int last;
    public void set(int first, int last) {
        synchronized(this) {
            this.first = first;
            this.last = last;
        }
    }
}
```
有些时候，通过一些巧妙的转换，可以把非原子操作变为原子操作。例如，上述代码如果改造成：
```java
class Pair {
    int[] pair;
    public void set(int first, int last) {
        int[] ps = new int[] { first, last };
        this.pair = ps;
    }
}
```
就不再需要同步，因为`this.pair = ps`是引用赋值的原子操作。而语句：
```java
int[] ps = new int[] { first, last };
```
这里的`ps`是方法内部定义的局部变量，每个线程都会有各自的局部变量，互不影响，并且互不可见，并不需要同步。

### 并发编程的锁机制：`synchronized`与`lock`
#### 1. 锁的种类
锁的种类挺多，包括：自旋锁、自旋锁的其他种类、阻塞锁、可重入锁、读写锁、互斥锁、悲观锁、乐观锁、公平锁、可重入锁等等，其余就不列出了。我们这边重点看如下几种：可重入锁、读写锁、可中断锁、公平锁。

##### 1.1 可重入锁
如果锁具备可重入性，则称作为可重入锁。`synchronized`和`ReentrantLock`都是可重入锁，可重入性在我看来实际上表明了锁的分配机制：基于线程的分配，而不是基于方法调用的分配。举比如说，当一个线程执行到`method1`的`synchronized`方法时，而在`method1`中会调用另外一个`synchronized`方法`method2`，此时该线程不必重新去申请锁，而是可以直接执行方法`method2`。(也就是说当前线程已经申请一个锁,在运行的过程中,需要某个调用的方法,同样需要获得这个锁,但是此时不必重新申请该锁,可以直接执行这个方法。)

##### 1.2 读写锁
读写锁将对一个资源的访问分成了2个锁，如文件，一个读锁和一个写锁。正因为有了读写锁，才使得多个线程之间的读操作不会发生冲突。`ReadWriteLock`就是读写锁，它是一个接口，`ReentrantReadWriteLock`实现了这个接口。可以通过`readLock()`获取读锁，通过`writeLock()`获取写锁。

##### 1.3 可中断锁
可中断锁，即可以中断的锁。在Java中，`synchronized`就不是可中断锁，而`Lock`是可中断锁。
如果某一线程A正在执行锁中的代码，另一线程B正在等待获取该锁，可能由于等待时间过长，线程B不想等待了，想先处理其他事情，我们可以让它中断自己或者在别的线程中中断它，这种就是可中断锁。
`Lock`接口中的`lockInterruptibly()`方法就体现了`Lock`的可中断性。

##### 1.4 公平锁
公平锁即尽量以请求锁的顺序来获取锁。同时有多个线程在等待一个锁，当这个锁被释放时，等待时间最久的线程（最先请求的线程）会获得该锁，这种就是公平锁。非公平锁即无法保证锁的获取是按照请求锁的顺序进行的，这样就可能导致某个或者一些线程永远获取不到锁。`synchronized`是非公平锁，它无法保证等待的线程获取锁的顺序。对于`ReentrantLock`和`ReentrantReadWriteLock`，默认情况下是非公平锁，但是可以设置为公平锁。

#### 2. `synchronized`和`lock`的用法
##### 2.1 `synchronized`
`synchronized`是Java的关键字，当它用来修饰一个方法或者一个代码块的时候，能够保证在同一时刻最多只有一个线程执行该段代码。简单总结如下四种用法。
###### 2.1.1 代码块
对某一代码块使用，`synchronized`后跟括号，括号里是变量，一次只有一个线程进入该代码块。
```java
public int synMethod(int m){
    synchronized(m) {
        //...
    }
}
```
###### 2.1.2 方法声明时
方法声明时使用，放在范围操作符之后,返回类型声明之前。即一次只能有一个线程进入该方法，其他线程要想在此时调用该方法，只能排队等候。
```java
public synchronized void synMethod() {
   //...
}
```
###### 2.1.3 `synchronized`后面括号里是对象
`synchronized`后面括号里是一对象，此时线程获得的是对象锁。
```java
public void test() {
    synchronized (this) {
        //...
    }
}
```
###### 2.1.4 `synchronized`后面括号里是类
`synchronized`后面括号里是类，如果线程进入，则线程在该类中所有操作不能进行，包括静态变量和静态方法，对于含有静态方法和静态变量的代码块的同步，通常使用这种方式。

##### 2.2 Lock

### 小结
多线程同时读写共享变量时，会造成逻辑错误，因此需要通过`synchronized`同步；
同步的本质就是给指定对象加锁，加锁后才能继续执行后续代码；
注意加锁对象必须是同一个实例；
对JVM定义的单个原子操作不需要同步。

## 同步方法
我们知道Java程序依靠`synchronized`对线程进行同步，使用`synchronized`的时候，锁住的是哪个对象非常重要。
让线程自己选择锁对象往往会使得代码逻辑混乱，也不利于封装。更好的方法是把`synchronized`逻辑封装起来。例如，我们编写一个计数器如下：
```java
public class Counter {
    private int count = 0;

    public void add(int n) {
        synchronized(this) {
            count += n;
        }
    }

    public void dec(int n) {
        synchronized(this) {
            count -= n;
        }
    }

    public int get() {
        return count;
    }
}
```
这样一来，线程调用`add()`、`dec()`方法时，它不必关心同步逻辑，因为`synchronized`代码块在`add()`、`dec()`方法内部。并且，我们注意到，`synchronized`锁住的对象是`this`，即当前实例，这又使得创建多个`Counter`实例的时候，它们之间互不影响，可以并发执行：
```java
var c1 = new Counter();
var c2 = new Counter();

// 对c1进行操作的线程:
new Thread(() -> {
    c1.add();
}).start();
new Thread(() -> {
    c1.dec();
}).start();

// 对c2进行操作的线程:
new Thread(() -> {
    c2.add();
}).start();
new Thread(() -> {
    c2.dec();
}).start();
```
现在，对于`Counter`类，多线程可以正确调用。
如果一个类被设计为允许多线程正确访问，我们就说这个类就是“线程安全”的（thread-safe），上面的`Counter`类就是线程安全的。Java标准库的`java.lang.StringBuffer`也是线程安全的。
还有一些不变类，例如`String`，`Integer`，`LocalDate`，它们的所有成员变量都是`final`，多线程同时访问时只能读不能写，这些不变类也是线程安全的。
最后，类似`Math`这些只提供静态方法，没有成员变量的类，也是线程安全的。
除了上述几种少数情况，大部分类，例如`ArrayList`，都是非线程安全的类，我们不能在多线程中修改它们。但是，如果所有线程都只读取，不写入，那么`ArrayList`是可以安全地在线程间共享的。
我们再观察`Counter`的代码：
```java
public class Counter {
    public void add(int n) {
        synchronized(this) {
            count += n;
        }
    }
    ...
}
```
当我们锁住的是`this`实例时，实际上可以用`synchronized`修饰这个方法。下面两种写法是等价的：
```java
public void add(int n) {
    synchronized(this) { // 锁住this
        count += n;
    } // 解锁
}
```
```java
public synchronized void add(int n) { // 锁住this
    count += n;
} // 解锁
```
因此，用`synchronized`修饰的方法就是同步方法，它表示整个方法都必须用`this`实例加锁。
我们再思考一下，如果对一个静态方法添加`synchronized`修饰符，它锁住的是哪个对象？
```java
public synchronized static void test(int n) {
    ...
}
```
对于`static`方法，是没有`this`实例的，因为`static`方法是针对类而不是实例。但是我们注意到任何一个类都有一个由JVM自动创建的`Class`实例，因此，对`static`方法添加`synchronized`，锁住的是该类的`Class`实例。上述`synchronized static`方法实际上相当于：
```java
public class Counter {
    public static void test(int n) {
        synchronized(Counter.class) {
            ...
        }
    }
}
```
我们再考察`Counter`的`get()`方法：
```java
public class Counter {
    private int count;

    public int get() {
        return count;
    }
    ...
}
```
它没有同步，因为读一个`int`变量不需要同步。
然而，如果我们把代码稍微改一下，返回一个包含两个`int`的对象：
```java
public class Counter {
    private int first;
    private int last;

    public Pair get() {
        Pair p = new Pair();
        p.first = first;
        p.last = last;
        return p;
    }
    ...
}
```
就必须要同步了。

### 小结
用`synchronized`修饰方法可以把整个方法变为同步代码块，`synchronized`方法加锁对象是`this`；
通过合理的设计和数据封装可以让一个类变为“线程安全”；
一个类没有特殊说明，默认不是thread-safe；
多线程能否安全访问某个非线程安全的实例，需要具体问题具体分析。

## 死锁
Java的线程锁是可重入的锁。
什么是可重入的锁？我们还是来看例子：
```java
public class Counter {
    private int count = 0;

    public synchronized void add(int n) {
        if (n < 0) {
            dec(-n);
        } else {
            count += n;
        }
    }

    public synchronized void dec(int n) {
        count += n;
    }
}
```
观察`synchronized`修饰的`add()`方法，一旦线程执行到`add()`方法内部，说明它已经获取了当前实例的`this`锁。如果传入的`n < 0`，将在`add()`方法内部调用`dec()`方法。由于`dec()`方法也需要获取`this`锁，现在问题来了：
对同一个线程，能否在获取到锁以后继续获取同一个锁？
答案是肯定的。**JVM允许同一个线程重复获取同一个锁，这种能被同一个线程反复获取的锁，就叫做可重入锁。**
由于Java的线程锁是可重入锁，所以，获取锁的时候，不但要判断是否是第一次获取，还要记录这是第几次获取。每获取一次锁，记录+1，每退出`synchronized`块，记录-1，减到0的时候，才会真正释放锁。

### 死锁
一个线程可以获取一个锁后，再继续获取另一个锁。例如：
```java
public void add(int m) {
    synchronized(lockA) { // 获得lockA的锁
        this.value += m;
        synchronized(lockB) { // 获得lockB的锁
            this.another += m;
        } // 释放lockB的锁
    } // 释放lockA的锁
}

public void dec(int m) {
    synchronized(lockB) { // 获得lockB的锁
        this.another -= m;
        synchronized(lockA) { // 获得lockA的锁
            this.value -= m;
        } // 释放lockA的锁
    } // 释放lockB的锁
}
```
在获取多个锁的时候，不同线程获取多个不同对象的锁可能导致死锁。对于上述代码，线程1和线程2如果分别执行`add()`和`dec()`方法时：
+ 线程1：进入`add()`，获得`lockA`；
+ 线程2：进入`dec()`，获得`lockB`。
随后：
+ 线程1：准备获得`lockB`，失败，等待中；
+ 线程2：准备获得`lockA`，失败，等待中。
此时，两个线程各自持有不同的锁，然后各自试图获取对方手里的锁，造成了双方无限等待下去，这就是死锁。
死锁发生后，没有任何机制能解除死锁，只能强制结束JVM进程。
因此，在编写多线程应用时，要特别注意防止死锁。
那么我们应该如何避免死锁呢？答案是：线程获取锁的顺序要一致。即严格按照先获取lockA，再获取lockB的顺序，改写dec()方法如下：
```java
public void dec(int m) {
    synchronized(lockA) { // 获得lockA的锁
        this.value -= m;
        synchronized(lockB) { // 获得lockB的锁
            this.another -= m;
        } // 释放lockB的锁
    } // 释放lockA的锁
}
```
### 小结
Java的`synchronized`锁是可重入锁；
死锁产生的条件是多线程各自持有不同的锁，并互相试图获取对方已持有的锁，导致无限等待；
避免死锁的方法是多线程获取锁的顺序要一致。

## 使用`wait`和`notify`
在Java程序中，`synchronized`解决了多线程竞争的问题。例如，对于一个任务管理器，多个线程同时往队列中添加任务，可以用`synchronized`加锁：
```java
class TaskQueue {
    Queue<String> queue = new LinkedList<>();

    public synchronized void addTask(String s) {
        this.queue.add(s);
    }
}
```
但是`synchronized`并没有解决多线程协调的问题。
仍然以上面的`TaskQueue`为例，我们再编写一个`getTask()`方法取出队列的第一个任务：
```java
class TaskQueue {
    Queue<String> queue = new LinkedList<>();

    public synchronized void addTask(String s) {
        this.queue.add(s);
    }

    public synchronized String getTask() {
        while (queue.isEmpty()) {
        }
        return queue.remove();
    }
}
```
上述代码看上去没有问题：`getTask()`内部先判断队列是否为空，如果为空，就循环等待，直到另一个线程往队列中放入了一个任务，`while()`循环退出，就可以返回队列的元素了。
但实际上`while()`循环永远不会退出。因为线程在执行`while()`循环时，已经在`getTask()`入口获取了`this`锁，其他线程根本无法调用`addTask()`，因为`addTask()`执行条件也是获取`this`锁。
因此，执行上述代码，线程会在`getTask()`中因为死循环而100%占用CPU资源。
如果深入思考一下，我们想要的执行效果是：
+ 线程1可以调用`addTask()`不断往队列中添加任务；
+ 线程2可以调用`getTask()`从队列中获取任务。如果队列为空，则`getTask()`应该等待，直到队列中至少有一个任务时再返回。
因此，多线程协调运行的原则就是：当条件不满足时，线程进入等待状态；当条件满足时，线程被唤醒，继续执行任务。
对于上述`TaskQueue`，我们先改造`getTask()`方法，在条件不满足时，线程进入等待状态：
```java
public synchronized String getTask() {
    while (queue.isEmpty()) {
        this.wait();
    }
    return queue.remove();
}
```
当一个线程执行到`getTask()`方法内部的`while`循环时，它必定已经获取到了`this`锁，此时，线程执行`while`条件判断，如果条件成立（队列为空），线程将执行`this.wait()`，进入等待状态。
这里的关键是：`wait()`方法必须在当前获取的锁对象上调用，这里获取的是`this`锁，因此调用`this.wait()`。
调用`wait()`方法后，线程进入等待状态，`wait()`方法不会返回，直到将来某个时刻，线程从等待状态被其他线程唤醒后，`wait()`方法才会返回，然后，继续执行下一条语句。
有些仔细的童鞋会指出：即使线程在`getTask()`内部等待，其他线程如果拿不到`this`锁，照样无法执行`addTask()`，肿么办？
这个问题的关键就在于`wait()`方法的执行机制非常复杂。首先，它不是一个普通的`Java`方法，而是定义在`Object`类的一个`native`方法，也就是由JVM的C代码实现的。其次，必须在`synchronized`块中才能调用`wait()`方法，因为`wait()`方法调用时，会释放线程获得的锁，`wait()`方法返回后，线程又会重新试图获得锁。
因此，只能在锁对象上调用`wait()`方法。因为在`getTask()`中，我们获得了`this`锁，因此，只能在`this`对象上调用`wait()`方法：
```java
public synchronized String getTask() {
    while (queue.isEmpty()) {
        // 释放this锁:
        this.wait();
        // 重新获取this锁
    }
    return queue.remove();
}
```
当一个线程在`this.wait()`等待时，它就会释放`this`锁，从而使得其他线程能够在`addTask()`方法获得`this`锁。
现在我们面临第二个问题：如何让等待的线程被重新唤醒，然后从`wait()`方法返回？答案是在相同的锁对象上调用`notify()`方法。我们修改`addTask()`如下：
```java
public synchronized void addTask(String s) {
    this.queue.add(s);
    this.notify(); // 唤醒在this锁等待的线程
}
```
注意到在往队列中添加了任务后，线程立刻对`this`锁对象调用`notify()`方法，这个方法会唤醒一个正在`this`锁等待的线程（就是在`getTask()`中位于`this.wait()`的线程），从而使得等待线程从`this.wait()`方法返回。

我们来看一个完整的例子：
```java
import java.util.*;
public class Main {
    public static void main(String[] args) throws InterruptedException {
        var q = new TaskQueue();
        var ts = new ArrayList<Thread>();
        for (int i=0; i<5; i++) {
            var t = new Thread() {
                public void run() {
                    // 执行task:
                    while (true) {
                        try {
                            String s = q.getTask();
                            System.out.println("execute task: " + s);
                        } catch (InterruptedException e) {
                            return;
                        }
                    }
                }
            };
            t.start();
            ts.add(t);
        }
        var add = new Thread(() -> {
            for (int i=0; i<10; i++) {
                // 放入task:
                String s = "t-" + Math.random();
                System.out.println("add task: " + s);
                q.addTask(s);
                try { Thread.sleep(100); } catch(InterruptedException e) {}
            }
        });
        add.start();
        add.join();
        Thread.sleep(100);
        for (var t : ts) {
            t.interrupt();
        }
    }
}

class TaskQueue {
    Queue<String> queue = new LinkedList<>();

    public synchronized void addTask(String s) {
        this.queue.add(s);
        this.notifyAll();
    }

    public synchronized String getTask() throws InterruptedException {
        while (queue.isEmpty()) {
            this.wait();
        }
        return queue.remove();
    }
}
```
这个例子中，我们重点关注`addTask()`方法，内部调用了`this.notifyAll()`而不是`this.notify()`，使用`notifyAll()`将唤醒所有当前正在`this`锁等待的线程，而`notify()`只会唤醒其中一个（具体哪个依赖操作系统，有一定的随机性）。这是因为可能有多个线程正在`getTask()`方法内部的`wait()`中等待，使用`notifyAll()`将一次性全部唤醒。通常来说，`notifyAll()`更安全。有些时候，如果我们的代码逻辑考虑不周，用`notify()`会导致只唤醒了一个线程，而其他线程可能永远等待下去醒不过来了。
但是，注意到`wait()`方法返回时需要重新获得`this`锁。假设当前有3个线程被唤醒，唤醒后，首先要等待执行`addTask()`的线程结束此方法后，才能释放`this`锁，随后，这3个线程中只能有一个获取到`this`锁，剩下两个将继续等待。
再注意到我们在`while()`循环中调用`wait()`，而不是`if`语句：
```java
public synchronized String getTask() throws InterruptedException {
    if (queue.isEmpty()) {
        this.wait();
    }
    return queue.remove();
}
```
这种写法实际上是错误的，因为线程被唤醒时，需要再次获取`this`锁。多个线程被唤醒后，只有一个线程能获取`this`锁，此刻，该线程执行`queue.remove()`可以获取到队列的元素，然而，剩下的线程如果获取`this`锁后执行`queue.remove()`，此刻队列可能已经没有任何元素了，所以，要始终在`while`循环中`wait()`，并且每次被唤醒后拿到`this`锁就必须再次判断：
```java
while (queue.isEmpty()) {
    this.wait();
}
```
所以，正确编写多线程代码是非常困难的，需要仔细考虑的条件非常多，任何一个地方考虑不周，都会导致多线程运行时不正常。

### 小结
`wait`和`notify`用于多线程协调运行：
+ 在`synchronized`内部可以调用`wait()`使线程进入等待状态；
+ 必须在已获得的锁对象上调用`wait()`方法；
+ 在`synchronized`内部可以调用`notify()`或`notifyAll()`唤醒其他等待线程；
+ 必须在已获得的锁对象上调用`notify()`或`notifyAll()`方法；
+ 已唤醒的线程还需要重新获得锁后才能继续执行。

## 使用`ReentrantLock`
