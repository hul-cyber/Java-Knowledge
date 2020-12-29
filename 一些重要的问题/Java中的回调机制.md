# Java中的回调机制
本文主要内容来自:https://www.cnblogs.com/xrq730/p/6424471.html
与
https://how2playlife.com/2019/09/11/11%E8%A7%A3%E8%AF%BBJava%E4%B8%AD%E7%9A%84%E5%9B%9E%E8%B0%83/

## 模块间的调用
在一个应用系统中，无论使用何种语言开发，必然存在模块之间的调用，调用的方式分为几种:
1. 同步调用
   ![](2020-10-15-17-47-19.png)
   同步调用是最基本并且最简单的一种调用方式，类A的方法a()调用类B的方法b()，一直等待b()方法执行完毕，a()方法继续往下走。这种调用方式适用于方法b()执行时间不长的情况，因为b()方法执行时间一长或者直接阻塞的话，a()方法的余下代码是无法执行下去的，这样会造成整个流程的阻塞。
   同步调用的例子:
   ```java
    public class Teacher {
        public void askQuestion(Student s) {
            s.answerQuestion();
        }
    } 
    public class Student {
        public void answerQuestion() {
            System.out.println("回答问题");
        }
    }    
    public class SyncCallDemo {
        public static void main(String[] args) {
            Teacher t = new Teacher();
            Student s = new Student();
            t.askQuestion(s);
        }
    }      
   ```
2. 异步调用
   ![](2020-10-15-17-47-59.png)
   异步调用是为了解决同步调用可能出现阻塞，导致整个流程卡住而产生的一种调用方式。类A的方法方法a()通过新起线程的方式调用类B的方法b()，代码接着直接往下执行，这样无论方法b()执行时间多久，都不会阻塞住方法a()的执行。但是这种方式，由于方法a()不等待方法b()的执行完成，在方法a()需要方法b()执行结果的情况下（视具体业务而定，有些业务比如启异步线程发个微信通知、刷新一个缓存这种就没必要），必须通过一定的方式对方法b()的执行结果进行监听。在Java中，可以使用Future+Callable的方式做到这一点，具体做法可以参见我的这篇文章Java多线程21：多线程下其他组件之CyclicBarrier、Callable、Future和FutureTask。
3. 回调
   ![](2020-10-15-17-49-14.png)
   最后是回调，回调的思想是:
    1) 类A的a()方法调用类B的b()方法
    2) 类B的b()方法执行完毕主动调用类A的callback()方法
   这样一种调用方式组成了上图，也就是一种双向的调用方式。
接下来看一下回调的代码示例，代码模拟的是这样一种场景：老师问学生问题，学生思考完毕回答老师。

首先定义一个回调接口，只有一个方法tellAnswer(int answer)，即学生思考完毕告诉老师答案:
```java
/**
 * 回调接口，原文出处http://www.cnblogs.com/xrq730/p/6424471.html
*/
public interface Callback {
    public void tellAnswer(int answer);     
}
```
定义一个老师对象，实现Callback接口：
```java
 /**
 * 老师对象，原文出处http://www.cnblogs.com/xrq730/p/6424471.html
 */
public class Teacher implements Callback {
        private Resolve resolve;
            
        public Teacher(Resolve resolve) {
            this.resolve = resolve;
        }

        public void askQuestion() {
            resolve.resolveQuestion(this);
        } 

        @Override
        public void tellAnswer(int answer) {
            System.out.println("知道了，你的答案是" + answer);
        }     
}
```
老师对象有两个public方法:
1. 回调接口tellAnswer(int answer)，即学生回答完毕问题之后，老师要做的事情
2. 问问题方法askQuestion()，即向学生问问题
接着定义一个学生接口，学生当然是解决问题，但是接收一个Callback参数，这样学生就知道解决完毕问题向谁报告:
```java
/**
 * 解决问题接口，原文出处http://www.cnblogs.com/xrq730/p/6424471.html
 */
public interface Resolve {
    public void resolveQuestion(Callback callback);  
}
```
我们使用Student类来实现上面的接口:
```java
public class Student implements Resolve {
    @Override
    public void resolveQuestion(Callback callback) {
        // 模拟解决问题
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {

        }

        // 回调，告诉老师作业答案
        callback.tellAnswer(3);
    }
}
```
之后我们可以运行下面的Demo:
```java
public class Demo {
    public static void main(String[] args) {
        Student ricky = new Student();
        Teacher teacher = new Teacher(ricky);

        teacher.askQuestion();
    }
}
```
对上面进行分析,首先我们创建了一个学生对象,之后我们将该学生对象作为参数构建一个老师对象,之后我们调用老师的`askQuestion()`方法,这个方法会根据传过来的学生对象对象来运行里面的`resolveQuestion()`方法,在`resolveQuestion()`中调用`Teacher`类中的回调方法。
上面的代码也比较好地体现了接口的作用,首先我们提取出了`Callback`接口以及`Resolve`接口,分别使用`Teacher`和`Student`来将他们实现,当我们将Teacher对象以及Student对象传递过去的时候,我们用`Callback`和`Resolve`来接收,这样的话,我们只能够使用`Callback`以及`Resolve`中的方法,这样的话,就保证了安全性。
最后一定要牢记回调的核心:**回调的核心就是调用方将本身即this传递给回调方，回调方再反过来调用调用方的方法**。对于我们上面的这个例子,即我们首先将`teacher`对象传递给回调方,之后回调方就能够根据传过来的`teacher`对象调用调用方中的方法。

## 多线程中回调机制
