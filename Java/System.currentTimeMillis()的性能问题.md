`System.currentTimeMillis()` 方法的源码注释上说:

```text
Returns the current time in milliseconds. Note that while the unit of time of the return value is a millisecond, the granularity of the value depends on the underlying operating system and may be larger. For example, many operating systems measure time in units of tens of milliseconds.
```

意思就是该方法返回的是毫秒数，但是值的粒度取决于底层的操作系统，因为有的操作系统是以 10ms 为粒度来返回的。因此该方法获取毫秒数不是很统一。

该方法不论是单线程还是多线程，都有可能存在竞争的情况。一旦存在竞争的情况，获得毫秒数就会出现不准确的情况。造成这种情况主要以下几个原因：

1. native 方法的调用需要从用户态切换到核心态
2. 受系统的计时器影响，在 HPEL 计时器下性能很差
3. 系统只有一个全局时钟源，高并发或频繁访问会造成严重的争用

1. 调用 gettimeofday() 需要从用户态切换到内核态
2. gettimeofday() 的表现受 Linux 系统的计时器（时钟源）影响，在 HPET 计时器下性能尤其差
3. 系统只有一个全局时钟源，高并发或频繁访问会造成严重的争用。

HPET 计时器性能较差的原因是会将所有对时间戳的请求串行执行; TSC 计时器性能较好，因为有专用的寄存器来保存时间戳。缺点是可能不稳定，因为它是纯硬件的计时器，频率可变（与处理器的 CLK 信号有关）

# Linux 下 currentTimeMillis 的原理

System.currentTimeMillis 确实**要访问系统时钟**，准确的说，是读取墙上时间（xtime），xtime 是 Linux 系统给用户空间用来获取当前时间的，内核自己基本不会使用，只是维护更新。**而且读写 xtime 使用的是 Linux 内核中的顺序锁，而非互斥锁，读线程间是互不影响的**  

大家可以把顺序锁当成是解决了“ABA问题”的 CompareAndSwap 锁。对于一个临界区资源（这里是 xtime ），有一个操作序列号，写操作会使序列号+1，读操作则不会。

写操作：CAS使序列号+1

读操作：先获取序列号，读取数据，再获取一次序列号，前后两次获取的序列号相同，则证明进行读操作时没有写操作干扰，那么这次读是有效的，返回数据，否则说明读的时侯可能数据被更改了，这次读无效，重新做读操作。

大家可能有个疑问：读 xtime 的时候数据可能被更改吗？难度读操作不是原子性的吗？这是因为 xtime 是 64 位的，对于 32 位机器是需要分两次读的，而 64 位机器不会产生这个并发的问题