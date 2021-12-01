---
layout: post
title:  Linux中的memorybarrier
date:   2018-10-25
tags:
      - 随笔
---
::: txtcont
https://blog.csdn.net/phenix_lord/article/details/50545364\

-                          基本的memory ordering特征

原则：内核按照最relax的memory ordering(DEC
Alpha)来设计，其内存模型的特点如下：

\

对不同地址的普通内存操作会发生乱序(对同一地址的读写，由cache一致性保证)

\

注：和ARM的内存模型不同的是：write
barrier/memorybarrier不能单独对系统中所有的CPU核心，write
barrier前的写操作先于其后的写操作完成，必须配对使用datadependency(存在address
dependency的两个写操作)、read barrier/memory barrier才能保证。

\

-                          compiler barrier

这种memorybarrier与CPU的内存模型无关，是通知编译器在优化过程中，保证该barrier前的内存操作语句和其后的内存操作语句发生乱序。也就是该barrier之前的内存操作语句对应的机器指令不会出现在该barrier之后的内存操作语句对应的机器指令之后，反之亦然。

\

 

\

在LINUX内核中对应的接口是barrier()，其根据不同的编译器有不同的定义。

\

-                          CPU memory barrier

这里描述的是Linux内核的接口的基本要求，实际实现过程中其memory
order作用可能有加强，需要根据在对于架构下内核代码使用的指令来判断。比如对于ARM下的LOCK指令的实现使用了smp_mb，其作用强于本身的定义。以下描述的所有barrier，确保的是按照program
order
在barrier接口前后的对应类型的内存操作的相对顺序，并不意味着barrier接口的执行完成，barrier接口前面的内存的操作就完成了(一些体系结构有相关的指令，比如ARM的DSB指令)。虽然在X86和ARM环境，所有的barrier接口都具有cumulative属性，但是内核只保证General
barrier的cumulative属性。除data dependency barrier以外的其他CPU memory
barrier都隐含了compiler barrier，不过对于data dependency
barrier的使用场景，编译器一般也不会发送乱序。

\

 

\

-            Write barrier

基本作用：和Readbarrier/memory barrier配合使用，保证program order在Write
barrier之前的写操作不会和其后的写操作发生乱序，Linux内核不保证Writebarrier有cumulative特性(就是和ARM的DMB类似的传递性，但是ARM和X86都保证)，当然对一些平台上的实现，是有cumulative特性的。其对应的内核接口为wmb()/smp_wmb()，其中的smp_wmb()在单CPU系统中就定义为空，如果不涉及和外设的互斥，smp_wmb()是个不错的选择，否则只能用wmb()。

\

-            Read barrier

基本作用：保证programorder在Read
barrier之前的读操作不会和其后的读操作发生乱序，Linux内核不保证Read
barrier有cumulative特性(就是和ARM的DMB类似的传递性)，当然对一些平台上的实现，是有cumulative特性的。

\

对应的内核接口为rmb()/smp_rmb()

\

-            Data dependency barrier

基本作用：用在存在addressdependency的两个读操作之间，和Writebarrier/memory
barrier配合使用，确保对这两个读操作操作的内存的写操作能够按照program
order可见，是一种弱化的Read
barrier。注：对于X86、ARM等主流系统，存在address
dependency内存操作在和write
barrier配合的时候可以确保对应的写操作按照program
order可见，不需要使用Data dependency
barrier，在X86和ARM架构下，这个接口定义为空。这种barrier仅仅对DEC
Alpha等CPU才定义为使用mb之类的指令。

\

例子：

\

{ A == 1, B == 2, C = 3, P == &A, Q == &C }

\

T1: B = 4;wmb() ;P=&B

\

T2: Q=P ; P=\*Q

\

在没有Data dependencybarrier的情况下，对某些CPU，Q=&B &&
P=2是可能出现的。这是因为虽然wmb确保了在T1上B =4先于
P=&B执行，但是扩散到T2的时候，可能对P的变更先于对B的变更到达T2，也就是单单的wmb不能确保对所有的CPU(PE)，B=4先于P=&B可见。需要注意的是，这里Q=P仍然是先于P=\*Q执行的，没有乱序。这一点和之前的X86、ARM明显不一样。。。

\

 

\

对应接口read_barrier_depends()/smp_read_barrier_depends()

\

 

\

注：可以认为write barrier只能确保CPU按照program order提交写操作，address
dependency能确保CPU按照program
order来提交读操作，但是写操作对读操作的可见顺序需要read barrier/data
dependency barrier来保证？

\

-            General barrier

基本作用：保证programorder在Generalbarrier之前的读写操作不会和其后的读写操作发生乱序，Linux内核保证Generalbarrier有cumulative特性(就是和ARM的DMB类似的传递性，如果在CPU1上看到A操作先于B操作，CPU2上看到B操作先于C操作，CPU3上看到C操作先于D操作；可以推断出CPU3上看到的A操作也先于D操作)，当然对一些平台上的实现，是有cumulative特性的。

\

对应的内核接口为mb()/smp_mb()

\

-            隐含的barrier

spinlock/unlock

其隐含的memorybarrier和ARM的Load acquire-Store
release类似：Lock确保program
order在其后的内存操作，不会先于Lock操作完成；Unlock确保program
order在其前的内存操作，不会后于UnLock操作完成。需要说明的是：Lock操作的memory
barrier保证只有Lock成功后才能保证。

\

A LOCK B UNLOCK C

\

B、C中的操作后于LOCK操作完成，而A、B中的操作先于UNLOCK完成。A中的操作可以在B中的操作之后，但是不能越过UNLOCK到C中的操作之后；同样，C中的操作可以越过UNLOCK到B中的操作之前，但是不能越过LOCK到A中的插在哦之前。

\

irq disable/enable

相当于compiler_barrier()

\

sleep/wakeup func

其隐含了smp_mb()，其隐含的smp_mb()其实是set_current_state()中调用的set_mb接口，其它的使用了该接口的API也都隐含了smp_mb()。

\

atomic操作

一些原子操作具有隐含的memorybarrier，具体查看对应接口的内核实现。

\

-                          MMIO write barrier

对应的内核接口：mmiowb()

\

原因：LOCK/UNLOCK等构成的临界区中执行的对外设的写操作虽然CPU发起操作的顺序得到了保证，但是到达外设后，还是可能会混在一起。比如：

\

    CPU 1               CPU 2

\

    ===============================

\

    spin_lock(Q)

\

    writel(0, ADDR)

\

    writel(1, DATA);

\

    spin_unlock(Q);

\

                    spin_lock(Q);

\

                    writel(4, ADDR);

\

                    writel(5, DATA);

\

                    spin_unlock(Q);

\

到达PCI bridge后的顺序可能为：

\

STORE \*ADDR = 0, STORE \*ADDR = 4, STORE \*DATA = 1, STORE \*DATA = 5。

\

这是因为一些体系结构(比如IA64，典型的X86-64和ARM其实没有这个需求)的PCI
bridge不参与cache coherence
protocol处理而导致了乱序，为了确保想要的临界区，需要在临界区的最后，添加mmiowb()，确保系统中在mmiowb()之前发起的所有wrtie操作在其后发起的write操作之前完成。由于readl操作也能确保写操作完成，故如果在UNLOCK之前有readl操作，效果和mmiowb()一样，并且不要求readl和writel访问的地址一样。

\

因此，如果需要保证writel的相关顺序，且临界区的最后一个操作是writel不是readl,需要使用mmiowb()来确保执行顺序，比如由LOCK/UNLOCK操作构成的临界区，对应使用IRQ-DISABLE/ENABLE完成和ISR串行化的操作的时候也可能需要使用mmiowb()。

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-- 

作者：phenix_lord

来源：CSDN 

原文：https://blog.csdn.net/phenix_lord/article/details/50545364 

版权声明：本文为博主原创文章，转载请附上博文链接！

\
:::
