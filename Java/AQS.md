# AQS 的核心思想

AQS(Abstract Queue Synchronizer) 的核心思想是： 如果请求的资源空闲，则将请求资源的线程设置为有效的工作线程，并将共享资源设置成锁定状态。如果请求的共享资源被占用。那么就需要一套阻塞等待以及唤醒之后锁的分配机制。AQS 通过 CLH 队列锁来实现上述机制，即将获取不到锁的线程放入到队列中。

```ad-info
CLH(Craig, Landin, and Hagersten) 队列是一个虚拟的双向队列(虚拟的双向队列，即不存在队列实例，仅存在节点之间的关联关系)。AQS 通过将每个请求共享资源的线程封装成一个 CLH 队列的节点来实现锁的分配
```

AQS 使用一个 int 类型的成员变量来表示同步状态，通过内置的 FIFO 队列来完成获取共享资源线程的排队工作。AQS 通过 CAS 对该同步状态进行修改。

```java
private volatile int state; // 共享变量，使用 volatile 来保证线程间的可见性
```

# 自定义同步器

AQS 使用模板设计模式，只需要重写几个特定的方法即可：

```java
// 该线程是否正在独占资源。只有用到condition才需要去实现它。
isHeldExclusively() 

// 独占方式。尝试获取资源，成功则返回true，失败则返回false。
tryAcquire(int) 

// 独占方式。尝试释放资源，成功则返回true，失败则返回false。
tryRelease(int) 

// 共享方式。尝试获取资源。负数表示失败；0表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源。
tryAcquireShared(int) 

// 共享方式。尝试释放资源，成功则返回true，失败则返回false。
tryReleaseShared(int) 
```

AQS 中的其他方法都是 fianl， 因此这些方法都无法被其他类所使用的，只有上述的几个方法可被其他类所使用.

# AQS 数据结构

AQS 使用 `CLH(Craig, Landin, and Hagersten)` 队列是一个虚拟的双向队列(虚拟的双向队列即不存在队列实例，仅存在节点之间的关系)。AQS 通过将每个请求共享资源的线程封装成一个 CLH 队列的节点来实现锁的分配。队列有两种类型：

- sync queue (同步队列): 该队列是双向链表。包含 head 和 tail 节点。head 节点主要用作调度工作
- Condition Queue (条件队列): 该队列不是必须的，只有在使用了 Condition 时，才会存在此单向链表，并且可能存在多个条件队列

![](../Attachment/Pasted%20image%2020230201143130.png)