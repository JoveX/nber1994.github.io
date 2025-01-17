# os-进程
# 进程和线程之间的区别：
[进程之间的关系](http://www.ruanyifeng.com/blog/2013/04/processes_and_threads.html)

## 进程经典定义
一个执行中程序的实例

## 进程上下文
* 同时进程运行依赖其进程运行时的上下文，上下文包括其
    * 代码和数据
    * 打开的文件描述符表
    * 环境变量
    * 运行时栈
    * 通用目的寄存器
![](/images/20190517154006060_1623927614.png)

## 进程提供的假象
一个独立的逻辑控制流，好像是独占cpu
一个独立的地址空间，好像是独占主存

## 并发流
多任务也叫时间分片。每个进程执行它部分逻辑流的一段时间叫做时间分片

## 私有地址空间
![一个进程常见的内存情况](/images/20190517154031478_981738775.png)

## 一个进程的常见内存情况
* 代码段总是从地址0x400000开始的，然后是数据段，然后是堆
* 内存最高位则是内核内存空间，用户栈是从2^48-1处开始向下延伸的
* 在用户栈和堆之间存在一块为共享库分配的内存空间


## 用户模式和内核模式
* 操作系统必须限制一个进程可以执行的指令和可以访问的内存空间，这种功能是通过控制某个模式位实现的
* 当设置了模式位时，进程就会运行在内核模式中
* 进程从用户态进入内核态的唯一方法是诸如中断，陷阱，故障这样的异常
* 当异常发生时，异常处理程序就会把模式位置为内核模式，当返回到用户程序时，处理器会将模式位置为用户位

## 调度
* 内核决定抢占当前进程，并重新开始一个之前被抢占的进程，叫做调度
* 调度是通过切换进程运行的上下文来进行的
    1. 保存当前进程的上下文
    2. 恢复新进程的上下文环境
    3. 将控制权移交给新的进程

### 调度的原因
* 阻塞：如果一个进程因为等待某个事件发生，如读取磁盘，内核可以让进程休眠，切换到另一个进程
* 中断：所有的机器都有产生周期性定时器中断的机制，通常为1毫秒或10毫秒，定时器终端时，内核可以进行进程调度

### 一个因为读取磁盘的调度实例
![](/images/20190517154055218_966731524.png)

1. 初始时，进程A运行在用户模式，进程调用read命令时，进程A会从用户态切换到内核态，同时内核会把A的内核态切换到B的内核态进程，然后再运行在B的用户态执行
2. 同时陷阱处理程序会通过DMA，不经过cpu，直接将数据从磁盘读取到内存中，读取完毕后，磁盘控制器会向cpu发送一个中断信息
3. 当内核觉得进程B已经执行了足够长的时间时，内核通过切换上下文，将控制权移交给A进程，同时从read的之后的指令开始执行

## 进程的三种状态
1. 运行 正在cpu执行，或者在等待被执行且最终会被执行
2. 停止 进程执行被挂起，且不会被调度。
3. 终止 进程永远的停止了 三种终止原因：1.收到一个信号 2.从主程序返回 3.exit函数退出

## 创建子进程
* 父进程创建子进程时，会拥有和父进程相同的代码段，数据段和用户栈以及文件描述符表
* 父进程和子进程最大的区别就是PID是不同的

## 回收子进程
* 当一个子进程由于某种原因终止时，内核并不会把它立即从系统中清除，而是保持在终止状态下，指到父进程回收
* 一个已经终止了，但是还未被回收的进程成为僵尸进程
* 当父进程终止了，内核会安排init进程作为他的孤儿子进程的养父，init进程的PID为1
    * 一号进程是在系统启动时创建的

## 信号
* 传送信号到目的进程，分为两个步骤
    1. 发送信号 内核通过更新目的进程上下文的某个状态，发送一个信号给目的进程
	* 发送信号的原因有两种： 1.内核检测到一个系统事件，比如除零操作或者子进程终止 2.一个进程调用了kill函数，	显示的要求信号给一个目的进程
    2. 当目的进程被内核强制对信号的发送做出反应时，同时进程可以忽略这个信号，终止或者执行一个信号处理程序来捕获信号
![](/images/20190517154117101_2142934313.png)
## 待处理信号
* 一个发出但是未被处理的信号被是待处理信号，任何时刻，一种信号最多有一个待处理信号，其他的信号会被简单的丢弃
* 同时进程可以阻塞某种信号，仍可以发送，但是不会被接收（处理）

## 发送信号
Unix提供了大量向进程发送信号的机制，这些机制是基于进程组的

### 进程组
每个进程都属于一个进程组，默认的子进程的进程组PGID是父进程的PID

* shell
    * 对于shell来说，最多有一个前台任务和多个后台任务，shell会为每个作业创建一个独立的进程组

## 信号的收发
![](/images/20190517154140699_848754754.png)

* 进程A发送信号给进程B，信号会被传递给内核，内核会对信号进行权限等处理，内核首先判断进程A是否允许对进程B发送该信号，如果可以的话，内核将对B进程的进程表进行设置，如果该信号已经被设置过之后，且还没有进行响应，则该信号则会被丢弃
* 进程B收到信号后，一般不会立即处理，而是会在中断返回之后或者从内核态返回用户态时对信号进行处理


## 接收信号
![](/images/20190517154210200_1434767104.png)
* 一个信号处理程序可以被其他信号处理程序中断


