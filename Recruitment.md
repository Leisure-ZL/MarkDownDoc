### 一、基础

#### 操作系统

##### 操作系统的四个特性 

并发： 同一段时间内多个程序执行(注意区别并行和并发，前者是同一时刻的多个事件，后者是同一时间段内的多个事件) 

共享： 系统中的资源可以被内存中多个并发执行的进线程共同使用 

虚拟： 通过时分复用（如分时系统）以及空分复用（如虚拟内存）技术实现把一个物理实体虚拟为多个 

异步： 系统中的进程是以走走停停的方式执行的，且以一种不可预知的速度推进 



##### 操作系统的主要功能 

处理机管理： 处理机分配都是以进程为单位，所以处理机管理也被看做是进程管理。包括进程控制，进程同步，进程通信和进程调度 

存储器管理（或者内存管理）： 内存分配，内存保护，地址映射，内存扩充 

设备管理： 管理所有外围设备，包括完成用户的IO请求；为用户进程分配IO设备；提高IO设备利用率；提高IO速度；方便IO的使用 

文件管理： 管理用户文件和系统文件，方便使用同时保证安全性。包括：磁盘存储 

空间管理，目录管理，文件读写管理以及文件共享和保护 

提供用户接口： 程序接口（如API）和用户接口（如GUI） 



##### 进程的状态与转换 

![image-20220204095437764](D:\Typora\img\image-20220204095437764.png)

运行状态：进程正在处理机上运行。在单处理机环境下，每一时刻最多只有一个进程处于运行状态。 

就绪状态：进程已处于准备运行的状态，即进程获得了除处理机之外的一切所需资源，一旦得到处理机即可运行。 

阻塞状态，又称等待状态：进程正在等待某一事件而暂停运行，如等待某资源为可用（不包括处理机）或等待输入/输出完成。即使处理机空闲，该进程也不能运行。 

注意区别就绪状态和等待状态： 就绪状态是指进程仅缺少处理机，只要获得处理机资源就立即执行；而等待状态是指进程需要其他资源（除了处理机）或等待某一事件。

就绪状态 **->** 运行状态： 处于就绪状态的进程被调度后，获得处理机资源（分派处理机时间片），于是进程由就绪状态转换为运行状态。 

运行状态 **->** 就绪状态： 处于运行状态的进程在时间片用完后，不得不让出处理机，从而进程由运行状态转换为就绪状态。此外，在可剥夺的操作系统中，当有更高优先级的进程就 、 绪时，调度程度将正执行的进程转换为就绪状态，让更高优先级的进程执行。 

运行状态 **->** 阻塞状态： 当进程请求某一资源（如外设）的使用和分配或等待某一事件的发生（如I/O操作的完成）时，它就从运行状态转换为阻塞状态。进程以系统调用的形式请求操作系统提供服务，这是一种特殊的、由运行用户态程序调用操作系统内核过程的形式。 

阻塞状态 **->** 就绪状态： 当进程等待的事件到来时，如I/O操作结束或中断结束时，中断处理程序必须把相应进程的状态由阻塞状态转换为就绪状态。 



##### 进程与线程的区别 

进程： 进程是进程实体的运行过程，是系统进行资源分配和调度的一个独立单位 （具有动态、并发、独立、异步的特性，以及就绪、执行、阻塞3种状态）；引入进程是为了使多个程序可以并发的执行，以提高系统的资源利用率和吞吐量。 

线程： 是比进程更小的可独立运行的基本单位，可以看做是轻量级的进程（具有轻型实体，独立调度分派单位，可并发执行，共享进程资源等属性）；引入目的是为了减少程序在并发执行过程中的开销，使OS的并发效率更高。 

两者的对比： 

1. 调度方面：在引入线程的OS中，线程是独立的调度和分派单位，而进程作为资源的拥有单位(相当于把未引入线程的传统OS中的进程的两个属性分开了)。由于线程不拥有资源，因此可以显著的提高并发度以及减少切换开销。 

2. 并发性：引入了线程的OS中，进程间可以并发，而且一个进程内部的多个线程之间也是可以并发的，这就使OS具有更好的并发性，有效的提高了系统资源利用率和吞吐量。 

3. 拥有资源：无论OS是否支持线程，进程都是基本的资源拥有单位，线程只拥有很少的基本的资源，但是线程可以访问所隶属的进程的资源（进程的代码段，数据段和所拥有的系统资源如fd） 

4. 系统开销：创建或者撤销进程的时候，系统要为之创建或回收PCB，系统资源等，切换时也需要保存和恢复CPU环境。而线程的切换只需要保存和恢复少量的寄存器，不涉及存储器管理方面的工作，所以开销较小。此外，统一进程中的多个线程由于共享地址空间，所以通信同步等都比较方便。 



##### 进程通信

进程通信是指进程之间的信息交换。PV操作是低级通信方式，髙级通信方式是指以较高的效率传输大量数据的通信方式。高级通信方法主要有以下三个类。 

**共享存储** 

在通信的进程之间存在一块可直接访问的共享空间，通过对这片共享空间进行写/读操作实现进程之间的信息交换。在对共享空间进行写/读操作时，需要使用同步互斥工具（如 P操作、V操作），对共享空间的写/读进行控制。共享存储又分为两种： 

低级方式的共享是基于数据结构的共享；高级方式则是基于存储区的共享。操作系统只负责为通信进程提供可共享使用的存储空间和同步互斥工具，而数据交换则由用户自己安排读/写指令完成。 

需要注意的是，用户进程空间一般都是独立的，要想让两个用户进程共享空间必须通过特殊的系统调用实现，而进程内的线程是自然共享进程空间的。

**消息传递** 

在消息传递系统中，进程间的数据交换是以**格式化的消息(Message)为单位**的。若通信的进程之间不存在可直接访问的共享空间，则必须利用操作系统提供的消息传递方法实现进程通信。进程通过系统提供的发送消息和接收消息两个原语进行数据交换。 

1) 直接通信方式：发送进程直接把消息发送给接收进程，并将它挂在接收进程的消息缓冲队列上，接收进程从消息缓冲队列中取得消息。 

2) 间接通信方式：发送进程把消息发送到某个中间实体中，接收进程从中间实体中取得消息。这种中间实体一般称为信箱，这种通信方式又称为信箱通信方式。该通信方式广泛应用于计算机网络中，相应的通信系统称为电子邮件系统。 

**管道通信** 

管道通信是消息传递的一种特殊方式。所谓“管道”，是**指用于连接一个读进程和一个写进程以实现它们之间通信的一个共享文件**，又名pipe文件。向管道（共享文件）提供输入的发送进程（即写进程），以字符流形式将大量的数据送入（写）管道；而接收管道输出的接收进程（即读进程），则从管道中接收（读）数据。为了协调双方的通信，管道机制必须提供以下三方面的协调能力：互斥、同步和确定对 方的存在。



##### 进程同步

多进程虽然提高了系统资源利用率和吞吐量，但是由于进程的异步性可能造成系统的混乱。进程同步的任务就是对多个相关进程在执行顺序上进行协调，使并发执行的多个进程之间可以有效的共享资源和相互合作，保证程序执行的可再现性 

同步机制需要遵循的原则： 

1. 空闲让进：当没有进程处于临界区的时候，应该许可其他进程进入临界区的申请 

2. 忙则等待：当前如果有进程处于临界区，如果有其他进程申请进入，则必须等待，保证对临界区的互斥访问 

3. 有限等待：对要求访问临界资源的进程，需要在有限时间内进入临界区，防止出现死等 

4. 让权等待：当进程无法进入临界区的时候，需要释放处理机，边陷入忙等 


经典的进程同步问题：生产者-消费者问题；哲学家进餐问题；读者-写者问题 

同步的解决方案：管程，信号量



##### 用户态和核心态

![image-20220204192026593](D:\Typora\img\image-20220204192026593.png)



当程序运行在**3**级特权级上时，就可以称之为运行在用户态，因为这是最低特权级，是普通的用户进程运行的特权级，大部分用户直接面对的程序都是运行在用户态；反之，当程序运行在级特权级上时，就可以称之为运行在内核态。 

虽然用户态下和内核态下工作的程序有很多差别，但最重要的差别就**在于特权级的不同**，即权力的不同。 **运行在用户态下的程序不能直接访问操作系统内核数据结构和程序。** 

当我们在系统中执行一个程序时，大部分时间是运行在用户态下的，在其需要操作系统帮助完成某些它没有权力和能力完成的工作时就会切换到内核态。 

用户态切换到内核态的**3**种方式 

1. 系统调用：内核提供的一组通用的访问接口。其实系统调用本身就是中断，但是软件中断，跟硬中断不同。
2. 异常：如果当前进程运行在用户态，如果这个时候发生了异常事件，就会触发切换。例如：缺页异常。
3. 外设中断：当外设完成用户的请求时，会向CPU发送中断信号。



##### 死锁 

死锁是指多个进程在运行过程中，因为争夺资源而造成的一种僵局，如果没有外力推进，处于僵局中的进程就无法继续执行。 

死锁原因： 

1. 竞争资源：请求同一有限资源的进程数多于可用资源数 

2. 进程推进顺序非法：进程执行中，请求和释放资源顺序不合理，如资源等待链 

死锁产生的必要条件： 

- **互斥条件：**一个资源每次只能被一个进程使用。

- **请求和保持条件：**一个进程因请求资源而阻塞时，对已获得的资源保持不放。

- **不可剥夺条件：**进程已获得的资源，在末使用完之前，不能强行剥夺。

- **循环等待条件：**若干进程之间形成一种头尾相接的循环等待资源关系。

死锁处理： 

**预防死锁**：破坏产生死锁的4个必要条件中的一个或者多个；实现起来比较简单，但是如果限制过于严格会降低系统资源利用率以及吞吐量 

**避免死锁**：在资源的动态分配中，防止系统进入不安全状态(可能产生死锁的状态)-如银行家算法 

**检测死锁**：允许系统运行过程中产生死锁，在死锁发生之后，采用一定的算法进行检测，并确定与死锁相关的资源和进程，采取相关方法清除检测到的死锁。实现难度大

**解除死锁**：与死锁检测配合，将系统从死锁中解脱出来（撤销进程或者剥夺资源）。对检测到的和死锁相关的进程以及资源，通过撤销或者挂起的方式，释放一些资源并将其分配给处于阻塞状态的进程，使其转变为就绪态。实现难度大 



##### 进程调度算法 

**先来先服务调度算法（FCFS）**： 既可以作为作业调度算法也可以作为进程调度算法；按作业或者进程到达的先后顺序依次调度；因此对于长作业比较有利； 

**短作业优先调度算法（SJF）**： 作业调度算法，算法从就绪队列中选择估计时间最短的作业进行处理，直到得出结果或者无法继续执行；缺点：不利于长作业；未考虑作 业的重要性；运行时间是预估的，并不靠谱 ； 

**优先级调度算法**： 优先级调度算法每次从就绪队列中选择优先级最高的进程，将处理机分配给它，使之投入运行。

**高响应比优先调度算法：**高响应比优先调度算法主要用于作业调度，该算法是对FCFS调度算法和SJF调度算法的一种综合平衡，同时考虑每个作业的等待时间和估计的运行时间。在每次进行作业调度时，先计算后备作业队列中每个作业的响应比，从中选出响应比最高的作业投入运行。响应比=（等待时间+要求服务时间）/要求服务时间；

- 当作业的等待时间相同时，则要求服务时间越短，其响应比越高，有利于短作业。
- 当要求服务时间相同时，作业的响应比由其等待时间决定，等待时间越长，其响应比越高，因而它实现的是先来先服务。
- 对于长作业，作业的响应比可以随等待时间的增加而提高，当其等待时间足够长时，其响应比便可升到很高，从而也可获得处理机。克服了饥饿状态，兼顾了长作业。

**时间片轮转调度（RR）**： 按到达的先后对进程放入队列中，然后给队首进程分配CPU时间片，时间片用完之后计时器发出中断，暂停当前进程并将其放到队列尾部，循环 ; 

**多级反馈队列调度算法**： 

设置多个就绪队列，并为各个队列赋予不同的优先级。在优先权越高的队列中，为每个进程所规定的执行时间片就越小。

一个新进程进入内存后，首先放入第一队列的末尾，按照先来先去原则排队等候调度。如果他能在一个时间片中完成，便可撤离；如果未完成，就转入第二队列的末尾，同样等待调度.....如此下去，当一个长作业（进程）从第一队列依次将到第n队列（最后队列）后，便按第n队列时间片轮转运行。

仅当第一队列空闲的时候，调度程序才调度第二队列中的进程运行；仅当第1到（i-1）队列空时，才会调度第i队列中的进程运行，并执行相应的时间片轮转。

如果处理机正在处理第i队列中某进程，又有新进程进入优先权较高的队列，则此新队列抢占正在运行的处理机，并把正在运行的进程放在第i队列的队尾。



![71f5188f353e7cfac03dc806ba547a0b.gif](D:\Typora\img\71f5188f353e7cfac03dc806ba547a0b.gif)



##### 内存连续分配 

主要是指动态分区分配时所采用的几种算法。 动态分区分配又称为可变分区分配，是一种动态划分内存的分区方法。这种分区方法不预先将内存划分，而是在进程装入内存时，根据进程的大小动态地建立分区，并使分区的大小正好适合进程的需要。因此系统中分区的大小和数目是可变的。 

**首次适应(First Fit)算法**： 空闲分区以地址递增的次序链接。分配内存时顺序查找，找到大小能满足要求的第一个空闲分区。 每次都是优先利用低址部分的空闲分区，造成低址部分产生大量的外部碎片。

**最佳适应(Best Fit)算法**： 空闲分区按容量递增形成分区链，找到第一个能满足要求的空闲分区。 产生大量难以利用的外部碎片。

**最坏适应(Worst Fit)算法**： 又称最大适应(Largest Fit)算法，空闲分区以容量递减的次序链接。找到第一个能满足要求的空闲分区，也就是挑选出最大的分区。 当小作业把大空闲分区分小了，那么，大作业就找不到合适的空闲分区。

外部碎片：指的是还没有被分配出去（不属于任何进程），但由于太小了无法分配给申请内存空间的新进程的内存空闲区域。

内部碎片：是处于**（操作系统分配的用于装载某一进程的内存）区域内部**或页面内部**的存储块**。占有这些区域或页面的进程并不使用这个存储块。而在进程占有这块存储块时，系统无法利用它。直到进程释放它，或进程结束时，系统才有可能利用这个存储块。比如有6间仓库目前都是空置的，但是假设我们管理仓库的最小空间单位是间，今天运来了容量为2.5间仓库的货物，那也要占用我们1-3号3间仓库，尽管3号仓库还闲置着一半的空间，但是这半间仓库已经不能再利用了（因为是以间为最小单位么）；这时，我们的仓库中就形成了半间仓库的空间碎片，仓库的有效容量只剩下3间仓库了。





##### 基本分页储存管理方式 

把主存空间划分为大小相等且固定的块，块相对较小，作为主存的基本单位。每个进程也以块为单位进行划分，进程在执行时，以块为单位逐个申请主存中的块空间。

因为程序数据存储在不同的页面中，而页面又离散的分布在内存中，因此需要一个页表来记录逻辑地址和实际存储地址之间的映射关系，以实现从**页号到物理块号的映射**。 

由于页表也是存储在内存中的，因此和不适用分页管理的存储方式相比，访问分页系统中内存数据需要**两次的内存访问**(一次是从内存中访问页表，从中找到指定的物理块号，加上页内偏移得到实际物理地址；第二次就是根据第一次得到的物理地址访问内存取出数据)。 

![image-20220204195259029](D:\Typora\img\image-20220204195259029.png)

为了减少两次访问内存导致的效率影响，分页管理中引入了**快表机制**，包含快表机制的内存管理中，当要访问内存数据的时候，首先将页号在快表中查询，如果查找到说明要访问的页表项在快表中，那么直接从快表中读取相应的物理块号；如果没有找到，那么访问内存中的页表，从页表中得到物理地址，同时将页表中的该映射表项添加到快表中(可能存在快表换出算法)。

在某些计算机中如果内存的逻辑地址很大，将会导致程序的页表项会很多，而页表在内存中是连续存放的，所以相应的就需要较大的连续内存空间。为了解决这个问题，可以采用**两级页表或者多级页表**的方法，其中外层页表一次性调入内存且连续存放，内层页表离散存放。相应的访问内存页表的时候需要一次地址变换，访问逻辑地址对应的物理地址的时候也需要一次地址变换，而且一共需要访问内存3次才 可以读取一次数据。 



##### 基本分段储存管理方式 

分页是为了提高内存利用率，而分段是为了满足程序员在编写代码的时候的一些逻辑需求(比如数据共享，数据保护，动态链接等)。 

分段内存管理当中，**地址是二维的，一维是段号，一维是段内地址**；其中每个段的长度是不一样的，而且每个段内部都是从**0**开始编址的。由于分段管理中，每个段内部是连续内存分配，但是段和段之间是离散分配的，因此也存在一个逻辑地址到物理地址的映射关系，相应的就是**段表机制**。段表中的每一个表项记录了该段在内存中的起始地址和该段的长度。段表可以放在内存中也可以放在寄存器中。 

![image-20220204195810328](D:\Typora\img\image-20220204195810328.png)

访问内存的时候**根据段号和段表项的长度计算当前访问段在段表中的位置，然后访问段表，得到该段的物理地址，根据该物理地址以及段内偏移量就可以得到需要访问的内存**。由于也是两次内存访问，所以分段管理中同样引入了联想寄存器。 

**分段分页方式的比较** 

- 页是信息的物理单位，是出于系统内存利用率的角度提出的离散分配机制；段是信息的逻辑单位，每个段含有一组意义完整的信息，是出于用户角度提出的内存管理机制

- 页的大小是固定的，由系统决定；段的大小是不确定的，由用户决定 

分段其实是80*86系列CPU的legacy。对于现代操作系统来说，由于已经引入了分页，分段更多的是一种历史包袱，而不是能够提供多大实际作用的内存管理机制。Linux实际没有使用分段而只使用了分页。因为这样内存管理会更加简单，并且由于像RISC架构的处理器只对分段提供了非常有限的支持，不使用分段会提高Linux操作系统在不同CPU架构上的可移植性。



##### 虚拟内存 

如果存在一个程序，**所需内存空间超过了计算机可以提供的实际内存**，那么由于该程序无法装入内存所以也就无法运行。单纯的增加物理内存只能解决一部分问题，但是仍然会出现无法装入单个或者无法同时装入多个程序的问题。但是可以从逻辑的角度扩充内存容量，即可解决上述两种问题。 

基于局部性原理，**在程序装入时，可以将程序的一部分装入内存，而将其余部分留在外存，就可以启动程序执行**。在程序执行过程中，当所访问的信息不在内存时，由操作系统将所需要的部分调入内存**,**然后继续执行程序。另一方面，操作系统将内存中暂时不使用的内容换出到外存上，从而腾出空间存放将要调入内存的信息。这样，系统好像为用户提供了一个比实际内存大得多的存储器，称为虚拟存储器。 

虚拟存储器的特征： 

1. 多次性：一个作业可以分多次被调入内存。多次性是虚拟存储特有的属性 

2. 对换性：作业运行过程中存在换进换出的过程(换出暂时不用的数据换入需要的数据) 

3. 虚拟性：虚拟性体现在其从逻辑上扩充了内存的容量(可以运行实际内存需求比物理内存大的应用程序)。虚拟性是虚拟存储器的最重要特征也是其最终目标。虚拟性建立在多次性和对换性的基础上行，多次性和对换性又建立在离散分配的基础上。 



##### 页面置换算法 

**最佳置换算法**： 只具有理论意义的算法，用来评价其他页面置换算法。置换策略是将当前页面中在未来最长时间内不会被访问的页置换出去。 

**先进先出置换算法**： 简单粗暴的一种置换算法，没有考虑页面访问频率信息。每次淘汰最早调入的页面。 

**最近最久未使用算法LRU**： 算法赋予每个页面一个访问字段，用来记录上次页面被访问到现在所经历的时间t，每次置换的时候把t值最大的页面置换出去(实现方面可以采用寄存器或者栈的方式实现)。 

**时钟算法clock(最近未使用算法NRU)**： 页面设置一个访问位，并将页面链接为一个环形队列，页面被访问的时候访问位设置为1。页面置换的时候，如果当前指针所指页面访问为为0，那么置换，否则将其置为0，循环直到遇到一个访问为位0的页面。 

**改进型Clock算法**： 在Clock算法的基础上添加一个修改位，替换时根究访问位和修改位综合判断。优先替换访问位和修改位都是0的页面，其次是访问位为0修改位为1的页面。 

**最少使用算法LFU**： 设置寄存器记录页面被访问次数，每次置换的时候置换当前访问次数最少的。 





#### 计算机网络

##### TCP三次握手 

![image-20220205172741731](D:\Typora\img\image-20220205172741731.png)

![image-20220205172816052](D:\Typora\img\image-20220205172816052.png)

为什么要三次握手？ 

**为了防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误。 （网络中存在延迟的重复分组）**

client发出的第一个连接请求报文段并没有丢失，而是在某个网络结点长时间的滞留了，以致延误到连接释放以后的某个时间才到达server。本来这是一个早已失效的报文段。但server收到此失效的连接请求报文段后，就误认为是client再次发出的一个新的连接请求。于是就向client发出确认报文段，同意建立连接。



##### TCP四次挥手 

![image-20220205173717699](D:\Typora\img\image-20220205173717699.png)

![image-20220205173730957](D:\Typora\img\image-20220205173730957.png)

为什么要四次分手？ 

**可以看作客户端和服务端的两次挥手，也就是客户端和服务端分别释放连接的过程。**

TCP协议是一种面向连接的、可靠的、基于字节流的运输层通信协议。TCP是全双工模式，这就意味着，当主机1发出FIN报文段时，只是表示主机1已经没有数据要发送了，主机1告诉主机2，它的数据已经全部发送完毕了；但是，这个时候主机1还是可以接受来自主机2的数据；当主机2返回ACK报文段时，表示它已经知道主机1没有数据发送了，但是主机2还是可以发送数据到主机1的；当主机2也发送了FIN 报文段时，这个时候就表示主机2也没有数据要发送了，就会告诉主机1，我也没有数据要发送了，之后彼此就会愉快的中断这次TCP连接。 

MSL：报文段最大生存时间，它是任何报文段被丢弃前在网络内的最长时间。 

原因： 

- 保证TCP协议的全双工连接能够可靠关闭 

- 保证这次连接的重复数据段从网络中消失 

第一点：如果主机1直接CLOSED了，那么由于IP协议的不可靠性或者是其它网络原因，导致主机2没有收到主机1最后回复的ACK。那么主机2就会在超时之后继续发送FIN，此时由于主机1已经CLOSED了，就找不到与重发的FIN对应的连接。所以，主机1不是直接进入CLOSED，而是要保持TIME_WAIT状态。当再次收到FIN的时候，能够保证对方收到ACK，最后正确的关闭连接。

第二点：如果主机1直接CLOSED，然后又再向主机2发起一个新连接，我们不能保证这个新连接与刚关闭的连接的端口号是不同的。也就是说有可能新连接和老连接的端口号是相同的。一般来说不会发生什么问题，但是还是有特殊情况出现：假设新连接和已经关闭的老连接端口号是一样的，如果前一次连接的某些数据仍然滞留在网络中（称为Lost Duplicate），这些延迟数据在建立新连接之后才到达主机2， 由于新连接和老连接的端口号是一样的，TCP协议就认为那个延迟的数据是属于新连接的，这样就和真正的新连接的数据包发生混淆了。所以TCP连接要在TIME_WAIT状态等待2倍MSL，保证本次连接的所有数据都从网络中消失。 



##### TCP流量控制 

滑动窗口机制

 TCP滑动窗口技术通过动态改变窗口大小来调节两台主机间数据传输。每个TCP/IP主机支持全双工数据传输，因此每个TCP主机有两个滑动窗口一个用于接收数据，另一个用于发送数据。TCP使用肯定确认技术，其确认号指的是下一个所期待的字节。 假定发送方设备以每一次三个数据包的方式发送数据，也就是说，窗口大小为3。发送方发送序列号为1、2、3的三个数据包，接收方设备成功接收数据包，用序列号4确认。发送方设备收到确认，继续以窗口大小3发送数据。当接收方设备要求降低或者增大网络流量时，可以对窗口大小进行减小或者增加，本例降低窗口大小为2，每一次发送两个数据包。当接收方设备要求窗口大小为0，表明接收方已经接收了全部数据，或者接收方应用程序没有时间读取数据，要求暂停发送。发送方接收到携带窗口号为0的确认，停止这一方向的数据传输。



##### TCP拥塞控制 

![img](D:\Typora\img\20190731155254165.png)

慢开始算法： 

在刚刚开始发送报文段时，先把拥塞窗口 cwnd 设置为一个最大报文段 MSS（Maximum Segment Size，最大报文长度）的数值。而在每收到一个对新的报文段的确认后，把拥塞窗口增加至多一个MSS的数值（**底数为2的指数增长规律**）。

慢开始的“慢”并不是指cwnd的增长速率慢，而是指在TCP开始发送报文段时先设置cwnd=1，使得发送方在开始时只发送一个报文段（目的是试探一下网络的拥塞情况），然后再逐渐增大cwnd。 

为了防止拥塞窗口cwnd增长过大引起网络拥塞，还需要设置一个慢开始门限ssthresh状态变量。

拥塞避免 :

让拥塞窗口cwnd缓慢地增大，即每经过一个往返时间**RTT**就把发送方的拥塞窗口**cwnd**加**1**，而不是加倍。

无论在慢开始阶段还是在拥塞避免阶段，只要发送方判断网络出现拥塞（其根据就是没有收到确认），就要**把慢开始门限ssthresh设置为出现拥塞时的发送 方窗口值的一半**（但不能小于2）。然后把拥塞窗口cwnd重新设置为1，执行慢开始算法。 

快重传:

快重传算法首先要求接收方每收到一个失序的报文段后就立即发出重复确认（为的是使发送方及早知道有报文段没有到达对方）而不要等到自己发送数据时才进行捎带确认。 

快重传算法规定，发送方只要一连收到三个重复确认就应当立即重传对方尚未收到的报文段。

快恢复：

当发送方连续收到三个重复确认，就执行“乘法减小”算法，把慢开始门限ssthresh减半。 

与慢开始不同之处是现在**不执行慢开始算法**（即拥塞窗口cwnd现在不设置为1），而是把cwnd值设置为慢开始门限ssthresh减半后的数值，然后开始执行拥塞避免算法（“加法增大”），使拥塞窗口缓慢地线性增大。 

![image-20220205192256887](D:\Typora\img\image-20220205192256887.png)











##### HTTPS

**SSL/TSL原理**

SSL/TLS协议的基本思路是采用公钥加密算法，也就是说，客户端先向服务器端索要公钥，然后用公钥加密信息，服务器收到密文后，用自己的私钥解密。

但是，这里有两个问题。

**（1）如何保证公钥不被篡改？**

> 解决方法：将公钥放在数字证书中。只要证书是可信的，公钥就是可信的。

**（2）公钥加密计算量太大，如何减少耗用的时间？**

> 解决方法：每一次对话（session），客户端和服务器端都生成一个"对话密钥"（session key），用它来加密信息。由于"对话密钥"是对称加密，所以运算速度非常快，而服务器公钥只用于加密"对话密钥"本身，这样就减少了加密运算的消耗时间。

**因此，SSL/TLS协议的基本过程是这样的：**

> （1） 客户端向服务器端索要并验证公钥。
>
> （2） 双方协商生成"对话密钥"。
>
> （3） 双方采用"对话密钥"进行加密通信。



**HTTPS简介** 

HTTPS其实是有两部分组成：HTTP + SSL / TLS，也就是在HTTP上又加了一层处理加密信息的模块。服务端和客户端的信息传输都会通过TLS进行加密，所以传输的数据都是加密后的数据。

![image-20220206151352651](D:\Typora\img\image-20220206151352651.png)

**1. 客户端发起HTTPS请求** 

这个没什么好说的，就是用户在浏览器里输入一个https网址，然后连接到server的443端口。 

**2. 服务端的配置** 

采用HTTPS协议的服务器必须要有一套数字证书，可以自己制作，也可以向组织申请。区别就是自己颁发的证书需要客户端验证通过，才可以继续访问，而使用受信任的公司申请的证书则不会弹出提示页。这套证书其实就是一对公钥和私钥。

**3. 传送证书** 

这个证书其实就是公钥，只是包含了很多信息，如证书的颁发机构，过期时间等等。

**4. 客户端解析证书** 

这部分工作是有客户端的TLS来完成的，首先会验证公钥是否有效，比如颁发机构，过期时间等等，如果发现异常，则会弹出一个警告框，提示证书存在问题。如果证书没有问题，那么就生成一个随即值。然后用证书对该随机值进行加密。

**5. 传送加密信息** 

这部分传送的是用证书加密后的随机值，目的就是让服务端得到这个随机值，**以后客户端和服务端的通信就可以通过这个随机值来进行加密解密了**。 

**6. 服务段加密信息** 

服务端用私钥解密后，得到了客户端传过来的随机值(私钥)，然后把内容通过该值进行**对称加密**。

**7. 传输加密后的信息** 

这部分信息是服务段用私钥加密后的信息，可以在客户端被还原 

**8. 客户端解密信息** 

客户端用之前生成的私钥解密服务段传过来的信息，于是获取了解密后的内容。整个过程第三方即使监听到了数据，也束手无策。 



#### 设计模式

> 注：整理了Android常用设计模式

> 创建型模式

##### 工厂模式

工厂模式（Factory Pattern）是 Java 中最常用的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。在工厂模式中，我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

在工厂方法模式中，工厂父类负责定义创建产品对象的公共接口，而工厂子类则负责生成具体的产品对象，这样做的目的是将产品类的实例化操作延迟到工厂子类中完成，即通过工厂子类来确定究竟应该实例化哪一个具体产品类。 

使用场景：

- 你不想直接**new**这个类的对象，怕以后这个类改变的时候你需要回来改代码，而此时依赖这个类的地方已经到处都是了。

- 这个类的对象构建过程非常复杂，你不愿意将这么复杂的构建过程一遍又一遍的写在需要用到此对象的地方。

- 这个类的对象在构建过程中依赖了很多其他的类，而你无法在调用的地方提供。

角色： 

●Product：抽象产品，工厂方法模式所创建的对象的超类，也就是所有产品类的共同父类或共同拥有的接口。在实际的系统中，这个角色也常常使用抽象类实现。 

●ConcreteProduct：具体产品，这个角色实现了抽象产品（Product）所声明的接口，工厂方法模式所创建的每一个对象都是某个具体产品的实例。 

●Factory：抽象工厂，担任这个角色的是工厂方法模式的核心，任何在模式中创建对象的工厂类必须实现这个接口。在实际的系统中，这个角色也常常使用抽象类实现。

●ConcreteFactory：具体工厂，担任这个角色的是实现了抽象工厂接口的具体Java类。具体工厂角色含有与业务密切相关的逻辑，并且受到使用者的调用以创建具体产品对象。 

实现：

> 需要生产灯泡和灯管

抽象的产品接口: **ILight** 

```java
public interface ILight { 
	void TurnOn(); 
	void TurnOff(); 
} 
```

具体的产品类：**BulbLight** 

```java
public class BulbLight implements ILight { 
	public void TurnOn() { 
		Console.WriteLine("BulbLight turns on."); 
	}

	public void TurnOff() { 
		Console.WriteLine("BulbLight turns off."); 
	} 
} 
```

具体的产品类：**TubeLight** 

```java
public class TubeLight implements ILight { 
	public void TurnOn() { 
		Console.WriteLine("TubeLight turns on."); 
	}

	public void TurnOff() { 
		Console.WriteLine("TubeLight turns off."); 
	} 
} 
```

抽象的工厂类 

```java
public interface ICreator { 
	ILight CreateLight(); 
} 
```

具体的工厂类**:BulbCreator** 

```java
public class BulbCreator implements ICreator { 
	public ILight CreateLight() { 
		return new BulbLight(); 
	} 
} 
```

具体的工厂类**:TubeCreator** 

```java
public class TubeCreator implements ICreator { 
	public ILight CreateLight() { 
		return new TubeLight(); 
	} 
} 
```

客户端调用 

```java
static void Main(string[] args) { 
	//先给我来个灯泡 
	ICreator creator = new BulbCreator(); 
	ILight light = creator.CreateLight(); 
	light.TurnOn(); 
	light.TurnOff(); 

	//再来个灯管看看 
	creator = new TubeCreator(); 
	light = creator.CreateLight(); 
	light.TurnOn(); 
	light.TurnOff(); 
} 
```



**优缺点:**

优点 

①在工厂方法模式中，**工厂方法用来创建客户所需要的产品，同时还向客户隐藏了哪种具体产品类将被实例化这一细节**，用户只需要关心所需产品对应的工厂，无须关心创建细节，甚至无须知道具体产品类的类名。 

②基于工厂角色和产品角色的**多态性设计**是工厂方法模式的关键。它能够使工厂可以自主确定创建何种产品对象，而如何创建这个对象的细节则完全封装在具体工厂内部。工厂方法模式之所以又被称为多态工厂模式，是因为所有的具体工厂类都具有同一抽象父类。 

③使用工厂方法模式的另一个优点是在系统中**加入新产品时，无须修改抽象工厂和抽象产品提供的接口**，无须修改客户端，也无须修改其他的具体工厂和具体产品，而只要添加一个具体工厂和具体产品就可以了。这样，系统的可扩展性也就变得非常好，完全符合**“**开闭原则**”**，这点比简单工厂模式更优秀。 

缺点

①在添加新产品时，**需要编写新的具体产品类，而且还要提供与之对应的具体工厂类，系统中类的个数将成对增加**，在一定程度上增加了系统的复杂度，有更多的类需要编译和运行，会给系统带来一些额外的开销。 

②由于考虑到系统的可扩展性，需要引入抽象层，在客户端代码中均使用抽象层进行定义，增加了系统的抽象性和理解难度，且在实现时可能需要用到DOM、反射等技术，增加了系统的实现难度。 



##### 单例模式

**1.**定义 

作为对象的创建模式，单例模式确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例。这个类称为单例类。 

**2.**特点 

单例类只能有一个实例。 

单例类必须自己创建自己的唯一实例。 

单例类必须给所有其他对象提供这一实例。 

**3.**创建单例模式的方式 

**①**懒汉式，线程不安全 

这种方式是最基本的实现方式，这种实现最大的问题就是不支持多线程。因为没有加锁 synchronized，所以严格意义上它并不算单例模式。这种方式 lazy loading 很明显，不要求线程安全，在多线程不能正常工作。

```java
public class Singleton {  
    private static Singleton instance;  
    private Singleton (){}  
  
    public static Singleton getInstance() {  
    if (instance == null) {  
        instance = new Singleton();  
    }  
    return instance;  
    }  
}
```

**②**懒汉式，线程安全 

这种方式具备很好的 lazy loading，能够在多线程中很好的工作，但是，效率很低，99% 情况下不需要同步。(使用不太频繁)

```java
public class Singleton {  
    private static Singleton instance;  
    private Singleton (){}  
    public static synchronized Singleton getInstance() {  
    if (instance == null) {  
        instance = new Singleton();  
    }  
    return instance;  
    }  
}
```

**③**双重检验锁 

双重检验锁模式（double checked locking pattern），是一种使用同步块加锁的方法。程序员称其为双重检查锁，因为会有两次检查 instance == null，一次是在同步块外，一次是在同步块内。为什么在同步块内还要再检验一次？因为可能会有多个线程一起进入同步块外的 if，如果在同步块内不进行二次检验的话就会生成多个实例了。

```java
public class Singleton {  
    private volatile static Singleton singleton;  //使用volatile，禁止指令重排序优化
    private Singleton (){}  
    public static Singleton getSingleton() {  
        if (singleton == null) {  
            synchronized (Singleton.class) {  
                if (singleton == null) {  
                    singleton = new Singleton();  
                }  
            }  
        }  
    	return singleton;  
    }  
}
```

**④**饿汉式

饿汉式其实是一种比较形象的称谓。既然饿，那么在创建对象实例的时候就比较着急，饿了嘛，于是在装载类的时候就创建对象实例。 

这种方法非常简单，因为单例的实例被声明成 static 和 final 变量了，在第一次加载类到内存中时就会初始化，所以创建实例本身是线程安全的。 

```java
public class Singleton {  
    private static Singleton instance = new Singleton();  
    private Singleton (){}  
    
    public static Singleton getInstance() {  
    	return instance;  
    }  
}
```

缺点是它不是一种懒加载模式（lazy initialization），单例会在加载类后一开始就被初始化，即使客户端没有调用 **getInstance()**方法。 

**⑤**静态内部类

这种写法仍然使用JVM本身机制保证了线程安全问题。由于静态单例对象没有作为Singleton的成员变量直接实例化，因此类加载时不会实例化Singleton，第一次调用 **getInstance()**时将加载内部类**SingletonHolder**，在该内部类中定义了一个static类型的变量INSTANCE ，此时会首先初始化这个成员变量，由Java虚拟机来保证其线程安全性，确保该成员变量只能初始化一次。由于getInstance()方法没有任何线程锁定，因此其性能不会造成任何影响。 

由于 SingletonHolder 是私有的，除了 getInstance() 之外没有办法访问它，因此它是懒汉式的；同时读取实例的时候不会进行同步，没有性能缺陷；也不依赖 JDK 版本

```java
public class Singleton {  
    private static class SingletonHolder {  
   		private static final Singleton INSTANCE = new Singleton();  
    } 
    
    private Singleton (){}  
    public static final Singleton getInstance() {  
   		return SingletonHolder.INSTANCE;  
    }  
}
```

**⑥**枚举 **Enum** 

```java
public enum Singleton {  
    INSTANCE;  
    public void whateverMethod() {  
    }  
}
```

**总结** 

一般来说，单例模式有五种写法：懒汉、饿汉、双重检验锁、静态内部类、枚举。上述所说都是线程安全的实现，上文中第一种方式线程不安全，排除。 

**一般情况下直接使用饿汉式，如果明确要求要懒加载（lazy initialization）倾向于使用静态内部类**。如果涉及到反序列化创建对象时会试着使用枚举的方式来实现单例。 



##### 建造者模式

造者模式(Builder Pattern)：将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。 

建造者模式是一步一步创建一个复杂的对象，它允许用户只通过指定复杂对象的类型和内容就可以构建它们，用户不需要知道内部的具体构建细节。建造者模式属于对象创建型模式。根据中文翻译的不同，建造者模式又可以称为生成器模式。 

使用场景：**当一个类的构造函数参数个数超过4个，而且这些参数有些是可选的参数，考虑使用构造者模式。**

实现：

```java
public class Computer {
    private final String cpu;//必须
    private final String ram;//必须
    private final int usbCount;//可选
    private final String keyboard;//可选
    private final String display;//可选

    private Computer(Builder builder){
        this.cpu=builder.cpu;
        this.ram=builder.ram;
        this.usbCount=builder.usbCount;
        this.keyboard=builder.keyboard;
        this.display=builder.display;
    }
    public static class Builder{
        private String cpu;//必须
        private String ram;//必须
        private int usbCount;//可选
        private String keyboard;//可选
        private String display;//可选

        public Builder(String cup,String ram){
            this.cpu=cup;
            this.ram=ram;
        }

        public Builder setUsbCount(int usbCount) {
            this.usbCount = usbCount;
            return this;
        }
        public Builder setKeyboard(String keyboard) {
            this.keyboard = keyboard;
            return this;
        }
        public Builder setDisplay(String display) {
            this.display = display;
            return this;
        }        
        public Computer build(){
            return new Computer(this);
        }
    }
  //省略getter方法
}
```

使用：

```java
Computer computer=new Computer.Builder("因特尔","三星")
                .setDisplay("三星24寸")
                .setKeyboard("罗技")
                .setUsbCount(2)
                .build();
```

Android中使用：AlertDialog,OkHttp





> 结构型模式

##### 适配器模式

定义：适配器模式把一个类的接口变换成客户端所期待的另一种接口，从而使原本因接口不匹配而无法在一起工作的两个类能够在一起工作。 

使用场景：

- 当需要使用一个现存的类，但它提供的接口与我们系统的接口不兼容，而我们还不能修改它时
- 当多个团队独立开发系统的各功能模块，然后组合在一起，但由于某些原因事先不能确定接口时。

角色： 

- 目标(Target)角色：这就是所期待得到的接口。注意：由于这里讨论的是类适配器模式，因此目标不可以是类。 

- 源(Adapee)角色：现在需要适配的接口。 

- 适配器(Adaper)角色：适配器类是本模式的核心。适配器把源接口转换成目标接口。显然，这一角色不可以是接口，而必须是具体类。 

实现：

> 日志系统需要使用第三方日志接口时，接口不兼容。

1.确定目标接口

系统原来的日志接口如下

```java
public interface LogFactory {
    void debug(String tag,String message);
}
```

2.三方库接口及实现

```java
public interface NewLogger {
    void debug(int priority, String message, Object ... obj);
}
//具体提供日志功能的实现类
public class NewLoggerImp implements NewLogger {
    @Override
    public void debug(int priority, String message, Object... obj) {
        //...
    }
}
```

3.构建适配器类

这个类是适配器模式的核心，通过此类就可以将三方库提供的接口转换为系统的目标接口

```java
public class LogAdapter implements LogFactory {
    private NewLogger newLogger;

    public LogAdapter(NewLogger newLogger) {
        this.newLogger = newLogger;
    }

    @Override
    public void debug(String tag, String message) {
        Objects.requireNonNull(newLogger);
        newLogger.debug(1, message);
    }
}
```

4.客户端使用

```java
public class AdapterClient {
    public void recordLog() {
        LogFactory logFactory = new LogAdapter(new NewLoggerImp());
        logFactory.debug("xxx");
    }
}
```

Android的使用:ListView的Adapter

通过增加Adapter一层来将Item View的操作抽象起来，ListView等集合视图通过Adapter对象获得Item的个数、数据元素、Item View等，从而达到适配各种数据、各种Item视图的效果。因为Item View和数据类型千变万化，Android的架构师们将这些变化的部分交给用 户来处理，通过getCount、getItem、getView等几个方法抽象出来，也就是将ItemView的构造过程交给用户来处理，灵活地运用了适配器模式，达到了无限适配、拥抱变化的目的。 



##### 装饰者模式

定义：装饰模式是在不必改变原类和使用继承的情况下，动态地扩展一个对象的功能。它是通过创建一个包装对象，也就是装饰来包裹真实的对象。

使用场景：

- 需要在运行时动态的给一个对象增加额外的职责时候

- 需要给一个现有的类增加职责，但是又不想通过继承的方式来实现的时候，或者通过继承的方式不现实的时候。

实现：

> 有一个原味咖啡的类 `OriginalCoffee`， 目前的需求就是要**动态**的给这个类的**一些实例**增加一些**额外功能**，此处就是动态的对某些咖啡制作过程增加新的流程，例如加奶，加糖，而有的咖啡却保持原味不变。
>
> 这种需求要是通过继承的方式就不太好实现，因为咖啡制作过程是动态变化的。例如有的需要原味咖啡，有的需要加奶咖啡，有的需要加糖咖啡，而有的需要先加奶再加糖咖啡，而有的需要先加糖再加奶的咖啡。。。这是一个排列组合问题，如果使用类继承的方式实现，**必须预先**将所有可能组合都想清楚，然后生成相应的子类，随着咖啡口味的增多，以及添加顺序的改变，几乎是不可扩展和维护的。

第一步：先声明一个原始对象的接口

```java
public interface ICoffee {
    void makeCoffee();
}
```

第二步：构建我们的原始对象，此处为原味咖啡对象，它实现了`ICoffee`接口。

```java
public class OriginalCoffee implements ICoffee {
    @Override
    public void makeCoffee() {
        System.out.print("原味咖啡 ");
    }
}
```

第三步：构建装饰者抽象基类，它要实现与原始对象相同的接口`ICoffee`，其内部持有一个`ICoffee`类型的引用，用来接收被装饰的对象

```java
public abstract class CoffeeDecorator implements ICoffee {
    private  ICoffee coffee;
    public CoffeeDecorator(ICoffee coffee){
        this.coffee=coffee;
    }

    @Override
    public void makeCoffee() {
        coffee.makeCoffee();
    }
}
```

第四步：构建各种装饰者类，他们都继承至装饰者基类 `CoffeeDecorator`。此处生成了两个，一个是加奶的装饰者,另一个是加糖的装饰者。

```java
public class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(ICoffee coffee) {
        super(coffee);
    }
    @Override
    public void makeCoffee() {
        super.makeCoffee();
        addMilk();
    }
    private void addMilk(){
           System.out.print("加奶 ");
     }    
}
public class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(ICoffee coffee) {
        super(coffee);
    }
    @Override
    public void makeCoffee() {
        super.makeCoffee();
        addSugar();
    }
    private void addSugar(){
           System.out.print("加糖");
     } 
}
```

第五步：客户端使用

```java
public static void main(String[] args) {
        //原味咖啡
        ICoffee coffee=new OriginalCoffee();
        coffee.makeCoffee();
        System.out.println("");

        //加奶的咖啡
        coffee=new MilkDecorator(coffee);
        coffee.makeCoffee();
        System.out.println("");

        //先加奶后加糖的咖啡
        coffee=new SugarDecorator(coffee);
        coffee.makeCoffee();
    }
```

Java中的使用：Java I/O标准库的设计



##### 代理模式

定义：给某一个对象提供一个代理，并由代理对象控制对原对象的引用。代理模式的英 文叫做Proxy或Surrogate，它是一种对象结构型模式。 

角色： 

- Subject: 抽象主题角色 

- Proxy: 代理主题角色 

- RealSubject: 真实主题角色

实现：

抽象对象角色 

```java
public abstract class AbstractObject { 
	//操作 
	public abstract void operation(); 
} 
```

目标对象角色 

```java
public class RealObject extends AbstractObject { 
	@Override 
	public void operation() { 
		//一些操作 
		System.out.println("一些操作"); 
	} 
} 
```

代理对象角色 

```java
public class ProxyObject extends AbstractObject{ 
	RealObject realObject = new RealObject(); 
	@Override 
	public void operation() { 
		//调用目标对象之前可以做相关操作 
		System.out.println("before"); 
		realObject.operation(); 
		//调用目标对象之后可以做相关操作 
		System.out.println("after"); 
	} 
} 
```

客户端

```java
public class Client { 
	public static void main(String[] args) { 
		AbstractObject obj = new ProxyObject(); 
		obj.operation(); 
	} 
} 
```

Android中的使用：Binder

Binder是一个非常典型的代理模式，是一种远程代理，实际上Proxy代理的是另外一个进程中的Stub对象。内部是将接口函数标记为对应的ID，然后根据这个ID来标识目前调用的是哪一个函数。由DESCRIPTOR来作为Token分隔不同的接口调用，另外通过Parcel来写入函数参数和接受函数返回值（Stub端对应接受参数和写入结果）。

Proxy类接收一个IBinder参数，这个参数实际上就是服务端Service中的onBind方法返回的Binder对象在客户端重新打包后的结果，因为客户端无法直接通过这个打包的Binder和服务端通信，因此客户端必须借助Proxy类来和服务端通信，这里Proxy的作用就是代理的作用，客户端所有的请求全部通过Proxy来代理，具体工作流程为：Proxy接收到客户端的请求后，会将客户端的请求参数打包到Parcel对象中，然后将Parcel对象通过它内部持有的Ibinder对象传送到服务端，服务端接收数据、执行方法后返回结果给客户端的Proxy，Proxy解析数据后返回给客户端的真正调用者。很显然，上述所分析的就是典型的代理模式。



##### 外观模式

有些人可能炒过股票，但其实大部分人都不太懂，这种没有足够了解证券知识的情况下做股票是很容易亏钱的，刚开始炒股肯定都会想，如果有个懂行的帮帮手就好，其实基金就是个好帮手，支付宝里就有许多的基金，它将投资者分散的资金集中起来，交由专业的经理人进行管理，投资于股票、债券、外汇等领域，而基金投资的收益归持有者所有，管理机构收取一定比例的托管管理费用。

由于当投资者自己买股票时，由于众多投资者对众多股票的联系太多，反而不利于操作，这在软件中就成为耦合性太高，而有了基金后，就变成众多用户只和基金打交道，关心基金的上涨和下跌，而实际上的操作确是基金经理人与股票和其它投资产品打交道，这就是外观模式。

**外观模式（Facade）**，为子系统中的一组接口提供一个一致的界面，此模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。

> 实例：假设一台电脑，它包含了 CPU（处理器），Memory（内存） ，Disk（硬盘）这几个部件，若想要启动电脑，则先后必须启动 CPU、Memory、Disk。关闭也是如此。
>
> 但是实际上我们在电脑开/关机时根本不需要去操作这些组件，因为电脑已经帮我们都处理好了，并隐藏了这些东西。
>
> 这些组件好比子系统角色，而电脑就是一个外观角色。

实现：

```java
//SubSystem 子系统角色
public class CPU {   
    public void startup(){  
        System.out.println("cpu startup!");  
    }  
    public void shutdown(){  
        System.out.println("cpu shutdown!");  
    }  
}
 
public class Memory {    
    public void startup(){  
        System.out.println("memory startup!");  
    }  
    public void shutdown(){  
        System.out.println("memory shutdown!");  
    }  
} 
 
public class Disk {  
    public void startup(){  
        System.out.println("disk startup!");  
    }  
    public void shutdown(){  
        System.out.println("disk shutdown!");  
    }  
}
```

```java
//Facade 外观角色
public class Computer {  
    private CPU cpu;  
    private Memory memory;  
    private Disk disk;  
 
    public Computer(){  
        cpu = new CPU();  
        memory = new Memory();  
        disk = new Disk();  
    }  
 
    public void startup(){  
        System.out.println("start the computer!");  
        cpu.startup();  
        memory.startup();  
        disk.startup();  
        System.out.println("start computer finished!");  
    }  
 
    public void shutdown(){  
        System.out.println("begin to close the computer!");  
        cpu.shutdown();  
        memory.shutdown();  
        disk.shutdown();  
        System.out.println("computer closed!");  
    }  
}
```





> 行为型模式

##### 命令模式

定义：将一个请求封装成一个对象，从而让用户使用不同的请求把客户端参数化；对请求排队或记录日志，以及支持可撤销的操作。

命令模式是对命令的封装。命令模式把发出命令的责任和执行命令的责任分割开，委派给不同的对象。 

每一个命令都是一个操作：请求的一方发出请求要求执行一个操作；接收的一方收到请求，并执行操作。命令模式允许请求的一方和接收的一方独立开来，使得请求的一方不必知道接收请求的一方的接口，更不必知道请求是怎么被接收，以及操作是否被执行、何时被执行，以及是怎么被执行的。

使用场景：

- 当需要将各种执行的动作抽象出来，使用时通过不同的参数来决定执行哪个对象
- 当某个或者某些操作需要支持撤销的场景
- 当要对操作过程记录日志，以便后期通过日志将操作过程重新做一遍时
- 当某个操作需要支持事务操作的时候

角色： 

- 客户端(Client)角色：创建请求者，接收者以及命令对象，执行具体逻辑。 

- 命令(Command)角色：声明了一个给所有具体命令类的抽象接口。 

- 具体命令(ConcreteCommand)角色：定义一个接收者和行为之间的弱耦合； 实现execute()方法，负责调用接收者的相应操作。execute()方法通常叫做执行方法。

- 请求者(Invoker)角色：负责调用命令对象执行请求，相关的方法叫做行动方法。

- 接收者(Receiver)角色：负责具体实施和执行一个请求。任何一个类都可以成为接收者，实施和执行请求的方法叫做行动方法。 

实现：

接收者角色类 

```java
public class Receiver { 
/**
* 真正执行命令相应的操作 
*/ 
	public void action(){ 
		System.out.println("执行操作"); 
	} 
} 
```

抽象命令角色类 

```java
public interface Command { 
/**
* 执行方法 
*/ 
	void execute(); 
} 
```

具体命令角色类 

```java
public class ConcreteCommand implements Command { 
	//持有相应的接收者对象 
	private Receiver receiver = null; 

	public ConcreteCommand(Receiver receiver){ 
		this.receiver = receiver; 
	}
    
	@Override 
	public void execute() { 
		//通常会转调接收者对象的相应方法，让接收者来真正执行功能 
		receiver.action(); 
	} 
} 
```

请求者角色类 

```java
public class Invoker { 
/*
 持有命令对象 
*/ 
	private Command command = null; 

	public Invoker(Command command){ 
		this.command = command; 
	}

/*
* 行动方法 
*/ 
	public void action(){ 
		command.execute(); 
	} 
} 
```

客户端角色类 

```java
public class Client { 
	public static void main(String[] args) { 
		//创建接收者 
		Receiver receiver = new Receiver(); 
		//创建命令对象，设定它的接收者 
		Command command = new ConcreteCommand(receiver); 
		//创建请求者，把命令对象设置进去 
		Invoker invoker = new Invoker(command); 
		//执行方法 
		invoker.action(); 
	} 
} 
```



##### 观察者模式

定义：观察者模式定义了一种一对多的依赖关系，让多个观察者对象同时监听某一个主题对象。这个主题对象在状态上发生变化时，会通知所有观察者对象，使它们能够自动更新自己。

使用场景：一个对象（目标对象）的状态发生改变，所有的依赖对象（观察者对象）都将得到通知，进行广播通知。

- 一个抽象模型有两个方面，其中一个方面依赖于另一个方面。将这些方面封装在独立的对象中使它们可以各自独立地改变和复用。
- 一个对象的改变将导致其他一个或多个对象也发生改变，而不知道具体有多少对象将发生改变，可以降低对象之间的耦合度。
- 一个对象必须通知其他对象，而并不知道这些对象是谁。
- 需要在系统中创建一个触发链，A对象的行为将影响B对象，B对象的行为将影响C对象……，可以使用观察者模式创建一种链式触发机制。

实现：

> 输出不同进制数

创建 Subject 类。

```java
public class Subject {
   
   private List<Observer> observers 
      = new ArrayList<Observer>();
   private int state;
 
   public int getState() {
      return state;
   }
 
   public void setState(int state) {
      this.state = state;
      notifyAllObservers();
   }
 
   public void attach(Observer observer){
      observers.add(observer);      
   }
 
   public void notifyAllObservers(){
      for (Observer observer : observers) {
         observer.update();
      }
   }  
}
```

创建 Observer 类。

```java
public abstract class Observer {
   protected Subject subject;
   public abstract void update();
}
```

创建实体观察者类。

```java
public class BinaryObserver extends Observer{
 
   public BinaryObserver(Subject subject){
      this.subject = subject;
      this.subject.attach(this);
   }
 
   @Override
   public void update() {
      System.out.println( "Binary String: " 
      + Integer.toBinaryString( subject.getState() ) ); 
   }
}
```

```java
public class OctalObserver extends Observer{
 
   public OctalObserver(Subject subject){
      this.subject = subject;
      this.subject.attach(this);
   }
 
   @Override
   public void update() {
     System.out.println( "Octal String: " 
     + Integer.toOctalString( subject.getState() ) ); 
   }
}
```

```java
public class HexaObserver extends Observer{
 
   public HexaObserver(Subject subject){
      this.subject = subject;
      this.subject.attach(this);
   }
 
   @Override
   public void update() {
      System.out.println( "Hex String: " 
      + Integer.toHexString( subject.getState() ).toUpperCase() ); 
   }
}
```

使用 *Subject* 和实体观察者对象。

```java
public class ObserverPatternDemo {
   public static void main(String[] args) {
      Subject subject = new Subject();
 
      new HexaObserver(subject);
      new OctalObserver(subject);
      new BinaryObserver(subject);
 
      System.out.println("First state change: 15");   
      subject.setState(15);
      System.out.println("Second state change: 10");  
      subject.setState(10);
   }
}
```





#### SQL语句

**创建表**

```sql
CREATE TABLE Student 
( 
	Id INT NOT NULL UNIQUE PRIMARY KEY, 
	Name VARCHAR(20) NOT NULL, 
	Age INT NULL, 
	Gender VARCHAR(4) NULL 
); 
```



**删除表**

```sql
DROP TABLE <表名>;
```



**清空表**

```sql
TRUNCATE TABLE <表名>;
```



**修改表**

```sql
-- 添加学生表`Phone`列 
ALTER TABLE Student ADD Phone VARCHAR(15) NULL; 

-- 删除学生表`Phone`列 
ALTER TABLE Student DROP COLUMN Phone; 

-- 修改学生表`Phone`列 
ALTER TABLE Student MODIFY Phone VARCHAR(13) NULL; 
```



**查询**

```sql
SELECT [ALL | DISTINCT] <目标列表达式>[,<目标列表达式>]… 
	FROM <表名或视图名>[,<表名或视图名>]… 
	[WHERE <条件表达式>] 
	[GROUP BY <列名> [HAVING <条件表达式>]] 
	[ORDER BY <列名> [ASC|DESC]…] 
```

> SQL查询语句的顺序：SELECT、FROM、WHERE、GROUP BY、HAVING、ORDER BY。
>
> SELECT、FROM是必须的，HAVING子句只能与 GROUP BY搭配使用。 

```sql
SELECT * FROM Student 
	WHERE Id>10 
	GROUP BY Age HAVING AVG(Age) > 20 
	ORDER BY Id DESC 
```



**插入**

```sql
-- 插入不存在的数据 
INSERT INTO Student (Name,Age,Gender) VALUES ('Andy',30,'女'); 

-- 将查询的数据插入到数据表中 
INSERT INTO Student (Name,Age,Gender) 
	SELECT Name,Age,Gender FROM Student_T WHERE Id >10; 
```



**更新**

```sql
-- 将Id在(10,100)的Age加1 
UPDATE Student 
	SET Age= Age+1 
	WHERE Id>10 AND Id<100;
```



**删除**

```sql
-- 删除Id小于10的数据记录 
DELETE FROM Student WHERE Id<10;
```



**索引** 

索引是一种特殊的查询表，可以被数据库搜索引擎用来加速数据的检索。简单说来，索引就是指向表中数据的指针。数据库的索引同书籍后面的索引非常相像。 

例如，如果想要查阅一本书中与某个特定主题相关的所有页面，你会先去查询索引（索引按照字母表顺序列出了所有主题），然后从索引中找到一页或者多页与该主题相关的页面。 

索引能够提高 SELECT 查询和 WHERE 子句的速度，但是却降低了包含 UPDATE语句或 INSERT 语句的数据输入过程的速度。索引的创建与删除不会对表中的数据产生影响。 

```sql
-- 建立学生表索引：单一字段Id索引倒序 
CREATE UNIQUE INDEX INDEX_SId ON Student (Id DESC); 

-- 建立学生表索引：多个字段Id、Name索引倒序 
CREATE UNIQUE INDEX INDEX_SId_SName ON Student (Id DESC,Name DESC); 
```

> **UNIQUE**：表明此索引的每一个索引值只对应唯一的数据记录 **CLUSTER**：表明建立的索引是聚集索引 次序：可选ASC(升序)或DESC(降序)，默认ASC 

```sql
-- 删除学生表索引 
INDEX_SId DROP INDEX INDEX_SId;
```



**视图** 

视图无非就是存储在数据库中并具有名字的 SQL 语句，或者说是以预定义的 SQL查询的形式存在的数据表的成分。 

视图可以包含表中的所有列，或者仅包含选定的列。视图可以创建自一个或者多个表，这取决于创建该视图的 SQL 语句的写法。 

视图，一种虚拟的表，允许用户执行以下操作： 

- 以用户或者某些类型的用户感觉自然或者直观的方式来组织数据； 

- 限制对数据的访问，从而使得用户仅能够看到或者修改（某些情况下）他们需要的数据； 

- 从多个表中汇总数据，以产生报表。 

创建视图

```sql
CREATE VIEW <视图名> 
	AS SELECT 查询子句 
	[WITH CHECK OPTION]
```

> 查询子句：子查询可以是任何SELECT语句，但是常不允许含有 ORDER BY 子句和 DISTINCT 短语；
>
>  **WITH CHECK OPTION**：表示对UPDATE、INSERT、DELETE操作时要保证更新。 

```sql
CREATE VIEW VIEW_Stu_Man 
	AS SELECT * FROM Student WHERE Gender = '男' 
	WITH CHECK OPTION;
```

删除视图

```sql
DROP VIEW <视图名>;
```



**ORDER BY** 

**ORDER BY** 子句根据一列或者多列的值，按照升序或者降序排列数据。某些数据库默认以升序排列查询结果

```sql
SELECT [ALL | DISTINCT] <目标列表达式>[,<目标列表达式>]… 
	FROM <表名或视图名>[,<表名或视图名>]… 
	[WHERE <条件表达式>] 
	[ORDER BY <列名>] [ASC | DESC]; 
```



**LIKE** 

**LIKE** 子句通过通配符来将一个值同其他相似的值作比较。可以同 LIKE 运算符一起使用的通配符有两个： 

%：替代 0 个或多个字符

_：替代一个字符

```sql
SELECT FROM table_name 
WHERE column LIKE 'XXXX%'
```



**HAVING** 

HAVING 子句使你能够指定过滤条件，从而控制查询结果中哪些组可以出现在最终结果里面。 

WHERE 子句对被选择的列施加条件，而 HAVING 子句则对 GROUP BY 子句所产生的组施加条件。 

```sql
SELECT ID, NAME, AGE, ADDRESS, SALARY 
FROM CUSTOMERS 
GROUP BY age 
HAVING COUNT(age) >= 2;
```



**DISTINCT** 

**DISTINCT** 关键字同 SELECT 语句一起使用，可以去除所有重复记录，只返回唯一项。

```sql
SELECT DISTINCT SALARY 
FROM CUSTOMERS 
ORDER BY SALARY
```



**AND和OR** 

将 N 个条件用 AND 运算符结合在一起。对于 SQL 语句要执行的动作来说——无论是事务还是查询，AND 运算符连接的所有条件都必须为 TRUE。 

```sql
SELECT ID, NAME, SALARY 
FROM CUSTOMERS 
WHERE SALARY > 2000 AND age < 25;
```



对于 SQL 语句要执行的动作来说——无论是事务还是查询，OR 运算符连接的所有条件中只需要有一个为 TRUE 即可。

```sql
SELECT ID, NAME, SALARY 
FROM CUSTOMERS 
WHERE SALARY > 2000 OR age < 25; 
```



**UNION** 

**UNION** 子句/运算符用于将两个或者更多的 SELECT 语句的运算结果组合起来。在使用 UNION 的时候，每个 SELECT 语句必须有相同数量的选中列、相同数量的列表达式、相同的数据类型，并且它们出现的次序要一致，不过长度不一定要相同。

```sql
SELECT Txn_Date FROM Store_Information 
UNION 
SELECT Txn_Date FROM Internet_Sales;
```

UNION ALL 子句： 

UNION ALL 运算符用于将两个 SELECT 语句的结果组合在一起，重复行也包含在内。

INTERSECT子句： 

用于组合两个 SELECT 语句，但是只返回两个 SELECT 语句的结果中都有的行。 

EXCEPT 子句： 

组合两个 SELECT 语句，并将第一个 SELECT 语句的结果中存在，但是第二个SELECT 语句的结果中不存在的行返回。 



**JOIN** 

连接（**JOIN**） 子句用于将数据库中两个或者两个以上表中的记录组合起来。连接通过共有值将不同表中的字段组合在一起。 

SQL 中有多种不同的连接： 

内连接（INNER JOIN）：当两个表中都存在匹配时，才返回行。 

左连接（LEFT JOIN）：返回左表中的所有行，如果左表中行在右表中没有匹配行，则结果中右表中的列返回空值。 

右连接（RIGHT JOIN）：恰与左连接相反，返回右表中的所有行，如果右表中行在左表中没有匹配行，则结果中左表中的列返回空值。 

全连接（FULL JOIN）：返回左表和右表中的所有行。当某行在另一表中没有匹配行，则另一表中的列返回空值 

```sql
SELECT ID, NAME, AMOUNT, DATE 
FROM CUSTOMERS INNER 
JOIN ORDERS 
ON CUSTOMERS.ID = ORDERS.CUSTOMER_ID;
```





### 二、Java

#### 2.1 Java集合

![img](D:\Typora\img\1155586-20191121113020194-1422948337.jpg)

**List集合**

有序列表，允许存放重复的元素；
实现类：

- ArrayList：数组实现，查询快，增删慢，轻量级(线程不安全)；
- LinkedList：双向链表实现，增删快，查询慢 (线程不安全)；
- Vector：数组实现，重量级 (线程安全、使用少)；

**ArrayList**

底层是Object**数组**，所以ArrayList具有数组的**查询速度快**的优点以及**增删速度慢**的缺点。

**LinkedList**

LinkedList是采用**双向循环链表**实现的。利用LinkedList实现栈(stack)、队列(queue)、双向队列(double-ended queue )。 

**查询速度慢**、**增删速度快**；

**Vector**

与ArrayList相似，区别是Vector是重量级的组件，使用使消耗的资源比较多。

在考虑并发的情况下用Vector（保证线程的安全）。

在不考虑并发的情况下用ArrayList（不能保证线程的安全）。



**ArrayList扩容**

当增加数据的时候，如果ArrayList的大小已经不满足需求时，那么就将 数组变为原长度的1.5倍，之后的操作就是把老的数组拷到新的数组里面。



**Set集合**

扩展Collection接口
无序集合，**不允许存放重复**的元素；允许使用null元素；对 add()、equals() 和 hashCode() 方法添加了限制

实现类 :

- HashSet：equals返回true，hashCode返回相同的整数；哈希表；存储的数据是无序的。
- LinkedHashSet：此实现与HashSet的不同之外在于，后者**维护着一个运行于所有条目的双重链接列表**。**存储的数据是有序的**。
- TreeSet：该类实现了Set接口，可以实现排序等功能。

**HashSet类**

HashSet类直接实现了Set接口，其底层其实是包装了一个HashMap去实现的。HashSet采用HashCode算法来存取集合中的元素，因此具有**比较好的读取和查找性能**。

**HashSet的特征:**

- 不仅不能保证元素插入的顺序，而且在元素在以后的顺序中也可能变化（这是由HashSet按HashCode存储对象（元素）决定的，对象变化则可能导致HashCode变化）
- HashSet是线程非安全的
- HashSet元素值可以为NULL

**LinkedHashSet的特征**

LinkedHashSet是HashSet的一个子类，LinkedHashSet也根据HashCode的值来决定元素的存储位置，但同时它还用一个双向链表来维护元素的插入顺序，插入的时候即要计算hashCode又要维护链表，而遍历的时候只需要按链表来访问元素。

底层数据结构采用链表和哈希表共同实现，链表保证了元素的顺序与存储顺序一致，哈希表保证了元素的唯一性。线程不安全，效率高。

**TreeSet的特征**

底层数据结构采用二叉树来实现，元素唯一且已经排好序；唯一性同样需要重写hashCode和equals()方法，二叉树结构保证了元素的有序性。



**Map集合**

Map用于保存具有映射关系的数据，Map里保存着两组数据：key和value，它们都可以使任何引用类型的数据，但key不能重复。所以通过指定的key就可以取出对应的value。

(1) HashMap：它根据键的hashCode值存储数据，大多数情况下可以直接定位到它的值，因而具有很快的访问速度，但遍历顺序却是不确定的。 HashMap最多只允许一条记录的键为null，允许多条记录的值为null。HashMap非线程安全，即任一时刻可以有多个线程同时写HashMap，可能会导致数据的不一致。如果需要满足线程安全，可以用 Collections的synchronizedMap方法使HashMap具有线程安全的能力，或者使用ConcurrentHashMap。

(2) Hashtable：Hashtable是遗留类，很多映射的常用功能与HashMap类似，不同的是它承自Dictionary类，并且是线程安全的，任一时间只有一个线程能写Hashtable，并发性不如ConcurrentHashMap，因为ConcurrentHashMap引入了分段锁。Hashtable不建议在新代码中使用，不需要线程安全的场合可以用HashMap替换，需要线程安全的场合可以用ConcurrentHashMap替换。

(3) LinkedHashMap：LinkedHashMap是HashMap的一个子类，保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录肯定是先插入的，也可以在构造时带参数，按照访问次序排序。

(4) TreeMap：TreeMap实现SortedMap接口，能够把它保存的记录根据键排序，默认是按键值的升序排序，也可以指定排序的比较器，当用Iterator遍历TreeMap时，得到的记录是排过序的。如果使用排序的映射，建议使用TreeMap。在使用TreeMap时，key必须实现Comparable接口或者在构造TreeMap传入自定义的Comparator，否则会在运行时抛出java.lang.ClassCastException类型的异常

![这里写图片描述](D:\Typora\img\20180803205546704)







#### 2.2 HashMap

**2.2.1 问：HashMap 有用过吗？您能给我说说他的主要用途吗？**

答：有用过，我在平常工作中经常会用到 HashMap 这种数据结构，HashMap 是基于 Map 接口实现的一种键-值对<key,value>的存储结构，允许 null 值，同时非有序，非同步(即线程不安全)。HashMap 的底层实现是数组 + 链表 + 红黑树（JDK1.8 增加了红黑树部分）。它存储和查找数据时，是根据键 key 的 hashCode的值计算出具体的存储位置。HashMap 最多只允许一条记录的键 key 为 null，HashMap 增删改查等常规操作都有不错的执行效率，是 ArrayList 和 LinkedList等数据结构的一种折中实现。



**2.2.2 问：您能说说 HashMap 常用操作的底层实现原理吗？如存储 put(K key, V value)，查找get(Object key)，删除 remove(Object key)，修改 replace(K key, V value)等操作.**

答：调用 put(K key, V value)操作添加 key-value 键值对时，进行了如下操作：

1.判断哈希表 Node<K,V>[] table 是否为空或者 null，是则执行 resize()方法进行扩容。

2.根据插入的键值 key 的 hash 值， hash 值 % hash 表长度计算出存储位置 table[i]。如果存储位置没有元素存放，则将新增结点存储在此位置table[i]。如果存储位置已经有键值对元素存在，则判断该位置元素的 hash 值和 key值是否和当前操作元素一致，一致则证明是修改 value 操作，覆盖 value即可。

3.当前存储位置即有元素，又不和当前操作元素一致，则证明此位置table[i]已经发生了 hash 冲突，则通过判断头结点是否是 treeNode，如果是 treeNode 则证明此位置的结构是红黑树，已红黑树的方式新增结点。如果不是红黑树，则证明是单链表，将新增结点插入至链表的最后位置，随后判断当前链表长度是否 大于等于 8，是则将当前存储位置的链表转化为红黑树。遍历过程中如果发现 key 已经存在，则直接覆盖 value。

4.插入成功后，判断当前存储键值对的数量 大于 阈值 threshold 是则扩容。



**2.2.3 问：HashMap 的容量为什么一定要是 2 的 n 次方？**

答：因为调用 put(K key, V value)操作添加 key-value 键值对时，具体确定此元素的位置是通过 hash 值 % 模上 哈希表 Node<K,V>[] table 的长度 hash % length 计算的。但是"模"运算的消耗相对较大，通过位运算 h & (length-1)也可以得到取模后的存放位置，而位运算的运行效率高，但只有 length 的长度是2 的 n 次方时，h & (length-1) 才等价于 h % length。

而且当数组长度为 2 的 n 次幂的时候，不同的 key 算出的 index 相同的几率较小，那么数据在数组上分布就比较均匀，也就是说碰撞的几率小，相对的，查询的时候就不用遍历某个位置上的链表，这样查询效率也就较高了。



**2.2.4 问：HashMap 的负载因子是什么，有什么作用？**

答：负载因子表示哈希表空间的使用程度（或者说是哈希表空间的利用率）。

底层哈希表 Node<K,V>[] table 的容量大小 capacity 为 16，负载因子load factor 为 0.75，则当存储的元素个数 size = capacity 16 * load factor 0.75 等于 12 时，则会触发 HashMap 的扩容机制，调用 resize()方法进行扩容（原来长度两倍）。

当负载因子越大，则 HashMap 的装载程度就越高。也就是能容纳更多的元素，元素多了，发生 hash 碰撞的几率就会加大，从而链表就会拉长，此时的查询效率就会降低。

当负载因子越小，则链表中的数据量就越稀疏，此时会对空间造成浪费，但是此时查询效率高。

我们可以在创建 HashMap 时根据实际需要适当地调整 load factor 的值；如果程序比较关心空间开销、内存比较紧张，可以适当地增加负载因子；如果程序比较关心时间开销，内存比较宽裕则可以适当的减少负载因子。通常情况下，默认负载因子 (0.75) 在时间和空间成本上寻求一种折中，程序员无需改变负载因子的值。



**2.2.5 问：您说 HashMap 不是线程安全的，那如果多线程下，它是如何处理的？并且什么情况下会发生线程不安全的情况？**

答：HashMap 不是线程安全的，如果多个线程同时对同一个 HashMap 更改数据的话，会导致数据不一致或者数据污染。如果出现线程不安全的操作时，HashMap会尽可能的抛出 ConcurrentModificationException 防止数据异常，当我们在对一个 HashMap 进行遍历时，在遍历期间，我们是不能对 HashMap 进行添加，删除等更改数据的操作的，否则也会抛出 ConcurrentModificationException 异常，此为 fail-fast（快速失败）机制。从源码上分析，我们在 put,remove 等更改 HashMap数据时，都会导致 modCount 的改变，当 expectedModCount != modCount 时，则抛出 ConcurrentModificationException。



**2.2.6 问：我们在使用 HashMap 时，选取什么对象作为 key 键比较好，为什么？**

答：我们在使用 HashMap 时，最好选择不可变对象作为 key。例如 String，Integer 等不可变类型作为 key 是非常明智的。

如果 key 对象是可变的，那么 key 的哈希值就可能改变。在 HashMap 中可变对象作为 Key 会造成数据丢失。因为我们再进行 hash & (length - 1)取模运算计算位置查找对应元素时，位置可能已经发生改变，导致数据丢失。



**2.2.7 问：你知道hash的实现吗？为什么要这样实现？** 

在Java 1.8的实现中，是通过hashCode()的高16位异或低16位实现的： (h = k.hashCode()) ^ (h >>> 16) ，主要是从速度、功效、质量来考虑的，这么做 可以在bucket的n比较小的时候，也能保证考虑到高低bit都参与到hash的计算中， 同时不会有太大的开销。



#### 2.3 Java反射

JAVA反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。

![img](D:\Typora\img\20170513133210763-1642236038542)

Java中编译类型有两种：

- **静态编译**：在编译时确定类型，绑定对象即通过。
- **动态编译**：运行时确定类型，绑定对象。动态编译最大限度地发挥了Java的灵活性，体现了多态的应用，可以减低类之间的耦合性。

**Java反射是Java被视为动态（或准动态）语言的一个关键性质**。这个机制允许程序在运行时透过Reflection APIs取得任何一个已知名称的class的内部信息，包括其modifiers（诸如public、static等）、superclass（例如Object）、实现之interfaces（例如Cloneable），也包括fields和methods的所有信息，并可于运行时改变fields内容或唤起methods。

Reflection可以在运行时加载、探知、使用编译期间完全未知的classes。即Java程序可以加载一个运行时才得知名称的class，获取其完整构造，并生成其对象实体、或对其fields设值、或唤起其methods。

反射（reflection）允许静态语言在运行时（runtime）检查、修改程序的结构与行为。
在静态语言中，使用一个变量时，必须知道它的类型。在Java中，变量的类型信息在编译时都保存到了class文件中，这样在运行时才能保证准确无误；换句话说，程序在运行时的行为都是固定的。如果想在运行时改变，就需要反射这东西了。

实现Java反射机制的类都位于java.lang.reflect包中：

1. Class类：代表一个类
2. Field类：代表类的成员变量（类的属性）
3. Method类：代表类的方法
4. Constructor类：代表类的构造方法
5. Array类：提供了动态创建数组，以及访问数组的元素的静态方法

**一句话概括就是使用反射可以赋予jvm动态编译的能力，否则类的元数据信息只能用静态编译的方式实现，例如热加载，Tomcat的classloader等等都没法支持。**



**动态代理**

动态代理：动态代理是指在运行时动态生成代理类。即，代理类的字节码将在运行时生成并载 入当前代理的 ClassLoader。与静态处理类相比，动态类有诸多好处。 

- 不需要为(RealSubject )写一个形式上完全一样的封装类，假如主题接口 （Subject）中的方法很多，为每一个接口写一个代理方法也很麻烦。如果接口有变动，则目标对象和代理类都要修改，不利于系统维护； 

- 使用一些动态代理的生成方法甚至可以在运行时制定代理类的执行逻辑，从而大大提升系统的灵活性

```java
public class DynamicProxyDemo { 
    public static void main(String[] args) { 
    //1.创建目标对象 
    RealSubject realSubject = new RealSubject(); 
    //2.创建调用处理器对象 
    ProxyHandler handler = new ProxyHandler(realSubject); 
    //3.动态生成代理对象 
    Subject proxySubject = (Subject)Proxy.newProxyInstance(RealSubject.class.getClassLoader(), 
                                                           RealSubj ect.class.getInterfaces(),
                                                           handler); 
    //4.通过代理对象调用方法
    proxySubject.request(); 
	} 
}

	/*** 主题接口 */
	interface Subject{
        void request(); 
    }

	/*** 目标对象类 */ 
    class RealSubject implements Subject{ 
        public void request(){ 
            System.out.println("====RealSubject Request===="); 
        } 
    }

	/*** 代理类的调用处理器 */
	class ProxyHandler implements InvocationHandler{
        private Subject subject; public ProxyHandler(Subject subject){
            this.subject = subject; 
        }
        @Override public Object invoke(Object proxy, Method method, Object[] a rgs) throws Throwable {
            //定义预处理的工作，当然你也可以根据 method 的不同进行不同的预处理工作
            System.out.println("====before====");
            //调用RealSubject中的方法
            Object result = method.invoke(subject, args);
            System.out.println("====after===="); return result; 
        } 
    }
                                                         
```

可以看到，我们通过newProxyInstance就产生了一个Subject 的实例，即代理类的 实例，然后就可以通过Subject .request()，就会调用InvocationHandler中的 invoke()方法，传入方法Method对象，以及调用方法的参数，通过Method.invoke调 用RealSubject中的方法的request()方法。同时可以在InvocationHandler中的 invoke()方法加入其他执行逻辑。



#### 2.4 Java泛型

**语法糖** 

语法糖（Syntactic Sugar），也称糖衣语法，是由英国计算机学家Peter.J.Landin发 明的一个术语，**指在计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用**。Java中最常用的语法糖主要有泛型、变长参数、条件编译、自动拆装箱、内部类等。虚拟机并不支持这些语法，它们在编译阶段就被 还原回了简单的基础语法结构，这个过程成为解语法糖。

**泛型的目的：** Java 泛型就是把一种语法糖，通过泛型使得在编译阶段完成一些类型转换的工作，避免在运行时强制类型转换而出现 ClassCastException ，即类型转换异常。

**泛型的好处 ：**

- 类型安全。类型错误现在在编译期间就被捕获到了，而不是在运行时当作 java.lang.ClassCastException展示出来，将类型检查从运行时挪到编译时有助于开发者更容易找到错误，并提高程序的可靠性。 

- 消除了代码中许多的强制类型转换，增强了代码的可读性。 

- 为较大的优化带来了可能。

**泛型的实质：**允许在定义接口、类时声明类型形参，类型形参在整个接口、 类体内可当成类型使用，几乎所有可使用普通类型的地方都可以使用这种类型形参。



#### 2.5 Java注解

**元数据概念** 

元数据是关于数据的数据。在编程语言上下文中，元数据是添加到程序元素如方 法、字段、类和包上的额外信息。对数据进行说明描述的数据。 

**元数据的作用 **

一般来说，元数据可以用于创建文档（根据程序元素上的注释创建文档），跟踪代 码中的依赖性（可声明方法是重载，依赖父类的方法），执行编译时检查（可声明 是否编译期检测），代码分析。 

1） 编写文档：通过代码里标识的元数据生成文档 

2）代码分析：通过代码里标识的元数据对代码进行分析 

3）编译检查：通过代码里标识的元数据让编译器能实现基本的编译检查 

**Java平台元数据 **

注解Annotation就是java平台的元数据，是 J2SE5.0新增加的功能，该机制允许在 Java 代码中添加自定义注释，并允许通过反射（reflection），以编程方式访问元 数据注释。通过提供为程序元素（类、方法等）附加额外数据的标准方法，元数据 功能具有简化和改进许多应用程序开发领域的潜在能力，其中包括配置管理、框架 实现和代码生成。 Annotation能被用来为程序元素（类、方法、成员变量等）设置元素据。 

**Annotaion**不影响程序代码的执行，无论增加、删除**Annotation**，代码都始终如一 地执行。如果希望让程序中的**Annotation**起一定的作用，只有通过解析工具或编译 工具对**Annotation**中的信息进行解析和处理。



#### 2.6 Java IO

**编码**

Java采用unicode编码，2个字节来表示一个字符，这点与C语言中不同，C语言中采用ASCII，在大多数系统中，一个字符通常占1个字节，但是在0~127整数之间的 字符映射，unicode向下兼容ASCII。而Java采用unicode来表示字符，一个中文或英文字符的unicode编码都占2个字节。但如果采用其他编码方式，一个字符占用的字节数则各不相同。



**File类**

File类是java.io包下代表与平台无关的文件和目录，也就是说，如果希望在程序中操作文件和目录，都可以通过File类来完成。



**IO流**

**输入流和输出流** 

根据数据流向不同分为：输入流和输出流。 

输入流:只能从中读取数据，而不能向其写入数据。 

输出流：只能向其写入数据，而不能从中读取数据。 

**字节流和字符流**

字节流和字符流和用法几乎完全一样，区别在于字节流和字符流所操作的数据单元 不同。 

字符流的由来： 因为数据编码的不同，而有了对字符进行高效操作的流对象。本质 其实就是基于字节流读取时，去查了指定的码表。字节流和字符流的区别： 

（1）读写单位不同：字节流以字节（8bit）为单位，字符流以字符为单位，根据码 表映射字符，一次可能读多个字节。 

（2）处理对象不同：字节流能处理所有类型的数据（如图片、avi等），而字符流 只能处理字符类型的数据。 

注：只要是处理纯文本数据，就优先考虑使用字符流。 除此之外都使用字节流。 

**节点流和处理流** 

按照流的角色来分，可以分为节点流和处理流。 

可以从/向一个特定的IO设备（如磁盘、网络）读/写数据的流，称为节点流，节点流也被成为低级流。 

处理流是对一个已存在的流进行连接或封装，通过封装后的流来实现数据读/写功 能，处理流也被称为高级流。 

```java
//节点流，直接传入的参数是IO设备 
FileInputStream fis = new FileInputStream("test.txt"); 
//处理流，直接传入的参数是流对象
BufferedInputStream bis = new BufferedInputStream(fis);
```

当使用处理流进行输入/输出时，程序并不会直接连接到实际的数据源，没有和实际的输入/输出节点连接。使用处理流的一个明显好处是，只要使用相同的处理流，程序就可以采用完全相同的输入/输出代码来访问不同的数据源，随着处理流所包装节点流的变化，程序实际所访问的数据源也相应地发生变化。

**IO流的四大基类**

![image-20220116154629503](D:\Typora\img\image-20220116154629503.png)





#### 2.7 Java异常

Java异常是Java提供的一种识别及响应错误的一致性机制。 

Java异常机制可以使程序中异常处理代码和正常业务代码分离，保证程序代码更加优雅，并提高程序健壮性。在有效使用异常的情况下，异常能清晰的回答what, where, why这3个问题：异常类型回答了“什么”被抛出，异常堆栈跟踪回答了“在哪“抛出，异常信息回答了“为什么“会抛出。 

Java异常机制用到的几个关键字：**try**、**catch**、**finally**、**throw**、**throws**。 

• **try** -- 用于监听。将要被监听的代码(可能抛出异常的代码)放在try语句块之内，当try语句块内发生异常时，异常就被抛出。 

• **catch** -- 用于捕获异常。catch用来捕获try语句块中发生的异常。 

• **finally** -- finally语句块**总是会被执行**。**它主要用于回收在try块里打开的物力资源 (如数据库连接、网络连接和磁盘文件)。只有finally块，执行完成之后，才会回来执行try或者catch块中的return或者throw语句，如果finally中使用了return或者throw等 终止方法的语句，则就不会跳回执行，直接停止。** 

• **throw** -- 用于抛出异常。 

• **throws** -- 用在方法签名中，用于声明该方法可能抛出的异常。 



#### 2.8 Java抽象类和接口的区别

接口和抽象类的概念不一样。接口是对动作的抽象，抽象类是对根源的抽象。从设计理念上，接口反映的是 **“like-a”** 关系，抽象类反映的是 **“is-a”** 关系。 抽象类表示的是，这个对象是什么。接口表示的是，这个对象能做什么。比如，男人，女人，这两个类，他们的抽象类是人。说明，他们都是人。 人可以吃东西，狗也可以吃东西，你可以把“吃东西”定义成一个接口，然后让这些类去实现它. 所以，在高级语言上，一个类只能继承一个类（抽象类）(正如人不可能 同时是生物和非生物)，但是可以实现多个接口(吃饭接口、走路接口)。

1. 抽象类和接口都不能直接实例化，如果要实例化，抽象类变量必须指向实现所有抽象方法的子类对象，接口变量必须指向实现所有接口方法的类对象。 

2. 抽象类要被子类继承，接口要被类实现。 

3. 接口里定义的变量只能是公共的静态的常量，抽象类中的变量是普通变量。 

4. 抽象类里可以没有抽象方法。 

5. 接口可以被类多实现（被其他接口多继承），抽象类只能被单继承。 

6. 接口中没有 this 指针，没有构造函数，不能拥有实例字段（实例变量）或实例方法。 

7. 抽象类不能在Java 8 的 lambda 表达式中使用。



#### 2.9 浅拷贝和深拷贝

**浅拷贝** 

浅拷贝是按位拷贝对象，它会创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值；如果属性是内存地址（引用类型），拷贝的就是内存地址 ，因此如果其中一个对象改变了这个地址，就会影响到另一个对象。

![image-20220116163244409](D:\Typora\img\image-20220116163244409.png) 

在上图中，SourceObject有一个int类型的属性 "field1"和一个引用类型属 性"refObj"（引用ContainedObject类型的对象）。当对SourceObject做浅拷贝时， 创建了CopiedObject，它有一个包含"field1"拷贝值的属性"field2"以及仍指向refObj 本身的引用。由于"field1"是基本类型，所以只是将它的值拷贝给"field2"，但是由 于"refObj"是一个引用类型, 所以CopiedObject指向"refObj"相同的地址。**因此对SourceObject中的"refObj"所做的任何改变都会影响到CopiedObject。**



**深拷贝** 

深拷贝会拷贝所有的属性,并拷贝属性指向的动态分配的内存。当对象和它所引用的对象一起拷贝时即发生深拷贝。深拷贝相比于浅拷贝速度较慢并且花销较大。 

![image-20220116163347158](D:\Typora\img\image-20220116163347158.png)

在上图中，SourceObject有一个int类型的属性 "field1"和一个引用类型属性"refObj1"（引用ContainedObject类型的对象）。当对SourceObject做深拷贝 时，创建了CopiedObject，它有一个包含"field1"拷贝值的属性"field2"以及包含"refObj1"拷贝值的引用类型属性"refObj2" 。因此对SourceObject中的"refObj"所做的任何改变都不会影响到CopiedObject 。



**延迟拷贝** 

延迟拷贝是浅拷贝和深拷贝的一个组合，实际上很少会使用。 当最开始拷贝一个对象时，会使用速度较快的浅拷贝，还会使用一个计数器来记录有多少对象共享这个 数据。当程序想要修改原始的对象时，它会决定数据是否被共享（通过检查计数器）并根据需要进行深拷贝。



**如何选择** 

如果对象的属性全是基本类型的，那么可以使用浅拷贝，但是如果对象有引用属性，那就要基于具体的需求来选择浅拷贝还是深拷贝。我的意思是如果对象引用任 何时候都不会被改变，那么没必要使用深拷贝，只需要使用浅拷贝就行了。如果对 象引用经常改变，那么就要使用深拷贝。没有一成不变的规则，一切都取决于具体需求。 



#### Java引用

Java对引用的分类有 Strong reference, SoftReference, WeakReference,PhatomReference 四种。 

![image-20220127175038529](D:\Typora\img\image-20220127175038529.png)



#### 2.10 Java序列化

- 把对象转换为字节序列的过程称为对象的序列化。

- 把字节序列恢复为对象的过程称为对象的反序列化。

  对象的序列化主要有两种用途：
  　　1） 把对象的字节序列永久地保存到硬盘上，通常存放在一个文件中；
  　　2） 在网络上传送对象的字节序列。

ObjectOutputStream代表对象输出流，它的writeObject(Object obj)方法可对参数指定的obj对象进行序列化，把得到的字节序列写到一个目标输出流中。
ObjectInputStream代表对象输入流，它的readObject()方法从一个源输入流中读取字节序列，再把它们反序列化为一个对象，并将其返回。
只有实现了Serializable和Externalizable接口的类的对象才能被序列化。Externalizable接口继承自 Serializable接口，实Externalizable接口的类完全由自身来控制序列化的行为，而仅实现Serializable接口的类可以 采用默认的序列化方式 。
对象序列化包括如下步骤：
1） 创建一个对象输出流，它可以包装一个其他类型的目标输出流，如文件输出流；
2） 通过对象输出流的writeObject()方法写对象。

对象反序列化的步骤如下：
1） 创建一个对象输入流，它可以包装一个其他类型的源输入流，如文件输入流；
2） 通过对象输入流的readObject()方法读取对象。



**serialVersionUID**

虚拟机是否允许反序列化，不仅取决于类路径和功能代码是否一致，一个非常重要的一点是两个类的序列化 ID 是否一致。如果两个类的功能代码完全一致，但是序列化 ID 不同，那么他们无法相互序列化和反序列化。

显式地定义serialVersionUID有两种用途：
1、 在某些场合，希望类的不同版本对序列化兼容，因此需要确保类的不同版本具有相同的serialVersionUID；
2、 在某些场合，不希望类的不同版本对序列化兼容，因此需要确保类的不同版本具有不同的serialVersionUID。



**静态变量序列化**

序列化保存的是对象的状态，静态变量属于类的状态，因此 **序列化并不保存静态变量**。



**transient**

在实际开发过程中，我们常常会遇到这样的问题，这个类的有些属性需要序列化，而其他属性不需要被序列化，打个比方，如果一个用户有一些敏感信息（如密 码，银行卡号等），为了安全起见，不希望在网络操作（主要涉及到序列化操作，本地序列化缓存也适用）中被传输，这些信息对应的变量就可以加上transient关键字。换句话说，这个字段的生命周期仅存于调用者的内存中而不会写到磁盘里持久 化。

反序列化时transient 变量的值被设为初始值，如 int 型的是 0，对象型的是 null。



#### 2.11 Java finally与return执行顺序 

Java中异常捕获机制try...catch...finally块中的finally语句是不是一定会被执行？不是，至少有两 种情况下**finally**语句是不会被执行的： 

（**1**）**try**语句没有被执行到，如在**try**语句之前就返回了，这样**finally**语句就不会执行，这也说明了**finally**语句被执行的必要而非充分条件是：相应的**try**语句一定被执行到。 

（**2**）在**try**块中有 **System.exit(0);** 这样的语句， **System.exit(0);** 是终止**Java**虚拟机**JVM**的，连**JVM**都停止了，所有都结束了，当然**finally**语句也不会被执 行到。

**1. finally**语句在**return**语句执行之后**return**返回之前执行的。 

**2. finally**块中的**return**语句会覆盖**try**块中的**return**返回。

**3.** 如果**finally**语句中没有**return**语句覆盖返回值，那么原来的返回值可能因为**finally**里的修改而改变也可能不变。（取决于变量是基本数据类型还是引用数据类型）

**4. try**块里的**return**语句在异常的情况下不会被执行，这样具体返回哪个看情况。（取决于catch改变返回值还是finally改变，还是同时）

**5.** 当发生异常后，**catch**中的**return**执行情况与未发生异常时**try**中**return**的执行情况完全一样。 

总结：**finally**块的语句在**try**或**catch**中的**return**语句执行之后返回之前执行且**finally**里的修改语句可能影响也可能不影响**try**或**catch**中 **return**已经确定的返回值，若**finally**里也有**return**语句则覆盖**try**或**catch**中的**return**语句直接返回。 



#### 2.12 Java传值还是传址

```java
public class Example {
    String str = new String("good");
    char[] ch = { 'a', 'b', 'c' };
    
    public static void main(String args[]) {
        Example ex = new Example();
        ex.change(ex.str, ex.ch);
        System.out.print(ex.str + " and ");
        System.out.print(ex.ch);
    }

    public void change(String str, char ch[]) {
        str = "test ok";
        ch[0] = 'g';
    }
}
输出：good and gbc
```

在java里没有引用传递，只有值传递这个值指的是实参的地址的拷贝，得到这个拷贝地址后，你可以通过它**修改这个地址的内容**（引用不变），因为此时这个内容的地址和原地址是同一地址，但是你**不能改变这个地址本身使其重新引用其它的对象**，也就是值传递，

**不管是引用数据类型还是基本类型，在函数中都不能改变其实际地址但能改变其中的内容。**



#### 2.13 Java创建线程三种方式

一、继承**Thread**类创建线程类 

（1）定义Thread类的子类，并重写该类的run方法，该run方法的方法体就代表了线程要完成的任务。因此把run()方法称为执行体。 

（2）创建Thread子类的实例，即创建了线程对象。 

（3）调用线程对象的start()方法来启动该线程。 

```java
public class MyThread extends Thread{//继承Thread类
　　public void run(){
　　//重写run方法
　　}
}

public class Main {
　　public static void main(String[] args){
　　　　new MyThread().start();//创建并启动线程
　　}
}
```



二、通过**Runnable**接口创建线程类 

（1）定义runnable接口的实现类，并重写该接口的run()方法，该run()方法的方法体同样是该线程的线程执行体。 

（2）创建 Runnable实现类的实例，并依此实例作为Thread的target来创建Thread对象，该Thread对象才是真正的线程对象。 

（3）调用线程对象的start()方法来启动该线程。 

```java
public class MyThread2 implements Runnable {//实现Runnable接口
　　public void run(){
　　//重写run方法
　　}
}

public class Main {
　　public static void main(String[] args){
　　　　//创建并启动线程
　　　　MyThread2 myThread=new MyThread2();
　　　　Thread thread=new Thread(myThread);
　　　　thread().start();
　　　　//或者    new Thread(new MyThread2()).start();
　　}
}
```



三、通过Callable和Future创建线程 

（1）创建Callable接口的实现类，并实现call()方法，该call()方法将作为线程执行体，并且有返回值。

（2）创建Callable实现类的实例，使用FutureTask类来包装Callable对象，该 FutureTask对象封装了该Callable对象的call()方法的返回值。 

（3）使用FutureTask对象作为Thread对象的target创建并启动新线程。 

（4）调用FutureTask对象的get()方法来获得子线程执行结束后的返回值，调用 get()方法会阻塞线程。

```java
public class Main {
　　public static void main(String[] args){
　　	MyThread3 th=new MyThread3();
　　　//使用Lambda表达式创建Callable对象
　　  //使用FutureTask类来包装Callable对象
　　　FutureTask<Integer> future=new FutureTask<Integer>(
　　　　(Callable<Integer>)()->{
　　　　　　return 5;
　　　　}
　　  );
　　　new Thread(task,"有返回值的线程").start();//实质上还是以Callable对象来创建并启动线程
　　  try{
　　　　System.out.println("子线程的返回值："+future.get());//get()方法会阻塞，直到子线程执行结束才返回
 　　 }catch(Exception e){
　　　　ex.printStackTrace();
　　　}
　　}
}
```

三种方式对比：

采用实现**Runnable**、**Callable**接口的方式创见多线程时，优势是： 

线程类只是实现了Runnable接口或Callable接口，还可以继承其他类。 在这种方式下，**多个线程可以共享同一个target对象，所以非常适合多个相同线程来处理同一份资源的情况**，从而可以将CPU、代码和数据分开，形成清晰的模型，较好地体现了面向对象的思想。 

劣势是： 

编程稍微复杂，如果要访问当前线程，则必须使用Thread.currentThread()方法。

其中，**Runnable**和**Callable**区别是：**Runnable**执行体run()无返回值，而**Callable**执行体call()有返回值；



使用继承**Thread**类的方式创建多线程时优势是：

编写简单，如果需要访问当前线程，则无需使用Thread.currentThread()方法，直接使用this即可获得当前线程。 

劣势是： 

线程类已经继承了Thread类，所以不能再继承其他父类。

**注：推荐使用实现接口方式创建线程**



#### 2.14 Java线程池

通过ThreadPoolExecutor来创建一个线程池。 

```java
public ThreadPoolExecutor(int corePoolSize,
	int maximumPoolSize,
	long keepAliveTime,
	TimeUnit unit,
	BlockingQueue<Runnable> workQueue,
	ThreadFactory threadFactory,
	RejectedExecutionHandler handler)
```

**ThreadPoolExecutor**参数含义 :

**1. corePoolSize** 

线程池中的核心线程数，默认情况下，核心线程一直存活在线程池中，即便他们在线程池中处于闲置状态。除非我们将ThreadPoolExecutor的allowCoreThreadTimeOut属性设为true的时候，这时候处于闲置的核心线程在等待新任务到来时会有超时策略，这个超时时间由keepAliveTime来指定。一旦超过所设置的超时时间，闲置的核心线程就会被终止。 

**2. maximumPoolSize** 

线程池中所容纳的最大线程数，如果活动的线程达到这个数值以后，后续的新任务将会被阻塞。包含核心线程数+非核心线程数。 

**3. keepAliveTime** 

非核心线程闲置时的超时时长，对于非核心线程，闲置时间超过这个时间，非核心线程就会被回收。只有对ThreadPoolExecutor的allowCoreThreadTimeOut属性设 为true的时候，这个超时时间才会对核心线程产生效果。 

**4. unit** 

用于指定keepAliveTime参数的时间单位。他是一个枚举，可以使用的单位有天 （TimeUnit.DAYS），小时（TimeUnit.HOURS），分钟 （TimeUnit.MINUTES），毫秒(TimeUnit.MILLISECONDS)，微秒 (TimeUnit.MICROSECONDS, 千分之一毫秒)和毫微秒(TimeUnit.NANOSECONDS, 千分之一微秒); 

**5. workQueue** 

线程池中保存等待执行的任务的阻塞队列。通过线程池中的execute方法提交的Runable对象都会存储在该队列中。

**6. threadFactory** 

线程工厂，为线程池提供新线程的创建。ThreadFactory是一个接口，里面只有一个newThread方法。 默认为DefaultThreadFactory类。 

**7. handler** 

是RejectedExecutionHandler对象，而RejectedExecutionHandler是一个接口，里面只有一个rejectedExecution方法。当任务队列已满并且线程池中的活动线程已经达到所限定的最大值或者是无法成功执行任务，这时候**ThreadPoolExecutor**会调用**RejectedExecutionHandler**中的**rejectedExecution**方法。在 ThreadPoolExecutor中有四个内部类实现RejectedExecutionHandler接口。在线程池中它默认是**AbortPolicy**，在无法处理新任务时抛出**RejectedExecutionException**异常



**提交任务：**

可以通过execute和submit两种方式来向线程池提交一个任务。 

**execute** 当我们使用execute来提交任务时，由于execute方法没有返回值，所以说我们也就无法判定任务是否被线程池执行成功。 

```java
service.execute(new Runnable() { 
	public void run() { 
		System.out.println("execute方式"); 
	} 
}); 
```

**submit** 

当我们使用submit来提交任务时,它会返回一个future,我们就可以通过这个future来 判断任务是否执行成功，还可以通过future的get方法来获取返回值。如果子线程任 务没有完成，get方法会阻塞住直到任务完成，而使用get(long timeout, TimeUnitunit)方法则会阻塞一段时间后立即返回，这时候有可能任务并没有执行完。 

```java
Future<Integer> future = service.submit(new Callable<Integer>(){
	@Override
	public Integer call() throws Exception {
		System.out.println("submit方式");
		return 2;
	}});

try {
	Integer number = future.get();
} catch (ExecutionException e) {
	// TODO Auto-generated catch block
	e.printStackTrace();
}
```



**线程池关闭** 

调用线程池的 shutdown() 或 shutdownNow() 方法来关闭线程池

shutdown原理：将线程池状态设置成SHUTDOWN状态，然后中断所有没有正在执行任务的线程。 

shutdownNow原理：将线程池的状态设置成STOP状态，然后中断所有任务(包括正在执行的)的线程，并返回等待执行任务的列表。 

中断采用**interrupt**方法，所以无法响应中断的任务可能永远无法终止。 但调用上述的两个关闭之一，isShutdown()方法返回值为true，当所有任务都已关闭，表示线程池关闭完成，则isTerminated()方法返回值为true。当需要立刻中断所有的线程，不一定需要执行完任务，可直接调用shutdownNow()方法。



**线程池执行流程**

![image-20220117160256937](D:\Typora\img\image-20220117160256937.png)



#### 2.15 Java死锁

**死锁条件**

1.互斥条件：一个资源每次只能被一个线程使用。 

2.请求与保持条件：一个线程因请求资源而阻塞时，对已获得的资源保持不放。 

3.不剥夺条件：线程已获得的资源，在未使用完之前，不能强行剥夺。 

4.循环等待条件：若干线程之间形成一种头尾相接的循环等待资源关系。 



在JAVA编程中，有3种典型的死锁类型： 

静态的锁顺序死锁，动态的锁顺序死锁，协作对象之间发生的死锁。

**静态的锁顺序死锁** 

a和b两个方法都需要获得A锁和B锁。一个线程执行a方法且已经获得了A锁，在等 待B锁；另一个线程执行了b方法且已经获得了B锁，在等待A锁。这种状态，就是发生了静态的锁顺序死锁。

解决静态的锁顺序死锁的方法就是：所有需要多个锁的线程，都要以相同的顺序来获得锁。

**动态的锁顺序死锁**

动态的锁顺序死锁是指两个线程调用同一个方法时，传入的参数颠倒造成的死锁。一个线程调用了transferMoney方法并传入参数accountA,accountB；另一个线程调用了transferMoney方法并传入参数accountB,accountA。此时就可能发生在静态的锁顺序死锁中存在的问题，即：第一个线程获得了accountA锁并等待accountB锁，第二个线程获得了accountB锁并等待accountA锁。

动态的锁顺序死锁解决方案如下：使用**System.identifyHashCode**来定义锁的顺序。确保所有的线程都以相同的顺序获得锁。 

**协作对象之间发生的死锁**

有时，死锁并不会那么明显，比如两个相互协作的类之间的死锁，比如一个线程调用了Taxi对象的setLocation方法，另一个线程调用了Dispatcher对象的getImage方法。此时可能会发生，第一个线程持有Taxi对象锁并等待Dispatcher对象锁，另一个线程持有Dispatcher对象锁并等待Taxi对象锁。 

 我们在持有锁的情况下调用了外部的方法，这是非常危险的（可能发生死锁）。为了避免这种危险的情况发生， 我们使用开放调用。如果调用某个外部方法时不需要持有锁，我们称之为开放调用。 

解决协作对象之间发生的死锁：需要使用开放调用，即避免在持有锁的情况下调用外部的方法。 

**在写代码时，要确保线程在获取多个锁时采用一致的顺序。同时，要避免在持有锁的情况下调用外部方法。**



#### 2.16 synchronized**/**ReentrantLock

**synchronized**关键字 

**1.synchronized**简介 

synchronized实现同步的基础：Java中每个对象都可以作为锁。当线程试图访问同步代码时，必须先获得对象锁，退出或抛出异常时必须释放锁。 

Synchronzied实现同步的表现形式分为：代码块同步 和 方法同步。

**2.synchronized**原理 

JVM基于进入和退出 Monitor 对象来实现 代码块同步 和 方法同步 ，两者实现细节不同。 

代码块同步： 在编译后通过将 monitorenter 指令插入到同步代码块的开始处，将 monitorexit 指令插入到方法结束处和异常处，通过反编译字节码可以观察 到。任何一个对象都有一个 monitor 与之关联，线程执行 monitorenter 指令时，会尝试获取对象对应的 monitor 的所有权，即尝试获得对象的锁。 

方法同步： synchronized方法在 method_info结构 有 ACC_synchronized 标记，线程执行时会识别该标记，获取对应的锁，实现方法同步。 

两者虽然实现细节不同，但本质上都是对一个对象的监视器（monitor）的获取。任意一个对象都拥有自己的监视器，当同步代码块或同步方法时，执行方法的线程必须先获得该对象的监视器才能进入同步块或同步方法，没有获取到监视器的线程将 会被阻塞，并进入同步队列，状态变为 BLOCKED 。当成功获取监视器的线程释放了锁后，会唤醒阻塞在同步队列的线程，使其重新尝试对监视器的获取。 



**ReentrantLock**锁 

ReentrantLock，一个可重入的互斥锁，它具有与使用synchronized方法和语句所访问的隐式监视器锁相同的一些基本行为和语义，但功能更强大。

关于ReentrantLock的使用很简单，只需要显示调用，获得同步锁，释放同步锁即 可。

```java
ReentrantLock lock = new ReentrantLock(); //参数默认false，不公平锁 
...
lock.lock(); //如果被其它资源锁定，会在此等待锁释放，达到暂停的效果 
try {
//操作 
} finally { 
	lock.unlock(); //释放锁 
} 
```



**重入锁** 

当一个线程得到一个对象后，再次请求该对象锁时是可以再次得到该对象的锁的。 

具体概念就是：自己可以再次获取自己的内部锁。 

Java里面内置锁(synchronized)和Lock(ReentrantLock)都是可重入的。

```java
public class SynchronizedTest { 
	public void method1() { 
		synchronized (SynchronizedTest.class) { 
			System.out.println("方法1获得ReentrantTest的锁运行了"); 
			method2(); 
		} 
	}

	public void method2() { 
		synchronized (SynchronizedTest.class) { 
			System.out.println("方法1里面调用的方法2重入锁,也正常运行了"); 
		} 
	}

	public static void main(String[] args) { 
		new SynchronizedTest().method1(); 
	} 
} 
```



**公平锁** 

CPU在调度线程的时候是在等待队列里随机挑选一个线程，由于这种随机性所以是无法保证线程先到先得的（synchronized控制的锁就是这种非公平锁）。但这样就会产生饥饿现象，即有些线程（优先级较低的线程）可能永远也无法获取CPU的执行权，优先级高的线程会不断的强制它的资源。那么如何解决饥饿问题呢，这就需要公平锁了。公平锁可以保证线程按照时间的先后顺序执行，避免饥饿现象的产 生。但公平锁的效率比较低，因为要实现顺序执行，需要维护一个有序队列。

ReentrantLock便是一种公平锁，通过在构造方法中传入true就是公平锁，传入false，就是非公平锁。 

```java
public ReentrantLock(boolean fair) { 
	sync = fair ? new FairSync() : new NonfairSync(); 
} 
```



**synchronized**和**ReentrantLock**的比较 

**1.**区别： 

1）Lock是一个接口，而synchronized是Java中的关键字，synchronized是内置的 

语言实现； 

2）synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现 

象发生；而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造 

成死锁现象，因此使用Lock时需要在finally块中释放锁； 

3）Lock可以让等待锁的线程响应中断，而synchronized却不行，使用 

synchronized时，等待的线程会一直等待下去，不能够响应中断； 

4）通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。 

5）Lock可以提高多个线程进行读操作的效率。

总结：**ReentrantLock**相比**synchronized**，增加了一些高级的功能。但也有一定 

缺陷。 

在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而 当竞争资源非常激烈时（即有大量线程同时竞争），此时**ReentrantLock**的性能要远远优于**synchronized** 。

官方表示，他们也更支持synchronize，在未来的版本中还有优化余地，所以还是提倡在synchronized能实现需求的情况下，优先考虑使用synchronized来进行同步。 



#### 2.17 线程间通信的两种方式 

**wait()/notify()** 

Object类中相关的方法有notify方法和wait方法。因为wait和notify方法定义在Object类中，因此会被所有的类所继承。这些方法都是**final**的，即它们都是不能被重写的，不能通过子类覆写去改变它们的行为。 

wait()方法： 让当前线程进入等待，并释放锁。 

wait(long)方法： 让当前线程进入等待，并释放锁，不过等待时间为long，超过这个时间没有对当前线程进行唤醒，将自动唤醒。 

notify()方法： 让当前线程通知那些处于等待状态的线程，当前线程执行完毕后 释放锁，并从其他线程中唤醒其中一个继续执行。 

notifyAll()方法： 让当前线程通知那些处于等待状态的线程，当前线程执行完毕后释放锁，将唤醒所有等待状态的线程。 



**wait()**方法使用注意事项 

①当前的线程必须拥有当前对象的monitor，也即lock，就是锁，才能调用wait()方法，否则将抛出异常java.lang.IllegalMonitorStateException。 

②线程调用wait()方法，释放它对锁的拥有权，然后等待另外的线程来通知它（通知 的方式是notify()或者notifyAll()方法），这样它才能重新获得锁的拥有权和恢复执行。

③要确保调用wait()方法的时候拥有锁，即，wait()方法的调用必须放在synchronized方法或synchronized块中。 



**wait()**与**sleep()**比较 

当线程调用了wait()方法时，它会释放掉对象的锁。Thread.sleep()，它会导致线程睡眠指定的毫秒数，但线程在睡眠的过程中是不会释放掉对象的锁的。 



**notify()**方法使用注意事项 

①如果多个线程在等待，它们中的一个将会选择被唤醒。这种选择是随意的，和具体实现有关。（线程等待一个对象的锁是由于调用了wait()方法）。 

②被唤醒的线程是不能被执行的，需要等到当前线程放弃这个对象的锁，当前线程会在方法执行完毕后释放锁。



**Condition实现等待/通知** 

关键字synchronized与wait()和notify()/notifyAll()方法相结合可以实现等待/通知模式，类似ReentrantLock也可以实现同样的功能，但需要借助于Condition对象。关于Condition实现等待/通知就不详细介绍了，可以完全类比wait()/notify()，基本使用和注意事项完全一致。 

就只简单介绍下类比情况： 

**condition.await()————>lock.wait()** 

**condition.await(long time, TimeUnit unit)————>lock.wait(long timeout)** 

**condition.signal()————>lock.notify()** 

**condition.signaAll()————>lock.notifyAll()** 

特殊之处：**synchronized**相当于整个**ReentrantLock**对象只有一个单一的**Condition**对象情况。而一个**ReentrantLock**却可以拥有多个**Condition**对象，来实现通知部分线程。 



#### 2.18 并发编程

并发编程的三大概念：原子性，有序性，可见性.

**原子性**：即一个操作或者多个操作，要么全部执行，并且执行的过程不会被任何因素打断，要么就都不执行。 

在Java中，对基本数据类型的**变量的读取和赋值操作**是原子性操作，即这些操作是不可被中断的，要么执行，要么不执行。 

Java内存模型只保证了基本读取和赋值是原子性操作，如果要实现更大范围操作的原子性，可以通过**synchronized**和**Lock**来实现。



**可见性**：是指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。 

对于可见性，Java提供了volatile关键字来保证可见性。 当一个共享变量被**volatile**修饰时，它会保证修改的值会立即被更新到主存，当有 其他线程需要读取时，它会去内存中读取新值。 

另外，通过synchronized和Lock也能够保证可见性，synchronized和Lock能保证同一时刻只有一个线程获取锁然后执行同步代码，并且在释放锁之前会将对变量的修改刷新到主存当中。因此可以保证可见性。 



**有序性**：即程序执行的顺序按照代码的先后顺序执行。 

指令重排序，一般来说，处理器为了提高程序运行效率，可能会对输入代码进行优化，它不保证程序中各个语句的执行先后顺序同代码中的顺序一致，但是它会保证程序最终执行结果和代码顺序执行的结果是一致的。 

在Java内存模型中，允许编译器和处理器对指令进行重排序，但是重排序过程不会影响到单线程程序的执行，却会影响到多线程并发执行的正确性。在Java里面，可以通过volatile关键字来保证一定的“有序性”。另外可以通过synchronized和Lock来保证有序性，很显然，synchronized和Lock保证每个时刻是有一个线程执行同步代码，相当于是让线程顺序执行同步代码，自然就保证了有序性



**volatile的应用场景** 

synchronized关键字是防止多个线程同时执行一段代码，那么就会很影响程序执行效率，而volatile关键字在某些情况下性能要优于synchronized，但是要注意volatile关键字是无法替代synchronized关键字的，因为**volatile关键字无法保证操作的原子性**。通常来说，使用volatile必须具备以下2个条件： 

1）对变量的写操作不依赖于当前值 

2）该变量没有包含在具有其他变量的不变式中 



#### 2.18 悲观锁与乐观锁

**悲观锁**：总是假设最坏的情况，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁

实现：synchronized、ReentrantLock



**乐观锁**：总是假设最好的情况，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据。乐观锁适用于读多写少的场景，这样可以提高程序的吞吐量。

实现：CAS



**CAS（compare and swap）**

**CAS** 操作包含三个操作数 **——** 内存位置（**V**）、预期原值（**A**）和新值**(B)**。**执行CAS操作的时候，将内存位置的值与预期原值比较，如果相匹配，那么处理器会自动将该位置值更新为新值。否则，处理器不做任何操作。** 

下面是通过CAS修改变量数据的示例，CAS通过该变量的地址即可获取该变量当前的内存值V。当本轮CAS操作失败后，**会重新读取该变量内存中最新的值并重新计算新值，直到其CAS操作修改变量成功为止**.

```
do{
    1. 读取变量值，记为E。用于写入修改时，判断该变量是否被修改
    2. 使用读到的变量值E，计算该变量的新值,记为U
} while( !CAS(变量内存地址，E，U) )
```



**原理：**

CPU层面上，CAS的比较-写入操作上是通过**cmpxchg**指令去实现完成的。然而不幸的是，cmpxchg指令并不是一个原子操作。即可能会发生这样的场景，线程A在执行cmpxchg指令的过程中，发现当前内存值V与预期值E一致，正准备将新值U写入内存，这个时候另外一个线程B打断了线程A的操作，将该变量修改了。显然这个变量发生了线程安全的问题，为此为了保证cmpxchg指令的原子性，不会被打断，需要在cmpxchg指令前添加一个前缀指令lock。通过对cmpxchg指令进行加锁(总线锁或缓存锁)来保证操作的原子性。通常我们会说CAS算法是一个无锁算法，但其实我们可以看到底层依然是加了锁的，只不过这个锁的粒度是很小的。



**缺陷：**

1.**ABA问题**。因为CAS需要在操作值的时候检查下值有没有发生变化，如果没有 发生变化则更新，但是如果一个值原来是A，变成了B，又变成了A，那么使用CAS进行检查时会发现它的值没有发生变化，但是实际上却变化了。

ABA问题的解决思路就是使用版本号。在变量前面追加上版本号，每次变量更新的时候把版本号加一，那么A－B－A 就会变成1A-2B-3A。

2.**循环时间长开销大**。自旋CAS如果长时间不成功，会给CPU带来非常大的执行开销。 

3.**只能保证一个共享变量的原子操作**。当对一个共享变量执行操作时，我们可以使用循环CAS的方式来保证原子操作，但是对多个共享变量操作时，循环CAS就无法保证操作的原子性，从Java1.5开始JDK提供了AtomicReference类来保证引用对象之间的原子性，你可以把多个变量放在一个对象里来进行CAS操作。



#### 2.19 AQS

**AQS介绍与作用**

**AQS定义了一套多线程访问共享资源的同步器框架，许多同步类实现都依赖于它**

 AQS（AbstractQueuedSynchronizer）就是抽象的队列式的同步器，AQS定义了一套多线程访问共享资源的同步器框架，许多同步类实现都依赖于它，AQS是一个Java提供的底层同步工具类，用一个int类型的变量表示同步状态，并提供了一系列的CAS操作来管理这个同步状态。**AQS的主要作用是为Java中的并发同步组件提供统一的底层支持**，如常用的ReentrantLock/Semaphore/CountDownLatch等等就是基于AQS实现的，用法是通过继承AQS实现其模版方法，然后将子类作为同步组件的内部类。

![image-20220118152952837](D:\Typora\img\image-20220118152952837.png)



**AQS原理**

 同步队列是AQS很重要的组成部分，它是一个双端队列，遵循FIFO原则，主要作用是用来存放在锁上阻塞的线程，当一个线程尝试获取锁时，如果已经被占用，那么当前线程就会被构造成一个Node节点加入到同步队列的尾部，队列的头节点是成功获取锁的节点，当头节点线程释放锁时，会唤醒后面的节点并释放当前头节点的引用。

![img](D:\Typora\img\721070-20170504110246211-10684485.png)

AQS定义两种资源共享方式：**Exclusive**（独占，只有一个线程能执行，如ReentrantLock）和**Share**（共享，多个线程可同时执行，如Semaphore/CountDownLatch）。



**总结**

AQS核心是通过一个共享变量来同步状态，变量的状态由子类去维护，而AQS框架做的是： 

- 线程阻塞队列的维护 

- 线程阻塞和唤醒 

共享变量的修改都是通过Unsafe类提供的CAS操作完成的。 



#### 2.20 BlockingQueue

阻塞队列（BlockingQueue）是一个支持两个附加操作的队列。这两个附加的操作是：在队列为空时，获取元素的线程会等待队列变为非空。当队列满时，存储元素的线程会等待队列可用。阻塞队列常用于生产者和消费者的场景，生产者是往队列里添加元素的线程，消费者是从队列里拿元素的线程。阻塞队列就是生产者存放元素的容器，而消费者也只从容器里拿元素。

阻塞队列提供了四种处理方法：![img](D:\Typora\img\1645058-20190402175753071-1811310426.png)

- 抛出异常：是指当阻塞队列满时候，再往队列里插入元素，会抛出IllegalStateException(“Queue full”)异常。当队列为空时，从队列里获取元素时会抛出NoSuchElementException异常 。
- 返回特殊值：插入方法会返回是否成功，成功则返回true。移除方法，则是从队列里拿出一个元素，如果没有则返回null
- 一直阻塞：当阻塞队列满时，如果生产者线程往队列里put元素，队列会一直阻塞生产者线程，直到拿到数据，或者响应中断退出。当队列空时，消费者线程试图从队列里take元素，队列也会阻塞消费者线程，直到队列可用。
- 超时退出：当阻塞队列满时，队列会阻塞生产者线程一段时间，如果超过一定的时间，生产者线程就会退出。



JDK7提供了7个阻塞队列。分别是：

- ArrayBlockingQueue ：一个由数组结构组成的有界阻塞队列。
- LinkedBlockingQueue ：一个由链表结构组成的有界阻塞队列。
- PriorityBlockingQueue ：一个支持优先级排序的无界阻塞队列。
- DelayQueue：一个使用优先级队列实现的无界阻塞队列。
- SynchronousQueue：一个不存储元素的阻塞队列。
- LinkedTransferQueue：一个由链表结构组成的无界阻塞队列。
- LinkedBlockingDeque：一个由链表结构组成的双向阻塞队列。



**ArrayBlockingQueue**

ArrayBlockingQueue是一个用数组实现的**有界阻塞队列**。此队列按照先进先出（FIFO）的原则对元素进行排序。默认情况下不保证访问者公平的访问队列。

ArrayBlockingQueue内部是采用数组进行数据存储的（属性items），**为了保证线程安全，采用的是ReentrantLock lock，为了保证可阻塞式的插入、删除数据利用的是Condition**，当获取数据的消费者线程被阻塞时会将该线程放置到notEmpty等待队列中，当插入数据的生产者线程被阻塞时，会将该线程放置到notFull等待队列中。

注**:ArrayBlockingQueue**其实是一个循环队列



**LinkedBlockingQueue**

LinkedBlockingQueue是由一个基于链表的阻塞队列；

每个添加到LinkedBlockingQueue队列中的数据都将被封装成Node节点，添加的链表队列中，其中head和last分别指向队列的头结点和尾结点。与ArrayBlockingQueue不同的是，LinkedBlockingQueue内部分别使用了takeLock 和 putLock 对并发进行控制，也就是说，添加和删除操作并不是互斥操作，可以同时进行，这样也就可以大大提高吞吐量

这里如果不指定队列的容量大小，也就是使用默认的Integer.MAX_VALUE，如果存在添加速度大于删除速度时候，有可能会内存溢出，这点在使用前希望慎重考虑。另外，LinkedBlockingQueue对每一个lock锁都提供了一个Condition用来挂起和唤醒其他线程。

LinkedBlockingQueue的入队列的操作，其主要是通过获取到putLock锁来完成，当队列的数量达到最大值，此时会导致线程处于阻塞状态或者返回false(根据具体的方法来看)；如果队列还有剩余的空间，那么此时会新创建出一个Node对象，将其设置到队列的尾部，作为 LinkedBlockingQueue的last元素。



**LinkedBlockingQueue**与**ArrayBlockingQueue**的比较

ArrayBlockingQueue由于其底层基于数组，并且在创建时指定存储的大小，在完成后就会立即在内存分配固定大小容量的数组元素，因此其存储通常有限，故其是一个**“**有界**“**的阻塞队列； 

而LinkedBlockingQueue可以由用户指定最大存储容量，也可以无需指定，如果不指定则最大存储容量将是Integer.MAX_VALUE，即可以看作是一个**“**无界**”**的阻塞队列，由于其节点的创建都是动态创建，并且在节点出队列后可以被GC所回收，因此其具有灵活的伸缩性。

但是由于ArrayBlockingQueue的有界性，因此其能够更好的对于性能进行预测，而LinkedBlockingQueue由于没有限制大小，当任务非常多的时候，不停地向队列中存储，就有可能导致内存溢出的情况发生。其次，ArrayBlockingQueue中在入队列和出队列操作过程中，使用的是同一个lock，所以即使在多核CPU的情况下，其读取和操作的都无法做到并行，而LinkedBlockingQueue的读取和插入操作所使用的锁是两个不同的lock，它们之间的操作互相不受干扰，因此两种操作可以并行完成，故LinkedBlockingQueue的吞吐量要高于ArrayBlockingQueue。



JDK中选用LinkedBlockingQueue作为阻塞队列的原因就在于其无界性。因为线程大小固定的线程池，其线程的数量是不具备伸缩性的，当任务非常繁忙的时候，就势必会导致所有的线程都处于工作状态，如果使用一个有界的阻塞队列来进行处理，那么就非常有可能很快导致队列满的情况发生，从而导致任务无法提交而抛出RejectedExecutionException，而使用无界队列由于其良好的存储容量的伸缩性，可以很好的去缓冲任务繁忙情况下场景，即使任务非常多，也可以进行动态扩容，当任务被处理完成之后，队列中的节点也会被随之被GC回收，非常灵活。 



#### 2.21 ConcurrentHashMap

**Hashtable线程安全但效率低下**

Hashtable容器使用synchronized来保证线程安全，但在线程竞争激烈的情况下Hashtable的效率非常低下。因为当一个线程访问Hashtable的同步方法时，其他线程访问Hashtable的同步方法时，可能会进入阻塞或轮询状态。如线程1使用put进行添加元素，线程2不但不能使用put方法添加元素，并且也不能使用get方法来获取元素，所以竞争越激烈效率越低。



**Java7**

ConcurrentHashMap采用 分段锁的机制，实现并发的更新操作，底层采用数组+链表+红黑树的存储结构。 其包含两个核心静态内部类 Segment和HashEntry。

1. Segment继承**ReentrantLock**用来充当锁的角色，**每个 Segment 对象守护每个散列映射表的若干个桶**。 

2. HashEntry 用来封装映射表的键 / 值对； 

3. 每个桶是由若干个 HashEntry 对象链接起来的链表。 

![image-20220119134112722](D:\Typora\img\image-20220119134112722.png)



**Java8**

JDK1.8的实现已经摒弃了Segment的概念，而是直接用**Node数组+链表+红黑树**的数据结构来实现，并发控制使用**Synchronized和CAS**来操作，整个看起来就像是优化过且线程安全的HashMap。

![img](D:\Typora\img\5220087-63281d7b737f1109.png)



**1. ConcurrentHashMap中变量使用final和volatile修饰有什么用呢？**
Final域使得确保初始化安全性（initialization safety）成为可能，初始化安全性让不可变形对象不需要同步就能自由地被访问和共享。
使用volatile来保证某个变量内存的改变对其他线程即时可见，在配合CAS可以实现不加锁对并发操作的支持。get操作可以无锁是由于Node的元素val和指针next是用volatile修饰的，在多线程环境下线程A修改结点的val或者新增节点的时候是对线程B可见的。

**2.我们可以使用CocurrentHashMap来代替Hashtable吗？**
我们知道Hashtable是synchronized的，但是ConcurrentHashMap同步性能更好，因为它仅仅根据同步级别对map的一部分进行上锁。ConcurrentHashMap当然可以代替HashTable，但是HashTable提供更强的线程安全性。它们都可以用于多线程的环境，但是当Hashtable的大小增加到一定的时候，性能会急剧下降，因为迭代时需要被锁定很长的时间。因为ConcurrentHashMap引入了分割(segmentation)，不论它变得多么大，仅仅需要锁定map的某个部分，而其它的线程不需要等到迭代完成才能访问map。简而言之，在迭代的过程中，**ConcurrentHashMap仅仅锁定map的某个部分，而Hashtable则会锁定整个map。**

**3. ConcurrentHashMap有什么缺陷吗？**
ConcurrentHashMap 是设计为非阻塞的。在更新时会局部锁住某部分数据，但不会把整个表都锁住。同步读取操作则是完全非阻塞的。好处是在保证合理的同步前提下，效率很高。**坏处是严格来说读取操作不能保证反映最近的更新**。例如线程A调用putAll写入大量数据，期间线程B调用get，则只能get到目前为止已经顺利插入的部分数据。

**4. ConcurrentHashMap在JDK 7和8之间的区别**

- JDK1.8的实现降低锁的粒度，JDK1.7版本锁的粒度是基于Segment的，包含多个HashEntry，而JDK1.8锁的粒度就是HashEntry（首节点）
- JDK1.8版本的数据结构变得更加简单，使得操作也更加清晰流畅，因为已经使用synchronized来进行同步，所以不需要分段锁的概念，也就不需要Segment这种数据结构了，由于粒度的降低，实现的复杂度也增加了
- JDK1.8使用红黑树来优化链表，基于长度很长的链表的遍历是一个很漫长的过程，而红黑树的遍历效率是很快的，代替一定阈值的链表，这样形成一个最佳拍档



#### 2.22 Java虚拟机

**Java内存区域**

![img](D:\Typora\img\aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxNy85LzQvZGE3N2Q5MDE0Njc4NmMwY2IzZTE3MGI5YzkzNzZhZTQ_aW1hZ2VWaWV3Mi8wL3cvMTI4MC9oLzk2MC9mb3JtYXQvd2VicC9pZ25vcmUtZXJyb3IvMQ)



**方法区（公有）**： 用户存储已被虚拟机加载的**类信息**，**常量**（final），**静态常量**（static），**常量池**（用户存放编译器生成的各种字面量和符号引用）， 即时编译器编译后的代码等数据。异常状态 OutOfMemoryError 

**堆（公有）**： 是JVM所管理的内存中最大的一块。唯一目的就是存放实例对象，几乎所有的对象实例都在这里分配。Java堆是垃圾收集器管理的主要区域，因此很多时候也被称为“GC堆”。异常状态 OutOfMemoryError ；新生区（ed,from,to）、老年区、永久区；

**虚拟机栈（线程私有）**： 描述的是java方法执行的内存模型：每个方法在执行时都会创建一个栈帧，用户存储局部变量表，操作数栈，动态连接，方法出口等信息。每一个方法从调用直至完成的过程，就对应着一个栈帧在虚拟机栈中入栈到出栈的过程。 对这个区域定义了两种异常状态 OutOfMemoryError StackOverflowError 

**本地方法栈（线程私有）**: 与虚拟机栈所发挥的作用相似。它们之间的区别不过是虚拟机栈为虚拟机执行java方法，而本地方法栈为虚拟机使用到的Native方法服务。**被native修饰的方法会通过JNI（Java Native Interface）连接本地库接口调用本地方法库，最后入栈本地方法栈；**

**程序计数器（线程私有）**： 一块较小的内存，当前线程所执行的字节码的行号指示器。字节码解释器工作时，就是通过改变这个计数器的值来选取下一条需要执行的字节码指令。



**Java类加载器**

启动类加载器（**Bootstrap ClassLoader**）： 由C++语言实现（针对HotSpot）,负责将存放在\lib目录或-Xbootclasspath参数指定的路径中的类库加载到内存中，即负责加载Java的核心类。 

扩展类加载器（**Extension ClassLoader**）： 负责加载\lib\ext目录或java.ext.dirs系统变量指定的路径中的所有类库，即负责加载Java扩展的核心类之外的类。 

应用程序类加载器（**Application ClassLoader**）： 负责加载用户类路径 上的指定类库，我们可以直接使用这个类加载器，通过 

ClassLoader.getSystemClassLoader()方法直接获取。一般情况，如果我们没有自定义类加载器默认就是用这个加载器。

**双亲委派机制**

如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把请求委托给父加载器去完成，依次向上，因此，所 有的类加载请求最终都应该被传递到顶层的启动类加载器中，只有当父加载器在它的搜索范围中没有找到所需的类时，即无法完成该加载，子加载器才会尝试自己去加载该类。 

![image-20220119161552460](D:\Typora\img\image-20220119161552460.png)

这样的好处是不同层次的类加载器具有不同优先级，比如所有Java对象的超级父类java.lang.Object，位于rt.jar，无论哪个类加载器加载该类，最终都是由启动类加载器进行加载，**保证安全**。即使用户自己编写一个java.lang.Object类并放入程序中，虽能正常编译，但不会被加载运行，保证不会出现混乱。 



**Java对象**

**对象的内存布局** 

分为**3**个区域：**对象头，实例数据，对齐填充**。 

对象头：包括两部分信息，第一部分：对象自身的运行时数据，如哈希码，GC分代年龄，锁状态标志，线程持有的锁，偏向线程ID，偏向时间戳等，这部分数据的长度在32位和64位的虚拟机中分别为32 bit和64 bit，官方称它为“Mark Word”。 第二部分：类型指针，即对象指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象是哪个类的实例。如果对象是一个java数组，那在对象头中还必须有一 块用于记录数组长度的数据。 

实例数据：是对象真正存储的有效信息，也是在程序代码中所定义的各种类型的字段内容。 

对齐填充：对齐填充不是必然存在的。HotSpot VM的自动内存管理系统要求对象起始地址必须是8字节的整数倍，也就是说对象的大小必须是8字节的整数倍。而对象头部分正好是8字节的整数倍。因此，当对象实例数据部分没有对齐时，就需要通过对其补充来补全了。



**对象的创建** 

1.虚拟机遇到一个new指令时，首先将去检查这个指令的参数是否能在常量池中定位到一个类的符号引用； 

2.检查这个符号引用代表的类是否已经被加载，解析和初始化过。如果没有，那必须先执行响应的类加载过程； 

3.在类加载检查功通过后，为新生对象分配内存。对象所需的内存大小在类加载完成后便可完全确定。 



**对象的访问定位** 

Java程序需要通过栈上了reference数据来操作堆上的具体对象。目前主流的访问方式有使用句柄和直接指针两种。 

句柄访问：Java堆中会划分出一块内存来作为句柄池，reference中存储的就是对象的句柄地址，而句柄中包含了对实例数据与类型数据的各自具体的地址信息。

![image-20220119152616192](D:\Typora\img\image-20220119152616192.png)

直接指针访问：reference中存储的直接就是对象地址。

![image-20220119152653962](D:\Typora\img\image-20220119152653962.png)



**JVM 堆**
Java堆是被所有线程共享的一块内存区域，所有对象和数组都在堆上进行内存分配。为了进行高效的垃圾回收，虚拟机把堆内存划分成新生代、老年代和永久代（1.8中无永久代，使用metaspace实现）三块区域。

 Heap Memory 又被分为两大区域：

**Young/New Generation 新生代**: 新生对象放置在新生代中，新生代由Eden 与Survivor Space 组成。

**Old/Tenured Generation 老年代**: 老年代用于存放程序中经过几次垃圾回收后还存活的对象。

![img](D:\Typora\img\aHR0cDovL2RsMi5pdGV5ZS5jb20vdXBsb2FkL2F0dGFjaG1lbnQvMDA4OC82MjYyLzZhZjZhMjI0LThiMmQtM2YyMy04YjU4LTc5MjYzY2ZkYTljNC5wbmc)

![img](D:\Typora\img\20200524163240284.png)

JDK1.8以前Java虚拟机将堆内存划分为新生代、老年代和永久代，永久代是HotSpot虚拟机特有的概念（JDK1.8之后为metaspace替代永久代），它采用永久代的方式来实现方法区，其他的虚拟机实现没有这一概念，而且HotSpot也有取消永久代的趋势，在JDK 1.7中HotSpot已经开始了“去永久化”，把原本放在永久代的字符串常量池移出。永久代主要存放常量、类信息、静态变量等数据，与垃圾回收关系不大，新生代和老年代是垃圾回收的主要区域。

**永久代（Permanent Generationn）**：永久代存储类信息、常量、静态变量、即时编译器编译后的代码等数据，对这一区域而言，Java虚拟机规范指出可以不进行垃圾收集，一般而言不会进行垃圾回收。

![img](D:\Typora\img\aHR0cHM6Ly9pbWcyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvOTU1MDkyLzIwMTkwMy85NTUwOTItMjAxOTAzMTMxNjU0Mjc1MjItMTI1MzY2MDMwNi5qcGc)



**永久代和方法区区别：**

1、方法区

　　方法区（Method Area）是jvm规范里面的运行时数据区的一个组成部分，jvm规范中的运行时数据区还包含了：pc寄存器、虚拟机栈、堆、方法区、运行时常量池、本地方法栈。主要用来存储class、运行时常量池、字段、方法、代码、JIT代码等。运行时数据区跟内存不是一个概念，方法区是运行时数据区的一部分。方法区是jvm规范中的一部分，并不是实际的实现，切忌将规范跟实现混为一谈。

2、永久代

　　永久带又叫Perm区，只存在于hotspot jvm中，并且只存在于jdk7和之前的版本中，jdk8中已经彻底移除了永久带，jdk8中引入了一个新的内存区域叫metaspace。并不是所有的jvm中都有永久带，ibm的j9，oracle的JRocket都没有永久带，永久带是实现层面的东西，永久带里面存的东西基本上就是方法区规定的那些东西。

3、区别

　　方法区是规范层面的东西，规定了这一个区域要存放哪些东西，永久带或者是metaspace是对方法区的不同实现，是实现层面的东西。



**GC**

**垃圾收集算法** 

**1.标记-清除算法**

最基础的收集算法是“标记-清除”（Mark-Sweep）算法，如同它的名字一样，算法分为“标记”和“清除”两个阶段。 

①首先标记出所有需要回收的对象 

②在标记完成后统一回收所有被标记的对象。 

不足： 

效率问题：标记和清除两个过程的效率都不高 

空间问题：标记清除之后产生大量不连续的内存碎片，空间碎片太多可能会导致以后程序运行过程中需要分配较大对象时，无法找到足够的连续内存而不得不提前触发另一次垃圾收集动作。 

![image-20220119202915410](D:\Typora\img\image-20220119202915410.png)



**2.复制算法 **

目的： 为了解决效率问题。将可用内存按容量大小划分为大小相等的两块，每次只使用其中的一块。当一块内存使用完了，就将还存活着的对象复制到另一块上面，然后再把已使用过的内存空间一次清理掉。这样使得每次都是对整个半区进行内存回收，内存分配时也就不用考虑内存碎片等复杂情况。 

缺点： 将内存缩小为了原来的一半。 

![image-20220119202944060](D:\Typora\img\image-20220119202944060.png)

现代的商业虚拟机都采用这种收集算法来**回收新生代**，IBM公司的专门研究表明，新生代中对象98%对象是“朝生夕死”的，所以不需要按照1：1的比例来划分内存空间，而是将内存分为较大的**Eden**空间和两块较小的**Survivor**空间，每次使用**Eden**和其中一块**Survivor**（**谁空谁是to**）。**HotSpot**虚拟机中默认**Eden**和**Survivor**的大小比例是**8**：**1**



**3.标记-整理算法 **

复制收集算法在对象存活率较高时，就要进行较多的复制操作，效率就会变低。 根据老年代的特点，提出了“标记-整理”算法。 

标记过程仍然与”标记-清除“算法一样，但后续步骤不是直接对可回收对象进行清理，而是让所有存活的对象都向一端移动，然后直接清理掉边界以外的内存。

![image-20220119203108761](D:\Typora\img\image-20220119203108761.png)



**4.分代收集算法** 

一般是把Java堆分为新生代和老年代，这样就可以根据各个年代的特点采用最适当的收集算法在新生代中，每次垃圾收集时都发现有大批对象死去，只有少量存活，那就选用复制算法。在老年代中，因为对象存活率高、没有额外空间对它进行分配担保，就必须采用“标记-清除”或“标记-整理”算法来进行回收。 



**判断对象存活**

**引用计数法** 

给对象添加一个引用计数器，每当有一个地方引用它时，计数器值就加1；当引用失效时，计数器值就减1；任何时刻计数器为0的对象就是不可能被再使用的。主流的**JVM**里面没有选用引用计数算法来管理内存，其中最主要的原因是它很难解决对象间的互循环引用的问题。 



**可达性分析算法** 

通过一些列的称为“GC Roots”的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径称为引用链，当一个对象到GC Roots没有任何引用链相连时（就是从GC Roots 到这个对象是不可达），则证明此对象是不可用的。所以它们会被判定为可回收对象（例如图B中的对象既是不可达的）。在Java语言中，可以作为GC Roots的对象包括下面几种： 

- 虚拟机栈（栈帧中的本地变量表）中引用的对象； 

- 方法区中类静态属性引用的对象； 

- 方法区中常量引用的对象； 

- 本地方法栈中JNI（即一般说的Native方法）引用的对象； 

总结就是，方法运行时，**方法中引用的对象；类的静态变量引用的对象；类中常量引用的对象；Native方法中引用的对象** 



**Java引用**

ava对引用的概念进行了扩充，将引用分为强引用（StrongReference）、软引用（Soft Reference）、弱引用（Weak Reference）、虚引用 （Phantom Reference）四种，这四种引用强度依次逐渐减弱。 

**强引用**： 就是指在程序代码之中普遍存在的，类似“Object obj = new Object()”这类的引用，只要强引用还存在，垃圾收集器永远不会回收掉被引用的对象。 

**软引用**： 用来描述一些还有用但并非必须的对象。在系统将要发生内存溢出异常之前，将会把这些对象列进回收范围之中进行第二次回收。 

**弱引用**： 用户描述非必须对象的。被弱引用关联的对象只能生存到下一次垃圾收集发生之前。当垃圾收集器工作时，无论当前内存是否足够，都会回收掉只被弱引用关联的对象。 

**虚引用**： 一个对象是否有虚引用存在，完全不会对其生存时间构成影响，也无法通过虚引用来取得一个对象实例。为一个对象设置虚引用的唯一目的就是能在这个对象被收集器回收时刻得到一个系统通知。 



### 三、Android

#### Activity

**Activity生命周期**

**onSaveInstanceState**和**onRestoreInstanceState**。 

在Activity由于异常情况下终止时，系统会调用onSaveInstanceState来保存当前Activity的状态。这个方法的调用是在onStop之前，它和onPause没有既定的时序关系，该方法只在Activity被异常终止的情况下调用。当异常终止的Activity被重建以 后，系统会调用onRestoreInstanceState，并且把Activity销毁时onSaveInstanceState方法所保存的Bundle对象参数同时传递给onRestoreInstanceState和onCreate方法。因此，可以通过onRestoreInstanceState方法来恢复Activity的状态，该方法的调用时机是在onStart之后。其中**onCreate**和**onRestoreInstanceState**方法来恢复**Activity**的状态的区别： onRestoreInstanceState回调则表明其中Bundle对象非空，不用加非空判断。onCreate需要非空判断。建议使用onRestoreInstanceState。

![image-20220120141744405](D:\Typora\img\image-20220120141744405.png)



横竖屏切换的生命周期：onPause()->onSaveInstanceState()-> onStop()- >onDestroy()->onCreate()->onStart()->onRestoreInstanceState->onResume() 

可以通过在AndroidManifest文件的Activity中指定如下属性： 

```
android:configChanges = "orientation| screenSize" 
```

来避免横竖屏切换时，Activity的销毁和重建，而是回调了下面的方法： 

```java
@Override 

public void onConfigurationChanged(Configuration newConfig) { 
	super.onConfigurationChanged(newConfig); 
} 
```

Activity优先级的划分和下面的Activity的三种运行状态是对应的。 

(1) 前台Activity——正在和用户交互的Activity，优先级最高。 

(2) 可见但非前台Activity——比如Activity中弹出了一个对话框，导致Activity可见但是位于后台无法和用户交互。 

(3) 后台Activity——已经被暂停的Activity，比如执行了onStop，优先级最低。当系统内存不足时，会按照上述优先级从低到高去杀死目标Activity所在的进程。



**Activity的三种运行状态 **

**Resumed（活动状态）** 又叫Running状态，这个Activity正在屏幕上显示，并且有用户焦点。就是用户正在操作的那个界面。 

**Paused（暂停状态）** 这是一个比较不常见的状态。这个Activity在屏幕上是可见的，但是并不是在屏幕最前端的那个Activity。比如有另一个非全屏或者透明的Activity是Resumed状态，没有完全遮盖这个Activity。 

**Stopped（停止状态）** 当Activity完全不可见时，此时Activity还在后台运行，仍然在内存中保留Activity的状态，并不是完全销毁。这个也很好理解，当跳转的另外一个界面，之前的界面还在后台，按回退按钮还会恢复原来的状态，大部分软件在打开的时候，直接按 Home键，并不会关闭它，此时的Activity就是Stopped状态。 



**Activity的启动模式 **

Android提供了四种Activity启动方式： 

**标准模式**（standard） 

每启动一个Activity创建一个实例压栈，适用于绝大多数Activity；

**栈顶复用模式**（singleTop） 

启动的Activity位于栈顶则重用栈顶实列，并回调onNewIntent方法

```java
@Override protected void onNewIntent(Intent intent) { 
    super.onNewIntent(intent); 
}
```

适用于在通知栏点击收到的通知，然后需要启动一个Activity，这个Activity就可以用singleTop，否则每次点击都会新建一个Activity。

实际的开发过程中，某个场景下连续快速点击，启动了两个Activity。 如果这个时候待启动的Activity使用 singleTop模式可以避免这个Bug。

**栈内复用模式**（singleTask） 

该模式是一种单例模式，即一个栈内只有一个该Activity实例。该模式，可以通过在 AndroidManifest文件的Activity中指定该Activity需要加载到那个栈中，即singleTask 的Activity可以指定想要加载的目标栈。singleTask和taskAffinity配合使用，指定开启的Activity加入到哪个栈中。 

启动一个Activity如果其中有该Activity实例，则把该Activity实例之上的Activity杀死清除出栈，重用并让该Activity实例处在栈顶，然后调用onNewIntent()方法。 

适用于App首页。

**单例模式**（singleInstance） 

作为栈内复用模式（singleTask）的加强版,打开该Activity时，直接创建一个新的任务栈，并创建该Activity实例放入新栈中。一旦该模式的Activity实例已经存在于某个栈中，任何应用再激活该Activity时都会重用该栈中的实例。 

适用于呼叫来电页面。



**Activity**的**Flags** 

在启动Activity时，通过Intent的addFlags()方法设置。 

**(1)FLAG_ACTIVITY_NEW_TASK** 其效果与指定Activity为singleTask模式一致。 

**(2)FLAG_ACTIVITY_SINGLE_TOP** 其效果与指定Activity为singleTop模式一致。 

**(3)FLAG_ACTIVITY_CLEAR_TOP** 具有此标记位的Activity，当它启动时，在同一个任务栈中所有位于它上面的Activity都要出栈。如果和**singleTask模式**一起出现，若被启动的Activity已经存在栈中，则**清除其之上的Activity**，并调用该Activity的onNewIntent方法。如果被启动的Activity采用standard模式，那么**该Activity连同之上的所有Activity出栈**，然后**创建新的Activity实例**并压入栈中。 



#### Service

**启动/停止服务**

```java
 Intent startIntent = new Intent(this, MyService.class); 
 startService(startIntent); // 启动服务

 Intent stopIntent = new Intent(this, MyService.class);
 stopService(stopIntent); // 停止服务
```



**活动和服务通信**

​		新建了一个 DownloadBinder 类，并让它继承自 Binder，然后在它的内部提供了开始下载以及查看下载进度的方法。接着，在 MyService 中创建了 DownloadBinder 的实例，然后在 onBind()方法里返回了这个实例.

```java
//Service
public class MyService extends Service { 
	 private DownloadBinder mBinder = new DownloadBinder(); 
	 class DownloadBinder extends Binder { 
		 public void startDownload() { 
		 Log.d("MyService", "startDownload executed"); 
         } 
		 public int getProgress() { 
		 	Log.d("MyService", "getProgress executed");
     		return 0; 
 		} 
 }
    
 @Override 
 public IBinder onBind(Intent intent) { 
 	return mBinder; 
 } 
 ... 
}
```

​		首先创建了一个 ServiceConnection 的匿名类，在里面重写了 onServiceConnected()方法和 onServiceDisconnected()方法，这两个方法分别会在活动与服务成功绑定以及活动与服务的连接断开的时候调用。

​		建出了一个 Intent 对象，然后调用 bindService()方法将 MainActivity 和 MyService 进行绑定。bindService()方法接收 3 个参数，第一个参数就是刚刚构建出的 Intent 对象，第二个参数是前面创建出的 ServiceConnection 的实例，第三个参数则是一个标志位.

```java
private MyService.DownloadBinder downloadBinder;
private ServiceConnection connection = new ServiceConnection() { 
 	@Override 
	 public void onServiceDisconnected(ComponentName name) { 
 	} 
	 @Override 
	 public void onServiceConnected(ComponentName name, IBinder service) { 
		 downloadBinder = (MyService.DownloadBinder) service; 
		 downloadBinder.startDownload(); 
		 downloadBinder.getProgress(); 
	 } 
 };
 Button bindService = (Button) findViewById(R.id.bind_service); 
 Button unbindService = (Button) findViewById(R.id.unbind_service); 
 bindService.setOnClickListener(this); 
 unbindService.setOnClickListener(this);

 @Override 
 public void onClick(View v) { 
 	switch (v.getId()) { 
	 ... 
	 case R.id.bind_service: 
 		Intent bindIntent = new Intent(this, MyService.class); 
 		bindService(bindIntent, connection, BIND_AUTO_CREATE); // 绑定服务
 		break; 
	 case R.id.unbind_service: 
 		unbindService(connection); // 解绑服务
		break; 
 	 default: 
 		break; 
 	 } 
 }
```



**Service的几种典型使用实例 **

**1.不可交互的后台服务** 

不可交互的后台服务即是普通的Service，通过startService()方式开启。Service的生命周期很简单，分别为onCreate、onStartCommand、onDestroy这三个。 

```java
public class MyService extends Service {
    private Thread mThread;
    public MyService() {
    }

    @Override
    public IBinder onBind(Intent intent) {
        // TODO: Return the communication channel to the service.
        throw new UnsupportedOperationException("Not yet implemented");
    }

    @Override
    public void onCreate() {
        super.onCreate();
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
    	mThread = new Thread() {
            @Override
            public void run() {
                try {
                    while (true) {
                        //等待停止线程
                        if (this.isInterrupted()) {
                                throw new InterruptedException();
                            }
                        //耗时操作。
                        System.out.println("执行耗时操作");
                     }
                } catch (InterruptedException e) {
                        e.printStackTrace();
                }
            }
        };
        mThread.start();
        return super.onStartCommand(intent, flags, startId);
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        mThread.interrupt();
    }
}
```

配置服务： 

```
<service android:name=".BackService"> </service> 
```

配置远程服务，加如下代码： 

```
android:process="remote" 
```

配置好Service类，只需要在前台，调用startService()方法，就会启动耗时操作。 

注意： 

①不运行在一个独立的进程中，它同样执行在UI线程中，因此，在Service中创建了子线程来完成耗时操作。 

②当Service关闭后，如果在onDestory()方法中不关闭线程，你会发现我们的子线程进行的耗时操作是一直存在的，此时关闭该子线程的方法需要直接关闭该应用程序。因此，在**onDestory()**方法中要进行必要的清理工作。 



**2.可交互的后台服务**

可交互的后台服务是指前台页面可以调用后台服务的方法，通过bindService()方式开启。Service的生命周期很简单，分别为onCreate、onBind、onUnBind、 onDestroy这四个。 

可交互的后台服务实现步骤是和不可交互的后台服务实现步骤是一样的，**区别在于启动的方式和获得Service的代理对象**。 

```java
public class BackService extends Service {
    @Override
    public void onCreate() {
        super.onCreate();
    }

    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
	//返回MyBinder对象
        return new MyBinder();
    }

    //需要返回给前台的Binder类
    class MyBinder extends Binder {
        public void showTip() {
            System.out.println("我是来此服务的提示");
        }
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
    }
}
```

前台调用 ,通过以下方式绑定服务： 

bindService(mIntent,con,BIND_AUTO_CREATE);

其中需要一个ServiceConnection作为第二个参数

```java
private ServiceConnection con=new ServiceConnection(){
    @Override
    public void onServiceConnected(ComponentName name,IBinder service){
        BackService.MyBinder myBinder=(BackService.MyBinder)service;
        myBinder.showTip();
    }
    @Override
    public void onServiceDisconnected(ComponentName name){
        
        }
};
```

当建立绑定后，onServiceConnected中的service便是Service类中onBind的返回值。如此便可以调用后台服务类的方法，实现交互。

当调用unbindService()停止服务，同时要在onDestory()方法中做好清理工作。 

注意：通过**bindService**启动的**Service**的生命周期依附于启动它的**Context**。因此当前台调用**bindService**的**Context**销毁后，那么服务会自动停止。 



**3.前台服务**

所谓前台服务只不是通过一定的方式将服务所在的进程级别提升了。前台服务会一直有一个正在运行的图标在系统的状态栏显示，非常类似于通知的效果。  

在Service的基础上创建一个Notification，然后使用Service的startForeground()方法即可启动为前台服务。 



#### Broadcast

**作用** 

用于监听 / 接收 应用发出的广播消息，并做出响应应用场景 

- 不同组件之间通信（包括应用内 / 不同应用之间） 

- 与 Android 系统在特定情况下的通信如当电话呼入时、网络可用时 

- 多线程通信 



**实现原理** 

Android 中的广播使用了设计模式中的观察者模式：基于消息的发布/订阅事件模型。因此，Android将广播的发送者 和 接收者 解耦，使得系统方便集成，更易扩展 

模型中有3个角色： 

1. 消息订阅者（广播接收者） 

2. 消息发布者（广播发布者） 

3. 消息中心（ AMS ，即 Activity Manager Service ） 

![image-20220120164747683](D:\Typora\img\image-20220120164747683.png)

原理描述： 

1. 广播接收者 通过 Binder 机制在 AMS 注册 

2. 广播发送者 通过 Binder 机制向 AMS 发送广播 

3. AMS 根据 广播发送者 要求，在已注册列表中，寻找合适的广播接收者 寻找依据： IntentFilter / Permission 

4. AMS 将广播发送到合适的广播接收者相应的消息循环队列中； 

5. 广播接收者通过 消息循环 拿到此广播，并回调 onReceive() 

注意：广播发送者 和 广播接收者的执行 是 异步的，发出去的广播不会关心有 无接收者接收，也不确定接收者到底是何时才能接收到； 



**使用** 

**1.自定义广播接收者BroadcastReceiver** 

- 继承自BroadcastReceivre基类 

- 必须复写抽象方法onReceive()方法 



1. 广播接收器接收到相应广播后，会自动回调onReceive()方法 

2. 一般情况下，onReceive方法会涉及与其他组件之间的交互，如发送Notification、启动service等 

3. 默认情况下，广播接收器运行在UI线程，因此，onReceive方法不能执行耗时操作，否则将导致ANR。 

```java
public class mBroadcastReceiver extends BroadcastReceiver {
    //接收到广播后自动调用该方法 
    @Override 
    public void onReceive(Context context, Intent intent) { 
        //写入接收广播后的操作 
    } 
}
```



**2.广播接收器注册** 

注册的方式分为两种：静态注册、动态注册 

**静态注册** 

在AndroidManifest.xml里通过 标签声明 

```xml
<receiver 
    android:name=".mBroadcastReceiver" > //此广播接收者类是mBroadcastReceiver
    //用于接收网络状态改变时发出的广播 
    <intent-filter> 
        <action android:name="android.net.conn.CONNECTIVITY_CH ANGE" />
    </intent-filter> 
</receiver>
```

当此App首次启动时，系统会自动实例化mBroadcastReceiver类，并注册到系 统中。 



**动态注册** 

在代码中通过调用Context的*registerReceiver*（）方法进行动态注册 

注意：动态广播最好在Activity的onResume()注册、onPause()注销。 

对于动态广播，有注册就必然得有注销，否则会导致内存泄露 ，重复注册、重复注销也不允许。



**区别：**

![image-20220120170117522](D:\Typora\img\image-20220120170117522.png)



**广播类型**

广播的类型主要分为5类： 

**普通广播**（Normal Broadcast） ：

即开发者自身定义intent的广播（最常用）

**系统广播**（System Broadcast） ：

Android中内置了多个系统广播：只要涉及到手机的基本操作（如开机、网络状态变化、拍照等等），都会发出相应的广播 ；当使用系统广播时，只需要在注册广播接收者时定义相关的action即可，并不需要手动发送广播，当系统有相关操作时会自动进行系统广播 

**有序广播**（Ordered Broadcast） ：

定义发送出去的广播被广播接收者按照先后顺序接收 

广播接受者接收广播的顺序规则（同时面向静态和动态注册的广播接受者） 

1. 按照Priority属性值从大-小排序； 

2. Priority属性相同者，动态注册的广播优先； 

**特点**

1. 接收广播按顺序接收 

2. 先接收的广播接收者可以对广播进行截断，即后接收的广播接收者不再接收到此广播； 

3. 先接收的广播接收者可以对广播进行修改，那么后接收的广播接收者将接收到被修改后的广播 

**具体使用**

 有序广播的使用过程与普通广播非常类似，差异仅在于广播的发送方式：

```java
sendOrderedBroadcast(intent); 
```



**App应用内广播**（Local Broadcast） :

App应用内广播可理解为一种局部广播，广播的发送者和接收者都同 属于一个App。 相比于全局广播（普通广播），App应用内广播优势体现在：安全性高 & 效率高 

**使用**

1.注册广播时将exported属性设置为*false*，使得非本App内部发出的此广播

2.在广播发送和接收时，增设相应权限permission，用于权限验证； 

3.发送广播时指定该广播接收器所在的包名，此广播将只会发送到此包中的App内与之相匹配的有效广播接收器中。不被接收； 



#### ContentProvider

**作用**

进程间 进行**数据交互 & 共享**，即跨进程通信

![img](D:\Typora\img\944365-3c4339c5f1d4a0fd.png)



**原理**

ContentProvider的底层原理 = Android中的Binder机制



**URI**

![img](D:\Typora\img\944365-96019a2054eb27cf.png)



```java
// 设置URI
Uri uri = Uri.parse("content://com.carson.provider/User/1") 
// 上述URI指向的资源是：名为 `com.carson.provider`的`ContentProvider` 中表名 为`User` 中的 `id`为1的数据

// 特别注意：URI模式存在匹配通配符* & ＃
    
// *：匹配任意长度的任何有效字符的字符串
// 以下的URI 表示 匹配provider的任何内容
content://com.example.app.provider/* 
// ＃：匹配任意长度的数字字符的字符串
// 以下的URI 表示 匹配provider中的table表的所有行
content://com.example.app.provider/table/# 
```



**MIME数据类型**

MIME：全称Multipurpose Internet Mail Extensions，多功能Internet 邮件扩充服务。它是一种多用途网际邮件扩充协议，在1992年最早应用于电子邮件系统，但后来也应用到浏览器。MIME类型就是设定某种扩展名的文件用一种应用程序来打开的方式类型，当该扩展名文件被访问的时候，浏览器会自动使用指定应用程序来打开。多用于指定一些客户端自定义的文件名，以及一些媒体文件打开方式。 

作用：指定某个扩展名的文件用某种应用程序来打开
如指定`.html`文件采用`text`应用程序打开、指定`.pdf`文件采用`flash`应用程序打开



**ContentProvider类**

**组织数据方式**

ContentProvide`主要以 **表格的形式** 组织数据，同时也支持文件数据，只是表格形式用得比较多

每个表格中包含多张表，每张表包含行 & 列，分别对应记录 & 字段（同数据库）

**主要方法**

- 进程间共享数据的本质是：添加、删除、获取 & 修改（更新）数据
- 所以`ContentProvider`的核心方法也主要是上述4个作用

```dart
<-- 4个核心方法 -->
  public Uri insert(Uri uri, ContentValues values) 
  // 外部进程向 ContentProvider 中添加数据

  public int delete(Uri uri, String selection, String[] selectionArgs) 
  // 外部进程 删除 ContentProvider 中的数据

  public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs)
  // 外部进程更新 ContentProvider 中的数据

  public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs,  String sortOrder)　 
  // 外部应用 获取 ContentProvider 中的数据

// 注：
// 1. 上述4个方法由外部进程回调，并运行在ContentProvider进程的Binder线程池中（不是主线程）
// 2. 存在多线程并发访问，需要实现线程同步
	// a. 若ContentProvider的数据存储方式是使用SQLite & 一个，则不需要，因为SQLite内部实现好了线程同步，若是多个SQLite则需要，因为SQL对象之间无法进行线程同步
	// b. 若ContentProvider的数据存储方式是内存，则需要自己实现线程同步

<-- 2个其他方法 -->
public boolean onCreate() 
// ContentProvider创建后 或 打开系统后其它进程第一次访问该ContentProvider时 由系统进行调用
// 注：运行在ContentProvider进程的主线程，故不能做耗时操作

public String getType(Uri uri)
// 得到数据类型，即返回当前 Url 所代表数据的MIME类型
```

Android为常见的数据（如通讯录、日程表等）提供了内置了默认的ContentProvider

但也可根据需求自定义ContentProvider，但上述6个方法必须重写

ContentProvider类并不会直接与外部进程交互，而是通过ContentResolver类；



**ContentResolver类**

**作用**

统一管理不同 ContentProvider间的操作，即通过 URI 即可操作 不同的ContentProvider中的数据

外部进程通过 ContentResolver类 从而与ContentProvider类进行交互

**使用**

```java
// 外部进程向 ContentProvider 中添加数据
public Uri insert(Uri uri, ContentValues values)　 

// 外部进程 删除 ContentProvider 中的数据
public int delete(Uri uri, String selection, String[] selectionArgs)

// 外部进程更新 ContentProvider 中的数据
public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs)　 

// 外部应用 获取 ContentProvider 中的数据
public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder)
```

**总结**

![image-20220120180305425](D:\Typora\img\image-20220120180305425.png)



#### Fragment

**生命周期**

![fragment_life](file://C:/Users/86182/Desktop/Android/fragment_life.jpg?lastModify=1642746555)



**切换Fragment方式**

1.准备好Fragment，然后再Activity中实例化，v4包的Fragment是通过getSupportFragmentManager()方法新建Fragment管理器对象;

2.然后通过Fragment管理器对象调用beginTransaction()方法，实例化FragmentTransaction对象，即事务 ;

3.调用FragmentTransaction对象的replace方法;

4.最后调用commit方法.

```java
    public void  replaceFragment(Fragment fragment){
        FragmentManager fragmentManager = getSupportFragmentManager();
        FragmentTransaction transaction = fragmentManager.beginTransaction();
        transaction.replace(R.id.main_activity_content,fragment);
        transaction.addToBackStack(null);
        transaction.commit();
    }
```



**Fragment的回退栈**

Fragment的回退栈是用来保存每一次Fragment事务发生的变化 如果你将Fragment任务添加到回退栈，当用户点击后退按钮时，将看到上一次的保存的Fragment。一旦Fragment完全从后退栈中弹出，用户再次点击后退键，则退出当前Activity .

Fragment的点击事件里写的是replace方法，相当于remove和add的合体，并且如果不添加事务到回退栈，**前一个Fragment实例会被销毁**。我们调用tx.addToBackStack(null)将当前的事务添加到了回退栈，所以FragmentOne实例不会被销毁，**但是视图层次依然会被销毁，即会调用onDestoryView和onCreateView**。

如果不希望视图重绘，可以不调用replace，使用hide先隐藏了当前的Fragment，然后添加 FragmentThree的实例，最后将事务添加到回退栈。

```java
FragmentTransaction tx = fm.beginTransaction(); 
tx.hide(this); 
tx.add(R.id.id_content , fThree, "THREE");
//tx.replace(R.id.id_content, fThree, "THREE");
tx.addToBackStack(null); tx.commit();
```

 

**Fragment与Activity之间的通信** 

![img](D:\Typora\img\8133609-bf508cc0b413fc55.jpg)



Bundle方式：新建fragment自带（最好的例子）

```java

    // TODO: Rename parameter arguments, choose names that match
    // the fragment initialization parameters, e.g. ARG_ITEM_NUMBER
    private static final String ARG_PARAM1 = "param1";
    private static final String ARG_PARAM2 = "param2";

    // TODO: Rename and change types of parameters
    private String mParam1;
    private String mParam2;

    public BlankFragment() {
        // Required empty public constructor
    }

    public static BlankFragment newInstance(String param1, String param2) {
        BlankFragment fragment = new BlankFragment();
        Bundle args = new Bundle();
        args.putString(ARG_PARAM1, param1);
        args.putString(ARG_PARAM2, param2);
        fragment.setArguments(args);
        return fragment;
    }
```

接口回调方式

```java
//Fragment.java
...
    public interface ICallback{
        void sendMsg(String string);
    }

    public void sendMessage(ICallback callback){
        callback.sendMsg("123");
    }
...
```

```java
//Activity.java
fragment.sendMessage(new BlankFragment.ICallback() {
	@Override
	public void sendMsg(String string) {
		System.out.println(string);
	}
});
```



**运行时保存数据**

当屏幕发生旋转，Activity发生重新启动，默认的Activity中的Fragment也会跟着Activity重新创建；这样造成当旋转的时候，本身存在的Fragment会重新启动，然后当执行Activity的onCreate时，又会再次实例化一个新的Fragment。

通过检查onCreate的参数Bundle savedInstanceState就可以判断，当前是否发生Activity的重新创建 默认的savedInstanceState会存储一些数据，包括Fragment的实例；所以，只需要在onCreate时判断只有在savedInstanceState==null时，才进行创建Fragment实例 。

Fragment也有onSaveInstanceState的方法，在此方法中进行保存数据，然后在onCreate或者onCreateView或者onActivityCreated进行恢复。 



#### Android消息机制 

在Android中使用消息机制，我们首先想到的就是Handler。Handler是Android消息机制的上层接口。Handler的使用过程很简单，通过它可以轻松地将一个任务切换到Handler所在的线程中去执行。通常情况下，Handler的使用场景就是更新UI。

**消息机制的模型** 

消息机制主要包含：MessageQueue，Handler和Looper这三大部分，以及Message；

**Message**：需要传递的消息，可以传递数据； 

**MessageQueue**：消息队列，但是它的内部实现并不是用的队列，实际上是通过一个单链表的数据结构来维护消息列表，因为单链表在插入和删除上比较有优势。主要功能向消息池投递消息(MessageQueue.enqueueMessage)和取走消息池消息 (MessageQueue.next)； 

**Handler**：消息辅助类，主要功能向消息池发送各种消息事件(Handler.sendMessage)和处理相应消息事件(Handler.handleMessage)； 

**Looper**：不断循环执行(Looper.loop)，从MessageQueue中读取消息，按分发机制将消息分发给目标处理者。 

![image-20220121160057435](D:\Typora\img\image-20220121160057435.png)



**MessageQueue**，**Handler**和**Looper**三者之间的关系：每个线程中只能存在一个Looper，Looper是保存在ThreadLocal中的。主线程（UI线程）已经创建了一个Looper，所以在主线程中不需要再创建Looper，但是在其他线程中需要创建Looper。每个线程中可以有多个Handler，即一个Looper可以处理来自多个Handler的消息。 Looper中维护一个MessageQueue，来维护消息队列，消息队列中的Message可以来自不同的Handler。



**原理**



![image-20220121161404460](D:\Typora\img\image-20220121161404460.png)

Looper：

loop()进入循环模式，不断重复下面的操作，直到消息为空时退出循环:读取MessageQueue的下一条Message（关于next()，后面详细介绍）；把Message分发给相应的target。当**next()**取出下一条消息时，队列中已经没有消息时，**next()**会无限循环，产生阻塞。等待**MessageQueue**中加入消息，然后重新唤醒。 



Handler：

对于Handler的无参构造方法，默认采用当前线程TLS中的Looper对象，并且callback回调方法为null，且消息为同步处理方式。只要执行 的 Looper.prepare() 方法，那么便可以获取有效的Looper对象。 



发送消息：

发送消息有几种方式，但是归根结底都是调用了 sendMessageAtTime() 方法。在子线程中通过Handler的post()方式或send()方式发送消息，最终都是调用了 sendMessageAtTime() 方法。 

sendMessageAtTime()方法的作用很简单，就是调用MessageQueue的enqueueMessage()方法，往消息队列中添加一个消息。 

MessageQueue是按照Message触发时间的先后顺序排列的，队头的消息是将要最早触发的消息。当有消息需要加入消息队列时，会从队列头开始遍历，直到找到消息应该插入的合适位置，以保证所有消息的时间顺序。



获取消息 ：

当发送了消息后，在MessageQueue维护了消息队列，然后在Looper中通过 loop() 方法，不断地获取消息。上面对 loop() 方法进行了介绍，其中最重要的是调用了 queue.next() 方法,通过该方法来提取下一条信息

 next() 方法根据消息的触发时间，获取下一条需要执行的消息,队列中消息为空时，则会进行阻塞操作。



分发消息 ：

在loop()方法中，获取到下一条消息后，执行 msg.target.dispatchMessage(msg) ，来分发消息到目标Handler对象。 

当Message的 msg.callback 不为空时，则回调方法 msg.callback.run() ; 

当Handler的 mCallback 不为空时，则回调方法 mCallback.handleMessage(msg) ； 

最后调用Handler自身的回调方法 handleMessage() ，该方法默认为空，Handler子类通过覆写该方法来完成具体的逻辑。 

![image-20220121161935960](D:\Typora\img\image-20220121161935960.png)

总结：

发送message时，message中含有当前handler的引用，将message加入MessageQueue，Looper循环取出头message，通过查看message中目标handler的引用来回调handler的handleMessage()方法来接收消息；

Handler负责发送消息和处理消息的，Looper负责接收Handler发送的消息，并直接把消息回传给Handler自己（handleMessage），MessageQueue就是一个存储消息的容器是单向链表表结构，（单项链表的增删比队列要有优势）。



#### **Android事件分发机制**

- ![img](D:\Typora\img\944365-d0a7e6f3c2bbefcc.png)



![img](D:\Typora\img\944365-f75af2b6bcee061d.png)





#### AsyncTask

AsyncTask是一个抽象类，它是由Android封装的一个轻量级异步类（轻量体现在使用方便、代码简洁），它可以在线程池中执行后台任务，然后把执行的进度和最终结果传递给主线程并在主线程中更新UI。 

AsyncTask的内部封装了两个线程池(SerialExecutor和THREAD_POOL_EXECUTOR)和一个**Handler**(InternalHandler)。 

其中**SerialExecutor**线程池用于任务的排队，让需要执行的多个耗时任务，按顺序排列，**THREAD_POOL_EXECUTOR**线程池才真正地执行任务，**InternalHandler**用于从工作线程切换到主线程。 



**优点**

- 方便实现异步通信
  不需使用 “任务线程（如继承`Thread`类） + `Handler`”的复杂组合
- 节省资源
  采用线程池的缓存线程 + 复用线程，避免了频繁创建 & 销毁线程所带来的系统资源开销



**使用**

```java
private class MyTask extends AsyncTask<Params, Progress, Result> {

	  //执行线程任务前的操作
      @Override
      protected void onPreExecute() {
           ...
        }

    
      //接收输入参数、执行任务中的耗时操作、返回 线程任务执行的结果
      @Override
      protected String doInBackground(String... params) {

            // 自定义的线程任务
            // 可调用publishProgress（）显示进度, 之后将执行onProgressUpdate（）
             publishProgress(count);
         }

      // 在主线程显示线程任务执行的进度
      @Override
      protected void onProgressUpdate(Integer... progresses) {
            ...
        }

      //接收线程任务执行结果、将执行结果显示到UI组件
      @Override
      protected void onPostExecute(String result) {
         ...// UI操作
        }

      @Override
        protected void onCancelled() {
        ...
        }
  }


  MyTask mTask = new MyTask();
/**
  * 步骤3：手动调用execute(Params... params) 从而执行异步线程任务
  * 注：
  *    a. 必须在UI线程中调用
  *    b. 同一个AsyncTask实例对象只能执行1次，若执行第2次将会抛出异常
  *    c. 执行任务中，系统会自动调用AsyncTask的一系列方法：onPreExecute() 、doInBackground()、onProgressUpdate() 、onPostExecute() 
  *    d. 不能手动调用上述方法
  */
  mTask.execute()；

```



**注意**

- `AsyncTask`不与任何组件绑定生命周期。在`Activity` 或 `Fragment`中使用 `AsyncTask`时，最好在`Activity` 或 `Fragment`的`onDestory（）`调用 `cancel(boolean)`；
-  若`AsyncTask`被声明为`Activity`的非静态内部类，当`Activity`需销毁时，会因`AsyncTask`保留对`Activity`的引用 而导致`Activity`无法被回收，最终引起内存泄露；`AsyncTask`应被声明为`Activity`的静态内部类。

- 当`Activity`重新创建时（屏幕旋转 / `Activity`被意外销毁时后恢复），之前运行的`AsyncTask`（非静态的内部类）持有的之前`Activity`引用已无效，故复写的`onPostExecute()`将不生效，即无法更新UI操作；在`Activity`恢复时的对应方法重启任务线程。



#### HandlerThread

![img](D:\Typora\img\944365-b234918bcc41dd03.png)

**使用**

1. 创建HandlerThread的实例对象 

```java
HandlerThread handlerThread = new HandlerThread("myHandlerThread"); 
handlerThread.start();
```

2.就是将线程的looper与Handler绑定在一 起

```java
//创建与主线程关联的Handler
mainHandler = new Handler();
//创建工作线程
mThreadHandler = new Handler(mHandlerThread.getLooper()) { 
	@Override 
	public void handleMessage(Message msg) { 
		switch(msg.what){
        	case 1:
        		try {
        			//延时操作
        			Thread.sleep(1000);
        		} catch (InterruptedException e) {
        			e.printStackTrace();
        		}
        		// 通过主线程Handler.post方法进行在主线程的UI更新操作
       			mainHandler.post(new Runnable() {
        			@Override
        			public void run () {
        				text.setText("我爱学习");
        			}
        		});
        	break;
	} 
}; 


    button1 = (Button) findViewById(R.id.button1);
        button1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // 通过sendMessage（）发送
                // a. 定义要发送的消息
                Message msg = Message.obtain();
                msg.what = 1; //消息的标识
                msg.obj = "A"; // 消息的存放
                // b. 通过Handler发送消息到其绑定的消息队列
                mThreadHandler.sendMessage(msg);
            }
        });
```



**HandlerThread与AsyncTask区别**

AsyncTask：为 UI 线程与工作线程之间进行快速的切换提供一种简单便捷的机制。适用于当下立即需要启动，但是异步执行的生命周期短暂的使用场景。

HandlerThread:为某些回调方法或者等待某些任务的执行设置一个专属的线程，并提供线程任务的调度机制。

HandlerThread 继承于 Thread,它本质上是一个线程，只不过是 Android 为我们封装好了 Looper 和 MessageQueue的线程，简化了操作。



#### IntentService

- 它本质是一种特殊的Service,继承自Service并且本身就是一个抽象类
- 它可以用于在后台执行耗时的异步任务，当任务完成后会自动停止
- 它拥有较高的优先级，不易被系统杀死（继承自Service的缘故），因此比较适合执行一些高优先级的异步任务它内部通过HandlerThread和Handler实现异步操作
- 创建IntentService时，只需实现onHandleIntent和构造方法，onHandleIntent为异步方法，可以执行耗时操作



**使用**

1.首先继承IntentService类，实现onHandleIntent方法

```java
  public class MyIntentService extends IntentService {
  private static final String TAG = "MyIntentService";
  public MyIntentService() {
      super("MyIntentService");
  }

  @Override
  protected void onHandleIntent(Intent intent) {
      //在这里通过intent携带的数据，开进行任务的操作。
      String taskName = intent.getExtras().getString("taskName" );
      switch (taskName) {
          case "task1":
              ...
              break;
  }
      
```

2.注册IntentService

```xml
<service android:name=".myIntentService"> 
    <intent-filter> 
        <action android:name="cn.scu.finch"/>
    </intent-filter> 
</service>
```

3.通过Bundle传递参数，再使用intent启动service

```java
  startService.setOnClickListener(new View.OnClickListener() {
          @Override
          public void onClick(View v) {
              Intent intent = new Intent(MainActivity.this, MyIntentService.class);
              Bundle bundle = new Bundle();
              bundle.putString("taskName", "task1");
              intent.putExtras(bundle);
              startService(intent);
          }
      });
```



**在源码handleMessage方法中为什么执行完handleIntent方法会去调用带参数的stopSelf()**

因为stopSelf()的执行会立刻将服务停止掉，而带参数的stopSelf(int startId)会在所有任务执行完毕后将服务给停止。通常情况下调用stopSelf(int satrtId)方法不会立刻去执行停止服务的操作，会去判断最近执行任务的次数是否和startId相等，如果相等就立刻执行停止服务的操作。



注意：工作任务队列是顺序执行的。 

如果一个任务正在IntentService中执行，此时你再发送一个新的任务请求，这个新的任务会一直等待直到前面一个任务执行完毕才开始执行。 

原因： 

1. 由于onCreate() 方法只会调用一次，所以只会创建一个工作线程； 

2. 当多次调用 startService(Intent) 时（onStartCommand也会调用多次）其实并不会创建新的工作线程，只是把消息加入消息队列中等待执行，所以，多次启 动 **IntentService** 会按顺序执行事件； 

3. 如果服务停止，会清除消息队列中的消息，后续的事件得不到执行。 



#### 缓存策略

LRU(Least Recently Used)缓存算法，LRU是最近最少使用的算法，它的核心思想是当缓存满时，会优先淘汰那些最近最少使用的缓存对象。采用 LRU算法的缓存有两种：LrhCache和DisLruCache，分别用于实现内存缓存和硬盘缓存，其核心思想都是LRU缓存算法。 

**LruCache的使用** 

LruCache是Android 3.1所提供的一个缓存类，所以在Android中可以直接使用LruCache实现内存缓存。

LruCache是个泛型类，主要算法原理是把最近使用的对象用强引用（即我们平常使用的对象引用方式）存储在 **LinkedHashMap** 中。当缓存满时，把最近最少使用的对象从内存中移除，并提供了get和put方法来完成缓存的获取和添加操作。 

```java
//缓存图片的例子
int maxMemory = (int) (Runtime.getRuntime().totalMemory() / 1024 );
int cacheSize = maxMemory / 8; 
mMemoryCache = new LruCache<String, Bitmap>(cacheSize) { 
    @Override 
    protected int sizeOf(String key, Bitmap value) { 
        return value.getRowBytes() * value.getHeight() / 1024; 
    } 
};
```



#### Activity与View关联

![img](D:\Typora\img\916005-20191123165341106-2015631979.png)

- **Activity :** 对于每一个 activity 都会有拥有一个 PhoneWindow。
- **PhoneWindow** ：该类继承于 Window 类，是 Window 类的具体实现，即我们可以通过该类具体去绘制窗口。并且，该类内部包含了一个 DecorView 对象，该 DectorView 对象是所有应用窗口的根 View。
- **DecorView** 是一个应用窗口的根容器，它本质上是一个 FrameLayout。DecorView 有唯一一个子 View，它是一个垂直 LinearLayout，包含两个子元素，一个是 TitleView（ ActionBar 的容器），另一个是 ContentView（窗口内容的容器）。
- **ContentView** ：是一个 FrameLayout（android.R.id.content)，我们平常用的 setContentView 就是设置它的子 View 。

- **WindowManager :** 是一个接口，里面常用的方法有：添加View，更新View和删除View。主要是用来管理 Window 的。WindowManager 具体的实现类是WindowManagerImpl。最终，WindowManagerImpl 会将业务交给 WindowManagerGlobal 来处理。

- **WindowManagerService (WMS)** ： 负责管理各 app 窗口的创建，更新，删除， 显示顺序。运行在 system_server 进程。

- **ViewRootImpl** ：拥有 DecorView 的实例，通过该实例来控制 DecorView 绘制。ViewRootImpl 的一个内部类 W，实现了 IWindow 接口，IWindow 接口是供 WMS 使用的，WSM 通过调用 IWindow 一些方法，通过 Binder 通信的方式，最后执行到了 W 中对应的方法中。同样的，ViewRootImpl 通过 IWindowSession 来调用 WMS 的 Session 一些方法。Session 类继承自 `IWindowSession.Stub`，每一个应用进程都有一个唯一的 Session 对象与 WMS 通信。 

**总结：**

**Activity**就像个控制器，不负责视图部分。**Window**像个承载器，装着内部视图。**DecorView**就是个顶层视图，是所有**View**的最外层布局。**ViewRoot**像个连接器，负责沟通，通过硬件的感知来通知视图，进行用户之间的交互。 



#### View绘制

![image-20220125123135796](D:\Typora\img\image-20220125123135796.png)

View的绘制是从上往下一层层迭代下来的。DecorView-->ViewGroup（--- >ViewGroup）-->View ，按照这个流程从上往下，依次measure(测量),layout(布局),draw(绘制)。 

![image-20220125123217140](D:\Typora\img\image-20220125123217140.png)



**Measure流程 **

测量每个控件的大小

调用measure()方法，进行一些逻辑处理，然后调用onMeasure()方法，在其中调用setMeasuredDimension()设定View的宽高信息，完成View的测量操作。 

measure()方法中，传入了两个参数 widthMeasureSpec, heightMeasureSpec 表示View的宽高的一些信息。 

**MeasureSpec**

![image-20220125123409337](D:\Typora\img\image-20220125123409337.png)

MeasureSpec由两部分组成，一部分是测量模式，另一部分是测量的尺寸大小。其中，Mode模式共分为三类 

UNSPECIFIED ：不对View进行任何限制，要多大给多大，一般用于系统内部(ScrollView)

EXACTLY：对应LayoutParams中的match_parent和具体数值这两种模式。检测到View所需要的精确大小，这时候View的最终大小就是SpecSize所指定的值， 

AT_MOST ：对应LayoutParams中的wrap_content。View的大小不能大于父容器的大小。 

**MeasureSpec又是如何确定的？ **

对于DecorView，其确定是通过屏幕的大小，和自身的布局参数LayoutParams。 

对于其他View（包括ViewGroup），其确定是通过父布局的MeasureSpec和自身的布局参数LayoutParams。 

![image-20220125124015197](D:\Typora\img\image-20220125124015197.png)

从这里看出**MeasureSpec**的指定也是从顶层布局开始一层层往下去，父布局影响子布局



**Layout流程**

测量完View大小后，就需要将View布局在Window中，View的布局主要通过确定上下左右四个点来确定的。 

其中布局也是自上而下，不同的是**ViewGroup**先在**layout()**中确定自己的布局，然后在**onLayout()**方法中再调用子**View**的**layout()**方法，让子**View**布局。在**Measure**过程中，**ViewGroup**一般是先测量子**View**的大小，然后再确定自身的大小。

**layout()**会将四个位置参数传递给**setOpticalFrame()**或者**setFrame()**，而**setOpticalFrame()**内部会调用**setFrame()**，所以最终通过**setFrame()**确定**View**在**ViewGroup**中的位置。位置确定完毕会调用**onLayout(l,t,r,b**)对子**View**进行摆放。
**View**和**ViewGroup**在执行完**setFrame()**后都会调用**onLayout()**方法，但上面也有提到该方法的作用是对子View进行位置摆放，所以单一View是不需要重写此方法。而ViewGroup会根据自己的特性任意对子View进行摆放。



**Draw**

绘制顺序：

①绘制背景 background.draw(canvas) 

②绘制自己（onDraw） 

③绘制Children(dispatchDraw) 

④绘制装饰（onDrawScrollBars） 

无论是**ViewGroup**还是单一的**View**，都需要实现这套流程，不同的是，在**ViewGroup**中，实现了 **dispatchDraw()**方法，而在单一子**View**中不需要实现该方法。自定义**View**一般要重写**onDraw()**方法，在其中绘制不同的样式。 



#### Android虚拟机

**Dalvik虚拟机特点**

- 体积小，占用内存空间小； 

- 专有的DEX可执行文件格式，体积更小，执行速度更快； 

- 常量池采用32位索引值，寻址类方法名，字段名，常量更快； 

- 基于寄存器架构，并拥有一套完整的指令系统； 

- 提供了对象生命周期管理，堆栈管理，线程管理，安全和异常管理以及垃圾回收等重要功能； 

- 所有的Android程序都运行在Android系统进程里，每个进程对应着一个Dalvik虚拟机实例。

**Dalvik虚拟机和Java虚拟机的区别 **

1.**Java虚拟机运行的是Java字节码，Dalvik虚拟机运行的是Dalvik字节码。**

Java程序字节码保存在class文件中，Java虚拟机通过解码运行程序，而Dalvik虚拟机运行的是相应的Dalvik字节码，所有的Dalvik字节码都是由Java字节码转换过来的，被打包到一个dex可执行文件中，通过解释dex文件来执行程序。

![image-20220125142002015](D:\Typora\img\image-20220125142002015.png)



**2.Dalvik可执行文件体积更小**

SDK有一个dx工具可以将Java字节码转换为Dalvik字节码，dx工具可以将Java类文件中常量池分解，冗余的信息剔除，重新组合一个常量池，所有文件共享一个常量池。

**3.Java虚拟机与Dalvik虚拟机架构不同**

Java虚拟机基于栈架构，Dalvik虚拟机基于寄存器架构Java频繁的从栈上读写数据，需要更多的指令分派和访存次数，开销大，Dalvik虚拟机通过寄存器间直接传递，比栈方式快很多。



**ART虚拟机**

Google在2014年推出了新的虚拟机ART，在Android4.4系统中提供给用户试用，力图从根本上改善系统卡顿的问题 ，并且从Android5.0开始废弃了Dalvik，全面推行ART。

ART代表Android Runtime，其处理应用程序执行的方式完全不同于Dalvik，Dalvik是依靠一个Just-In-Time (JIT)编译器去解释字节码。开发者编译后的应用代码需要通过一个解释器在用户的设备上运行，这一机制并不高效，但让应用能更容易在不同硬件和架构上运行。ART则完全改变了这套做法，在应用安装时就预编译字节码到机器语言，这一机制叫Ahead-Of-Time (AOT）编译。在移除解释代码这一过程 后，应用程序执行将更有效率，启动更快。 



**ART**优点： 

1. 系统性能的显著提升。 

2. 应用启动更快、运行更快、体验更流畅、触感反馈更及时。 

3. 更长的电池续航能力。 

4. 支持更低的硬件。 

**ART**缺点： 

1. 更大的存储空间占用，可能会增加10%-20%。 

2. 更长的应用安装时间。 



**ART虚拟机相对于Dalvik虚拟机的提升 **

**1.预编译** 

在dalvik中，如同其他大多数JVM一样，都采用的是JIT来做及时翻译(动态翻译)，将dex或odex中并排的dalvik code(或者叫smali指令集)运行态翻译成native code去执行。JIT的引入使得dalvik提升了3~6倍的性能。 

而在ART中，完全抛弃了dalvik的JIT，**使用了AOT直接在安装时将其完全翻译成native code**。这一技术的引入，使得虚拟机执行指令的速度又一重大提升。 

**2.垃圾回收机制** 

dalvik的GC的过程：

1. 当gc被触发时候，其会去查找所有活动的对象，这个时候整个程序与虚拟机内部的所有线程就会挂起，这样目的是在较少的堆栈里找到所引用的对象； 注意：这个回收动作和应用程序非并发； 

2. gc对符合条件的对象进行标记； 

3. gc对标记的对象进行回收； 

4. 恢复所有线程的执行现场继续运行。 

**dalvik**这么做的好处是，当**pause**了之后，**GC**势必是相当快速的。但是如果出现**GC**频繁并且内存吃紧势必会导致**UI**卡顿、掉帧、操作不流畅等。 

后来ART改善了这种GC方式， 主要的改善点在将其非并发过程改成了**部分并发**，还有就是对**内存的重新分配**管理。 

当ART GC发生时: 

1. GC将会锁住Java堆，扫描并进行标记； 

2. 标记完毕释放掉Java堆的锁，并且挂起所有线程； 

3. GC对标记的对象进行回收； 

4. 恢复所有线程的执行现场继续运行； 

5. 重复**2-4**直到结束。 

整个过程做到了部分并发使得时间缩短

**3.提高内存使用，减少碎片化** 

**Dalvik**内存管理特点是**:**内存碎片化严重，当然这也是Mark and Sweep算法带来的弊端。 

在ART中，它将Java分了一块空间命名为**Large-Object-Space**，这 块内存空间的引入用来专门存放large object。同时ART又引入了moving collector的技术，即将不连续的物理内存块进行对齐。对齐了后内存碎片化就得到了很好的解决。Large-Object-Space的引入是因为moving collector对大块内存的位移时间成本太高。根官方统计，ART的内存利用率提高10倍了左右，大大提高了内存的利用率。



#### Android APK编译打包流程

![img](D:\Typora\img\1813550-bc40133c18135e56)

1.Java编译器对工程本身的java代码进行编译，这些java代码有三个来源：app的源代码，由资源文件生成的R文件(aapt工具)，以及有aidl文件生成的java接口文件(aidl工具)。产出为.class文件。 

2..class文件和依赖的三方库文件通过dex工具生成Delvik虚拟机可执行的.dex文件，包含了所有的class信息，包括项目自身的class和依赖的class。产出为.dex文件。

3.apkbuilder工具将.dex文件和编译后的资源文件生成未经签名对齐的apk文件。这里编译后的资源文件包括两部分，一是由aapt编译产生的编译后的资源文件，二是依赖的三方库里的资源文件。产出为未经签名的.apk文件。 

4.分别由Jarsigner和zipalign对apk文件进行签名和对齐，生成最终的apk文件。 

总结为：编译-->DEX-->打包-->签名和对齐 ；



#### Android IPC几种方式

**一、使用 Intent** 

1. Activity，Service，Receiver 都支持在 Intent 中传递 Bundle 数据，而 Bundle实现了 Parcelable 接口，可以在不同的进程间进行传输。 

2. 在一个进程中启动了另一个进程的 Activity，Service 和 Receiver ，可以在Bundle 中附加要传递的数据通过 Intent 发送出去。 

**二、使用文件共享** 

1. Windows 上，一个文件如果被加了排斥锁会导致其他线程无法对其进行访问，包括读和写；而 Android 系统基于 Linux ，使得其并发读取文件没有限制地进行，甚至允许两个线程同时对一个文件进行读写操作，尽管这样可能会出问题。 

2. 可以在一个进程中序列化一个对象到文件系统中，在另一个进程中反序列化恢复这个对象（注意：并不是同一个对象，只是内容相同。）。 

3. SharedPreferences 是个特例，系统对它的读 / 写有一定的缓存策略，即内存中会有一份 ShardPreferences 文件的缓存，系统对他的读 / 写就变得不可靠，当面对高并发的读写访问，SharedPreferences 有很多大的几率丢失数据。因此，IPC 不建议采用 SharedPreferences。 

**三、使用 Messenger** 

Messenger 是一种轻量级的 IPC 方案，它的底层实现是 AIDL ，可以在不同进程中传递 Message 对象，它一次只处理一个请求，在服务端不需要考虑线程同步的问题，服务端不存在并发执行的情形。 

**四、使用 AIDL** 

Messenger 是以串行的方式处理客户端发来的消息，如果大量消息同时发送到服务端，服务端只能一个一个处理，所以大量并发请求就不适合用 Messenger ，而且Messenger 只适合传递消息，不能跨进程调用服务端的方法。AIDL 可以解决并发和跨进程调用方法的问题，要知道 Messenger 本质上也是 AIDL ，只不过系统做了封装方便上层的调用而已。 

**AIDL** 文件支持的数据类型 

- 基本数据类型； 

- *String* 和 *CharSequence*  

- *ArrayList* ，里面的元素必须能够被 AIDL 支持；  

- *HashMap* ，里面的元素必须能够被 AIDL 支持； 

- *Parcelable* ，实现 Parcelable 接口的对象； 注意：如果 **AIDL** 文件中用到了  

- 自定义的 **Parcelable** 对象，必须新建一个和它同名的 **AIDL** 文件。 

- *AIDL* ，AIDL 接口本身也可以在 AIDL 文件中使用。

**五、使用 ContentProvider** 

用于不同应用间数据共享，和 Messenger 底层实现同样是 Binder 和 AIDL，系统 做了封装，使用简单。 系统预置了许多 ContentProvider ，如通讯录、日程表，需要跨进程访问。

 使用方法：继承 ContentProvider 类实现 6 个抽象方法，这六个方法均运行在 ContentProvider 进程中，除 onCreate 运行在主线程里，其他五个方法均由外界回调运行在 Binder 线程池中。 

ContentProvider 的底层数据，可以是 SQLite 数据库，可以是文件，也可以是内存中的数据。 

**六、使用 Socket** 



#### Bitmap压缩策略

**加载Bitmap的方式 **

Bitmap在Android中指的是一张图片。通过BitmapFactory类提供的四类方法：decodeFile,decodeResource,decodeStream和decodeByteArray,分别从文件系统，资源，输入流和字节数组中加载出一个Bitmap对象，其中decodeFile,decodeResource又间接调用了decodeStream方法，这四类方法最终是在Android的底层实现的，对应着BitmapFactory类的几个native方法。 



**BitmapFactory.Options的参数 **

**inSampleSize参数** 

上述四类方法都支持BitmapFactory.Options参数，而Bitmap的按一定采样率进行缩放就是通过BitmapFactory.Options参数实现的，主要用到了inSampleSize参数，即**采样率**。通过对inSampleSize的设置，对图片的像素的高和宽进行缩放。

当inSampleSize=1，即采样后的图片大小为图片的原始大小。小于1，也按照1来计算。 当inSampleSize>1，即采样后的图片将会缩小，缩放比例为1/(inSampleSize 的二次方)。 

例如：一张1024 ×1024像素的图片，采用ARGB8888格式存储，那么内存大小1024×1024×4=4M。如果inSampleSize=2，那么采样后的图片内存大小：512×512×4=1M。 

注意：官方文档支出，**inSampleSize**的取值应该总是**2**的指数，如**1**，**2**，**4**，**8**等。如果外界传入的**inSampleSize**的值不为**2**的指数，那么系统会向下取整并选择一个最接近**2**的指数来代替。比如**3**，系统会选择**2**来代替。当时经验证明并非在所有**Android**版本上都成立。 

关于**inSampleSize**取值的注意事项： 通常是根据图片宽高实际的大小/需要的宽高大小，分别计算出宽和高的缩放比。但应该取其中最小的缩放比，避免缩放图片太小，到达指定控件中不能铺满，需要拉伸从而导致模糊



**inJustDecodeBounds参数**

我们需要获取加载的图片的宽高信息，然后交给inSampleSize参数选择缩放比缩放。那么如何能先不加载图片却能获得图片的宽高信息，通过inJustDecodeBounds=true，然后加载图片就可以实现只解析图片的宽高信息，并 不会真正的加载图片，所以这个操作是轻量级的。当获取了宽高信息，计算出缩放比后，然后在将inJustDecodeBounds=false,再重新加载图片，就可以加载缩放后的图片。 

注意：**BitmapFactory**获取的图片宽高信息和图片的位置以及程序运行的设备有关，比如同一张图片放在不同的**drawable**目录下或者程序运行在不同屏幕密度的设备上，都可能导致**BitmapFactory**获取到不同的结果，和**Android**的资源加载机制有关。 



**流程**

①将BitmapFactory.Options的inJustDecodeBounds参数设为true并加载图片。 

②从BitmapFactory.Options中取出图片的原始宽高信息，它们对应于outWidth和outHeight参数。 

③根据采样率的规则并结合目标View的所需大小计算出采样率inSampleSize。 

④将BitmapFactory.Options的inJustDecodeBounds参数设为false，然后重新加载图片。 

```java
public static Bitmap decodeSampledBitmapFromResource(Resources res, int resId, int reqWidth, int reqHeight){ 
	BitmapFactory.Options options = new BitmapFactory.Options(); 
    
	options.inJustDecodeBounds = true; 
	//加载图片 
	BitmapFactory.decodeResource(res,resId,options); 
	//计算缩放比 
	options.inSampleSize = calculateInSampleSize(options,reqHeight,reqWidth); 
	//重新加载图片 
	options.inJustDecodeBounds =false; 
	return BitmapFactory.decodeResource(res,resId,options); 
}

private static int calculateInSampleSize(BitmapFactory.Options options, int reqHeight, int reqWidth) { 
	int height = options.outHeight; 
	int width = options.outWidth; 
	int inSampleSize = 1; 
	if(height>reqHeight||width>reqWidth){ 
		int halfHeight = height/2; 
		int halfWidth = width/2; 
		//计算缩放比，是2的指数 
		while((halfHeight/inSampleSize)>=reqHeight&&(halfWidth/inSampleSize)>=reqWidth){ 
			inSampleSize*=2;
    	} 
	}
	return inSampleSize; 
} 
```



#### Android动画总结 

**帧动画**

帧动画就是顺序播放一组预先定义好的图片，就类似于我们观看视频，就是一张一张的图片连续播放。

帧动画的使用很简单，总共就两个步骤：

1、在res/drawable目录下定义一个XML文件，根节点为系统提供的animation-list，然后放入定义更好的图片；
2、使用AnimationDrawable类播放第一步定义好的Drawable中的图片，形成动画效果；

第一步，创建Drawable文件：

```xml
<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
	android:oneshot="false">
	<item android:drawable="@drawable/image01" android:duration="500"/>
	<item android:drawable="@drawable/image02" android:duration="500"/>
	<item android:drawable="@drawable/image03" android:duration="500"/>
</animation-list>
```

上述xml中，有些属性我们要了解到：

1、android:oneshot=“false”： 表示是否重复播放动画，还是只播放一次；
2、每个item都有Drawable和duration属性，Drawable表示我们要播放的图片；duration表示这张图播放的时间；

第二步，用AnimationDrawable播放动画：

```java
Button button = (Button) findViewById(R.id.bt_001);
button.setBackgroundResource(R.drawable.frame_animation);//把Drawable设置为button的背景
//拿到这个我们定义的Drawable，实际也就是AnimationDrawable
AnimationDrawable animationDrawable = (AnimationDrawable) button.getBackground();
animationDrawable.start();//开启动画
```


**补间动画（View动画）**

补间动画又可以分为四种形式，分别是 **alpha**（淡入淡出），**translate**（位移），**scale**（缩放大小），**rotate**（旋转）。 补间动画的实现，一般会采用xml 文件的形式；代码会更容易书写和阅读，同时也更容易复用。 

![在这里插入图片描述](D:\Typora\img\20190621225246708.png)



**View动画的组合动画–AnimationSet**
我们可以使用AnimationSet把View动画的平移、缩放、旋转、渐变都揉在一起，也是既能通过代码实现，也可以通过xml实现

xml实现：

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
 android:interpolator="@android:anim/accelerate_decelerate_interpolator"
    >
    <scale
        android:duration="3000"
        android:fromXScale="0.0"
        android:fromYScale="0.0"
        android:pivotX="50%"
        android:pivotY="50%"
        android:toXScale="1.0"
        android:toYScale="1.0"/>
    <alpha
        android:duration="3000"
        android:fromAlpha="1.0"
        android:toAlpha="0.5" />
    <rotate
        android:fromDegrees="0"
        android:toDegrees="720"
        android:pivotX = "50%"
        android:pivotY="50%"
        android:duration = "3000"
        />
    <translate
        android:fromXDelta="0"
        android:toXDelta="100"
        android:fromYDelta="0"
        android:toYDelta="100" />
</set>
```

代码实现：

```java
AnimationSet setAnimation = new AnimationSet(true);
    // 特别说明以下情况
    // 因为在下面的旋转动画设置了无限循环(RepeatCount = INFINITE)
    // 所以动画不会结束，而是无限循环
    // 所以组合动画的下面两行设置是无效的， 以后设置的为准
    setAnimation.setRepeatMode(Animation.RESTART);
    setAnimation.setRepeatCount(1);// 设置了循环一次,但无效

    // 旋转动画
    Animation rotate = new RotateAnimation(0,360,Animation.RELATIVE_TO_SELF,0.5f,Animation.RELATIVE_TO_SELF,0.5f);
    rotate.setDuration(1000);
    rotate.setRepeatMode(Animation.RESTART);
    rotate.setRepeatCount(Animation.INFINITE);

    // 平移动画
    Animation translate = new TranslateAnimation(TranslateAnimation.RELATIVE_TO_PARENT,-0.5f,
            TranslateAnimation.RELATIVE_TO_PARENT,0.5f,
            TranslateAnimation.RELATIVE_TO_SELF,0
            ,TranslateAnimation.RELATIVE_TO_SELF,0);
    translate.setDuration(10000);

    // 透明度动画
    Animation alpha = new AlphaAnimation(1,0);
    alpha.setDuration(3000);
    alpha.setStartOffset(7000);

    // 缩放动画
    Animation scale1 = new ScaleAnimation(1,0.5f,1,0.5f,Animation.RELATIVE_TO_SELF,0.5f,Animation.RELATIVE_TO_SELF,0.5f);
    scale1.setDuration(1000);
    scale1.setStartOffset(4000);

    // 将创建的子动画添加到组合动画里
    setAnimation.addAnimation(alpha);
    setAnimation.addAnimation(rotate);
    setAnimation.addAnimation(translate);
    setAnimation.addAnimation(scale1);
    // 使用
    mButton.startAnimation(setAnimation);
```



**使用场景**
**1.LayoutAnimation**
LayoutAnimation作用于ViewGroup，为ViewGroup指定一个动画，然后，当它的子元素出场时都会具有这种效果。这种效果常用与ListView，有的ListView的每个item都以一定的动画形式出现，就是用到的LayoutAnimation。

**2.Activity的切换效果**
Activity有默认的切换效果，但是我们可以定制，主要用到overridePendingTransition（int enterAnima, int exitAnima）这个方法：

```java
Intent intent = new Intent(this,TestActivity.class);
startActivity(intent);
overridePendingTransition(R.anim.enter_anim,R.anim.exit_anim);
//注意， 这个方法必须在startActivity或者finish方法之后调用才会生效。
```

**3.Fragment的切换效果**
可以使用FragmentTransaction的setCustomAnimation方法添加切换动画。



**属性动画**

属性动画可以看作是增强版的补间动画，与补间动画的不同之处体现在：

- 补间动画只能定义两个关键帧在透明、旋转、位移和倾斜这四个属性的变换，但是属性动画可以定义任何属性的变化。
- 补间动画只能对 UI 组件执行动画，但属性动画可以对任何对象执行动画.

与补间动画类似的是，属性动画也需要定义几个方面的属性：

- 动画持续时间。默认为 300ms，可以通过 android:duration 属性指定。
- 动画插值方式。通过 android:interploator 指定。
- 动画重复次数。通过 android:repeatCount 指定。
- 重复行为。通过 android:repeatMode 指定。
- 动画集。在属性资源文件中通过 <set …/> 来组合。
- 帧刷新率。指定多长时间播放一帧。默认为 10 ms。



#### 进程优先级 

**前台进程** 

- 处于正在与用户交互的activity 

- 与前台activity绑定的service 

- 调用了startForeground（）方法的service 

- 正在执行oncreate（），onstart（），ondestroy方法的 service。 

- 进程中包含正在执行onReceive（）方法的BroadcastReceiver。

系统中的前台进程并不会很多，而且一般前台进程都不会因为内存不足被杀死。特殊情况除外。当内存低到无法保证所有的前台进程同时运行时，才会选择杀死某个 进程。 



**可视进程** 

- 为处于前台，但仍然可见的activity（例如：调用了onpause（）而还没调用onstop（）的activity）。典型情况是：运行activity时，弹出对话框（dialog等），此时的activity虽然不是前台activity，但是仍然可见。  

- 可见activity绑定的service。（处于上诉情况下的activity所绑定的service） 

可视进程一般也不会被系统杀死，除非为了保证前台进程的运行不得已而为之。 



**服务进程** 

- 已经启动的service 



**后台进程** 

- 不可见的activity（调用onstop（）之后的activity） 

后台进程不会影响用户的体验，为了保证前台进程，可视进程，服务进程的运行，系统随时有可能杀死一个后台进程。当一个正确实现了生命周期的activity处于后台被杀死时，如果用户重新启动，会恢复之前的运行状态。 



**空进程** 

- 任何没有活动的进程  

系统会杀死空进程，但这不会造成影响。空进程的存在无非为了一些缓存，以便于下次可以更快的启动。 



#### Context详解 

Context提供了关于应用环境全局信息的接口。它是一个抽象类，它的执行被**Android**系统所提供。它允许获取以应用为特征的资源和类型，是一个统领一些资源（应用程序环境变量等）的上下文。

![image-20220126142229564](D:\Typora\img\image-20220126142229564.png)

**Context**的两个子类分工明确，其中**ContextImpl**是 **Context**的具体实现类，**ContextWrapper**是**Context**的包装类。Activity， 

Application，Service虽都继承自ContextWrapper（Activity继承自ContextWrapper的子类ContextThemeWrapper），但它们初始化的过程中都会创建ContextImpl对象，由ContextImpl实现Context中的方法。 

Context数量=Activity数量+Service数量 +1



**作用域**

![image-20220126142359310](D:\Typora\img\image-20220126142359310.png)

总结：凡是跟UI相关的，都应该使用Activity做为Context来处理；其他的一些操作，Service,Activity,Application等实例都可以，当然了，注意Context引用的持有，防止内存泄漏。 



**getApplication()和getApplicationContext() 区别**

通过代码，打得出两者的内存地址都是相同的，说明它们是同一个对象。其实这个结果也很好理解，因为前面已经说过了， Application本身就是一个Context，所以这里获取getApplicationContext()得到的结果就是Application本身的实例。那么问题来了，既然这两个方法得到的结果都是相同的，那么Android为什么要提供两个功能重复的方法呢？实际上这两个方法**在作用域上有比较大的区别**。getApplication()方法的语义性非常强，一看就知道是用来获取Application实例的，但是这个方法只有在**Activity**和**Service**中才能调用的到。那么也许在绝大多数情况下我们都是在Activity或者Service中使用Application的，但是如果在一些其它的场景，比如BroadcastReceiver中也想获得Application的实例，这时就可以借助getApplicationContext()方法了。 



**正确使用Context** 

一般Context造成的内存泄漏，几乎都是当Context销毁的时候，却因为被引用导致销毁失败，而Application的Context对象可以理解为随着进程存在的，所以我们总结出使用Context的正确姿势： 

1. 当Application的Context能搞定的情况下，并且生命周期长的对象，优先使用Application的Context。 

2. 不要让生命周期长于Activity的对象持有到Activity的引用。 

3. 尽量不要在Activity中使用非静态内部类，因为非静态内部类会隐式持有外部类实例的引用，如果使用静态内部类，将外部实例引用作为弱引用持有。 



#### Android MVP模式

**概述**

**MVP**，全称 **Model-View-Presenter**，即模型**-**视图**-**层现器。 

提到MVP，就必须要先介绍一下它的前辈MVC，因为MVP正是基于MVC的基础发展而来的。两个之间的关系也是源远流长。 

**MVC**，全称**Model-View-Controller**，即模型**-**视图**-**控制器。 具体如下： 

**View**：对应于布局文件 

**Model**：业务逻辑和实体模型 

**Controllor**：对应于Activity 

但是View对应于布局文件，其实能做的事情特别少，实际上关于该布局文件中的数据绑定的操作，事件处理的代码都在Activity中，造成了Activity**既像View又像Controller**，使得Activity变得臃肿。 

而当将架构改为MVP以后，Presenter的出现，**将Actvity视为View层**，Presenter负责完成View层与Model层的交互。现在是这样的： 

**View** 对应于**Activity**，负责**View**的绘制以及与用户交互 

**Model** 依然是业务逻辑和实体模型 

**Presenter** 负责完成View于Model间的交互 

![image-20220126151215148](D:\Typora\img\image-20220126151215148.png)

总结：**MVP**模式减少了**Activity**的职责，简化了**Activity**中的代码，将复杂的逻辑代码提取到了**Presenter**中进行处理，模块职责划分明显，层次清晰。与之对应的好处就是，耦合度更低，更方便的进行测试。 

**MVC和MVP的区别**

![image-20220126151303289](D:\Typora\img\image-20220126151303289.png)

**MVC**中是允许**Model**和**View**进行交互的，而**MVP**中很明显，**Model**与**View**之间的交互由**Presenter**完成。还有一点就是**Presenter**与**View**之间的交互是通过接口的。 而且**MVC**中**V**对应的是布局文件，**MVP**中**V**对应的是**Activity**。



**MVP模式的整个核心流程：** 

View与Model并不直接交互，而是使用Presenter作为View与Model之间的桥梁。其中Presenter中同时持有View层的Interface的引用以及Model层的引用，而View层持有Presenter层引用。当View层某个界面需要展示某些数据的时候，首先会调用Presenter层的引用，然后Presenter层会调用Model层请求数据，当Model层数据加载成功之后会调用Presenter层的回调方法通知Presenter层数据加载情况，最后Presenter层再调用View层的接口将加载后的数据展示给用户。 

TODO：尝试写一个MVP模型的小demo



#### Binder机制及AIDL使用

**IPC原理** 

![image-20220126175430186](D:\Typora\img\image-20220126175430186.png)

每个Android的进程，只能运行在自己进程所拥有的虚拟地址空间。例如，对应一个4GB的虚拟地址空间，其中3GB是用户空间，1GB是内核空间。当然内核空间的大小是可以通过参数配置调整的。对于用户空间，不同进程之间是不能共享的，而内核空间却是可共享的。Client进程向Server进程通信，恰恰是利用进程间可共享 的内核内存空间来完成底层通信工作的。Client端与Server端进程往往采用ioctl等方法与内核空间的驱动进行交互。 



**Binder原理** 

Binder通信采用C/S架构，从组件视角来说，包含Client、Server、ServiceManager以及Binder驱动，其中ServiceManager用于管理系统中的各种服务。架构图如下所示： 

![image-20220126175521836](D:\Typora\img\image-20220126175521836.png)

**Client**进程：使用服务的进程。 

**Server**进程：提供服务的进程。 

**ServiceManager**进程：ServiceManager的作用是将字符形式的Binder名字转化成Client中对该Binder的引用，使得Client能够通过Binder名字获得对Server中Binder实体的引用。 

**Binder**驱动：驱动负责进程之间Binder通信的建立，Binder在进程之间的传递，Binder引用计数管理，数据包在进程之间的传递和交互等一系列底层支持。 



**Binder运行机制** 

图中Client/Server/ServiceManage之间的相互通信都是基于Binder机制。既然基于Binder机制通信，那么同样也是C/S架构，则图中的3大步骤都有相应的Client端与Server端。 

**注册服务(addService)：**Server进程要先注册Service到ServiceManager。该过程：Server是客户端，ServiceManager是服务端。 

**获取服务(getService)：**Client进程使用某个Service前，须先向ServiceManager中获取相应的Service。该过程：Client是客户端，ServiceManager是服务端。 

**使用服务：**Client根据得到的Service信息建立与Service所在的Server进程通信的通路，然后就可以直接与Service交互。该过程：Client是客户端，Server是服务端。

图中的Client，Server，Service Manager之间交互都是虚线表示，是由于它们彼此之间不是直接交互的，而是都通过与Binder驱动进行交互的，从而实现IPC通信 （Interprocess Communication）方式。其中Binder驱动位于内核空间，Client，Server，Service Manager位于用户空间。Binder驱动和Service Manager可以看做是Android平台的基础架构，而Client和Server是Android的应用层，开发人员只需自 定义实现Client、Server端，借助Android的基本平台架构便可以直接进行IPC通信。



**AIDL的使用** 

AIDL (Android Interface Definition Language) 是一种接口定义语言，用于生成可以在Android设备上两个进程之间进行进程间通信(Interprocess Communication, IPC)的代码。如果在一个进程中（例如Activity）要调用另一个进程中（例如Service）对象的操作，就可以使用AIDL生成可序列化的参数，来完成进程间通信。 

服务端进程：

1：创建AIDL文件如xxx.aidl

2：在接口中自定义方法，如果有bean则实例化改bean实体类

3：构建Service远程服务

4：最后在清单文件里 注册服务，其中属性要加上export：true 让外部程序可以访问。

客户端进程：

1：将服务端的AIDL文件拷贝到main文件夹下，（如果有bean也要对应拷贝）。（包名要求与服务端的一致）

2：重建一下项目检查一下java文件是否构建成功

3：连接绑定服务，获取aidl接口实例。

 4：按需调用远程接口。

TODO：尝试做一个小demo



#### Parcelable和Serializable的区别

**作用** 

Serializable的作用是为了保存对象的属性到本地文件、数据库、网络流、**rmi**以方便数据传输，当然这种传输可以是程序内的也可以是两个程序间的。而Android的Parcelable的设计初衷是因为Serializable效率过慢，为了在程序内不同组件间以及不同**Android**程序间**(AIDL)**高效的传输数据而设计，这些数据仅在内存中存在， Parcelable是通过IBinder通信的消息的载体。

**效率及选择** 

Parcelable的性能比Serializable好，在内存开销方面较小，所以在**内存间数据传输时推荐使用Parcelable，如activity间传输数据，而Serializable可将数据持久化方便保存，所以在需要保存或网络传输数据时选择Serializable**，因为android不同版本Parcelable可能不同，所以不推荐使用Parcelable进行数据持久化。 

**编程实现** 

对于Serializable，类只需要实现Serializable接口，并提供一个序列化版本id(serialVersionUID)即可。而Parcelable则需要实现writeToParcel、describeContents函数以及静态的CREATOR变量，实际上就是将如何打包和解包的工作自己来定义，而序列化的这些操作完全由底层实现。

```java
//Serializable
public class Person implements Serializable{
	private static final long serialVersionUID = -7060210544600464481L;
 	private String name;
	 private int age;
  
 public String getName(){
 	 return name;
 }
  
 public void setName(String name){
	  this.name = name;
 }
  
 public int getAge(){
	  return age;
 }
  
 public void setAge(int age){
 	 this.age = age;
 }
}
```

```java
//Parcelable
public class MyParcelable implements Parcelable { 
	private int mData; 
	private String mStr; 
    
	public int describeContents() { 
		return 0; 
	}

	// 写数据进行保存 
	public void writeToParcel(Parcel out, int flags) { 
		out.writeInt(mData); 
		out.writeString(mStr); 
	}

	// 用来创建自定义的Parcelable的对象 
	public static final Parcelable.Creator<MyParcelable> CREATOR = new Parcelable.Creator<MyParcelable>() { 
		public MyParcelable createFromParcel(Parcel in) { 
			return new MyParcelable(in); 
	}

	public MyParcelable[] newArray(int size) { 
		return new MyParcelable[size]; 
	}};

	// 读数据进行恢复 
	private MyParcelable(Parcel in) { 
		mData = in.readInt(); 
		mStr = in.readString(); 
	} 
}
```

Serializable序列化不保存静态变量，可以使用Transient关键字对部分字段不进行序列化，也可以覆盖writeObject、readObject方法以实现序列化过程自定义。 



#### App启动流程

![image-20220127142150419](D:\Typora\img\image-20220127142150419.png)

启动流程： 

①点击桌面App图标，Launcher进程采用Binder IPC向system_server进程发起startActivity请求； 

②system_server进程接收到请求后，向zygote进程发送创建进程的请求； 

③Zygote进程fork出新的子进程，即App进程； 

④App进程，通过Binder IPC向sytem_server进程发起attachApplication请求； 

⑤system_server进程在收到请求后，进行一系列准备工作后，再通过binder IPC向App进程发送scheduleLaunchActivity请求； 

⑥App进程的binder线程（ApplicationThread）在收到请求后，通过handler向主线程发送LAUNCH_ACTIVITY消息； 

⑦主线程在收到Message后，通过发射机制创建目标Activity，并回调Activity.onCreate()等方法。 

⑧到此，App便正式启动，开始进入Activity生命周期，执行完onCreate/onStart/onResume方法，UI渲染结束后便可以看到App的主界面。



**zygote** 

zygote意为“受精卵“。Android是基于Linux系统的，而在Linux中，所有的进程都是由init进程直接或者是间接fork出来的，zygote进程也不例外。 

我们都知道，每一个App其实都是 

● 一个单独的dalvik虚拟机 

● 一个单独的进程 

所以当系统里面的第一个zygote进程运行之后，在这之后再开启App，就相当于开启一个新的进程。而为了实现资源共用和更快的启动速度，Android系统开启新进程的方式，是通过fork第一个zygote进程实现的。所以说，除了第一个zygote进程，其他应用所在的进程都是zygote的子进程。



**system_server** 

SystemServer也是一个进程，而且是由zygote进程fork出来的。 

知道了SystemServer的本质，我们对它就不算太陌生了，这个进程是Android Framework里面两大非常重要的进程之一——另外一个进程就是上面的zygote进程。

为什么说SystemServer非常重要呢？因为系统里面重要的服务都是在这个进程里面开启的，比如 ActivityManagerService、PackageManagerService、WindowManagerService等等。 



**ActivityManagerService** 

ActivityManagerService，简称AMS，服务端对象，负责系统中所有Activity的生命周期。 

ActivityManagerService进行初始化的时机很明确，就是在SystemServer进程开启的时候，就会初始化ActivityManagerService。 

如果想打开一个**App**的话，需要**AMS**去通知**zygote**进程，除此之外，其实所有的**Activity**的开启、暂停、关闭都需要**AMS**来控制，所以我们说，**AMS**负责系统中所有**Activity**的生命周期。

在Android系统中，任何一个**Activity**的启动都是由**AMS**和应用程序进程（主要是**ActivityThread**）相互配合来完成的。**AMS**服务统一调度系统中所有进程的**Activity**启动，而每个**Activity**的启动过程则由其所属的进程具体来完成。 



**Launcher** 

**Launcher**本质上也是一个应用程序，和我们的**App**一样，也是继承自**Activity** 。Launcher实现了点击、长按等回调接口，来接收用户的输入。我们点击图标的时候，捕捉图标点击事件，然后**startActivity()**发送对应的**Intent**请求。



**Instrumentation**和**ActivityThread** 

每个Activity都持有Instrumentation对象的一个引用，但是整个进程只会存在一个Instrumentation对象。 Instrumentation这个类里面的方法大多数和Application和Activity有关，这个类就是完成对**Application**和**Activity**初始化和生命周期的工具类。Instrumentation这个类很重要，对Activity生命周期方法的调用根本就离不开他，他可以说是一个大管家。 

ActivityThread，依赖于UI线程。App和AMS是通过Binder传递信息的，那么ActivityThread就是专门与AMS的外交工作的。 



**ApplicationThread** 

前面我们已经知道了App的启动以及Activity的显示都需要AMS的控制，那么我们便需要和服务端的沟通，而这个沟通是双向的。 

客户端**-->**服务端 

![image-20220127144919975](D:\Typora\img\image-20220127144919975.png)

而且由于继承了同样的公共接口类，ActivityManagerProxy提供了与ActivityManagerService一样的函数原型，使用户感觉不出Server是运行在本地还是远端，从而可以更加方便的调用这些重要的系统服务。 

服务端**-->**客户端 

![image-20220127144930982](D:\Typora\img\image-20220127144930982.png)

还是通过Binder通信，不过是换了另外一对，换成了ApplicationThread和ApplicationThreadProxy。 他们也都实现了相同的接口IApplicationThread 



**总结**：创建进程---》绑定Application---》显示Activity界面



#### Android性能优化

**布局优化** 

关于布局优化的思想很简单，就是尽量减少布局文件的层级。这个道理很浅显，布局中的层级少了，就意味着Android绘制时的工作量少了，那么程序的性能自然就提高了。 

**①**删除布局中无用的控件和层次，其次有选择地使用性能比较低的**ViewGroup**。 

例如：如果布局中既可以使用LinearLayout也可以使用RelativeLayout，那么就采用LinearLayout，这是因为RelativeLayout的功能比较复杂，它的布局过程需要花费更多的CPU时间。FrameLayout和LinearLayout一样都是一种简单高效的ViewGroup，因此可以考虑使用它们，但是很多时候单纯通过一个**LinearLayout**或者**FrameLayout**无法实现产品效果，需要通过嵌套的方式来完成。这种情况下还是建议采用**RelativeLayout,**因为**ViewGroup**的嵌套就相当于增加了布局的层级，同样会降低程序的性能。

**②**采用标签**,**标签**,ViewStub**。 

标签主要用于布局重用。 

标签一般和配合使用，可以降低减少布局的层级。 

ViewStub提供了按需加载的功能，当需要时才会将ViewStub中的布局加载到内存，提高了程序初始化效率。 

**③**避免多度绘制 

过度绘制（Overdraw）描述的是屏幕上的某个像素在同一帧的时间内被绘制了多次。在多层次重叠的 UI 结构里面，如果不可见的 UI 也在做绘制的操作，会导致某些像素区域被绘制了多次，同时也会浪费大量的 CPU 以及 GPU 资源。 



**绘制优化** 

绘制优化是指**View**的**onDraw**方法要避免执行大量的操作，这主要体现在两个方面：

**①**onDraw中不要创建新的局部对象。 

因为onDraw方法可能会被频繁调用，这样就会在一瞬间产生大量的临时对象，这不仅占用了过多的内存而且还会导致系统更加频繁gc，降低了程序的执行效率。 

**②**onDraw方法中不要做耗时的任务，也不能执行成千上万次的循环操作，尽管每次循环都很轻量级，但是大量的循环仍然十分抢占**CPU**的时间片，这会造成**View**的绘制过程不流畅。 



**内存泄漏优化** 

内存泄漏是开发过程中的一个需要重视的问题，但是由于内存泄露问题对开发人员的经验和开发意识有较高的要求，因此也是开发人员最容易犯的错误之一。内存泄露的优化分为两个方面： 

**①**在开发过程中避免写出有内存泄漏的代码 

**②**通过一些分析工具比如**MAT**来找出潜在的内存泄露，然后解决。 



**响应速度优化** 

响应速度优化的核心思想就是避免在主线程中做耗时操作。 

如果有耗时操作，可以开启子线程执行，即采用异步的方式来执行耗时操作。 

如果在主线程中做太多事情，会导致Activity启动时出现黑屏现象，甚至ANR。 

**Android**规定，**Activity**如果**5**秒钟之内无法响应屏幕触摸事件或者键盘输入事件就会出现**ANR**，而**BroadcastReceiver**如果**10**秒钟之内还未执行完操作也会出现**ANR**。 

当一个进程发生了ANR之后，系统会在/data/anr目录下创建一个文件traces.txt，通过分析这个文件就能定位出ANR的原因。



**ListView/RecycleView及Bitmap优化** 

**ListView/RecycleView**的优化思想主要从以下几个方面入手： 

①使用ViewHolder模式来提高效率 

②异步加载：耗时的操作放在异步线程中 

③ListView/RecycleView的滑动时停止加载和分页加载 

**Bitmap**优化 

主要是对加载图片进行压缩，避免加载图片多大导致OOM出现。



**线程优化**

线程优化的思想就是采用线程池，避免程序中存在大量的**Thread**。线程池可以重用内部的线程，从而避免了线程的创建和销毁锁带来的性能开销，同时线程池还能有效地控制线程池的最大并发数，避免大量的线程因互相抢占系统资源从而导致阻塞现象的发生。因此在实际开发中，尽量采用线程池，而不是每次都要创建一个Thread对象。 



**其他性能优化建议** 

①避免过度的创建对象 

②不要过度使用枚举，枚举占用的内存空间要比整型大 

③常量请使用static final来修饰 

④使用一些Android特有的数据结构，比如SparseArray和Pair等 

⑤适当采用软引用和弱引用 

⑥采用内存缓存和磁盘缓存 

⑦尽量采用静态内部类，这样可以避免潜在的由于内部类而导致的内存泄漏。 



#### Android 内存泄漏总结 

内存泄漏，简单通俗的讲，就是该被释放的对象没有释放，一直被某个或某些实例所持有却不再被使用导致 GC 不能回收 。



**Java 内存分配策略** 

- 静态存储区（方法区）：主要存放静态数据、全局 static 数据和常量。这块内存在程序编译时就已经分配好，并且在程序整个运行期间都存在。 

- 栈区 ：当方法被执行时，方法体内的局部变量都在栈上创建，并在方法执行结束时这些局部变量所持有的内存将会自动被释放。因为栈内存分配运算内置于处理器的指令集中，效率很高，但是分配的内存容量有限。 

- 堆区 ： 又称动态内存分配，通常就是指在程序运行时直接 new 出来的内存。这部分内存在不使用时将会由 Java 垃圾回收器来负责回收。 

局部变量的基本数据类型和引用存储于栈中，引用的对象实体存储于堆中。—— 因为它们属于方法中的变量，生命周期随方法而结束。 

成员变量全部存储于堆中（包括基本数据类型，引用和引用的对象实体）—— 因为它们属于类，类对象终究是要被new出来使用的



**Java内存泄漏**

在Java中，内存泄漏就是存在一些被分配的对象，这些对象有下面两个特点，首先，这些对象是可达的，即在有向图中，存在通路可以与其相连；其次，这些对象是无用的，即程序以后不会再使用这些对象。如果对象满足这两个条件，这些对象就可以判定为Java中的内存泄漏，这些对象不会被GC所回收，然而它却占用内存。

![image-20220127151132783](D:\Typora\img\image-20220127151132783.png)





**Android中常见的内存泄漏**

- **集合类泄漏**

集合类如果仅仅有添加元素的方法，而没有相应的删除机制，导致内存被占用。如果这个集合类是全局性的变量 (比如类中的静态属性，全局性的 map 等即有静态引用或 final 一直指向它)，那么没有相应的删除机制，很可能导致集合所占用的内存只增不减。

- **单例造成的内存泄漏** 

由于单例的静态特性使得其生命周期跟应用的生命周期一样长，所以如果使用不恰当的话，很容易造成内存泄漏。

```java
public class AppManager { 
	private static AppManager instance; 
	private Context context; 
	private AppManager(Context context) { 
		this.context = context; //this.context = context.getApplicationContext();
	}
    
	public static AppManager getInstance(Context context) { 
		if (instance == null) { 
		instance = new AppManager(context); 
		}
		return instance; 
	} 
} 
```

这是一个普通的单例模式，当创建这个单例的时候，由于需要传入一个Context，所以这个Context的生命周期的长短至关重要： 

1、如果此时传入的是 Application 的 Context，因为 Application 的生命周期就是整个应用的生命周期，所以这将没有任何问题。 

2、如果此时传入的是 Activity 的 Context，当这个 Context 所对应的 Activity 退出时，由于该 Context 的引用被单例对象所持有，其生命周期等于整个应用程序的生命周期，所以当前 Activity 退出时它的内存并不会被回收，这就造成泄漏了。 



- **匿名内部类/非静态内部类和异步线程** 

  - 非静态内部类创建静态实例造成的内存泄漏 

  非静态内部类默认会持有外部类的引用，而该非静态内部类又创建了一个静态的实例，该实例的生命周期和应用的一样长，这就导致了该静态实例一直会持有该Activity的引用，导致Activity的内存资源不能正常回收。正确的做法为： 将该内部类设为静态内部类或将该内部类抽取出来封装成一个单例。

  - 匿名内部类 

  android开发经常会继承实现Activity/Fragment/View，此时如果你使用了匿名类，并被异步线程持有了，那要小心了，如果没有任何措施这样一定会导致泄露。

```java
public class MainActivity extends Activity { 
... 
	Runnable ref1 = new MyRunable(); 
	Runnable ref2 = new Runnable() {  
		@Override 
		public void run() { 
            
		} 
	};
... 
} 
```

ref1没什么特别的。 但ref2这个匿名类的实现对象里面多了一个引用：this$0这个引用指向MainActivity.this，也就是说当前的MainActivity实例会被ref2持有，如果将这个引用再传入一个异步线程，此线程和此Acitivity生命周期不一致的时候，就造成了Activity的泄露。 



- **Handler 造成的内存泄漏** 

由于 Handler 属于 TLS(Thread Local Storage) 变量, 生命周期和 Activity 是不一致的。因此这种实现方式一般很难保证跟 View或者 Activity 的生命周期保持一致，故很容易导致无法正确释放。 

```java
public class SampleActivity extends Activity { 
	private final Handler mLeakyHandler = new Handler() { 
		@Override 
		public void handleMessage(Message msg) { 
		// ... 
		} 
	}

	@Override 
	protected void onCreate(Bundle savedInstanceState) { 
		super.onCreate(savedInstanceState); 
		// Post a message and delay its execution for 10 minutes. 
		mLeakyHandler.postDelayed(new Runnable() { 
			@Override 
			public void run() { /* ... */ } 
		}, 1000 * 60 * 10); 
		// Go back to the previous Activity. 
		finish(); 
	} 
} 
```

在该 SampleActivity 中声明了一个延迟10分钟执行的消息 Message，mLeakyHandler 将其 push 进了消息队列 MessageQueue 里。当该 Activity 被finish() 掉时，延迟执行任务的 Message 还会继续存在于主线程中，它持有该Activity 的 Handler 引用，所以此时 finish() 掉的 Activity 就不会被回收了从而造成内存泄漏（因 Handler 为非静态内部类，它会持有外部类的引用，在这里就是指SampleActivity）

修复方法：在 Activity 中避免使用非静态内部类，比如上面我们将 Handler 声明为静态的，则其存活期跟 Activity 的生命周期就无关了。同时通过弱引用的方式引入Activity，避免直接将 Activity 作为 context 传进去

```java
private static class MyHandler extends Handler { 
    private final WeakReference<SampleActivity> mActivity;
    public MyHandler(SampleActivity activity) {
        mActivity = new WeakReference<SampleActivity>(activity); 
    }
    @Override
    public void handleMessage(Message msg) { 
        SampleActivity activity = mActivity.get(); 
        if (activity != null) { // ... 
        } 
    }
}
```

即推荐使用静态内部类 + WeakReference 这种方式。每次使用前注意判空。

- **尽量避免使用 static 成员变量** 

如果成员变量被声明为 static，那我们都知道其生命周期将与整个app进程生命周期一样。这会导致一系列问题，如果你的app进程设计上是长驻内存的，那即使app切到后台，这部分内存也不会被释放。按照现在手机app内存管理机制，占内存较大的后台进程将优先回收，因为如果此app做过进程互保保活，那会造成app在后台频繁重启。当手机安装了你参与开发的app以后一夜时间手机被消耗空了 电量、流量，你的app不得不被用户卸载或者静默。 

修复方法是： 不要在类初始时初始化静态成员。可以考虑lazy初始化。 架构设计上要思考是否真的有必要这样做，尽量避免。如果架构需要这么设计，那么此对象的生命周期你有责任管理起来

- **避免 override finalize()**

1、finalize 方法被执行的时间不确定，不能依赖与它来释放紧缺的资源。时间不确定的原因是： 

虚拟机调用GC的时间不确定 

Finalize daemon线程被调度到的时间不确定 

2、finalize 方法只会被执行一次，即使对象被复活，如果已经执行过了 finalize方法，再次被 GC 时也不会再执行了，原因是： 

含有 finalize 方法的 object 是在 new 的时候由虚拟机生成了一个 finalize reference 在来引用到该Object的，而在 finalize 方法执行的时候，该 object 所对应的 finalize Reference 会被释放掉，即使在这个时候把该 object 复活(即用强引用引用住该 object )，再第二次被 GC 的时候由于没有了 finalize reference 与之对应，所以 finalize 方法不会再执行。 

3、含有Finalize方法的object需要至少经过两轮GC才有可能被释放。 

- **资源未关闭造成的内存泄漏** 

对于使用了BraodcastReceiver，ContentObserver，File，游标 Cursor，Stream，Bitmap等资源的使用，应该在Activity销毁时及时关闭或者注销，否则这些资源将不会被回收，造成内存泄漏。 



**工具分析 **

 MAT(Memory AnalysisTools) 和 YourKit 



#### Android布局优化

**<include/>**

<include/>标签在布局优化中是使用最多的一个标签了，它就是为了解决重复定义布局的问题。标签就相当于C、C++中的include头文件一样，把一些常用的底层的API封装起来，需要的时候引入即可。



**<merge/>** 

<merge/>标签都是与**<include/>**标签组合使用的，它的作用就是可以有效减少View树的层次来优化布局。

另外有两点需要特别注意：

1.<merge/>只可以作为xml layout的根节点。

2.当需要扩充的xml layout本身是由merge作为根节点的话，需要将被导入的xml layout置于 viewGroup中，同时需要设置attachToRoot为True。



**ViewStub** 

在开发过程中，经常会遇到这样一种情况，有些布局很复杂但是却很少使用。例如条目详情、进度条标识或者未读消息等，这些情况如果在一开始初始化，虽然设置可见性 View.GONE ,但是在Inflate的时候View仍然会被Inflate，仍然会创建对象，由于这些布局又想到复杂，所以会很消耗系统资源。ViewStub就是为了解决上面问题的，ViewStub是一个轻量级的View，它一个看不见的，不占布局位置，占用资源非常小的控件。 



#### Android权限处理 

![image-20220128144458234](D:\Typora\img\image-20220128144458234.png)

Android6.0以上把权限分为普通权限和危险权限，所以危险权限是需要动态申请，给予用户提示的。 

危险权限都是一组一组的。 分组对权限机制的申请是有一定影响的。例如app运行在android 6.x的机器上，对于授权机制是这样的。如果你申请某个危险的权限，假设你的app早已被用户授权了同一组的某个危险权限，那么系统会立即授权，而不需要用户去点击授权。比如你的app对READ_CONTACTS已经授权了，当你的app申请WRITE_CONTACTS时，系统会直接授权通过。 

动态申请权限的逻辑：

![image-20220128144939932](D:\Typora\img\image-20220128144939932.png)



**开源项目** 

PermissionsDispatcher 使用注解的方式，动态生成类处理运行时权限. 

Grant 简化运行时权限的处理，比较灵活 

android-RuntimePermissions Google官方的例子 



#### Android热修复原理 

**Android中的ClassLoader**

Android跟java有很大的渊源，基于jvm的java应用是通过ClassLoader来加载应用中的class的，Android对jvm优化过，使用的是dalvik虚拟机，且**class文件会被打包进一个dex文件中**，底层虚拟机有所不同，那么它们的类加载器当然也是会有所区别。

Android中最主要的类加载器有如下4个

- BootClassLoader：加载Android Framework层中的class字节码文件（类似java的Bootstrap ClassLoader）
- PathClassLoader：加载已经安装到系统中的Apk的class字节码文件（类似java的App ClassLoader）
- DexClassLoader：加载制定目录的class字节码文件（类似java中的Custom ClassLoader）
- BaseDexClassLoader：PathClassLoader和DexClassLoader的父类



**PathClassLoader与DexClassLoader的区别**

- PathClassLoader：只能加载已经安装到Android系统中的apk文件（/data/app目录），是Android默认使用的类加载器。
- DexClassLoader：可以加载任意目录下的dex/jar/apk/zip文件，比PathClassLoader更灵活，是实现热修复的重点。
- PathClassLoader与DexClassLoader都继承于BaseDexClassLoader。
- PathClassLoader与DexClassLoader在构造函数中都调用了父类的构造函数，但DexClassLoader多传了一个optimizedDirectory。



**Element集合—DexPathList**

DexPathList的构造函数是将一个个的程序文件（可能是dex、apk、jar、zip）封装成一个个Element对象，最后添加到Element集合中。



**总结：**安卓的类加载器在加载一个类时会先从自身DexPathList对象中的Element数组中获取（Element[] dexElements）到对应的类，之后再加载。采用的是数组遍历的方式，不过遍历出来的是一个个的dex文件。在for循环中，首先遍历出来的是dex文件，然后再是从dex文件中获取class，所以，我们只要让修复好的class打包成一个dex文件，放于Element数组的第一个元素，这样就能保证获取到的class是最新修复好的class了（当然，有bug的class也是存在的，不过是放在了Element数组的最后一个元素中，所以没有机会被拿到而已）。



#### Android插件化入门

**Android插件化介绍** 

所谓插件化，就是让我们的应用不必再像原来一样把所有的内容都放在一个**apk**中，可以把一些功能和逻辑单独抽出来放在插件apk中，然后主apk做到［按需调用］，这样的好处是一来可以减少主apk的体积，让应用更轻便，二来可以做到热插拔，更加动态化。 

**Android插件化基础** 

**1.插件化的特点 **

1) 应用在运行的时候通过加载一些本地不存在的可执行文件实现一些特定的功能; 

2) 这些可执行文件是可以替换的; 

3) 更换静态资源（比如换启动图、换主题、或者用服务器参数开关控制广告的隐藏现实等）不属于动态加载; 

4) Android中动态加载的核心思想是动态调用外部的 dex文件，极端的情况下，Android APK自身带有的Dex文件只是一个程序的入口（或者说空壳），所有的功能都通过从服务器下载最新的Dex文件完成; 

**2.插件化必备基础** 

**①ClassLoader类加载器 **

要想实现加载外部dex文件（即插件）来实现热部署，那么必然要把其中的class文件加载到内存中。 

其中涉及到两种ClassLoader:DexClassLoader和PathClassLoader。而DexClassLoader可以加载外部的jar,dex等文件，正是我们需要的。 

**②Java反射 **

因为插件apk与宿主apk不在一个apk内，那么一些类的访问必然要通过反射进行获取。

**③插件资源访问** 

res里的每一个资源都会在R.java里生成一个对应的Integer类型的id，APP启动时会先把R.java注册到当前的上下文环境，我们在代码里以R文件的方式使用资源时正是通过使用这些id访问res资源，然而插件的R.java并没有注册到当前的上下文环境，所以插件的res资源也就无法通过id使用了。查看源码，通过“addAssetPath”方法重新生成一个新的Resource对象来保存插件中的资源，避免冲突。 

**④代理模式** 

插件化实现的过程主要靠欺上瞒下，坑蒙拐骗来实现。想想虽然加载进来了Activity等组件，但也仅仅是最为一个对象而存在，并没有在AndroidManifest中注册，没有生命周期的回调，并不能实现我们想要的效果。因此无论是dynamic_load_apk通过代理activity来操控插件activity的方式，还是DroidPlugin通过hook activity启动过程来启动插件activity的方式，都是对代理模式的应用。 





#### ListView缓存

ListView的缓存有两级，在ListViewi里面有一个内部类 RecycleBin，RecycleBin有两个对象Active View和Scrap View来管理缓存，Active View是第一级，Scrap View是第二级。

- **Active View**：是缓存在屏幕内的ItemView，当列表数据发生变化时，屏幕内的数据可以直接拿来复用，无须进行数据绑定。
- **Scrap view**：缓存屏幕外的ItemView，这里所有的缓存的数据都是"脏的"，也就是数据需要重新绑定，也就是说屏幕外的所有数据在进入屏幕的时候都要走一遍getView（）方法

当Active View和Scrap View中都没有缓存的时候就会直接create view。



#### RecyclerView缓存机制

Recycler缓存ViewHolder对象有4个等级，优先级从高到底依次为：

- mAttachedScrap：缓存屏幕中可见范围的ViewHolder；
- mCachedViews：缓存滑动时即将与RecyclerView分离的ViewHolder，默认最大2个；
- ViewCacheExtension：自定义实现的缓存；
- RecycledViewPool ：ViewHolder缓存池，可以支持不同的ViewType；

**mAttachedScrap**

mAttachedScrap存储的是当前屏幕中的ViewHolder，mAttachedScrap的对应数据结构是ArrayList，在调用LayoutManager#onLayoutChildren方法时对views进行布局，此时会将RecyclerView上的Views全部暂存到该集合中，该缓存中的ViewHolder的特性是，如果和RV上的position或者itemId匹配上了那么可以直接拿来使用的，无需调用onBindViewHolder方法。

**mChangedScrap**

mChangedScrap和mAttachedScrap属于同一级别的缓存，不过mChangedScrap的调用场景是notifyItemChanged和notifyItemRangeChanged，只有发生变化的ViewHolder才会放入到mChangedScrap中。mChangedScrap缓存中的ViewHolder是需要调用onBindViewHolder方法重新绑定数据的。

**mCachedViews**

mCachedViews缓存滑动时即将与RecyclerView分离的ViewHolder，按子View的position或id缓存，默认最多存放2个。mCachedViews对应的数据结构是ArrayList，但是该缓存对集合的大小是有限制的。该缓存中ViewHolder的特性和mAttachedScrap中的特性是一样的，只要position或者itemId对应就无需重新绑定数据。开发者可以调用setItemViewCacheSize(size)方法来改变缓存的大小，该层级缓存触发的一个常见的场景是滑动RecyclerView。当然调用notify()也会触发该缓存。

**ViewCacheExtension**

ViewCacheExtension是需要开发者自己实现的缓存。

**RecyclerViewPool**

ViewHolder缓存池，本质上是一个SparseArray，其中key是ViewType(int类型)，value存放的是 ArrayList< ViewHolder>，默认每个ArrayList中最多存放5个ViewHolder。



### 四、Android框架

#### OkHttp解析

##### 基本流程

**基本用法：**

```java
OkHttpClient client = new OkHttpClient();

Request request = new Request.Builder()
                .url(url)
                .build();
// 同步
Response response = client.newCall(request).execute();

// 异步
client.newCall(request).enqueue(new Callback() {
    @Override
    public void onFailure(Call call, IOException e) {
    }

    @Override
    public void onResponse(Call call, Response response) throws IOException {
    }
});
```

**OkHttpClient、Request 及 Call 的创建：**

OkHttpClient 的创建采用了 **Builder** 模式，可以配置 Interceptor、Cache 等。可以设置的参数很多，其中部分参数如下:

```java
  final Dispatcher dispatcher;  // 请求的分发器
  final @Nullable Proxy proxy; // 代理
  final List<Protocol> protocols;  // http协议
  final List<ConnectionSpec> connectionSpecs; 
  final List<Interceptor> interceptors;
  final List<Interceptor> networkInterceptors;
  final EventListener.Factory eventListenerFactory;
  final ProxySelector proxySelector;
  final CookieJar cookieJar;
  final @Nullable Cache cache;
```

Request 与 OkHttpClient 的创建类似，也是用了 Buidler 模式，但是其参数要少很多：

```java
public final class Request {
	final HttpUrl url;
	final String method;
	final Headers headers;
	final @Nullable RequestBody body;
	final Object tag;
	...
}
```

参数的含义都很明确，即使 Http 协议的url、header、method 以及 body 部分。变量 `tag` 用于标识一条 `Request`，可用于发送后取消这条请求。

`client.newCall(request)` 生成一个 Call 对象。Call 实际上是一个接口，它封装了 Request，并且用于发起实际的网络请求。下面是 Call 的全部代码：

```java
public interface Call extends Cloneable {
    
  Request request();

  Response execute() throws IOException;

  void enqueue(Callback responseCallback);

  void cancel();

  boolean isExecuted();

  boolean isCanceled();

  Call clone();

  interface Factory {
    Call newCall(Request request);
  }
}
```

其中包含了与网络请求相关的操作，包括发起、取消等。看一下 `OkHttpClient` 是如何创建 `Call` 的：

```java
  @Override public Call newCall(Request request) {
    return new RealCall(this, request, false /* for web socket */);
  }
```

从代码可以看到，实际上是创建了一个 `RealCall` 对象，它也是 Call 的唯一一个实现类。

有了 `RealCall` 对象后，就可以发起网络请求了，可以是同步请求（`execute`）或者是异步请求（`enqueue`）。

**同步请求：**

调用 `RealCall#execute()` 即是发起同步请求，代码如下：

```java
  @Override public Response execute() throws IOException {
    synchronized (this) {
       //判断这条请求是不是已经执行过，如果是则会抛出异常
      if (executed) throw new IllegalStateException("Already Executed");
      executed = true;
    }
    captureCallStackTrace();
    try {
      //把当前的 Call 加入到 Dispatcher 的一个队列中
      client.dispatcher().executed(this);
      //真正执行了网络请求并获得 Response 并返回
      Response result = getResponseWithInterceptorChain();
      if (result == null) throw new IOException("Canceled");
      return result;
    } finally {
      //从队列中移除这条请求
      client.dispatcher().finished(this);
    }
  }
```

拦截器 Interceptor

`getResponseWithInterceptorChain` 的代码如下：

```java
  Response getResponseWithInterceptorChain() throws IOException {
   // Build a full stack of interceptors.
   //创建一个 List 用于添加 Interceptor
   List<Interceptor> interceptors = new ArrayList<>();
   //添加创建OkHttpClient对象时自定义的 interceptors
   interceptors.addAll(client.interceptors());
   //重试及重定向
   interceptors.add(retryAndFollowUpInterceptor);
   //请求参数的添加
   interceptors.add(new BridgeInterceptor(client.cookieJar()));
   //缓存
   interceptors.add(new CacheInterceptor(client.internalCache()));
   //连接
   interceptors.add(new ConnectInterceptor(client));
   if (!forWebSocket) {
       //用户自定义的 networkinterceptors
     interceptors.addAll(client.networkInterceptors());
   }
   //发送和接收数据
   interceptors.add(new CallServerInterceptor(forWebSocket));

   Interceptor.Chain chain = new RealInterceptorChain(
       interceptors, null, null, null, 0, originalRequest);
   return chain.proceed(originalRequest);
 }
```

在研究各个 interceptor 之前，需要考虑一下如何让这些拦截器一个接着一个的执行？在添加了各种 interceptors 之后，创建了一个 `RealInterceptorChain` 对象。`RealInterceptorChain` 是接口 `Chain` 的实现类，`Chain` 是 **链** 的意思，其作用是把各个 Interceptor 串起来依次执行。在获得了 `RealInterceptorChain` 之后调用其 `proceed` 方法，让 `Request` 请求继续执行。

下面具体分析 `RealInterceptorChain`，它有如下的成员变量：

```java
  private final List<Interceptor> interceptors;  // 拦截器
  private final StreamAllocation streamAllocation; // 流管理器
  private final HttpCodec httpCodec; // http流，发送请求数据并读取响应数据
  private final RealConnection connection;  // scoket的连接
  private final int index; // 当前拦截器的索引
  private final Request request; // 当前的请求
  private int calls; // chain 的 proceed 调用次数的记录
```

其中 `streamAllocation`、`httpCodec` 和 `connection` 都与 socket 连接有关。看一下 `proceed` 方法：

```java
public Response proceed(Request request, StreamAllocation streamAllocation, HttpCodec httpCodec,
      RealConnection connection) throws IOException {
    if (index >= interceptors.size()) throw new AssertionError();

    calls++;

    // 如果已经有了一个流，确保即将到来的 request 是用它
    if (this.httpCodec != null && !this.connection.supportsUrl(request.url())) {
      throw new IllegalStateException("network interceptor " + interceptors.get(index - 1)
          + " must retain the same host and port");
    }
    
    // 如果已经有了一个流，确保这是对 call 唯一的调用
    if (this.httpCodec != null && calls > 1) {
      throw new IllegalStateException("network interceptor " + interceptors.get(index - 1)
          + " must call proceed() exactly once");
    }


    // Call the next interceptor in the chain. 
    //创建新的 RealInterceptorChain，其中 index 加1用于标识当前的拦截器
    RealInterceptorChain next = new RealInterceptorChain(
        interceptors, streamAllocation, httpCodec, connection, index + 1, request);  
    
    //通过 index 获取当前的拦截器
    Interceptor interceptor = interceptors.get(index); 
    //调用下一个拦截器的 intercept 方法，并把上面生成的新的 RealInterceptorChain 对象 next 传进去
    Response response = interceptor.intercept(next);   

    // Confirm that the next interceptor made its required call to chain.proceed().                                 
    if (httpCodec != null && index + 1 < interceptors.size() && next.calls != 1) {
      throw new IllegalStateException("network interceptor " + interceptor
          + " must call proceed() exactly once");
    }

    // Confirm that the intercepted response isn't null.
    if (response == null) {
      throw new NullPointerException("interceptor " + interceptor + " returned null");
    }

    return response;
  }
```

当前 `RealInterceptorChain` 的interceptors 的第一个是 `RetryAndFollowUpInterceptor`，下面是其 `intercept` 的代码：

```java
  @Override public Response intercept(Chain chain) throws IOException {
    Request request = chain.request();

    streamAllocation = new StreamAllocation(
        client.connectionPool(), createAddress(request.url()), callStackTrace);

    int followUpCount = 0;
    Response priorResponse = null;
    while (true) {
      if (canceled) {
        streamAllocation.release();
        throw new IOException("Canceled");
      }

      Response response = null;
      boolean releaseConnection = true;
      try {
        // 调用 chain 的 proceed
        response = ((RealInterceptorChain) chain).proceed(request, streamAllocation, null, null);
        releaseConnection = false;
      } catch (RouteException e) {
        ... // 省略部分代码，主要是错误重试以及重定向
    }
  }
```

这个 Interceptor 主要用于出错重试以及重定向的逻辑，其中省略了部分代码。在这个方法当中要关注的是再次调用了 `chain` 的 `proceed` 方法，这里的 `chain` 是之前新创建的 `next` 对象。相当于说通过调用 `Chain#proceed()` 将网络请求推向下一个拦截器（`proceed` 中会获取下一个 Interceptor 并调用其 `intercept` 方法），并且得到 response 对象，而下一个拦截器也是类似的操作。于是，多个 interceptors 就通过这种方式串起来依次执行，并且前一个 Interceptor 可以得到后一个 Interceptor 执行后的 response 从而进行处理。

通过不同的 Interceptor，OkHttp 实现了不同的功能。各个 Inercept 职责分明又不会互相耦合，并且可以非常方便的添加 Interceptor，这是 **责任链** 模式的体现，非常优雅的设计。



**异步请求：**

相比于同步请求，异步请求主要是增加了 Dispatcher 的处理。Dispatcher 是请求的分发器，它有一下的成员变量：

```java
private int maxRequests = 64;   // 最大连接数
private int maxRequestsPerHost = 5;  // 单个 host 最大连接数
private @Nullable Runnable idleCallback;   // 空闲时的回调

/** Executes calls. Created lazily. */
private @Nullable ExecutorService executorService; // 线程池

// 准备执行的异步 Call 的队列
private final Deque<AsyncCall> readyAsyncCalls = new ArrayDeque<>();

// 正在执行的的异步 Call 的队列
private final Deque<AsyncCall> runningAsyncCalls = new ArrayDeque<>();

// 正在执行的同步 Call 的队列
private final Deque<RealCall> runningSyncCalls = new ArrayDeque<>();
```

在 Dispatcher 中，默认支持的最大并发连接数是64，每个 host 最多可以有5个并发请求。

下面看一下线程池 `executorService` 的创建。线程池会在两个地方创建，分别是 Dispatcher 的构造函数或者是 `executorService` 方法中(如果调用了默认的构造函数)：

```java
// 默认构造函数没有创建
public Dispatcher() {}
// 自定义线程池
public Dispatcher(ExecutorService executorService) {
    this.executorService = executorService;
}
// 如果没有自定义线程池，则默认创建
public synchronized ExecutorService executorService() {
    if (executorService == null) {
      executorService = new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60, TimeUnit.SECONDS,
          new SynchronousQueue<Runnable>(), Util.threadFactory("OkHttp Dispatcher", false));
    }
    return executorService;
}
```

**enqueue**

每个 Call 被添加到某一个队列，如果是同步请求添加到 `runningSyncCalls` 中：

```java
synchronized void executed(RealCall call) {
    runningSyncCalls.add(call);
}
```

异步请求添加的逻辑如下：

```java
synchronized void enqueue(AsyncCall call) {
    //判断是否超出总共的最大连接数以及单个 host 的最大连接数
    if (runningAsyncCalls.size() < maxRequests && runningCallsForHost(call) < maxRequestsPerHost) {
        //如果没有则添加到 runningAsyncCalls 并且提交到线程池执行
      	runningAsyncCalls.add(call);
     	executorService().execute(call);
    } else {
        //否则添加到 readyAsyncCalls 等待后续执行
    	readyAsyncCalls.add(call);
    }
}
```

需要注意的是异步请求的 Call 不是原始的 Call，而是被包装为 `AsyncCall`：

```java
//AsyncCall 继承自 NamedRunnable，它其实就是一个为线程设置了名字的 Runnable
final class AsyncCall extends NamedRunnable {
    private final Callback responseCallback;

    AsyncCall(Callback responseCallback) {
      	super("OkHttp %s", redactedUrl());
      	this.responseCallback = responseCallback;
    }
    ...
    // AsyncCall 的主要逻辑都写在 execute 中。可以看到最终还是调用了 getResponseWithInterceptorChain 方法，所以后续执行网络请求的逻辑是一样的。
    @Override 
    protected void execute() {
    	boolean signalledCallback = false;
      	try {
        	// 调用 getResponseWithInterceptorChain
        	Response response = getResponseWithInterceptorChain();
        	if (retryAndFollowUpInterceptor.isCanceled()) {
          		signalledCallback = true;
          		responseCallback.onFailure(RealCall.this, new IOException("Canceled"));
       		} else {
          	signalledCallback = true;
          	responseCallback.onResponse(RealCall.this, response);
        }
      } catch (IOException e) {
        	if (signalledCallback) {
          	// Do not signal the callback twice!
          	Platform.get().log(INFO, "Callback failure for " + toLoggableString(), e);
        } else {
          	responseCallback.onFailure(RealCall.this, e);
        }
      } finally {
        	client.dispatcher().finished(this);
      }
    }
}
```



**finished**

在上面的代码中，finally 里面执行了 `client.dispatcher().finished(this)`，在同步请求 `RealCall#execute()` 中也有类似的一行代码。`finished` 的作用是让 Dispatcher 从队列中移除已完成的 Call，对于异步请求还会从 `readyAsyncCalls` 中取出等待中的请求提交给线程池。下面是具体代码：

```java
  /** Used by {@code AsyncCall#run} to signal completion. */
  void finished(AsyncCall call) {
    finished(runningAsyncCalls, call, true);
  }

  /** Used by {@code Call#execute} to signal completion. */
  void finished(RealCall call) {
    finished(runningSyncCalls, call, false);
  }
  private <T> void finished(Deque<T> calls, T call, boolean promoteCalls) {
    int runningCallsCount;
    Runnable idleCallback;
    synchronized (this) {
      if (!calls.remove(call)) throw new AssertionError("Call wasn't in-flight!");
      // 异步请求会进入
      if (promoteCalls) promoteCalls();
      runningCallsCount = runningCallsCount();
      idleCallback = this.idleCallback;
    }

    if (runningCallsCount == 0 && idleCallback != null) {
      idleCallback.run();
    }
  }
    private void promoteCalls() {
    if (runningAsyncCalls.size() >= maxRequests) return; // Already running max capacity.
    if (readyAsyncCalls.isEmpty()) return; // No ready calls to promote.

    for (Iterator<AsyncCall> i = readyAsyncCalls.iterator(); i.hasNext(); ) {
      AsyncCall call = i.next();
      // 找到一个等待队列中的 Call，符合连接数要求时加入 runningAsyncCalls 并提交给线程池执行。
      if (runningCallsForHost(call) < maxRequestsPerHost) {
        i.remove();
        runningAsyncCalls.add(call);
        executorService().execute(call);
      }

      if (runningAsyncCalls.size() >= maxRequests) return; // Reached max capacity.
    }
  }
```

有两个重载的 `finished` 方法均调用了另一个 pirvate 的 `finished`，区别在于这个 `finished` 的最后一个参数 `promoteCalls`。对于同步请求(参数为 `RealCall`) `promoteCalls` 为 `false`，而异步请求(参数为 `AsyncCall`) `promoteCalls` 为 `true`。 pirvate 的 `finished` 主要是从队列中移除 Call，异步请求会执行 `promoteCalls`。`promoteCalls` 里面主要是从 `readyAsyncCalls` 取出一个 Call，如果满足最大连接数的要求，则把这个 Call 加入 `runningAsyncCalls` 并提交给线程池执行。

通过 `runningAsyncCalls` 和 `readyAsyncCalls`，Dispatcher 实现了异步请求的调度执行。这里比较巧妙的方式是在 finally 中去执行 `readyAsyncCalls` 中的请求，避免了 wait/notity 的方式，避免了代码的复杂性。



**总结：**

OkHttp 的基本执行流程如下图所示：

![图片描述](D:\Typora\img\1460000012656610)

主要是以下步骤：

1. `OkHttpClient` 调用 `newCall` 创建 `RealCall` 对象，`Call` 封装了 `Request`，代表一条即将执行的请求。
2. 根据同步还是异步请求分别调用 `RealCall` 的 `execute` 或 `enqueue` 方法，将`Call` 加入 `Dispatcher` 的相应队列中。最终，同步或异步请求都会调用 `getResponseWithInterceptorChain`。
3. 在 `getResponseWithInterceptorChain` 中，OkHttp 添加用户自定义以及默认的 inceptors，并用一个 `Chain` 管理并依次执行每个 Interceptor。
4. 每个 Interceptor 调用 `Chain#proceed()` 将请求发送给下一级的 Inceptor，并能通过这个方法获得下一级 Interceptor 的 Response。所以上图所示，Request 一级级地往下传递，而获取了网络的 Response 之后一级级地往上传递。





##### 建立连接

OkHttp 中默认会添加 RetryAndFollowUpInterceptor、BridgeInterceptor、CacheInterceptor、ConnectInterceptor 以及 CallServerInterceptor 这几个拦截器。本文主要看一下 RetryAndFollupInterceptor 并引出建立连接相关的分析。

**RetryAndFollowUpInterceptor：**

Interceptor 最主要的代码都在 `intercept` 中，下面是 `RetryAndFollowUpInterceptor#intercept` 中的部分代码：

```java
@Override public Response intercept(Chain chain) throws IOException {
    Request request = chain.request();

    streamAllocation = new StreamAllocation(
        client.connectionPool(), createAddress(request.url()), callStackTrace);//创建一个StreamAllocation对象

    int followUpCount = 0;
    Response priorResponse = null;
    while (true) {
      if (canceled) {
        streamAllocation.release();  //调用release方法
        throw new IOException("Canceled");
      }
    ...
    //把StreamAllocation对象传给下一个 Interceptor
    response = ((RealInterceptorChain) chain).proceed(request, streamAllocation, null, null); 
    ...
}
```

**StreamAlloction：**

`StreamAllocation` 从名字上看是**流分配器**，其实它是统筹管理了几样东西。

简单来说， `StreamAllocation` 协调了 3 样东西：

- `Connections` ： 物理的 socket 连接
- `Streams`：逻辑上的 HTTP request/response 对。每个 `Connection` 有个变量 `allocationLimit` ，用于定义可以承载的并发的 `streams` 的数量。HTTP/1.x 的 `Connection` 一次只能有一个 `stream`， HTTP/2 一般可以有多个。
- `Calls` ： `Streams` 的序列。一个初始的 `request` 可能还会有后续的 `request`（如重定向）。OkHttp 倾向于让一个 `call` 所有的 `streams` 运行在同一个 `connection` 上。

`StreamAllocation` 提供了一些 API 来释放以上的资源对象。 在 `RetryAndFollowUpInterceptor` 中创建的 `StreamAllocation` 对象下一个用到的地方是 ConnectInterceptor，其 `intercept` 代码如下：

```java
@Override public Response intercept(Chain chain) throws IOException {
    RealInterceptorChain realChain = (RealInterceptorChain) chain;
    Request request = realChain.request();
    StreamAllocation streamAllocation = realChain.streamAllocation();

    // We need the network to satisfy this request. Possibly for validating a conditional GET.
    boolean doExtensiveHealthChecks = !request.method().equals("GET");
    //Streams,逻辑上的 HTTP request/response 对。
    HttpCodec httpCodec = streamAllocation.newStream(client, doExtensiveHealthChecks);
    RealConnection connection = streamAllocation.connection();	//物理的 socket 连接

    return realChain.proceed(request, streamAllocation, httpCodec, connection);
}
```



**newStream：**

`StreamAllocation` 中的 `newStream` 方法用于寻找新的 `RealConnection` 以及 `HttpCodec`，代码如下：

```java
public HttpCodec newStream(OkHttpClient client, boolean doExtensiveHealthChecks) {
    int connectTimeout = client.connectTimeoutMillis();
    int readTimeout = client.readTimeoutMillis();
    int writeTimeout = client.writeTimeoutMillis();
    boolean connectionRetryEnabled = client.retryOnConnectionFailure();

    try {
      //通过 findHealthyConnection 找到可用的 Connection ，并用这个 Connection 生成一个 HttpCodec 对象
      RealConnection resultConnection = findHealthyConnection(connectTimeout, readTimeout,
          writeTimeout, connectionRetryEnabled, doExtensiveHealthChecks);
      HttpCodec resultCodec = resultConnection.newCodec(client, this);

      synchronized (connectionPool) {
        codec = resultCodec;
        return resultCodec;
      }
    } catch (IOException e) {
      throw new RouteException(e);
    }
  }
```

 `findHealthyConnection` 是找到一个健康的连接，代码如下：

```java
private RealConnection findHealthyConnection(int connectTimeout, int readTimeout,
      int writeTimeout, boolean connectionRetryEnabled, boolean doExtensiveHealthChecks)
      throws IOException {
    while (true) {
      RealConnection candidate = findConnection(connectTimeout, readTimeout, writeTimeout,
          connectionRetryEnabled);

      // If this is a brand new connection, we can skip the extensive health checks.
      synchronized (connectionPool) {
      // successCount == 0 表示还未使用过，则可以使用
        if (candidate.successCount == 0) {
          return candidate;
        }
      }

      // Do a (potentially slow) check to confirm that the pooled connection is still good. If it
      // isn't, take it out of the pool and start again.
      if (!candidate.isHealthy(doExtensiveHealthChecks)) {
        noNewStreams();
        continue;
      }

      return candidate;
    }
 }

public boolean isHealthy(boolean doExtensiveChecks) {
    if (socket.isClosed() || socket.isInputShutdown() || socket.isOutputShutdown()) {
      return false;
    }
    ... // 省略 Http2 代码
    return true;
  }

```

在一个无限循环中，通过 `findConnection` 寻找一个 `connection`，并判断是否可用，首先如果没有使用过的肯定是健康的可直接返回，否则调用 `isHealthy`，主要就是判断 `socket` 是否关闭。这里的 `socket` 是在 `findConnection` 中赋值的，再看看 `findConnection` 的代码：

```java
private RealConnection findConnection(int connectTimeout, int readTimeout, int writeTimeout,
      boolean connectionRetryEnabled) throws IOException {
    Route selectedRoute;
    synchronized (connectionPool) {
      if (released) throw new IllegalStateException("released");
      if (codec != null) throw new IllegalStateException("codec != null");
      if (canceled) throw new IOException("Canceled");

      // Attempt to use an already-allocated connection.
      RealConnection allocatedConnection = this.connection;
      if (allocatedConnection != null && !allocatedConnection.noNewStreams) {
        return allocatedConnection;
      }

      // Attempt to get a connection from the pool.
      // 1. 从 ConnectionPool 取得 connection
      Internal.instance.get(connectionPool, address, this, null);
      if (connection != null) {
        return connection;
      }

      selectedRoute = route;
    }

    // If we need a route, make one. This is a blocking operation.
    if (selectedRoute == null) {
      selectedRoute = routeSelector.next();
    }

    RealConnection result;
    synchronized (connectionPool) {
      if (canceled) throw new IOException("Canceled");

      // Now that we have an IP address, make another attempt at getting a connection from the pool.
      // 2. 有了 ip 地址后再从 connectionpool中取一次
      // This could match due to connection coalescing.
      Internal.instance.get(connectionPool, address, this, selectedRoute);
      if (connection != null) return connection;

      // Create a connection and assign it to this allocation immediately. This makes it possible
      // for an asynchronous cancel() to interrupt the handshake we're about to do.
      route = selectedRoute;
      refusedStreamCount = 0;
      // 3. ConnectionPool 中没有，新创建一个
      result = new RealConnection(connectionPool, selectedRoute);
      // 3. 将 StreamAllocation 加入到 `RealConnection` 中的一个队列中
      acquire(result);
    }

    // Do TCP + TLS handshakes. This is a blocking operation.
    // 4. 建立连接，在其中创建 socket
    result.connect(connectTimeout, readTimeout, writeTimeout, connectionRetryEnabled);
    routeDatabase().connected(result.route());

    Socket socket = null;
    synchronized (connectionPool) {
      // Pool the connection.
      // 5. 将新创建的 connection 放到 ConnectionPool 中 
      Internal.instance.put(connectionPool, result);

      // If another multiplexed connection to the same address was created concurrently, then
      // release this connection and acquire that one.
      if (result.isMultiplexed()) {
        socket = Internal.instance.deduplicate(connectionPool, address, this);
        result = connection;
      }
    }
    closeQuietly(socket);

    return result;
  }
```

上面 `Connection` 的创建大体是以下几个步骤：

1. 调用 `Intenal.get` 方法从 `ConnectionPool` 中获取一个 `Connection`，主要根据 url 的 host 判断，相关代码在 `ConnectionPool` 中。
2. 如果没有并且又获取了 IP 地址，则再获取一次。
3. 如果 `ConnectionPool` 中没有， 则新创建一个 `RealConnection`，并调用 `acquire` 将 `StreamAllocation` 中加入 `RealConnection` 中的一个队列中。
4. 调用 `RealConnection#connect` 方法建立连接，在内部会创建 `Socket`。
5. 将新创建的 `Connection` 加入到 `ConnectionPool` 中。

获取到了 `Connection` 之后，再创建一个 `HttpCodec` 对象。

```java
public HttpCodec newCodec(
      OkHttpClient client, StreamAllocation streamAllocation) throws SocketException {
    if (http2Connection != null) {
      return new Http2Codec(client, streamAllocation, http2Connection);
    } else {
      socket.setSoTimeout(client.readTimeoutMillis());
      source.timeout().timeout(client.readTimeoutMillis(), MILLISECONDS);
      sink.timeout().timeout(client.writeTimeoutMillis(), MILLISECONDS);
      return new Http1Codec(client, streamAllocation, source, sink);
    }
}
```

根据是 Http1 还是 Http2 创建对应的 `HttpCodec`， 其中的 `socket` 是在 `RealConnection` 中的 `connect` 方法创建的。

**RealConnection：**

`RealConnection` 封装的是底层的 `Socket` 连接，内部必然有一个 `Socket` 对象，下面是 `RealConnection` 内部的变量：

```java
public final class RealConnection extends Http2Connection.Listener implements Connection {
  private static final String NPE_THROW_WITH_NULL = "throw with null exception";
  private final ConnectionPool connectionPool;
  //Route 表示的是与服务端建立的路径，其实内部封装了 Address，Address 则是封装了请求的 URL。
  private final Route route;

  //rawSocket 对象代表底层的连接，还有一个 socket 是用于 Https， 对于普通的 Http 请求来说，这两个对象是一样的。 
  private Socket rawSocket;

  private Socket socket;
  private Handshake handshake;
  private Protocol protocol;
  private Http2Connection http2Connection;
  //source 和 sink 则是利用 Okio 封装 socket 得到的输入输出流。
  private BufferedSource source;
  private BufferedSink sink;

  //noNewStream 对象用于标识这个 Connection 不能再用于 Http 请求了，一旦设置为 true, 则不会再变。
  public boolean noNewStreams;

  public int successCount;

  //allocationLimit 指的是这个 Connection 最多能同时承载几个 Http 流，对于 Http/1 来说只能是一个。
  public int allocationLimit = 1;

  //allocations 是一个 List 对象，里面保存着正在使用这个 Connection 的 StreamAllocation 的弱引用，当StreamAllocation 调用 acquire 时，便会将其弱引用加入这个 List，调用 release 则是移除引用。allocations 为空说明此 Connection 为闲置， ConnectionPool 利用这些信息来决定是否关闭这个连接。
  public final List<Reference<StreamAllocation>> allocations = new ArrayList<>();

  /** Nanotime timestamp when {@code allocations.size()} reached zero. */
  public long idleAtNanos = Long.MAX_VALUE;
  ...
}
```

**connect**

`RealConnection` 用于建立连接，里面有相应的 `connect` 方法：

```java
public void connect(
      int connectTimeout, int readTimeout, int writeTimeout, boolean connectionRetryEnabled) {
    ...
    while (true) {
      try {
        if (route.requiresTunnel()) {
          connectTunnel(connectTimeout, readTimeout, writeTimeout);
        } else {
          // 创建socket，建立连接
          connectSocket(connectTimeout, readTimeout);
        }
        // 建立
        establishProtocol(connectionSpecSelector);
        break;
      }
    ...
}

private void connectSocket(int connectTimeout, int readTimeout) throws IOException {
    Proxy proxy = route.proxy();
    Address address = route.address();
    // 创建 socket
    rawSocket = proxy.type() == Proxy.Type.DIRECT || proxy.type() == Proxy.Type.HTTP
        ? address.socketFactory().createSocket()
        : new Socket(proxy);

    rawSocket.setSoTimeout(readTimeout);
    try {
      // 建立连接，相当于调用 socket 的 connect 方法
      Platform.get().connectSocket(rawSocket, route.socketAddress(), connectTimeout);
    } catch (ConnectException e) {
      ConnectException ce = new ConnectException("Failed to connect to " + route.socketAddress());
      ce.initCause(e);
      throw ce;
    }
    
    try {
      // 获取输入输出流
      source = Okio.buffer(Okio.source(rawSocket));
      sink = Okio.buffer(Okio.sink(rawSocket));
    } catch (NullPointerException npe) {
      if (NPE_THROW_WITH_NULL.equals(npe.getMessage())) {
        throw new IOException(npe);
      }
    }
}
```

如果不是 Https, 则调用 `connectSocket`，在内部创建 `rawSocket` 对象，设置超时时间。紧接着 `Platform.get().connectSocket` 根据不同的平台调用相应的 `connect` 方法，这样 `rawSocket` 就连接到服务端了。然后是用 `Okio` 封装 `rawSocket` 的输入输出流，这里的输入输出流最终是交给 `HttpCodec` 进行 Http 报文的写入都读取。通过以上步骤，就实现了 Http 请求的连接。



**总结：**

本文从 `RetryAndFollowupIntercept` 中创建 `StreamAllocation` 对象，到 `Connection` 中创建 `RealConnection` 和 `HttpCodec`，分析了 OkHttp 建立连接的基本过程。可以看出， OkHttp 中的连接由`RealConnection` 封装，Http 流的输入输出由 `HttpCodec` 操作，而 `StreamAllocation` 则统筹管理这些资源。在连接的寻找与创建过程，有个关键的东西是 `ConnectionPool`， 即连接池。它负责管理所有的 `Connection`，OkHttp 利用这个连接池进行 `Connection` 的重用以提高网络请求的效率



##### 连接池

前面分析了 OkHttp 建立连接的过程，主要涉及到的几个类包括 `StreamAllocation`、`RealConnection` 以及 `HttpCodec`，其中 `RealConnection` 封装了底层的 Socket。Socket 建立了 TCP 连接，这是需要消耗时间和资源的，而 OkHttp 则使用连接池来管理这里连接，进行连接的重用，提高请求的效率。OkHttp 中的连接池由 `ConnectionPool` 实现，本文主要是对这个类进行分析。

在 `StreamAllocation` 的 `findConnection` 方法中，有这样一段代码：

```java
// Attempt to get a connection from the pool.
Internal.instance.get(connectionPool, address, this, null);
    if (connection != null) {
        return connection;
}
```

`Internal.instance.get` 最终是从 `ConnectionPool` 取得一个`RealConnection`， 如果有了则直接返回。下面是 `ConnectionPool` 中的代码：

```java
@Nullable 
RealConnection get(Address address, StreamAllocation streamAllocation, Route route) {
    assert (Thread.holdsLock(this));
    //connections 是 ConnectionPool 中的一个双端队列（ArrayDeque）
    for (RealConnection connection : connections) {
      if (connection.isEligible(address, route)) {
        streamAllocation.acquire(connection);
        return connection;
      }
    }
    return null;
}
```

从队列中取出一个 `Connection` 之后，判断其是否能满足重用的要求：

```java
public boolean isEligible(Address address, @Nullable Route route) {
    // 如果这个 Connection 已经分配的数量超过了分配限制或者被标记为不能再分配，则直接返回 false
    if (allocations.size() >= allocationLimit || noNewStreams) return false;

    // 判断 Address 中除了 host 以外的变量是否相同，如果有不同的，那么这个连接也不能重用
    if (!Internal.instance.equalsNonHost(this.route.address(), address)) return false;

    // 判断 host 是否相同，如果相同那么对于当前的 Address 来说， 这个 Connection 便是可重用的
    if (address.url().host().equals(this.route().address().url().host())) {
      return true; // This connection is a perfect match.
    }
   // 省略 http2 相关代码
   ...
}

boolean equalsNonHost(Address that) {
    return this.dns.equals(that.dns)
        && this.proxyAuthenticator.equals(that.proxyAuthenticator)
        && this.protocols.equals(that.protocols)
        && this.connectionSpecs.equals(that.connectionSpecs)
        && this.proxySelector.equals(that.proxySelector)
        && equal(this.proxy, that.proxy)
        && equal(this.sslSocketFactory, that.sslSocketFactory)
        && equal(this.hostnameVerifier, that.hostnameVerifier)
        && equal(this.certificatePinner, that.certificatePinner)
        && this.url().port() == that.url().port();
}
```

从上面的代码看来，`get` 逻辑还是比较简单明了的。

接下来看一下 `put`，在 `StreamAllocation` 的 `findConnection` 方法中，如果新创建了 `Connection`，则将其放到连接池中。

```java
Internal.instance.put(connectionPool, result);
```

最终调用的是 `ConnectionPool#put`：

```java
void put(RealConnection connection) {
    assert (Thread.holdsLock(this));
    if (!cleanupRunning) {
      cleanupRunning = true;
      executor.execute(cleanupRunnable);
    }
    connections.add(connection);
}
```

首先判断其否启动了清理线程，如果没有则将 `cleanupRunnable` 放到线程池中。最后是将 `RealConnection` 放到队列中。

**cleanup：**

线程池需要对闲置的或者超时的连接进行清理，`CleanupRunnable` 就是做这件事的：

```java
private final Runnable cleanupRunnable = new Runnable() {
    @Override public void run() {
      while (true) {
        long waitNanos = cleanup(System.nanoTime());
        if (waitNanos == -1) return;
        if (waitNanos > 0) {
          long waitMillis = waitNanos / 1000000L;
          waitNanos -= (waitMillis * 1000000L);
          synchronized (ConnectionPool.this) {
            try {
              ConnectionPool.this.wait(waitMillis, (int) waitNanos);
            } catch (InterruptedException ignored) {
            }
          }
        }
      }
    }
};
```

`run` 里面有个无限循环，调用 `cleanup` 之后，得到一个时间 `waitNano`，如果不为 -1 则表示线程的睡眠时间，接下来调用 `wait` 进入睡眠。如果是 -1，则表示当前没有需要清理的连接，直接返回即可。

清理的主要实现在 `cleanup` 方法中，下面是其代码：

```java
long cleanup(long now) {
    int inUseConnectionCount = 0;
    int idleConnectionCount = 0;
    RealConnection longestIdleConnection = null;
    long longestIdleDurationNs = Long.MIN_VALUE;

    // Find either a connection to evict, or the time that the next eviction is due.
    synchronized (this) {
      for (Iterator<RealConnection> i = connections.iterator(); i.hasNext(); ) {
        RealConnection connection = i.next();

        // 1. 判断是否是空闲连接
        if (pruneAndGetAllocationCount(connection, now) > 0) {
          inUseConnectionCount++;
          continue;
        }

        idleConnectionCount++;

        // 2. 判断是否是最长空闲时间的连接
        long idleDurationNs = now - connection.idleAtNanos;
        if (idleDurationNs > longestIdleDurationNs) {
          longestIdleDurationNs = idleDurationNs;
          longestIdleConnection = connection;
        }
      }
      //  3. 如果最长空闲的时间超过了设定的最大值，或者空闲链接数量超过了最大数量，则进行清理，否则计算下一次需要清理的等待时间
      if (longestIdleDurationNs >= this.keepAliveDurationNs
          || idleConnectionCount > this.maxIdleConnections) {
        // We've found a connection to evict. Remove it from the list, then close it below (outside
        // of the synchronized block).
        connections.remove(longestIdleConnection);
      } else if (idleConnectionCount > 0) {
        // A connection will be ready to evict soon.
        return keepAliveDurationNs - longestIdleDurationNs;
      } else if (inUseConnectionCount > 0) {
        // All connections are in use. It'll be at least the keep alive duration 'til we run again.
        return keepAliveDurationNs;
      } else {
        // No connections, idle or in use.
        cleanupRunning = false;
        return -1;
      }
    }
     // 3. 关闭连接的socket
    closeQuietly(longestIdleConnection.socket());

    // Cleanup again immediately.
    return 0;
}
```

清理的逻辑大致是以下几步：

1. 遍历所有的连接，对每个连接调用 `pruneAndGetAllocationCount` 判断其是否闲置的连接。如果是正在使用中，则直接遍历一下个。
2. 对于闲置的连接，判断是否是当前空闲时间最长的。
3. 对于当前空闲时间最长的连接，如果其超过了设定的最长空闲时间（5分钟）或者是最大的空闲连接的数量（5个），则清理此连接。否则计算下次需要清理的时间，这样 `cleanupRunnable` 中的循环变会睡眠相应的时间，醒来后继续清理。

`pruneAndGetAllocationCount` 用于清理可能泄露的 `StreamAllocation` 并返回正在使用此连接的 `StreamAllocation` 的数量，代码如下：

```java
private int pruneAndGetAllocationCount(RealConnection connection, long now) {
    List<Reference<StreamAllocation>> references = connection.allocations;
    for (int i = 0; i < references.size(); ) {
      Reference<StreamAllocation> reference = references.get(i);

      if (reference.get() != null) {
        i++;
        continue;
      }

      // We've discovered a leaked allocation. This is an application bug.
      // 如果 StreamAlloction 引用被回收，但是 connection 的引用列表中扔持有，那么可能发生了内存泄露
      StreamAllocation.StreamAllocationReference streamAllocRef =
          (StreamAllocation.StreamAllocationReference) reference;
      String message = "A connection to " + connection.route().address().url()
          + " was leaked. Did you forget to close a response body?";
      Platform.get().logCloseableLeak(message, streamAllocRef.callStackTrace);

      references.remove(i);
      connection.noNewStreams = true;

      // If this was the last allocation, the connection is eligible for immediate eviction.
      if (references.isEmpty()) {
        connection.idleAtNanos = now - keepAliveDurationNs;
        return 0;
      }
    }

    return references.size();
}
```

如果 `StreamAllocation` 已经被回收，说明应用层的代码已经不需要这个连接，但是 `Connection` 仍持有 `StreamAllocation` 的引用，则表示`StreamAllocation` 中 `release(RealConnection connection)` 方法未被调用，可能是读取 `ResponseBody` 没有关闭 I/O 导致的。



**总结：**

OkHttp 中的连接池主要就是保存一个正在使用的连接的队列，对于满足条件的同一个 host 的多个连接复用同一个 `RealConnection`，提高请求效率。此外，还会启动线程对闲置超时或者超出闲置数量的 `RealConnection` 进行清理。

@[**然则**](https://segmentfault.com/u/ming_55e57cb682df4)

博文地址：https://segmentfault.com/a/1190000012656606



#### Retrofit解析

Retrofit 是 Square 推出的 HTTP 框架，主要用于 Android 和 Java。Retrofit 将网络请求变成方法的调用，使用起来非常简洁方便。

- App应用程序通过Retrofit请求网络，实际上是使用Retrofit接口层封装请求参数，之后由OkHttp完成后续的请求操作。
- 在服务端返回数据之后，OkHttp将原始的结果交给Retrofit，Retrofit根据用户的需求对结果进行解析。
- 完成数据的转化(converterFactory)，适配(callAdapterFactory)，通过设计模式进行各种扩展。

**基本用法：**

```java
public interface GitHubService {
  @GET("users/{user}/repos")
  Call<List<Repo>> listRepos(@Path("user") String user);
}
```

在 `GithubService` 接口中有一个方法 `listRepos`，这个方法用了 `@GET` 的方式注解，这表明这是一个 `GET` 请求。在后面的括号中的 `users/{user}/repos` 是请求的路径，其中的 `{user}` 表示的是这一部分是动态变化的，它的值由方法的参数传递过来，而这个方法的参数`@Path("user") String user` 即是用于替换 `{user}` 。另外注意这个方法的返回值是 `Call<List<Repo>>`。可以看出 Retrofit 用注解的方式来描述一个网络请求相关的参数。

发出这个网络请求：

```java
Retrofit retrofit = new Retrofit.Builder()
.baseUrl("https://api.github.com/")
.addConverterFactory(GsonConverterFactory.create())
.build();

GitHubService service = retrofit.create(GitHubService.class);
Call<List<Repo>> repos = service.listRepos("octocat");
repos.enqueue(new Callback<List<Repo>>() {
    @Override
    public void onResponse(Call<List<Repo>> call, Response<List<Repo>> response) {
            
    }
    @Override
    public void onFailure(Call<List<Repo>> call, Throwable t) {

    }
 });
```

先是构建了一个 Retrofit 对象，其中传入了 `baseUrl` 参数，`baseUrl` 和上面的 `GET` 方法后面的路径组合起来才是一个完整的 url。除了 `baseUrl`，还有一个 `converterFactory`，它是用于把返回的 http response 转换成 Java 对象，对应方法的返回值 `Call<List<Repo>>` 中的 `List<Repo>>`，其中 `Repo` 是自定义的类。有了Retrofit 对象，接着调用它的 `create` 方法创建了 `GitHubService` 的实例，然后就可以调用这个实例的方法来请求网络了。调用 `listRepo` 方法得到一个 `Call`对象，然后可以使用 `enqueue` 或者 `execute` 来执行发起请求，`enqueue` 是异步执行，而 `execute` 是同步执行。



**源码分析：**

**Retrofit 的创建**

从 Retrofit 的创建方法可以看出，使用的是 Builder 模式。Retrofit 中有如下的几个关键变量：

```java
  //用于缓存解析出来的方法
  private final Map<Method, ServiceMethod> serviceMethodCache = new LinkedHashMap<>();
  
  //请求网络的OKHttp的工厂，默认是 OkHttpClient
  private final okhttp3.Call.Factory callFactory;
  
  //baseurl
  private final HttpUrl baseUrl;
  
  //请求网络得到的response的转换器的集合 默认会加入 BuiltInConverters     
  private final List<Converter.Factory> converterFactories;
  
  //把Call对象转换成其它类型
  private final List<CallAdapter.Factory> adapterFactories;
  
  //用于执行回调 Android中默认是 MainThreadExecutor
  private final Executor callbackExecutor;
  
  //是否需要立即解析接口中的方法
  private final boolean validateEagerly;
  
```

再看一下Retrofit 中的内部类 Builder 的 `build` 方法：

```java
public Retrofit build() {
  if (baseUrl == null) {
    throw new IllegalStateException("Base URL required.");
  }

  okhttp3.Call.Factory callFactory = this.callFactory;
  if (callFactory == null) {
    //默认创建一个 OkHttpClient
    callFactory = new OkHttpClient();
  }

  Executor callbackExecutor = this.callbackExecutor;
  if (callbackExecutor == null) {
     //Android 中返回的是 MainThreadExecutor
    callbackExecutor = platform.defaultCallbackExecutor();
  }

  // Make a defensive copy of the adapters and add the default Call adapter.
  List<CallAdapter.Factory> adapterFactories = new ArrayList<>(this.adapterFactories);
  adapterFactories.add(platform.defaultCallAdapterFactory(callbackExecutor));

  // Make a defensive copy of the converters.
  List<Converter.Factory> converterFactories = new ArrayList<>(this.converterFactories);

  return new Retrofit(callFactory, baseUrl, converterFactories, adapterFactories,
      callbackExecutor, validateEagerly);
}
```

在创建 Retrofit 的时候，如果没有指定 OkHttpClient，会创建一个默认的。如果没有指定 `callbackExecutor`，会返回平台默认的，在 Android 中是 `MainThreadExecutor`，并利用这个构建一个 `CallAdapter`加入 `adapterFactories`。



**create 方法**

有了 Retrofit 对象后，便可以通过 `create` 方法创建网络请求接口类的实例，代码如下：

```java
public <T> T create(final Class<T> service) {
Utils.validateServiceInterface(service);
if (validateEagerly) {
  //提前解析方法
  eagerlyValidateMethods(service);
}
return (T) Proxy.newProxyInstance(service.getClassLoader(), new Class<?>[] { service },
    new InvocationHandler() {
      private final Platform platform = Platform.get();

      @Override public Object invoke(Object proxy, Method method, Object... args)
          throws Throwable {
        // If the method is a method from Object then defer to normal invocation.如果是Object中的方法，直接调用
        if (method.getDeclaringClass() == Object.class) {
          return method.invoke(this, args);
        }
        //为了兼容 Java8 平台，Android 中不会执行
        if (platform.isDefaultMethod(method)) {
          return platform.invokeDefaultMethod(method, service, proxy, args);
        }
        //下面是重点，解析方法
        ServiceMethod serviceMethod = loadServiceMethod(method);
        OkHttpCall okHttpCall = new OkHttpCall<>(serviceMethod, args);
        return serviceMethod.callAdapter.adapt(okHttpCall);
      }
});
```

`reate` 方法接受一个 Class 对象，也就是我们编写的接口，里面含有通过注解标识的请求网络的方法。注意 `return` 语句部分，这里调用了 `Proxy.newProxyInstance` 方法，这个很重要，因为用了**动态代理模式**。

简单的描述就是，`Proxy.newProxyInstance` 根据传进来的 Class 对象生成了一个实例 A，也就是代理类。每当这个代理类 A 执行某个方法时，总是会调用 `InvocationHandler`(`Proxy.newProxyInstance` 中的第三个参数) 的 `invoke` 方法，在这个方法中可以执行一些操作(这里是解析方法的注解参数等)，通过这个方法真正的执行我们编写的接口中的网络请求。

**方法解析和类型转换**

下面具体看一下在 `invoke` 中解析网络请求方法的几行。首先是 `ServiceMethod serviceMethod = loadServiceMethod(method);`，其中 `loadServiceMethod` 代码如下：

```java
ServiceMethod loadServiceMethod(Method method) {
    ServiceMethod result;
    synchronized (serviceMethodCache) {
      result = serviceMethodCache.get(method);
      if (result == null) {
        result = new ServiceMethod.Builder(this, method).build();
        serviceMethodCache.put(method, result);
      }
    }
    return result;
}
```

这里是先到缓存中找，缓存中没有再去创建。这里创建了 `ServiceMethod` 对象。`ServiceMethod` 用于把接口方法的调用转换成一个 HTTP 请求。其实，在 `ServiceMethod` 中会解析接口中方法的注解、参数等，它还有个 `toRequest` 方法，用于生成一个 `Request` 对象。这个 `Request` 对象就是 OkHttp 中的 `Request`，代表了一条网络请求(Retrofit 实际上把真正请求网络的操作交给了 OkHttp 执行)。下面是创建 `ServiceMethod` 的部分代码：

```java
public ServiceMethod build() {
  //获取 callAdapter
  callAdapter = createCallAdapter();
  responseType = callAdapter.responseType();
  if (responseType == Response.class || responseType == okhttp3.Response.class) {
    throw methodError("'"
        + Utils.getRawType(responseType).getName()
        + "' is not a valid response body type. Did you mean ResponseBody?");
  }
  //获取 responseConverter 
  responseConverter = createResponseConverter();

  for (Annotation annotation : methodAnnotations) {
    //解析注解
    parseMethodAnnotation(annotation);
    //省略了一些代码
    ...
  }
}
```

在得到 `ServiceMethod` 对象后，把它连同方法调用的相关参数传给了 `OkHttpCall` 对象，也就是这行代码： `OkHttpCall okHttpCall = new OkHttpCall<>(serviceMethod, args);`。 

下面介绍 `OkHttpCall`，`OkHttpCall`继承于 `Call` 接口。`Call` 是Retrofit 的基础接口，代表发送网络请求与响应调用，`OkHttpCall` 是 `Call` 的一个实现类，它里面封装了 `OkHttp` 中的原生 `Call`，在这个类里面实现了 `execute` 以及 `enqueue` 等方法，其实是调用了 OkHttp 中原生 `Call` 的对应方法。

接下来把 `OkHttpCall` 传给 `serviceMethod.callAdapter` 对象，这里的callAdapter又是什么？在上面创建 `ServiceMethod` 的代码中有一行代码： `callAdapter = createCallAdapter()`，这里创建了 `calladapter`，在这个代码内部是根据方法的返回类型以及注解去寻找对应的 `CallAdapter`，去哪里寻找？去 `Retrofit` 对象的 `adapterFactories` 集合中找。当我们创建 `Retrofit` 的时候，可以调用 `addCallAdapter` 向 `adapterFactories` 中添加 `CallAdapter`。在前面的基本用法里面，我们并没有添加任何 `CallAdapter`，但 `adapterFactories` 中默认会添加一个 `ExecutorCallAdapterFactory`，调用其 `get` 方法便可获得 `CallAdapter` 对象。

那么 `CallAdapter` 是干嘛的呢？上面调用了`adapt` 方法，它是为了把一个 `Call` 转换成另一种类型，比如当 Retrofit 和 RxJava 结合使用的时候，接口中方法可以返回 `Observable<T>`，这里相当于**适配器模式**。默认情况下得到的是一个 `Call` 对象，它是`ExecutorCallbackCall`，代码如下：

```java
public CallAdapter<Call<?>> get(Type returnType, Annotation[] annotations, Retrofit retrofit) {
if (getRawType(returnType) != Call.class) {
  return null;
}
final Type responseType = Utils.getCallResponseType(returnType);
return new CallAdapter<Call<?>>() {
  @Override public Type responseType() {
    return responseType;
  }

  @Override public <R> Call<R> adapt(Call<R> call) {
    return new ExecutorCallbackCall<>(callbackExecutor, call);
  }
};
```

个 `ExecutorCallbackCall` 接受一个 `callbackExecutor`(Android 中默认为 `MainThreadExecutor`，把返回的数据传回主线程) 和一个 `call`，也就是 `OkhttpCall`。看下 `ExecutorCallbackCall` 部分代码：

```java
static final class ExecutorCallbackCall<T> implements Call<T> {
final Executor callbackExecutor;
final Call<T> delegate;

ExecutorCallbackCall(Executor callbackExecutor, Call<T> delegate) {
  this.callbackExecutor = callbackExecutor;
  this.delegate = delegate;
}

@Override public void enqueue(final Callback<T> callback) {
  if (callback == null) throw new NullPointerException("callback == null");

  delegate.enqueue(new Callback<T>() {
    @Override public void onResponse(Call<T> call, final Response<T> response) {
      callbackExecutor.execute(new Runnable() {
        @Override public void run() {
          if (delegate.isCanceled()) {
            // Emulate OkHttp's behavior of throwing/delivering an IOException on cancellation.
            callback.onFailure(ExecutorCallbackCall.this, new IOException("Canceled"));
          } else {
            callback.onResponse(ExecutorCallbackCall.this, response);
          }
        }
      });
    }

    @Override public void onFailure(Call<T> call, final Throwable t) {
      callbackExecutor.execute(new Runnable() {
        @Override public void run() {
          callback.onFailure(ExecutorCallbackCall.this, t);
        }
      });
    }
  });
}
```

在 `enqueue` 方法中，调用了 `OkHttpCall` 的 `enqueue`，所以这里相当于**静态的代理模式**。`OkHttpCall` 中的 `enqueue` 其实又调用了原生的 `OkHttp` 中的 `enqueue`，这里才真正发出了网络请求，部分代码如下:

```java
@Override public void enqueue(final Callback<T> callback) {
    if (callback == null) throw new NullPointerException("callback == null");
    //真正请求网络的 call
    okhttp3.Call call;
    Throwable failure;

    synchronized (this) {
      if (executed) throw new IllegalStateException("Already executed.");
      executed = true;
      //省略了部分发代码
      ...
      call = rawCall;
      //enqueue 异步执行
    call.enqueue(new okhttp3.Callback() {
      @Override public void onResponse(okhttp3.Call call, okhttp3.Response rawResponse)
          throws IOException {
        Response<T> response;
        try {
        //解析数据 会用到 conveterFactory，把 response 转换为对应 Java 类型
          response = parseResponse(rawResponse);
        } catch (Throwable e) {
          callFailure(e);
          return;
        }
        callSuccess(response);
      }

      @Override public void onFailure(okhttp3.Call call, IOException e) {
        try {
          callback.onFailure(OkHttpCall.this, e);
        } catch (Throwable t) {
          t.printStackTrace();
        }
      }

  private void callFailure(Throwable e) {
    try {
      callback.onFailure(OkHttpCall.this, e);
    } catch (Throwable t) {
      t.printStackTrace();
    }
  }

  private void callSuccess(Response<T> response) {
    try {
      callback.onResponse(OkHttpCall.this, response);
    } catch (Throwable t) {
      t.printStackTrace();
    }
  }
 });
}
```

OkHttp 获取数据后，解析数据并回调 callback 响应的方法，一次网络请求便完成了。

**总结:**

Retrofit 整个框架的代码不算太多，还是比较易读的。主要就是通过动态代理的方式把 Java 接口中的解析为响应的网络请求，然后交给 OkHttp 去执行。并且可以适配不同的 `CallAdapter`，可以方便地与 RxJava 结合使用。



#### EventBus介绍

EventBus是一个Android端优化的publish/subscribe消息总线，简化了应用程序内 各组件间、组件与后台线程间的通信。

 EventBus可以代替Android传统的Intent,Handler,Broadcast或接口函数,在Fragment,Activity,Service线程之间传递数据，执行方法。

用于线程间的通讯代替handler或用于组件间的通讯代替Intent



#### RxJava介绍

RxJava本质上是一个异步操作库，是一个能让你用极其简洁的逻辑去处理繁琐复杂任务的异步事件库。

