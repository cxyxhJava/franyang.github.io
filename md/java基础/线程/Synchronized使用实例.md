# Synchronized使用实例

## 前言
synchronized是java内的锁关键字,隐性锁.为了方便记忆我们根据锁的内容类型分为`,`方法锁,`同步代码块`,`类锁`.本文将会提供各自类锁的使用实例.

## 方法锁
`方法锁`其实是锁定`this`(也就是当前对象).下面我们写一个方法锁的代码实例:
```
public class SynchronizedObjectLock implements Runnable {
    static SynchronizedObjectLock lock = new SynchronizedObjectLock();

    @Override
    public void run() {
        method();
    }

    public synchronized void method() {
        System.out.println("我是线程" + Thread.currentThread().getName());
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + "结束");
    }

    public static void main(String[] args) {
        Thread t1 = new Thread(lock);
        Thread t2 = new Thread(lock);
        t1.start();
        t2.start();
    }
}
```
```
   输出结果：
　　我是线程Thread-0
　　Thread-0结束
　　我是线程Thread-1
　　Thread-1结束
```
## 同步代码块
`同步代码块`默认锁定的是this(当前对象),也可以传入其他对象.下面看代码实例:

### 使用默认this锁定
```
public class SynchronizedObjectLock implements Runnable {
    static SynchronizedObjectLock lock = new SynchronizedObjectLock();

    @Override
    public void run() {
        // 同步代码块形式——锁为this,两个线程使用的锁是一样的,线程1必须要等到线程0释放了该锁后，才能执行
        synchronized (this) {
            System.out.println("我是线程" + Thread.currentThread().getName());
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "结束");
        }
    }

    public static void main(String[] args) {
        Thread t1 = new Thread(lock);
        Thread t2 = new Thread(lock);
        t1.start();
        t2.start();
    }
}
```
```
   输出结果：
　　我是线程Thread-0
　　Thread-0结束
　　我是线程Thread-1
　　Thread-1结束
```
### 传入对象锁定
```
public class SynchronizedObjectLock implements Runnable {
    static SynchronizedObjectLock lock = new SynchronizedObjectLock();
    // 创建2把锁
    Object lock1 = new Object();
    Object lock2 = new Object();

    @Override
    public void run() {
        // 这个代码块使用的是第一把锁，当他释放后，后面的代码块由于使用的是第二把锁，因此可以马上执行
        synchronized (lock1) {
            System.out.println("lock1锁,我是线程" + Thread.currentThread().getName());
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("lock1锁,"+Thread.currentThread().getName() + "结束");
        }

        synchronized (lock2) {
            System.out.println("lock2锁,我是线程" + Thread.currentThread().getName());
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("lock2锁,"+Thread.currentThread().getName() + "结束");
        }
    }

    public static void main(String[] args) {
        Thread t1 = new Thread(lock);
        Thread t2 = new Thread(lock);
        t1.start();
        t2.start();
    }
```
```
   输出结果：
　　lock1锁,我是线程Thread-0
　　lock1锁,Thread-0结束
　　lock2锁,我是线程Thread-0　　// 可以看到当第一个线程在执行完第一段同步代码块之后，第二个同步代码块可以马上得到执行，因为他们使用的锁不是同一把
　　lock1锁,我是线程Thread-1
　　lock2锁,Thread-0结束
　　lock1锁,Thread-1结束
　　lock2锁,我是线程Thread-1
　　lock2锁,Thread-1结束
```
## 类锁
+ synchronize修饰`静态方法`,默认使用当前类的Class对象
+ 锁对象为`Class对象`

### 修饰静态方法
```
public class SynchronizedObjectLock implements Runnable {
    static SynchronizedObjectLock lock1 = new SynchronizedObjectLock();
    static SynchronizedObjectLock lock2 = new SynchronizedObjectLock();

    @Override
    public void run() {
        method();
    }

    // synchronized用在静态方法上，默认的锁就是当前所在的Class类，所以无论是哪个线程访问它，需要的锁都只有一把
    public static synchronized void method() {
        System.out.println("我是线程" + Thread.currentThread().getName());
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + "结束");
    }

    public static void main(String[] args) {
        Thread t1 = new Thread(lock1);
        Thread t2 = new Thread(lock2);
        t1.start();
        t2.start();
    }
}
```
```
   输出结果：
　　我是线程Thread-0
　　Thread-0结束
　　我是线程Thread-1
　　Thread-1结束
```
### 锁对象为Class对象
```
public class SynchronizedObjectLock implements Runnable {
    static SynchronizedObjectLock lock1 = new SynchronizedObjectLock();
    static SynchronizedObjectLock lock2 = new SynchronizedObjectLock();

    @Override
    public void run() {
        // 所有线程需要的锁都是同一把
        synchronized(SynchronizedObjectLock.class){
            System.out.println("我是线程" + Thread.currentThread().getName());
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "结束");
        }
    }

    public static void main(String[] args) {
        Thread t1 = new Thread(lock1);
        Thread t2 = new Thread(lock2);
        t1.start();
        t2.start();
    }
}
```
```
输出结果：
我是线程Thread-0
Thread-0结束
我是线程Thread-1
Thread-1结束
```