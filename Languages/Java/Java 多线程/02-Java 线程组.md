
## 2.1 线程组和线程优先级

### 2.1.1 线程组(ThreadGroup)

线程组是一个树状的结构，每个线程组下面可以有多个线程或者线程组。线程组可以起到统一控制线程的优先级和检查线程的权限的作用。

我们可以使用线程组对线程进行批量控制。每个Thread必然存在于一个ThreadGroup中，Thread不能独立于ThreadGroup存在。执行main()方法线程的名字是main，如果在new Thread时没有显式指定，那么默认将父线程（当前执行new Thread的线程）线程组设置为自己的线程组。

```java
public class Demo {
    public static void main(String[] args) {
        Thread testThread = new Thread(() -> {
            System.out.println("testThread当前线程组名字：" +
                    Thread.currentThread().getThreadGroup().getName());
            System.out.println("testThread线程名字：" +
                    Thread.currentThread().getName());
        });

        testThread.start();
        System.out.println("执行main方法线程名字：" + Thread.currentThread().getName());
    }
}

/*
执行main方法线程名字：main
testThread当前线程组名字：main
testThread线程名字：Thread-0
*/
```

- 首先，主线程（main线程）开始执行，打印出"执行main方法线程名字：main"。
- 然后，创建了一个新的线程`testThread`，并启动它。新线程开始执行，打印出线程组名字和线程名字。

ThreadGroup管理着它下面的Thread，ThreadGroup是一个标准的**向下引用**的树状结构，这样设计的原因是**防止"上级"线程被"下级"线程引用而无法有效地被GC（_Garbage Collection_）回收**。

>当垃圾回收器确定某个对象不再被引用时，它会回收该对象所占用的内存，并将其标记为可重用的空间。

### 2.1.2 线程的优先级

Java中线程优先级可以指定，范围是1~10。Java只是给操作系统一个优先级的**参考值**，线程最终**在操作系统的优先级**是多少还是由操作系统决定。

Java默认的线程优先级为5，线程的执行顺序由调度程序来决定（线程的调度策略采用**抢占式**），线程的优先级会在线程被调用之前设定。

通常情况下，高优先级的线程将会比低优先级的线程有**更高的几率**得到执行。在优先级相同的情况下，按照“先到先得”的原则。我们使用方法`Thread`类的`setPriority()`实例方法来设定线程的优先级。

```JAVA
public class Demo {
    public static void main(String[] args) {
        Thread a = new Thread();
        System.out.println("我是默认线程优先级："+a.getPriority());
        Thread b = new Thread();
        b.setPriority(10);
        System.out.println("我是设置过的线程优先级："+b.getPriority());
    }
}
```

Java中的优先级来说不是特别的可靠，**Java程序中对线程所设置的优先级只是给操作系统一个建议，操作系统不一定会采纳。而真正的调用顺序，是由操作系统的线程调度算法决定的**（Java提供一个**线程调度器**来监视和控制处于**RUNNABLE状态**的线程。）

```java
public class Demo {
    public static class T1 extends Thread {// 继承Thread类，并重写run方法
        @Override
        public void run() {
            super.run();
            System.out.println(String.format("当前执行的线程是：%s，优先级：%d",
                    Thread.currentThread().getName(),
                    Thread.currentThread().getPriority()));
        }
    }

    public static void main(String[] args) {
        IntStream.range(1, 10).forEach(i -> {
	        // T1类本身就是一个Runnable对象，因为它继承了Thread类并重写了run方法（Thread类是一个Runnable接口的实现类）
            Thread thread = new Thread(new T1());
            thread.setPriority(i);
            thread.start();// 这里的线程的实际启动顺序是由操作系统的线程调度决定的
        });
    }
}
```

执行结果：

```
当前执行的线程是：Thread-9，优先级：5
当前执行的线程是：Thread-15，优先级：8
当前执行的线程是：Thread-11，优先级：6
当前执行的线程是：Thread-17，优先级：9
当前执行的线程是：Thread-5，优先级：3
当前执行的线程是：Thread-3，优先级：2
当前执行的线程是：Thread-1，优先级：1
当前执行的线程是：Thread-7，优先级：4
当前执行的线程是：Thread-13，优先级：7
```

还有一种线程称为**守护线程（Daemon）**，守护线程默认的优先级比较低。

> 如果某线程是守护线程，那如果所有的非守护线程结束，这个守护线程也会自动结束。
> 一个线程默认是非守护线程，可以通过Thread类的setDaemon(boolean on)来设置。

如果某个线程优先级大于线程所在**线程组的最大优先级**，那么该线程的优先级将会失效，取而代之的是线程组的最大优先级。

```java
public static void main(String[] args) {
    ThreadGroup threadGroup = new ThreadGroup("t1");
    threadGroup.setMaxPriority(6);
    Thread thread = new Thread(threadGroup,"thread");
    thread.setPriority(9);
    System.out.println("我是线程组的优先级"+threadGroup.getMaxPriority());// 6
    System.out.println("我是线程的优先级"+thread.getPriority());// 6
}
```

## 2.2 线程组的常用方法及数据结构

### 2.2.1 线程组的常用方法

- 获取当前的线程组名字

```java
Thread.currentThread().getThreadGroup().getName()
```

- 复制线程组

```java
// 复制一个线程数组threads到一个线程组threadGroup
Thread[] threads = new Thread[threadGroup.activeCount()];
TheadGroup threadGroup = new ThreadGroup();
threadGroup.enumerate(threads);
```

- 线程组统一异常处理

```java
public class ThreadGroupDemo {
    public static void main(String[] args) {
        ThreadGroup threadGroup1 = new ThreadGroup("group1") {
            // 继承ThreadGroup并重新定义以下方法
            // 在线程成员抛出unchecked exception时会执行此方法
            public void uncaughtException(Thread t, Throwable e) {
	            // 打印Thread-0: 测试异常
                System.out.println(t.getName() + ": " + e.getMessage());
            }
        };

        // 这个线程是threadGroup1的一员
        Thread thread1 = new Thread(threadGroup1, new Runnable() {// 第一个参数g绑定线程组
            public void run() {
                // 抛出unchecked异常
                throw new RuntimeException("测试异常");
            }
        });
        thread1.start();
    }
}
```

### 2.2.2 线程组的数据结构

线程组还可以包含其他的线程组，不仅仅是线程。首先看看 `ThreadGroup`源码中的成员变量

```java
public class ThreadGroup implements Thread.UncaughtExceptionHandler {
    private final ThreadGroup parent; // 父亲ThreadGroup
    String name; // ThreadGroupr 的名称
    int maxPriority; // 线程最大优先级
    boolean destroyed; // 是否被销毁
    boolean daemon; // 是否守护线程
    boolean vmAllowSuspension; // 是否可以中断

    int nUnstartedThreads = 0; // 还未启动的线程
    int nthreads; // ThreadGroup中线程数目
    Thread threads[]; // ThreadGroup中的线程

    int ngroups; // 线程组数目
    ThreadGroup groups[]; // 线程组数组
}
```

然后看看构造函数：

```java
// 私有构造函数
private ThreadGroup() { 
    this.name = "system";
    this.maxPriority = Thread.MAX_PRIORITY;
    this.parent = null;
}

// 默认是以当前ThreadGroup传入作为parent  ThreadGroup，新线程组的父线程组是目前正在运行线程的线程组。
public ThreadGroup(String name) {
    this(Thread.currentThread().getThreadGroup(), name);
}

// 构造函数
public ThreadGroup(ThreadGroup parent, String name) {
    this(checkParentAccess(parent), parent, name);
}

// 私有构造函数，主要的构造函数
private ThreadGroup(Void unused, ThreadGroup parent, String name) {
    this.name = name;
    this.maxPriority = parent.maxPriority;
    this.daemon = parent.daemon;
    this.vmAllowSuspension = parent.vmAllowSuspension;
    this.parent = parent;
    parent.add(this);
}
```

第三个构造函数里调用了`checkParentAccess`方法，这里看看这个方法的源码：

```java
// 检查parent ThreadGroup
private static Void checkParentAccess(ThreadGroup parent) {
    parent.checkAccess();
    return null;
}

// 判断当前运行的线程是否具有修改线程组的权限
public final void checkAccess() {
    SecurityManager security = System.getSecurityManager();
    if (security != null) {// 如果安全管理器不为null，则表示安全管理器已经设置。
	    // 检查当前代码是否具有访问权限。如果没有访问权限，checkAccess()方法通常会抛出SecurityException异常
        security.checkAccess(this);
    }
}
```

`SecurityManager` 类：Java的安全管理器
- 允许应用程序在执行一个可能不安全或敏感的操作前确定该操作**是什么**
- 是否是在允许执行该操作的安全上下文中**执行它**