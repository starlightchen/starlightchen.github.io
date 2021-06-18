---
title: Java中的synchronized修饰符
date: 2020-10-23 23:46 +0800
categories: [Java]
tags: [Java基础]
---
# 基础

**Synchronized的作用主要有三个：**

1. 确保线程互斥的访问同步代码
2. 保证共享变量的修改能够及时可见
3. 有效解决重排序问题

## 用法

### 修饰普通方法

锁的是**实例对象**，进入同步方法前要获得实例对象的锁。同步范围为synchronized修饰的方法，其他线程可以同时访问该实例对象的非synchronized修饰方法

{% raw %}
```java
public class Test{
    public synchronized void method_f(){
        //do some thing;
    }
    public synchronized void method_s(){
        //do some thing;
    }
    public synchronized void method_t(){
        //do some thing;
    }

    public static void main(String[] args){
        final Test stest = new Test();

        new Thread(new Runnable(){
            @Override
            public void run(){
                stest.method_f();
            }
        }).start();

        new Thread(new Runnable(){
            @Override
            public void run(){
                stest.method_s();
            }
        }).start();
    }
}
```
{% endraw %}

程序将先由线程1执行完method_f()，之后线程2在执行method_s()

<br/>

如果修改main函数为：

{% raw %}
```java
public static void main(String[] args){
    final Test stest = new Test();
    final Test mtest = new Test();

        new Thread(new Runnable(){
            @Override
            public void run(){
                stest.method_f();
            }
        }).start();

        new Thread(new Runnable(){
            @Override
            public void run(){
                mtest.method_s();
            }
        }).start();
    }
}
```
{% endraw %}

两个线程将会同时执行各自的方法




<br/>

### 修饰静态方法

锁是class对象锁，进入同步方法前，需要获取当前类的class对象锁。

**静态成员不属于任何对象**，是类成员，因此通过class对象锁可以控制静态 成员的并发操作。

**注：** 如果一个线程A调用一个实例对象的非static synchronized方法，而线程B需要调用这个实例对象所属类的静态 synchronized方法，是允许的，不会发生互斥现象。因为访问静态 synchronized 方法占用的锁是当前类的class对象，而访问非静态 synchronized 方法占用的锁是当前实例对象锁.

<br/>

### 修饰代码块

**锁是括号里的对象**，对给定对象加锁，进入同步代码库前要获得给定对象的锁。

在某些情况下，我们编写的方法体可能比较大，同时存在一些比较耗时的操作，而需要同步的代码又只有一小部分，如果直接对整个方法进行同步操作，可能会得不偿失，此时我们可以使用同步代码块的方式对需要同步的代码进行包裹，这样就无需对整个方法进行同步操作了。

{% raw %}
```java
 public class synchronizedTest implements Runnable {
    static synchronizedTest instance=new synchronizedTest();
    static int i=0;
    @Override
    public void run() {
        //省略其他耗时操作....
        //使用同步代码块对变量i进行同步操作,锁对象为instance
        synchronized(instance){
            for(int j=0;j<10000;j++){
                i++;
            }
        }
    }
    public static void main(String[] args) throws InterruptedException {
        Thread t1=new Thread(instance);
        Thread t2=new Thread(instance);
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i);
    }
}

```
{% endraw %}

<br/><br/><br/>