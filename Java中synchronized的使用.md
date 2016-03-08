# 基本介绍 #
synchronized是Java中的关键字，表示同步的；下面展示两段单例模式的代码，这可能是大家最熟悉的代码了（基础面试题必出题目），为了保证单例模式的线程安全，这里使用synchronized：

** 线程安全的饿汉单例 **
<pre class="prettyprint">
    public class Singleton {  
      private static Singleton instance;  
      private Singleton (){}
      public static synchronized Singleton getInstance() {  
      if (instance == null) {  
          instance = new Singleton();  
      }  
      return instance;  
      }  
</pre>

** 双重校验锁的单例 **
<pre class="prettyprint">
    public class Singleton {  
      private volatile static Singleton singleton;  
      private Singleton (){}   
      public static Singleton getSingleton() {  
      if (singleton == null) {  
          synchronized (Singleton.class) {  
          if (singleton == null) {  
              singleton = new Singleton();  
          }  
         }  
     }  
     return singleton;  
     }  
    }  
</pre>

在第一段代码中，可以看到synchronized修饰了一个方法（同步方法），而在第二段中synchronized括住了一个class然后跟着一个代码块（同步代码块）；这两种有何区别呢，如何使用呢？接下来是synchronized使用的详细介绍。

# 同步方法#
首先，我们先看下不使用synchronized的执行情况：
<pre class="prettyprint">
    public class Demo {
        public static void main(String[] args) {
            Print pt = new Print();
            MyThread myThread = new MyThread(pt);
            Thread th0 = new Thread(myThread, "th0");
            Thread th1 = new Thread(myThread, "th1");
            Thread th2 = new Thread(myThread, "th2");
            th0.start();
            th1.start();
            th2.start();
        }
    }


    class Print {
        public void printNum() {
            for (int i = 0; i < Integer.MAX_VALUE; i++) {
                if ((i % 1000000) == 0) {
                    System.out.println(Thread.currentThread().getName() + "--" + i);
                }
            }
        }
    }


    class MyThread implements Runnable {
        private Print pt;

        public MyThread(Print pt) {
            this.pt = pt;
        }

        @Override
        public void run() {
            pt.printNum();
        }
    }
</pre>

代码执行结果：

>th0--0<br>
>th1--0<br>
>th2--0<br>
>th0--1000000<br>
>th2--1000000<br>
>th1--1000000<br>
>th2--2000000<br>
>th1--2000000<br>
>th2--3000000<br>
>th0--2000000<br>
>......

## 一个同步方法 ##
这是最一般的情况，一个类中包含一个同步方法；将上面代码的printNum()加上synchronized的：
<pre class="prettyprint">
    public synchronized void printNum()
</pre>
此时，根据执行结果我们发现虽然是多线程，但是一个线程必须等待当前线程执行完printNum()方法后才能执行printNum()方法；感觉是printNum()加了锁，并且只有一把钥匙；那么，当我们三个线程传入不同的Print对象会是什么情况？
<pre class="prettyprint">
    MyThread myThread0 = new MyThread(pt0);
    MyThread myThread1 = new MyThread(pt1);
    MyThread myThread2 = new MyThread(pt2);
</pre>
你会发现，好像又回到了非synchronized的状态；所以**synchronized方法锁的不是方法而是对象**，它锁的是调用方法的对象，在下面的例子中很容易得到证明。

## 多个同步方法 ##

当同一个类中，有多个同步方法时，如果是同一对象的调用，你会发现方法也是按顺序执行的，比如改造后的代码：

public class Demo {
    public static void main(String[] args) {
        Print pt = new Print();
        MyThread myThread = new MyThread(pt);
        MyThread2 myThread2 = new MyThread2(pt);
        Thread th0 = new Thread(myThread, "th0");
        Thread th1 = new Thread(myThread2, "th1");
        Thread th2 = new Thread(myThread, "th2");
        th0.start();
        th1.start();
        th2.start();
    }
}


class Print {
    public synchronized void printNum() {
        for (int i = 0; i < Integer.MAX_VALUE; i++) {
            if ((i % 1000000000) == 0) {
                System.out.println(Thread.currentThread().getName() + "--" + i);
            }
        }
    }

    public synchronized void printAbc() {
        for (int i = 0; i < Integer.MAX_VALUE; i++) {
            if ((i % 1000000000) == 0) {
                System.out.println(Thread.currentThread().getName() + "--" + "ABC");
            }
        }
    }
}


class MyThread implements Runnable {
    private Print pt;

    public MyThread(Print pt) {
        this.pt = pt;
    }

    public void run() {
        pt.printNum();
    }
}

class MyThread2 implements Runnable {
    private Print pt;

    public MyThread2(Print pt) {
        this.pt = pt;
    }

    public void run() {
        pt.printAbc();
    }
}

它的输出结果为：

>th0--0<br>
>th0--1000000000<br>
>th0--2000000000<br>
>th2--0<br>
>th2--1000000000<br>
>th2--2000000000<br>
>th1--ABC<br>
>th1--ABC<br>
>th1--ABC

这也更好的证明锁的是对象。


## 静态同步方法 ##

如果同步方法是静态方法，那就是锁类了，当我们创建不同的对象来调用时，你会发现还是同步执行的。

     Print pt = new Print();
        Print pt2 = new Print();
        MyThread myThread = new MyThread(pt);
        MyThread2 myThread2 = new MyThread2(pt2);
        Thread th0 = new Thread(myThread, "th0");
        Thread th1 = new Thread(myThread2, "th1");
        Thread th2 = new Thread(myThread, "th2");
        th0.start();
        th1.start();
        th2.start();
        


# 同步代码块 #
## synchronized(object) ##
## synchronized(class) ##
## synchronized(this) ##