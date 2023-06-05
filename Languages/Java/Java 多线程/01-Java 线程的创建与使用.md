
## 1.1 Thread类和Runnable接口

首先，我们需要有一个“线程”类。JDK提供了`Thread`类和`Runnalble`接口来让我们实现自己的“线程”类。

- 继承`Thread`类，并重写`run`方法。
- 实现`Runnable`接口的`run`方法，将 `Runnable` 对象传递给 `Thread` 构造函数。

### 1.1.1 继承Thread类

首先是继承`Thread`类：

```java
public class Demo {
    public static class MyThread extends Thread {
        @Override
        public void run() {
            System.out.println("MyThread");
        }
    }

    public static void main(String[] args) {
        Thread myThread = new MyThread();
        myThread.start();
    }
}
```

注意要调用`start()`方法后，该线程才算启动！

>我们在程序里面调用了start()方法后，**虚拟机会先为我们创建一个线程**，然后等到这个线程第一次**得到时间片时再调用run()方法**。
>注意不可多次调用start()方法。在第一次调用start()方法后，再次调用start()方法会抛出异常。

### 1.1.2 实现Runnable接口

接着我们来看一下`Runnable`接口(JDK 1.8 +)：

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

可以看到`Runnable`是一个函数式接口，这意味着我们可以使用**Java 8的函数式编程**来简化代码。

```java
public class Demo {
    public static class MyThread implements Runnable {
        @Override
        public void run() {
            System.out.println("MyThread");
        }
    }

    public static void main(String[] args) {
        new MyThread().start();// 创建了一个MyThread的实例并调用了start()方法

        // Java 8 函数式编程，使用Lamda表达式可以省略接口名和方法名
        new Thread(() -> {
            System.out.println("Java 8 匿名内部类");
        }).start();
        
        // 匿名内部类的写法，省略了MyThread类，将这个Runnable的匿名内部类的实例作为参数传递给Thread的构造函数
		new Thread(new Runnable() {// 接口名
		    @Override
		    public void run() {// 方法名
		        System.out.println("Java 8 匿名内部类");
		    }
		}).start();
    }
}
```

### 1.1.3 Thread类构造方法

`Thread`类是一个`Runnable`接口的实现类，因此，可以通过继承 `Thread` 类，并重写 `run` 方法，来实现线程的创建和执行逻辑。我们来看看`Thread`类的源码。

查看`Thread`类的构造方法，发现其实是简单调用一个私有的`init`方法来实现初始化。`init`的方法签名：

```java
// Thread类源码 

// 片段1 - init方法
private void init(ThreadGroup g, Runnable target, String name,
                      long stackSize, AccessControlContext acc,
                      boolean inheritThreadLocals)

// 片段2 - 构造函数调用init方法
public Thread(Runnable target) {
    init(null, target, "Thread-" + nextThreadNum(), 0);
}

// 片段3 - 使用在init方法里初始化AccessControlContext类型的私有属性
this.inheritedAccessControlContext = 
    acc != null ? acc : AccessController.getContext();

// 片段4 - 两个对用于支持ThreadLocal的私有属性
ThreadLocal.ThreadLocalMap threadLocals = null;
ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
```

- g：线程组，指定这个线程是在哪个线程组下；
- target：指定要执行的任务；
- name：线程的名字，多个线程的名字是可以重复的。如果不指定名字，见片段2；
- acc：见片段3，用于初始化私有变量`inheritedAccessControlContext`
- inheritThreadLocals：可继承的`ThreadLocal`，见片段4，`Thread`类里面有两个私有属性来支持`ThreadLocal`

实际情况下，我们大多是直接调用下面两个构造方法：

```java
Thread(Runnable target)
Thread(Runnable target, String name)
```

### 1.1.4 Thread类的几个常用方法

- currentThread()：静态方法，返回对当前正在执行的线程对象的引用；
- start()：开始执行线程的方法，java虚拟机会调用线程内的run()方法；
- yield()：yield在英语里有放弃的意思，同样，这里的yield()指的是当前线程愿意让出对当前处理器的占用。这里需要注意的是，就算当前线程调用了yield()方法，程序在调度的时候，也还有可能继续运行这个线程的；
- sleep()：静态方法，使当前线程睡眠一段时间；
- join()：使当前线程等待另一个线程执行完毕之后再继续执行，内部调用的是Object类的wait方法实现的；

### 1.1.5 Thread类与Runnable接口的比较

实现一个自定义的线程类，可以有继承`Thread`类或者实现`Runnable`接口这两种方式，它们之间有什么优劣呢？

- Java 不支持多重继承，因此继承了 Thread 类就无法继承其它类，但是可以实现多个接口；
- 如果使用线程时不需要使用Thread类的诸多方法，显然使用Runnable接口更为轻量。

所以，我们通常优先使用“实现`Runnable`接口”这种方式来自定义线程类。

## 1.2 Callable、Future与FutureTask

与 Runnable 相比，Callable 可以有返回值，返回值通过 FutureTask 进行封装。

### 1.2.1 Callable接口

`Callable`与`Runnable`类似，同样是只有一个抽象方法的函数式接口。不同的是，`Callable`提供的方法是**有返回值**的，而且支持**泛型**。

```java
@FunctionalInterface
public interface Callable<V> {
    V call() throws Exception;
}
```

`Callable`一般是配合线程池工具`ExecutorService`来使用的。`ExecutorService`可以使用`submit`方法来让一个`Callable`接口执行。它会返回一个`Future`，我们后续的程序可以通过这个`Future`的`get`方法得到结果。

```java
// 自定义Callable
class Task implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        // 模拟计算需要一秒
        Thread.sleep(1000);
        return 2;
    }
    public static void main(String args[]){
        // 使用
        ExecutorService executor = Executors.newCachedThreadPool();
        Task task = new Task();
        // 使用submit方法来让一个Callable接口执行，返回一个Future
        Future<Integer> result = executor.submit(task);
        // 注意调用get方法会阻塞当前线程，直到得到结果。
        // 所以实际编码中建议使用可以设置超时时间的重载get方法。
        System.out.println(result.get()); // 2
    }
}
```

### 1.2.2 Future接口

`Future`接口只有几个比较简单的方法：

```java
public abstract interface Future<V> {
    public abstract boolean cancel(boolean paramBoolean);
    public abstract boolean isCancelled();
    public abstract boolean isDone();
    public abstract V get() throws InterruptedException, ExecutionException;
    public abstract V get(long paramLong, TimeUnit paramTimeUnit)
            throws InterruptedException, ExecutionException, TimeoutException;
}
```

`cancel`方法是试图取消一个线程的执行。

注意是**试图**取消，**并不一定能取消成功**。因为任务可能已完成、已取消、或者一些其它因素不能取消，存在取消失败的可能。`boolean`类型的返回值是“是否取消成功”的意思。参数`paramBoolean`表示是否采用中断的方式取消线程执行。

所以有时候，为了让任务有能够取消的功能，就使用`Callable`来代替`Runnable`。如果为了可取消性而使用 `Future`但又不提供可用的结果，则可以声明 `Future<?>`形式类型、并返回 `null`作为底层任务的结果。

### 1.2.3 FutureTask类

`FutureTask`类是实现的`RunnableFuture`接口的，而`RunnableFuture`接口同时继承了`Runnable`接口和`Future`接口：

```java
public interface RunnableFuture<V> extends Runnable, Future<V> {
    /**
     * Sets this Future to the result of its computation
     * unless it has been cancelled.
     */
    void run();
}
```

那`FutureTask`类有什么用？为什么要有一个`FutureTask`类？前面说到了`Future`只是一个接口，而它里面的`cancel`，`get`，`isDone`等方法要自己实现起来都是**非常复杂**的。所以JDK提供了一个`FutureTask`类来供我们使用。

```java
// 自定义Callable，与上面一样
class Task implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        // 模拟计算需要一秒
        Thread.sleep(1000);
        return 2;
    }
    public static void main(String args[]){
        // 使用
        ExecutorService executor = Executors.newCachedThreadPool();
        FutureTask<Integer> futureTask = new FutureTask<>(new Task());
        executor.submit(futureTask);
        System.out.println(futureTask.get());
    }
}
```

Callable接口：

```java
    public static void main(String args[]){
        // 使用
        ExecutorService executor = Executors.newCachedThreadPool();
        Task task = new Task();
        // 使用submit方法来让一个Callable接口执行，返回一个Future
        Future<Integer> result = executor.submit(task);
        // 注意调用get方法会阻塞当前线程，直到得到结果。
        // 所以实际编码中建议使用可以设置超时时间的重载get方法。
        System.out.println(result.get()); // 2
    }
```

与Callable接口的区别在于：

|          | FutureTask类                    | Callable接口                                  |
|----------|--------------------------------|---------------------------------------------|
| get方法    | 直接取get取值                       | 通过submit方法返回的Future去取值。                     |
| 高并发      | 任务可能创建多次，只执行一次                 | 任务可能创建多次，执行多次                    |
| submit方法 | 调用的submit(Runnable task)，没有返回值 | 调用的是submit(Callable task)方法，返回值类型为Future |

### 1.2.4 FutureTask的几个状态

```java
/**
  *
  * state可能的状态转变路径如下：
  * NEW -> COMPLETING -> NORMAL
  * NEW -> COMPLETING -> EXCEPTIONAL
  * NEW -> CANCELLED
  * NEW -> INTERRUPTING -> INTERRUPTED
  */
private volatile int state;
private static final int NEW          = 0;
private static final int COMPLETING   = 1;
private static final int NORMAL       = 2;
private static final int EXCEPTIONAL  = 3;
private static final int CANCELLED    = 4;
private static final int INTERRUPTING = 5;
private static final int INTERRUPTED  = 6;
```

## 1.3 Java线程的6个状态

```java
// Thread.State 源码
public enum State {
    NEW,
    RUNNABLE,
    BLOCKED,
    WAITING,
    TIMED_WAITING,
    TERMINATED;
}
```

### 1.3.1 NEW

处于NEW状态的线程此时尚未启动。这里的尚未启动指的是还没调用Thread实例的start()方法。

```java
private void testStateNew() {
    Thread thread = new Thread(() -> {}); // 创建了线程而并没有调用start()方法
    System.out.println(thread.getState()); // 输出 NEW 
}
```

start()的源码：

```java
public synchronized void start() {
    if (threadStatus != 0)
        throw new IllegalThreadStateException();

    group.add(this);

    boolean started = false;
    try {
        start0();
        started = true;
    } finally {
        try {
            if (!started) {
                group.threadStartFailed(this);
            }
        } catch (Throwable ignore) {

        }
    }
}
```

可以看到threadStatus的值不为0会抛出异常。

查看当前线程状态的源码：

```java
// Thread.getState方法源码：
public State getState() {
    // get current thread state
    return sun.misc.VM.toThreadState(threadStatus);
}

// sun.misc.VM 源码：
public static State toThreadState(int var0) {// 返回State类型的结果，State是Java中的一个枚举类型，表示线程的不同状态（整数值转换为线程状态）
    if ((var0 & 4) != 0) {
        return State.RUNNABLE;
    } else if ((var0 & 1024) != 0) {
        return State.BLOCKED;
    } else if ((var0 & 16) != 0) {
        return State.WAITING;
    } else if ((var0 & 32) != 0) {
        return State.TIMED_WAITING;
    } else if ((var0 & 2) != 0) {// 按位与，全1为1，因此2 & 2的结果是2
        return State.TERMINATED;
    } else {
        return (var0 & 1) == 0 ? State.NEW : State.RUNNABLE;// 0 & 1的结果是0，是NEW
    }
}
```

- 反复调用同一个线程的start()方法不可行，因为第二次调用时threadStatus的值不为0。
- 一个线程执行完毕（此时处于TERMINATED状态），再次调用这个线程的start()方法不可行，因为此时threadStatus为2（传入var0为2时return State.TERMINATED）

### 1.3.2 RUNNABLE

Thread源码里对RUNNABLE状态的定义：

```java
/**
 * Thread state for a runnable thread.  A thread in the runnable
 * state is executing in the Java virtual machine but it may
 * be waiting for other resources from the operating system
 * such as processor.
 */
```

因此Java线程的**RUNNABLE**状态其实是包括了传统操作系统线程的**ready**和**running**两个状态的。分别对应：

- 等待其他系统资源（比如I/O）
- 在Java虚拟机中运行

### 1.3.3 BLOCKED

阻塞状态。处于BLOCKED状态的线程正等待锁的释放以进入同步区。

### 1.3.4 WAITING

等待状态。处于等待状态的线程变成RUNNABLE状态需要其他线程唤醒。调用如下3个方法会使线程进入等待状态：

- Object.wait()：使当前线程处于等待状态直到另一个线程唤醒它，调用wait()方法前线程必须持有对象的锁，线程调用wait()方法时，会释放当前的锁；
- Thread.join()：等待线程执行完毕，底层调用的是Object实例的wait方法，调用join()方法不会释放锁，会一直等待当前线程执行完毕（转换为TERMINATED状态）
- LockSupport.park()：除非获得调用许可，否则禁用当前线程进行线程调度

### 1.3.5 TIMED_WAITING

超时等待状态。线程等待一个具体的时间，时间到后会被自动唤醒（比如等待10分钟后，就自动唤醒，拥有了去争夺锁的资格）调用如下方法会使线程进入超时等待状态：

- Thread.sleep(long millis)：使当前线程睡眠指定时间；
- Object.wait(long timeout)：线程休眠指定时间，等待期间可以通过notify()/notifyAll()唤醒；
- Thread.join(long millis)：等待当前线程最多执行millis毫秒；
- LockSupport.parkNanos(long nanos)： 除非获得调用许可，否则禁用当前线程进行线程调度指定时间；
- LockSupport.parkUntil(long deadline)：同上，也是禁止线程进行调度指定时间；

### 1.3.6 TERMINATED

终止状态。此时线程已执行完毕。

## 1.4 线程状态转换

![](../../../Image/Java/Java%20线程状态转换.png)

### 1.4.1 BLOCKED与RUNNABLE状态的转换

```java
@Test
public void blockedTest() {

    Thread a = new Thread(new Runnable() {
        @Override
        public void run() {
            testMethod();
        }
    }, "a");
    Thread b = new Thread(new Runnable() {
        @Override
        public void run() {
            testMethod();
        }
    }, "b");

    a.start();
    b.start();
    System.out.println(a.getName() + ":" + a.getState()); // 输出RUNNABLE
    System.out.println(b.getName() + ":" + b.getState()); // 输出BLOCKED（大部分）
}

// 同步方法争夺锁
private synchronized void testMethod() {
    try {
        Thread.sleep(2000L);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```

测试方法blockedTest()内还有一个main线程，测试方法的main线程只保证了a，b两个线程调用start()方法（转化为RUNNABLE状态），还没等a线程真正开始争夺锁，就已经打印此时a线程的状态（RUNNABLE）了（**启动线程后执行run方法还是需要消耗一定时间的**）

b线程可能进入了同步方法争抢锁成功进入TIMED_WAITING或争抢锁失败进入BLOCKED

```java
public void blockedTest() throws InterruptedException {
    ······
    a.start();
    Thread.sleep(1000L); // 需要注意这里main线程休眠了1000毫秒，而testMethod()里休眠了2000毫秒
    b.start();
    System.out.println(a.getName() + ":" + a.getState()); // 输出TIMED_WAITING
    System.out.println(b.getName() + ":" + b.getState()); // 输出BLOCKED
}
```

**由于main线程休眠，所以线程a的run()方法跟着执行**（线程立马调度），在线程a执行run()调用testMethod()之后，线程a休眠了2000ms（注意这里是没有释放锁的），main线程休眠完毕，接着b线程执行的时候是争夺不到锁的。

### 1.4.2 WAITING与RUNNABLE状态的转换

```java
public void blockedTest() {
    ······
    a.start();
    a.join();// 调用join()方法不会释放锁，会一直等待当前a线程执行完毕（转换为TERMINATED状态）
    b.start();
    System.out.println(a.getName() + ":" + a.getState()); // 输出 TERMINATED
    System.out.println(b.getName() + ":" + b.getState()); // RUNNABLE或TIMED_WAITING（大部分）
}
```

要是没有调用join方法，main线程不管a线程是否执行完毕都会继续往下走。

a线程启动之后马上调用了join方法，这里main线程就会等到a线程执行完毕，所以这里a线程打印的状态固定是**TERMIATED**。

至于b线程的状态，有可能打印RUNNABLE（尚未进入同步方法），也有可能打印TIMED_WAITING（进入了同步方法）。

### 1.4.3 TIMED_WAITING与RUNNABLE状态转换

```java
public void blockedTest() {
    ······
    a.start();
    a.join(1000L);// 使当前a线程执行指定时间，并且使a线程进入TIMED_WAITING状态(执行时间小于a线程sleep的时间)
    b.start();
    System.out.println(a.getName() + ":" + a.getState()); // 输出 TIEMD_WAITING
    System.out.println(b.getName() + ":" + b.getState()); // RUNNABLE或BLOCKED（大部分）
}
```