# JUC并发编程

## 一、概述

### 1.1、什么是JUC

是Java `java.util.concurrent` 包中的类，包含了并发编程中常用的工具类。

### 1.2、进程与线程

进程：指在系统中正在运行的一个应用程序；程序一旦运行就是进程；进程是资源分配的最小单位。

线程：系统分配处理器时间资源的基本单位，或者说进程之内独立执行的一个单元的执行流。线程就是程序执行的最小单位。

### 1.3、线程的状态

#### 1.3. 1、线程状态枚举类

`Tread.State`

1.   NEW 新建
2.   RUNNABLE 准备就绪
3.   BLOCKED 阻塞
4.   WAITING 等待，不见不散
5.   TIMED_WAITING 等待，过时不候
6.   TERMINATED 终结，结束

#### 1.3.2、wait/sleep的区别

1.   `sleep()` 是 `Thread` 类的静态本地方法，而 `wait()` 方法是 `Object` 类的 `final` 本地方法。
1.   `sleep()` 方法用于让当前线程休眠，时间一到继续执行，在任何地方都可以使用。`wait()` 方法只能用在 `synchronized` 块中，需要获取锁，并且捕获 `InterruptedException` 异常。
1.   `sleep()` 一般用于线程休眠，而 `wait()` 一般用于线程间通信，唤醒需要使用 `notify()` 或 `notifyAll()` 方法。这两个方法也需要在 `synchronized` 块中执行，需要获取锁。`wait()` 也可以在调用时传入时间参数，时间一到就会参与到线程锁的竞争当中。
1.   `wait()` 可以释放当前线程所持有的锁，而 `sleep()` 不会。
1.   `sleep()` 会让出 CPU 执行时间且强制上下文切换，而 `wait()` 则不一定，`wait()` 后可能还是有机会重新竞争到锁继续执行的。

### 1.4、并发与并行

#### 1.4.1、串行模式

串行表示所有任务都依次按先后顺序进行。

#### 1.4.2、并行模式

并行：多个任务同时进行。并行的效率从代码层次上依赖于多进程/多线程，从硬件角度上则依赖于多核CPU。

#### 1.4.3、并发

并发（concurrent）指的是多个程序可以“同时运行”的现象，更细化的是多进程可以“同时运行”或者多指令可以“同时运行”。

同时运行并不是真的在同一时刻有多个任务在运行，一个物理CPU在同一时刻只能处理一个进程，并发是将CPU的工作事件分割成不同的事件间隔，在不同的时间间隔处理不同的进程，多个进程不断切换，由于切换的时间过于短暂，以致于看起来像“同时运行”。

#### 1.5、管程

管程（Monitor）就是监视器，Java中叫锁。它是一种同步机制，保证同一时间，只有一个线程能访问被保护的数据或者代码。

JVM同步基于进入和退出，使用管程对象实现。

#### 1.6、用户线程和守护线程

用户线程：自定义的线程，几乎都是用户线程。主线程结束，用户线程未结束，那么JVM也不会停止。

守护线程：例如JVM的垃圾回收线程。当JVM中没有用户线程时，JVM也就停止了。

用户线程可以设置为守护线程。

## 二、Lock接口

### 2.1、复习 `synchronized`

#### 2.1.1、线程同步

当线程同步运行，线程的调度由操作系统决定，程序本身无法决定。当多线程访问一个共享变量时，例如将一个数加一10000次，再减一10000次，最终结果每次都不一样，也不为0。因为加减一个数在操作系统中被分为三步：加载，计算，存储。当两个线程对同一个数字进行加一，结果很可能不是加二，而只加一。所以必须要让每次对数字的加减整个过程设置为原子操作，其中一个线程操作时，其他线程不允许操作，就是给这个操作上锁，操作结束解锁。

#### 2.1.2、synchronized

关键字是Java内部实现的自动加锁和结果，不需要用户直接操作。这个关键字可以修饰代码块、方法和类。

1.   修饰代码块需要一个对象作为锁，多个线程必须拿到这一个锁才能执行代码块里的操作。

```java
// 所有拥有这个代码块的对象都会在执行这部分操作时上锁
synchronized (this) { // 进入上锁
    ...
} // 离开解锁
```

2.   修饰方法

```java
// 所有该类的实例调用该方法都会上锁，如果是静态方法，调用方法的类也会上锁
public synchronized void sale() {
    
}
```

3.   修饰类

```java
// 所有的该类的对象都会被上锁
synchronized class Ticket {
    
}
```

### 2.2、Lock接口

`java.util.concurrent.locks`

为锁和等待条件提供一个框架的接口和类，它不同于内置同步和监视器。Lock实现提供了比使用synchronized方法和语句可获得的更广泛的锁定操作。

Lock有很多实现类，ReentrantLock就是其中一种，叫作可重入锁。

**可重入锁**：指一个线程可以多次获取同一把锁。例如一个线程在执行一个带锁的方法，该方法中有调用了另一个需要相同锁的方法（即可重入）而无需重新获得锁。

ReentrantLock和synchronized都是可重入锁。

使用ReentrantLock：

```java
class Ticket {
    // 首先声明一把锁
    private final ReentrantLock lock = new ReentrantLock();
    
    public void sale() {
        // 上锁
        lock.lock();
        try {
           	// 业务代码
        } finally {
            // 解锁，为了防止异常，将解锁放到finally中
            lock.unlock();
        }
    }
}
```

### 2.3、创建线程的多种方式

#### 2.3.1、继承Tread类

1.   定义一个类，它要继承Thread类，然后重写Thread中的run方法。

2.   然后创建这个类的实例对象，

3.   调用该对象的start方法启动该线程。

#### 2.3.2、实现Runnable接口

1.   定义Runnable接口的实现类，并重写该接口的run方法
2.   创建Runnable实现类的实例，并以此实例所谓Thread的构造函数参数创建Thread对象。
3.   调用创建的线程对象的start方法启动该线程。

```java
class Ticket implements Runnable {
    private int number;
    public void run() {
        ...
    }
    public static void main() {
        Ticket ticket = new Ticket();
        new Thread(ticket, "sale").start();
    }
}
```

一般是直接在new Thread的时候，传入一个箭头函数，它的前身是new Runnable对象，对象中重写run方法，因为Runnable是函数式接口，可以写成箭头函数的形式。

```java
public static void main() {
    new Thread(() -> {
        // 具体的操作，和run方法中的一样
    }, "sale").start();
}
```

缺点：没有返回值，也无法抛出异常。

#### 2.3.3、通过Callable和Future创建线程

上面两种方式创建的线程没有返回值，需要使用共享变量或者其他通信方式才能知道线程执行的结果。

##### 1、Callable

Callable接口的定义：

```java
public interface Callable<V> {
   V call() throws Exception;
}
```

Callable 接口的简单使用：类似Runable接口，直接在 `call()` 函数中写代码即可。返回值就时 `call()` 函数的返回值。

```java
// 创建多线程业务代码
Callable<String> callable =
	new Callable<String>() {
    	@Override
    	public String call() throws Exception {
        	return "2333";
    	}
	};

// 声明一个线程池
ExecutorService executor = Executors.newFixedThreadPool(1);
// 将任务提交，得到返回的结果
Future<String> result = executor.submit(callable);
System.out.println(result.get());
```

它不能直接配合Thread使用，需要使用 `ThreadPoolExecutor` 使用。

##### 2、Future

Future也是一个接口，它可以对 `Runnable` 或者 `Callable` 任务执行取消操作，也可以查看任务是否取消，是否完成，返回的结果。如果任务是 `Runnable` ，则返回值是null。

Future接口定义：

```java
public interface Future<V> {
    boolean cancel(boolean mayInterruptIfRunning);//取消任务
    boolean isCancelled();//判断任务是否已取消
    boolean isDone();//判断任务是否已结束
    
    V get() throws InterruptedException, ExecutionException;//获得任务执行结果
    //获得任务执行结果，支持超时
    V get(long timeout, TimeUnit unit) throws InterruptedException, 
    			ExecutionException, TimeoutException;
}
```

Future接口应用：

```java
Callable<String> callable =
	new Callable<String>() {
    	@Override
    	public String call() throws Exception {
        	return "2333";
    	}
	};
	
ExecutorService executor = Executors.newFixedThreadPool(1);
Future<String> result = executor.submit(callable);
result.cancel(true);
System.out.println(result.isCancelled());
```

#### 2.3.4、FutureTask

FutureTask是一个类，它实现了RunnablFuture接口，而RunnabFuture又继承了Runna接口和Future接口。

下面是两个构造方法的定义：

```java
FutureTask(Callable<V> callable);
FutureTask(Runnable runnable, V result);
```

FutureTask的简单应用：

```java
FutureTask<String> futureTask = new FutureTask<>(() -> "2333"); // 创建多线程任务
Thread T1 = new Thread(futureTask); // 创建线程
T1.start(); // 开启线程
String result = futureTask.get(); // 获取结果
System.out.println(result);
```

Future 之所以能够获得 Callable 和 Runnable 的返回值，是因为它在执行多线程任务时，将Runnable任务外面套了一层Callable。

### 2.4、Lock和synchronized的区别

1.   `Lock` 是一个接口，`synchronized` 是一个关键字，依靠内置语言实现。

2.   `synchronized` 会在异常时自动释放锁，`Lock` 不会，所以要使用 `finally` 避免发生死锁。
3.   Lock可以让等待锁的线程响应中断，synchronized不行，其他线程会一直等下去，效率低。
4.   通过Lock可以直到有没有成功获取锁，而synchronized不可以。
5.   Lock可以提高多个线程进行操作的效率。

## 三、线程间通信

#### 3.1、wait/notifyAll

wait和notifyAll是Object类中的方法，所有对象都可以执行的。

wait是让当前线程进入等待状态，代码暂停到这一行，直到被其他线程唤醒，继续执行。执行wait会让当前线程释放锁，等待状态结束后，线程会尝试再次获取锁。

notifyAll唤醒所有其他线程，然后开始抢夺锁。

### 3.2、虚假唤醒

这是wait的特点导致的，wait的特点是在哪睡，就在哪里醒。意思就是当wait结束时，会继续执行未完成的代码，不管前面是否有判断条件，这个时候它也许是没有锁的，所以会导致虚假唤醒的情况。

解决的办法是在判断条件中使用while循环，当wait状态解除时，会再次进入判断条件，如果不满足会再次进行入等待状态。

### 3.3、使用多线程的步骤

第一步：创建资源类，定义资源类的属性和方法

第二步：在资源类需要进行多线程的操作方法中有一下三部分：

​		（1）判断：是否执行满足条件

​		（2）干活：执行业务代码

​		（3）通知：通知其他线程解除等待状态，重新争夺锁。

第三步：创建多线程，调用资源类的操作方法。

第四步：防止虚假唤醒问题

### 3.4、await/signalAll

这是Condition接口中的方法，作用和上面的wait和notifyAll相同。Condition是和Lock在同一个包中 `java.util.concurrent.locks` 。

Lock接口通过 `newCondition` 方法创建一个 Condition 对象并返回。用这个对象可以将当前的线程进入等待状态或者唤醒其他线程。

### 3.5、线程间通信实现

#### 3.5.1、synchronized实现

```java
package top.zhuaowei.sync;

/**
 * @ClassName: ThreadCommunicate
 * @Description: TODO add a description
 * @Author: zhuaowei
 * @Date: 2022/5/22
 * @Version: 1.0
 */
class Share {
    private int number = 0;

    public synchronized void increase() throws InterruptedException {
        while (number != 0) {
            this.wait();
        }
        number++;
        System.out.println(Thread.currentThread().getName() + " -> " + number);
        this.notifyAll();
    }

    public synchronized void decrease() throws InterruptedException {
        while (number != 1) {
            this.wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName() + " -> " + number);
        this.notifyAll();
    }
}

public class ThreadCommunicate {
    public static void main(String[] args) {
        Share share = new Share();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.increase();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "Thread1").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.decrease();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "Thread2").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.increase();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "Thread3").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.decrease();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "Thread4").start();
    }
}

```

#### 3.5.2、ReentrantLock实现

```java
package top.zhuaowei.lock;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

/**
 * @ClassName: ThreadCommunicate
 * @Description: TODO add a description
 * @Author: zhuaowei
 * @Date: 2022/5/22
 * @Version: 1.0
 */

class Share {
    private int number = 0;
    private final ReentrantLock lock = new ReentrantLock();
    private final Condition condition = lock.newCondition();

    public void increase() throws InterruptedException {
        lock.lock();
        try {
            while (number != 0) {
                condition.await();
            }
            number++;
            System.out.println(Thread.currentThread().getName() + " -> " + number);
            condition.signalAll();
        } finally {
            lock.unlock();
        }
    }

    public void decrease() throws InterruptedException {
        lock.lock();
        try {
            while(number != 1) {
                condition.await();
            }
            number--;
            System.out.println(Thread.currentThread().getName() + " -> " + number);
            condition.signalAll();
        } finally {
            lock.unlock();
        }
    }
}

public class ThreadCommunicate {

    public static void main(String[] args) {
        Share share = new Share();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.increase();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "Thread1").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.decrease();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "Thread2").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.increase();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "Thread3").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    share.decrease();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }, "Thread4").start();
    }
}

```

## 四、线程间定制化通信

线程间定制化通信，使用一个变量作为信号通知其他线程，如果不是该线程的信号，则等待。

```java
class ShareResource {
    int signal = 1; // { 1: A, 2: B, 3: C }
    ReentrantLock lock = new ReentrantLock(); // 锁
    Condition c1 = lock.newCondition(); // 3个 condition 用于等待操作
    Condition c2 = lock.newCondition();
    Condition c3 = lock.newCondition();
    
    // loop: 第几轮
    public void print5(int loop) throw InterruptedException {
        // 先上锁
        lock.lock();
        try {
            while (this.signal != 1) {
                c1.await(); // 如果信号不是 1，继续等待
            }
            for (int i = 0; i < 5; i++) {
                System.out.println(Thread.currentThread().getName() + "::" + i + "::" + loop);
            }
            this.signal = 2; // 修改标识位
            c2.signal(); // 通知B线程
        } final {
            lock.unlock();
        }
    }
    
    // 同样方法写出 B C 线程的方法。
}
```

## 五、集合的线程安全

#### 5.1、List

##### 5.1.1、ArrayList的不安全

Java的ArrayList是线程不安全的，它的add方法并没有Synchronized关键字。在执行多线程操作时，可能会发生`java.util.ConcurrentModificaitonException`。

```java
List<String> list = new ArrayList<>();
for (int i = 0; i < 100; i++) {
    new Thread(() -> {
        list.add(UUID.RandomUUID.toString().substring(0, 8));
        System.out.println(list);
    }, String.valueOf(i)).start();
}
```

如何让集合变得有安全性，有以下3中方案。

##### 5.1.2、Vector

直接把ArrayList替换位Vector就可以实现线程安全，不过这个方法太古老了，java1.0时代提出的解决方案。

```java
List<String> list = new Vector<>(); // 只修改了这一行
for (int i = 0; i < 100; i++) {
    new Thread(() -> {
        list.add(UUID.RandomUUID.toString().substring(0, 8));
        System.out.println(list);
    }, String.valueOf(i)).start();
}
```

##### 5.1.3、Collections

这个方法也比较古老，是Java1.2出现的类。

```java
List<String> list = Collections.synchronizedList(new ArrayList()); // 只修改了这一行
```

用一个具体的List返回一个线程安全的List。

##### 5.1.4、CopyOnWriteArrayList

```java
List<String> list = new CopyOnWriteArrayList(); // 只修改了这一行
```

CopyOnWriteArrayList 是 java.util.concurrent 包中的一个类，它实现了结合的线程安全操作，其中最重要的技术就是写时复制技术。

写时复制技术：当有多个线程往数组中写入时，只有一个线程可以往里面写，也就是它的add方法是上锁了的，写时创建一个比原数组长度大一的数组，将原数组复制上去，然后再添加新的元素，最终将新数组合并过去。读的时候是允许多线程同时读的。

#### 5.2、Set

HashSet和HashMap和ArrayList同样也有线程不安全问题，他们的add和put方法都没有加synchronized关键字。

HashSet的解决方案是使用`java.util.concurrent` 包中的 `CopyOnWriteArraySet` 类。

```java
Set<String> set = new CopyOnWriteArraySet<>();
```

#### 5.3、Map

`HashMap` 的解决方案是使用 `java.util.concurrent` 包中的 `ConcurrentHashMap` 类。

```java
Map<String, String> map = new ConcurrentHashMap<>();
```

## 六、多线程锁

#### 6.1、Synchronized 锁的范围

Synchronized:

对于普通方法，锁住的当前的实例对象

对于静态方法，锁住的是该类的Class对象

对于同步方法块，锁的是块里配置的对象。

#### 6.2、非公平锁和公平锁

##### 6.2.1、非公平锁

特点：有部分线程执行次数很多，而其他线程执行次数很少，也就是拿到的CPU的次数少。

优点：无需排队等待，效率高

缺点：会造成线程饿死情况，即有的线程没有机会执行。

##### 6.2.2、公平锁

特点：所有线程抢到CPU的几率是差不多的。

优点：所有线程都有机会执行。

缺点：效率相对较低，因为去抢占之前需要先询问是否有排队，如果有还需要再等等。

##### 6.2.3、ReentrantLock

ReentrantLock （可重入锁）默认就是非公平锁，在构造时传入参数可以选择创建哪一种锁。

```java
ReentrantLock lock = new ReentrantLock(); // 非公平锁
ReentrantLock lock = new ReentrantLock(false); // 非公平锁
ReentrantLock lock = new ReentrantLock(true); // 公平锁
```

#### 6.3、可重入锁

ReentrantLock 指的是当一个需要锁的方法中调用了其他需要锁的方法，不需要再次获得锁，可以直接进入。所以这个锁也叫递归锁。

synchronized是隐式锁，也是可重入锁，因为它的上锁解锁操作是自动完成的。

可重入锁即使再嵌套情况下也要做到上锁解锁成对出现，否则该线程无法将锁释放，这时候其他线程想获得锁也不行了。

#### 6.4、死锁

死锁：当两个或两个以上的进程，因抢夺资源而发生的相互等待的情况。

死锁发生的原因：

1.   系统资源不足
2.   系统资源分配不当
3.   程序执行顺序不当。

死锁的实例：

```java
Object a = new Object();
Object b = new Object();

new Thread(() -> {
    synchronized (a) {
        System.out.println(Thread.currentThread().getName() + " 持有锁a，试图获取锁b。");
        TimeUnit.SECONDS.sleep(1);
        synchronized (b) {
            System.out.println(Thread.currentThread().getName() + " 获取了锁b。");
        }
    }
}, "A").start();

new Thread(() -> {
    synchronized (b) {
        System.out.println(Thread.currentThread().getName() + " 持有锁b，试图获取锁a。");
        TimeUnit.SECONDS.sleep(1);
        synchronized (a) {
            System.out.println(Thread.currentThread().getName() + " 获取了锁a。");
        }
    }
}, "B").start();
```

检测死锁工具：

1.   jps：jps 可以查看到死锁的进程和进程ID
2.   jstack：jstack 可以根据进程ID跟踪死锁的具体情况。

```shell
# 列出java进程
jps -l

# 查看该进程的详细信息。
jstack <PID>
```

## 七、Callable接口

#### 7.1、Callable/Runnable

|                | Callable | Runnable |
| -------------- | -------- | -------- |
| 是否有返回值   | 有       | 无       |
| 是否能抛出异常 | 有       | 无       |
| 方法名         | call     | run      |

#### 7.2、Callable

Callable接口是JDK1.5新添加的，它不能直接用Thread创建任务，需要一个中间人，它就是FutureTask。

```java
class MyThread1 extends Callable {
    @Override
    public Integer call() {
        return 100;
    }
}
FutureTask<Integer> task1 = new FutureTask<>(() -> {
    @Override
    public Integer call() throw NullPointerException {
        return 100;
    }
}); // 也可以通过自定义类继承该接口，然后创建该类的实例来创建任务。
new Thread(task1, "A").start(); // FutureTask提交任务到Thread。
task.get(); // 得到返回的结果。
```

因为FutureTask是函数式接口，所以它可以使用lambda表达式简化代码。

FutureTask（未来任务）相当于将任务单独开一个线程，自己的线程任务不受影响，等所有任务都完成后，将任务汇总，而且所有任务都只执行一次。

## 八、JUC辅助类

### 8.1、CountDownLatch

CountDownLatch（倒计时锁存器，减少计）实现了让一个或多个线程等待，直到其他线程完成一系列操作。

初始化 CountDownLatch 需要给定一个整数，作为计数。wait() 方法会阻塞当前线程，直到其他线程完成操作，**使count变为0**。

例子：当班里的同学走完后，班长才能锁门。

```java
CountDownLatch cd = new CountDownLatch(6);

for (int i = 1; i <= 6; i++) {
    new Thread(() -> {
        System.out.println(Thread.currentThread().getName() + "号同学已离开。");
        cd.countDown();
    }, String.valueOf(i)).start();
}
cd.await();
System.out.println(Thread.currentThread().getName() + "::班长锁门了！");
```

### 8.2、CyclicBarrier

CyclicBarrier（循环栅栏，也叫循环屏障）它会让一系列线程全部等待彼此，达到一个共同的屏障点。CyclicBarrier非常适合固定大小，需要相互等待的线程组。

例子：集齐七颗龙珠，召唤神龙。

```java
private static final int NUMBER = 7;
public static void main(String[] args) {
    CyclicBarrier cb = new CyclicBarrier(NUMBER, () -> {
        System.out.println("集齐了 7  颗龙珠，成功召唤神龙！");
    });

    for (int i = 1; i <= 7; i++) {
        new Thread(() -> {
            try {
                System.out.println(Thread.currentThread().getName() + "星龙珠已收集！");
                cb.await();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, String.valueOf(i)).start();
    }

}
```

### 8.3、Semaphore

Semaphore（信号等，也叫计数信号量）它维护了一个许可的数量。

如果没有许可，`acquire()` 会阻塞当前线程。`release()` 会添加一个许可，潜在地，它释放了一个阻塞的线程。

最终，许可的数量并没有被消耗，Semaphore只是维护了一个许可的数量并执行相应的行为。

例子：6辆车，3个停车位。

```java
// 3 个停车位
Semaphore s = new Semaphore(3);
for (int i = 1; i <= 6; i++) {
    new Thread(() -> {
        try {
            s.acquire(); // 抢占一个许可，也即一个停车位
            System.out.println(Thread.currentThread().getName() + "号车抢到了车位！");
            // 随机停车时间， 0 - 5s
            TimeUnit.SECONDS.sleep(new Random().nextInt(5));
            System.out.println(Thread.currentThread().getName() + "号车离开了车位！");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            // 释放许可，隐式地释放了一个线程
            s.release();
        }
    }, String.valueOf(i)).start();
}
```

## 九、ReentrantReadWriteLock

### 9.1、各种锁的概念

1、悲观锁：每个线程对数据操作都要先上锁，操作完成后再释放锁。

2、乐观锁：每个线程拿到数据不要上锁，对数据进行操作后得到一个数据版本，各线程对比版本，落后的版本提交不成功。

3、表锁：对表操作时，对整个表进行上锁。

4、行锁：对表操作时，只对操作的行上锁。

5、读锁：共享锁，可以同时允许多个线程读取数据。

6、写锁：每次只允许一个线程写数据。

7、共享锁：读锁就是一种共享锁，它允许同时对读进行操作。

8、独占锁：写锁就是一种独占锁，它只允许一个线程进行写操作。

### 9.2、ReentrantReadWriteLock

`ReentrantReadWriteLock`（读写锁）是包 `java.util.concurrent.locks` 中的，它实现了ReadWriteLock接口和ReentrantLock的功能，它也是有两种模式，默认是非公平锁，也可以设置为公平锁。

这个锁也是可重入的。

关于降级，可重入性允许写锁降级为读锁，但是读锁不可以升级为写锁。

它有两个静态的嵌套类，分别是 `ReadLock/WriteLock` 读锁和写锁，调用 `readLock()/writeLock()` 来获取这两个类的实例对象。这两个锁都是单例模式，每次获取的锁都是同一个锁。

实例：

```java
class MyCache {
    private volatile Map<String, String> map = new HashMap<>();
    private ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();


    public void put(String key, String value) {
        rwLock.writeLock().lock();

        try {
            System.out.println(Thread.currentThread().getName() + " is writing..." + key);
            TimeUnit.MICROSECONDS.sleep(500);
            map.put(key, value);
            System.out.println(Thread.currentThread().getName() + " has written." + key);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            rwLock.writeLock().unlock();
        }
    }

    public String get(String key) {
        rwLock.readLock().lock();
        String result = null;
        try {
            System.out.println(Thread.currentThread().getName() + " is reading..." + key);
            TimeUnit.MICROSECONDS.sleep(500);
            result = map.get(key);
            System.out.println(Thread.currentThread().getName() + " has read..." + key);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            rwLock.readLock().unlock();
        }
        return result;
    }

}

public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache mc = new MyCache();
        for (int i = 1; i <= 5; i++) {
            final int num = i;
            new Thread(() -> {
                mc.put(String.valueOf(num), String.valueOf(num));
            }, String.valueOf(i)).start();
        }
        for (int i = 1; i <= 5; i++) {
            final int num = i;
            new Thread(() -> {
                mc.get(String.valueOf(num));
            }, String.valueOf(i)).start();
        }
    }
}
```

结果可以看到：写操作是一个一个执行的，读操作是同时进行的。

>   volatile 关键字: 他是一个轻量级的同步关键字，它只保证线程的可见性，但不保证线程操作的原子性。
>
>   它会在编译时插入内存屏障，在指令重排时，屏障前的指令不会排到屏障后，同样后面的也不能排到前面。

### 9.3、写锁的降级

写锁可以降级为读锁，但是读锁不能降级为写锁。意思就是，当你有写锁的时候，你可以再获取读锁；但是当你有读锁的时候 ，是不可以获取写锁的。

写锁降级实例：

```java
ReentrantReadWriteLock rwlock = new ReentrantReadWriteLock();

// 1、获取写锁
rwLock.writeLock().lock();

// 2、获取读锁
rwLock.readLock().lock();

// 3、释放读锁
rwLock.readLock().unlock();

// 4、释放写锁
rwLock.writeLock().lock();

```

这就是最简单的写锁降级的实例，如果反过来，程序就会因为获取了读锁，而获取不到写锁，最终死锁。

## 十、阻塞队列（BlockingQueue）

### 10.1、概念

队列需要有这些功能，添加到队列和从队列中取出，顺序是先进先出。队列是线程共享的变量，所有的线程都可以往里面添加或者取出一些东西。当添加时，队列中必须有剩余的空间可添加；取出时，队列中必须有可取的东西，否则都会这些线程都会被阻塞。队列的操作包括添加、取出、检索三种操作，而且有四种超越边界操作的处理形式。一是抛出异常、二是返回特定值、三是阻塞直到完成、四是在时限内完成，否则就失败。

超越边界：是指当队列满时添加，当队列空时取出。

|      | throws exception | special value | blocks | times out            |
| ---- | ---------------- | ------------- | ------ | -------------------- |
| 插入 | add(e)           | offer(e)      | put(e) | offer(e, time, unit) |
| 移出 | remove()         | poll()        | take() | poll(time, unit)     |
| 检测 | element()        | peek()        | 未定义 | 未定义               |

1、抛出异常：越界时抛出异常

2、特殊值：越界时，满时返回false，空时返回null

3、阻塞：越界时，会一直阻塞当前线程，直到队列的空间满足操作的需要。

4、超时退出：在一定时限内阻塞，超过时限还没有完成就退出线程。`queue.poll(3L, TimeUnit.SECOENDS);`

### 10.2、分类

下面这些都是接口 BlockingQueue 的实现类。

ArrayBlockingQueue（常用）：数组阻塞队列，是定长的，创建时指定。

DelayQueue：延时队列。使用优先级队列实现的延迟无界阻塞队列。

LinkedBlockingDeque：链表双向阻塞队列。

LinkedBlockingQueue（常用）：链表阻塞队列。也是定长的，默认大小是 Integer.MAX_VALUE。

LinkedTransferQueue：链表无界阻塞队列。

PriorityBlockingQueue：优先级阻塞队列。支持优先级排序的队列

SynchronousQueue：同步队列。长度为1的阻塞队列。

## 十一、线程池

### 11.1、概念

ThreadPool（线程池）：一种线程使用模式。线程过多会带来调度开销，进而影响缓存局部性和整体性能。而线程池维护着多个线程，等待着监督管理者分配可并发执行的任务。这避免了在处理短时间任务时创建与销毁线程的代价。线程池不仅能够保证内核的充分利用，还能防止过分调度。

线程池的优点：

1、降低资源消耗，通过重复利用已创建的线程，降低线程创建和销毁造成的消耗。

2、提高响应速度，当任务到达时，任务可以不需要等待线程创建完成就能立即执行。

3、提高线程的可管理性：使用线程池可以进行统一的分配，调优和监控。

### 11.2、架构

Java中的线程池是使用 Execute 框架实现的。它有一个子接口 `ExecuteService` ，子接口 `ExecuteService` 有一个实现类和一个子接口，分别是 `AbstractExecutorService` 类 和  `scheduledExecutorService` 接口。

`AbstractExecutorService` 有一个 `ThreadPoolExecutor` 子类。而 `ScheduledThreadPoolExecutor` 继承了 `ThreadPoolExecutor` 类和 `scheduledExecutorService` 接口。

`Executors` 是一个线程创建的工具类。

```
                                            [I] Executor
                                                 ^
Executors                                      |
                                           [I] ExecutorService
                                            ^                ^
                          ---------------|                |---------------
                         |                                                      |
              [C] AbstractExecutorService                    [I] scheduledExecutorService
                         ^                                                      ^
                         |                                                      |
         --->[C] ThreadPoolExecutor                                        |
         |              |            ^                                         |
         |------------|            |------------            ------------|
                                                      |          |
                                         [C] ScheduledThreadPoolExecutor
```

### 11.3、使用方式

三种线程创建方式：

```java
// 1、创建固定大小的线程池
ExecutorService threadPool1 = Executors.newFixedThreadPool(5);
// 2、创建单线程大小的线程池
ExecutorService threadPool2 = Executors.newSingleThreadExecutor();
// 3、创建缓存线程池，线程大小随任务多少而变
ExecutorService threadPool3 = Executors.newCachedThreadPool();
```

将任务交给线程池执行：

```java
threadPool1.execute(() -> {
    System.out.println(Thread.currentThread().getName() + "正在处理业务...");
});
```

`execute()` 方法需要传入一个 `Runnable` 对象，可以使用 lambda 表达式。

### 11.4、底层原理

无论哪一种创建线程的方式，底层调用的构造器都是 `ThreadPoolExecutor` 。这个构造器一共需要传入7个参数：

| 参数            | 类型                     | 作用                 |
| --------------- | ------------------------ | -------------------- |
| corePoolSize    | int                      | 常驻线程数量（核心） |
| maximumPoolSize | int                      | 最大线程数量         |
| keepAliveTime   | long                     | 存活时间             |
| unit            | TimeUnit                 | 存活时间单位         |
| workQueue       | BlockingQueue<Runnable>  | 任务队列             |
| threadFactory   | ThreadFactory            | 线程工厂             |
| handler         | RejectedExecutionHandler | 拒绝策略             |

keepAliveTime：如果一定时间内都没有调用该线程，则线程销毁。

### 11.5、工作流程

1、创建线程池时并没有创建线程，而是当任务提交到线程池时才开始创建，即是 `execute()` 执行时才会创建。

2、首先创建核心线程，将任务分配给核心线程去做。（核心线程数取决于 `corePoolSize`）

3、如果再有任务，会将任务添加到任务队列中。

4、如果任务队列也满了，就会将新提交的任务交给最大线程池的其余部分去做。（任务队列中的任务不会动）

5、如果核心线程满了，任务队列业务满了，最大线程数量也满了，就会启动拒绝策略。

### 11.6、拒绝策略

一共有 4 中拒绝策略。

1、AbortPolicy：不接受任务，直接抛出异常 `RejectedExecutionException`

2、CallerrunsPolicy：调运者模式：将某些任务退到调用者。

3、DiscardOldestPolicy：放弃任务队列中等待时长最长的任务，然后将新任务加进去。

4、DiscardPolicy：直接把任务抛弃，如果允许抛弃任务，这是最佳选择。

### 11.7、自定义线程

根据《阿里巴巴Java开发手册》，不允许使用 `Executors` 创建线程池，要使用自定义的线程创建方式。

>   说明：`Executors` 返回的线程池对象的弊端如下：
>
>   1）`FixedThreadPool` 和 `SingleThreadPool`：
>
>   ​	允许的请求队列长度为 `Integer.MAX_VALUE`，可能会堆积大量请求，从而导致OOM。
>
>   2）`CachedThreadPool` 和 `ScheduledThreadPool`：
>
>   ​	允许的创建线程数量为 `Integer.MAX_VALUE`，可能会创建大量的线程，从而导致OOM。
>
>   OOM（outOfMemory）：内存溢出异常

```java
// 使用这 7 个参数创建自定义线程
ThreadPoolExecutor threadPool = new ThreadPoolExecutor(
        2,
        5,
        2L,
        TimeUnit.SECONDS,
        new ArrayBlockingQueue<>(5),
        new ThreadPoolExecutor.AbortPolicy()
);
try {
    for (int i = 1; i <= 10; i++) {
        threadPool.execute(() -> {
            System.out.println(Thread.currentThread().getName() + "正在处理业务...");
        });
    }
} catch (Exception e) {
    e.printStackTrace();
} finally {
    threadPool.shutdown();
}
```

## 十二、Fork/Join

### 12.1、ForkJoinTask

ForkJoinTask 是基础抽象类，它需要运行在 `ForkJoinPool`  中。`ForkJoinTask` 是一种类线程的较轻量级的实体。大量的计算任务可以托管到 `ForkJoinPool` 中一个个线程中。

主任务被提交到 `ForkJoinPool` 中开始运行，它通常会启动其他的子任务。这些任务使用 `fork()` 和 `join()` 对任务进行拆分、合并。

### 12.2、实例

例子：使用 `Fork/Join` 的方式计算 `1 + 2 + 3 + ... + 100`

```java
// 继承 RecursiveTask 接口，实现 compute 方法
class MyTask extends RecursiveTask<Integer> {
    /**
     * 计算的粒度，每个任务，开始和结束的最大差值
     */
    private static final Integer GRANULARITY = 10;
    // 每个计算任务的开始和结束
    private int begin;
    private int end;
    // 每个任务的计算结果
    private int result;

    /**
     * 有参构造
     */
    public MyTask(int begin, int end) {
        this.begin = begin;
        this.end = end;
    }

    @Override
    protected Integer compute() {
        // 停止条件
        if ((end - begin) <= GRANULARITY) {
            // 如果粒度合适，直接执行
            for (int i = begin; i <= end; i++) {
                result += i;
            }
        } else {
            // 如果粒度太大，再次拆分
            int middle = (begin + end) / 2;
            MyTask task1 = new MyTask(begin, middle);
            MyTask task2 = new MyTask(middle + 1, end);

            // fork 任务
            task1.fork();
            task2.fork();
            // join 任务
            result = task1.join() + task2.join();
        }
        return result;
    }
}

public class ForkJoinDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ForkJoinPool pool = new ForkJoinPool();
        MyTask task = new MyTask(0, 100);
        pool.submit(task);
        System.out.println(task.get());

    }
}
```

## 十三、CompletableFuture（异步调度）

### 13.1、CompletableFuture

异步调度是主任务需要某个线程任务的结果时，不需要等待这个线程完成，而是使用异步调度的方式，等这个任务完成后交给主任务，这期间，主任务可以干自己的事。

实例：

```java
// 1、没有返回值的异步调度
CompletableFuture completableFuture1 = CompletableFuture.runAsync(() -> {
    System.out.println(Thread.currentThread().getName() + "completableFuture1");
});
completableFuture1.get();
// 2、有返回值的异步调度
CompletableFuture completableFuture2 = CompletableFuture.supplyAsync(() -> {
    System.out.println(Thread.currentThread().getName() + "completableFuture2");
    return 1024;
});
completableFuture2.whenComplete((t, u) -> {
    System.out.println("t = " + t); // t: 异步调度的返回值
    System.out.println("u = " + u); // u: 异步调度的异常信息
});
```

