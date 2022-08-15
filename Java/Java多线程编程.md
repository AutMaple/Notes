# Java 中的线程

- ID：该属性存储了每个线程的唯一标识符。
- Name：该属性存储了线程的名字。
- Priority：该属性存储了 Thread 对象的优先级。在 Java 9 中， 线程优先级的范围为 1～10，其中 1 表示最低优先级，10 表示最高 优先级。通常不建议修改线程的优先级。线程优先级仅供底层操作系统作为参考，不能保证任何事情，如果一定要修改，请知晓优先级仅仅代表了一种可能性。
- Status：该属性保存了线程的状态。在 Java 中，线程有 6 种状态 —— Thread.State 枚举中定义这些状态：NEW、RUNNABLE、BLOCKED、WAITING、TIMED_WAITING 和 TERMINATED。这些状态的具体意义如下:
  - NEW：线程已经创建完毕但未开始执行。即 new Thread(...) 之后，线程处于 NEW 状态
  - RUNNABLE：线程正在 JVM 中执行。当调用线程的 start 方法之后处于 RUNNABLE 状态，Java 中将就绪态和运行态统一为了 RUNNABLE 状态
  - BLOCKED：线程处于阻塞状态，并且等待获取监视器。
  - WAITING：线程在等待另一个线程。
  - TIMED_WAITING：线程等待另一个线程一定的时间。
  - TERMINATED：线程执行完毕。

创建一个 Thread 对象并不意味着会创建一个新的执行线程。同样，调用实现了 Runnable 接口类的 run() 方法也不会创建新的执行线程。只有调用了start() 方法，一个新的执行线程才会真正创建。

每个 Java 应用程序都至少有一个执行线程。在程序启动时，JVM 会自动创建执行线程运行程序的 main() 方法。 当调用 Thread 对象的 start() 方法时，JVM 才会创建一个执行线程。也就是说，每个 Thread 对象的 start() 方法被调用时，才会创建开始执行的线程。

一个 Java 程序将在所有线程完成后结束。初始线程(执行 main()方法的线程)完成，其他线程仍会继续执行直到完成。**如果一个线程调用 System.exit() 命令去结束程序，那么所有线程将会终止各自的运行。**

# 线程中断

一个多线程 Java 程序，只有当其全部线程执行结束时(更具体地 说，是所有非守护线程结束或者某个线程调用 System.exit() 方法 的时候)，才会结束运行。

### interrupted 方法和 isInterrupted() 方法

interrupted 方法是 Thread 的静态方法, 该方法会测试当前线程是否被中断，并且会清除当前线程的 interrupted 属性，即将 interrupted 属性设置为 false;

isInterrupted 方式是 Thread 类的实例方法，该方法不会清除当前线程的 Interrupted 属性

这两个方法都是返回当前线程是否被中断，被中断则返回 true, 否则返回 false。需要注意的是，因为 interrupted 方法会修改线程的 interrupted 属性，因此调用 interrupted 方法两次，那么第 2 次调用将返回 false;

# 守护线程

Java 有一种名为守护(daemon)线程的特殊线程。当程序中仅剩守护线程还在运行时，JVM 会先结束这些线程然后结束程序。守护线程的典型案例就是 Java 的垃圾回收器。

# join 方法

当我们想等待其他线程结束之后在执行后面的方法时，可以使用该方法，该方法实现的逻辑是在一个 while 循环中判断调用该方法的线程是否还在运行，只要还在运行就一直阻塞

```java
public final synchronized void join(long millis) throws InterruptedException {
    long base = System.currentTimeMillis();
    long now = 0;
    if (millis < 0) {
        throw new IllegalArgumentException("timeout value is negative");
    }
    
    if (millis == 0) {
        while (isAlive()) { // isAlive 方法如果线程还在运行就返回 true, 否则返回 false
            wait(0);
        }
    } else {
        while (isAlive()) {
            long delay = millis - now;
            if (delay <= 0) {
                break;
            }
            wait(delay);
            now = System.currentTimeMillis() - base;
        }
    }
}

```

# 捕获异常

我们可以为每一个线程设置异常处理器，当线程因为异常而终止时，线程会使用 `getUncaughtExceptionHandler` 方法获取异常处理器，并调用这些异常处理器, 异常处理器的设置在创建线程时进行设置, 异常处理器则需要实现 `Thread.UncaughtExceptionHandler` 接口

当线程中抛出一个异常且未捕获（必须是非检查异常）时，JVM 会检查是否通过相关方法为该线程配置了未捕获异常处理器 (UncaughtExceptionHandler)。如果有，那么 JVM 将调用线程对象上相应的方法，并传递抛出的异常作为参数。如果线程对象没有配置未捕获异常处理器，则 JVM 会在控制台中打印出异常信息栈，然后结束抛出异常线程的执行。

```java
public class ExceptionHandler implements Thread.UncaughtExceptionHandler {
    @Override
    public void uncaughtException(Thread t, Throwable e) {
        System.out.println("An Exception has been captured");
        System.out.println("Thread：" + t.getId());
        System.out.println("Exception: " + e.getClass().getName() + " : " + e.getMessage());
        System.out.println("Stack Trace");
        e.printStackTrace();
        System.out.println("Thread status: " + t.getState());
    }
}
```

```java
public class Task implements Runnable {
    @Override
    public void run() {
        int numero = Integer.parseInt("TTT");
    }
    public static void main(String[] args) {
        Task task = new Task();
        Thread thread = new Thread(task);
        thread.setUncaughtExceptionHandler(new ExceptionHandler());
        thread.start();
    }
}
```

## 为应用中所有的线程对象设置默认未捕获的异常处理器

`Thread.setDefaultUncaughtExceptionHandler()` 方法可以为所有没有设置未捕获异常处理器的线程设置对应的处理器。

查找异常处理器的顺序：

1. 当前线程中找
2. 线程组中找
3. 默认的异常处理器，即通过 Thead 的静态方法 `setDefaultUncaughtExceptionHandler()` 设置的处理器

# 线程本地变量

有时希望多线程同时执行一个任务的实例时，能够独享一个变量。为此 Java 并发 API 中的线程本地变量(thread-local variable)

ThreadLocal 中的部分代码

```java
public T get() {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t); // 获取对应线程的 ThreadLocalMap， 该 ThreadLocalMap 以 TreadLocal 为 key，这样持有同样 ThreadLocal 对象的线程可以通过同一个 API 获取对应的值
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    return setInitialValue(); // 如果 ThreadLocalMap 对象为空则返回 iniitalVlaue 方法的返回值
}

ThreadLocalMap getMap(Thread t) {
    return t.threadLocals; // ThreadLocal.ThreadLocalMap threadLocals = null; 每个线程都持有一个 ThreadLocalMap 对象
}

 private T setInitialValue() {
    T value = initialValue(); // thredlocal 的默认实现是 null, 因此如果想要提供自己的值，则需要重写 initialVlaue 方法
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value); // 创建 ThreadLocalMap, 以 ThreadLocal 对象为 key, 以 initialVlaue() 方法的返回值为 value
    return value;
}

protected T initialValue() {
    return null;
}

void createMap(Thread t, T firstValue) {
    t.threadLocals = new ThreadLocalMap(this, firstValue); 
}

ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
    table = new Entry[INITIAL_CAPACITY];
    int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);
    table[i] = new Entry(firstKey, firstValue);
    size = 1;
    setThreshold(INITIAL_CAPACITY);
}

public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
}
```

通过上面的代码的分析可以知道，如果想要实现线程的本地变量关键就是 ThreadLocal 的 initialValue 方法。java 8 中提供了两种方式来实现，一种是继承 ThreadLocal 对象然后重写 initalValue 方法，另外一种则是通过函数式接口：Supplier 来实现

```java
private static ThreadLocal<Date> startDate = ThreadLocal.withInitial(Date::new);
```

```java
public static <S> ThreadLocal<S> withInitial(Supplier<? extends S> supplier) {
    return new SuppliedThreadLocal<>(supplier);
}

static final class SuppliedThreadLocal<T> extends ThreadLocal<T> {
    private final Supplier<? extends T> supplier;

    SuppliedThreadLocal(Supplier<? extends T> supplier) {
        this.supplier = Objects.requireNonNull(supplier);
    }

    @Override
    protected T initialValue() {
        return supplier.get();  // 获取我们设置的值
    }
}
```

# 使用工厂创建线程

Java 提供了一个接口(ThreadFactory接口) 来实现线程对象工厂