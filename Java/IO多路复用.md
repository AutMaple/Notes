# I/O 模型

我们所说的 I/O 模型是指网络 I/O 模型,就是服务端如何管理连接，如何请求连接的措施，是用一个进程管理一个连接（PPC），还是一个线程管理一个连接(TPC)，亦或者一个线程管理多个连接(Reactor)。

因此 IO 多路复用中多路就是多个TCP连接（或多个 Channel），复用就是指复用一个或少量线程，理解起来就是多个网路 IO 复用一个或少量线程来处理这些连接。

# 常见 I/O 模型

- 同步阻塞 IO（Blocking IO）：即传统 IO 模型
- 同步非阻塞 IO（Non-blocking IO）：默认常见的 socket 都是阻塞的，非阻塞 IO 要求 socket 被设置成 NONBLOCK
- IO 多路复用（IO Multiplexing）：即经典的 Reactor 设计模式，也被称为异步阻塞 IO，Java 中的 selector 和 linux 中的epoll 都是这种模型
- 异步 IO（Asychronous IO）：即 Proactor 设计模式，也被称为异步非阻塞 IO

同步和异步的概念描述的是用户线程与内核的交互方式，这里所说的用户进程/线程和内核是以传输层为分割线的，传输层以上是指用户进程，传输层以下（包括传输层）是指内核(处理所有通信细节，发送数据，等待确认，给无序到达的数据排序等，这四层是操作系统内核的一部分)。

- 同步是指用户线程发起 IO 请求后需要等待或者轮询内核 IO 操作，完成后才能继续执行。

- 异步是指用户线程发起 IO 请求后仍继续执行，当内核 IO 操作完成后回通知用户线程，或者调用用户线程注册的回调函数。

阻塞和非阻塞的概念描述的是用户线程调用内核 IO 操作的方式，

- 阻塞时指 IO 操作需要彻底完成后才能返回用户空间
- 非阻塞时指 IO 操作被调用后立即返回给用户一个状态值，无需等待 IO 操作彻底完成。

## 同步阻塞IO

同步阻塞 IO 是最简单的 IO 模型，用户线程在内核进行 IO 操作时被阻塞。用户线程通过调用系统调用 read 发起 IO 读操作，由用户空间转到内核空间。内核等到数据包到达后，然后将接受的数据拷贝到用户空间，完成 read 操作。整个 IO 请求过程，用户线程都是被阻塞的，对 CPU 利用率不够

![img](../Attachment/142330286789443.png)

## 同步非阻塞IO

在同步基础上，将 socket 设置为 NONBLOCK ，这样用户线程可以在发起 IO 请求后立即返回。虽说可以立即返回，但并未读到任何数据，用户线程需要不断的发起 IO 请求，直到数据到达后才能真正读到数据，然后去处理。

整个 IO 请求中，虽然可以立即返回，但是因为是同步的，为了等到数据，需要不断的轮询、重复请求，消耗了大量的 CPU 资源。因此，这种模型很少使用，实际用处不大。

![img](../Attachment/142332004602984.png)

## IO多路复用

不管是同步阻塞还是同步非阻塞，对系统性能的提升都是很小的。而通过复用可以使一个或一组线程（线程池）处理多个 TCP 连接。IO 多路复用使用两个系统调用（select/poll/epoll和recvfrom），blocking IO 只调用了 recvfrom。select/poll/epoll 核心是可以同时处理多个 connection，而不是更快，所以连接数不高的话，性能不一定比多线程+阻塞 IO 好。

select 是内核提供的多路分离函数，使用它可以避免同步非阻塞 IO 中轮询等待问题。

![img](../Attachment/142332187256396.png)

用户首先将需要进行 IO 操作的 socket 添加到 select 中，然后阻塞等待 select 系统调用返回。当数据到达时，socket 被激活，select 函数返回，用户线程正式发起 read 请求，读取数据并继续执行。

这么一看，这种方式和同步阻塞 IO 并没有太大区别，甚至还多了添加监视 socket 以及调用 select 函数的额外操作，效率更差。但是使用 select 以后，用户可以在一个线程内同时处理多个 socket 的 IO 请求，这就是它的最大优势。用户可以注册多个 socket，然后不断调用 select 读取被激活的 socket，**即可达到同一个线程同时处理多个IO请求的目的**。而在同步阻塞模型中，必须通过多线程方式才能达到这个目的。**所以 IO 多路复用设计目的其实不是为了快，而是为了解决线程/进程数量过多对服务器开销造成的压力。**

```c
select(socket); // 向select注册socket
while(true){
	sockets = select(); // 获取被激活的socket
	for(socket in sockets){
        if(can_read(socket)){	// socket 可读，调用 read 读取数据
            read(socket,buffer);
            process(buffer);
    	}
    }
}
```

虽然这种方式允许单线程内处理多个 IO 请求，但是每个 IO 请求的过程还是阻塞的（在 select 函数上阻塞），平均时间甚至比同步阻塞IO 模型还要长。如果用户线程只注册自己感兴趣的 socket，然后去做自己的事情，等到数据到来时在进行处理，则可以提高 CPU 利用率。

![img](../Attachment/142333254136604.png)

通过 Reactor 方式，用户线程轮询 IO 操作状态的工作统一交给 handle_events 事件循环处理。用户线程注册事件处理器之后可以继续执行做其他的工作（异步），而 Reactor 线程负责调用内核的 select 函数检查 socket 状态。当有 socket 被激活时，则通知相应的用户线程（或执行用户线程的回调函数），执行 handel_envent 进行数据的读取、处理工作。

由于 select 函数是阻塞的，因此多路 IO 复用模型就被称为异步阻塞 IO 模型，这里阻塞不是指 socket。因为使用 IO 多路复用时，socket 都设置 NONBLOCK，不过不影响，因为用户发起 IO 请求时，数据已经到达了，用户线程一定不会被阻塞。

IO 多路复用是最常用的 IO 模型，但其异步程度还不彻底，因为它使用了回阻塞线程的 select 系统调用。因此 IO 多路复用只能称为异步阻塞 IO，而非真正的异步IO。

附：Reactor设计模式

![img](../Attachment/142332350853195.png)

## 异步非阻塞IO

在 IO 多路复用模型中，事件循环文件句柄的状态事件通知给用户线程，由用户线程自行读取数据、处理数据。而异步 IO 中，当用户线程收到通知时候，数据已经被内核读取完毕，并放在了用户线程指定的缓冲区内，内核在 IO 完成后通知用户线程直接使用就行了。因此这种模型需要操作系统更强的支持，把 read 操作从用户线程转移到了内核。

相比于 IO 多路复用模型，异步 IO 并不十分常用，不少高性能并发服务程序使用 IO 多路复用+多线程任务处理的架构基本可以满足需求。不过最主要原因还是操作系统对异步 IO 的支持并非特别完善，更多的采用 IO 多路复用模拟异步 IO 方式(IO 事件触发时不直接通知用户线程，而是将数据读写完毕后放到用户指定的缓冲区)。

# select、poll、epoll详解

select，poll，epoll 都是 IO 多路复用的机制。I/O 多路复用就是通过一种机制，一个进程可以监视多个描述符（socket），一旦某个描述符就绪（一般是读就绪或者写就绪），能够通知程序进行相应的读写操作。虽说 IO 多路复用被称为异步阻塞IO，但 select，poll，epoll 本质上都是同步 IO，因为它们都需要在续写事件就绪后自己负责进行读写，也就是说这个读写过程是阻塞的，而真正意义上的异步 IO无需自己负责进行读写。

## select

```c++
int select(int n, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
```

select 函数监视的文件描述符有三类，readfds，writefds，exceptfds。调用后函数会阻塞，直到有描述符就绪（有数据读、写、或者有 except），或者超时（timeout 指定时间，如果立即返回设置 null），函数返回。当 select 函数返回后，可以通过遍历 fdset，来找到就绪的描述符。

优点：良好的跨平台性。

缺点：单个进程能够监视的文件描述符的数量存在最大限制，在 Linux 上为 1024，可以通过修改宏定义甚至重新编译内核的方式提升这一限制，但这样会造成效率的降低。

## poll

```c++
int poll(struct poll *fds, unsigned int nfds, int timeout);
struct pollfd{
  int fd;
  short events;
  short revents;
};
```

与 select 使用三个位图来表示 fdset，poll 使用一个 pollfd 的指针实现。pollfd 结构包含了要监视的 event 和发生的 event，**不在使用 select 参数传值的方式。同时 pollfd 并没有最大数量的限制**（但数量过大性能也会下降）。和 select 一样，poll返回后，需要轮询 pollfd 来获取就绪的描述符。

## epoll

epoll 是 select 和 poll 的增强版本，相比于前两者，它更加的灵活，没有描述符的限制。epoll 使用一个文件描述符管理多个描述符，将用户关系的文件描述符的事件存放到内核的一个事件表中，这样在用户空间和内核空间的 copy 只需要一次。