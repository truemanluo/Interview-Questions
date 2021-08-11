### 【2021.7.20】诺瓦科技笔试

1. Cookie是啥（Session和Cookie的区别：https://www.zhihu.com/question/19786827）
   
   - 核心是提供用户识别和**提供持久会话的**功能。

   - 需要注意的核心点：
     - Cookie是封装在Http头中被发送给服务器端的
     - Cookie可以笼统分为两类：
       - 会话Cookie：临时的，用户退出浏览器后就被删除
       - 持久性Cookie：存储在硬盘上，会设置过期时间（Expires和Max-Age参数）
   - Cookie是实现Session的一种方式

   - 工作流程：
   1. 客户端第一次访问服务器时，服务器响应返回`set-cookie`，将cookie”贴到“客户端
   2. 下一次客户端访问时通过在request header中提供Cookie字段（唯一标识码）。服务器可以用这个数字来查找服务器为其访问者积累的数据
库信息（购物历史、地址信息等）。

   - Cookie实现会话跟踪：
  
        在这些事务中 Web 服务器会通过一系列的重定向、URL 重写以及 cookie 设置来附加标识信息。

2. 解除死锁的方法
   
   产⽣死锁的四个必要条件是：互斥条件、持有并等待、不可剥夺、环路等待。因此解除死锁只需要破坏其中的条件。

   - 资源有序分配：破坏环路等待条件，保证线程获取资源的顺序一致
3. BFS和DFS的区别
4. CAS无锁队列
5. 数字签名
   ![](https://gblobscdn.gitbook.com/assets%2F-MRP-_mTHVixnnXPCTr3%2F-Mf6gTon76CdutXuS6wv%2F-Mf79JlAQvEhHK0b9njp%2Fimage.png?alt=media&token=1003b7c5-9ee2-4714-b18c-67444571c27e)
6. 全连接队列和已连接队列
7. 多态、重载、覆盖
8. 只出现一次的字符
9.  出现最多的字符
10.  实现一个线程安全的单例模式
11. 进程调度的算法
    
    > 调度算法影响的是等待时间（进程在就绪队列中等待调度的时间总和），⽽不能影响进程真在使⽤ CPU 的时间和 I/O 时间。

    - 先来先服务：**FCFS 对⻓作业有利，适⽤于 CPU 繁忙型作业的系统，⽽不适⽤于 I/O 繁忙型作业的系统。**
    - 最短作业优先：**对长作业不利**
    - 高响应比优先：

        优先权 = （要求服务的时间 + 等待时间）/ 要求服务的时间

    - 时间片轮转
    - 最⾼优先级调度算法
      - 优先级
        - 静态优先级：创建进程时确定
        - 动态优先级：运行过程中确定
      - 处理方式：
        - 抢占式：直接抢占当前正在执行的进程
        - 非抢占式：待当前进程执行完再调度

    - 多级反馈队列：时间片轮转+最高优先级
  
    ![](https://gblobscdn.gitbook.com/assets%2F-MRP-_mTHVixnnXPCTr3%2F-Mf6gTon76CdutXuS6wv%2F-Mf6gX0jx_75nuD3OvB9%2Fimage.png?alt=media&token=c6fa33ef-723a-4811-9bde-cbac54cbc2b2)
      - 优点：对长作业而言，执行时间虽然变长，但是响应时间变短了。对短作业而言，可以在前几级队列完成任务。
12. select和epoll的区别
    ![](https://gblobscdn.gitbook.com/assets%2F-MRP-_mTHVixnnXPCTr3%2F-Mf6gTon76CdutXuS6wv%2F-Mf6tKqUofQwz8oQQFWN%2Fimage.png?alt=media&token=8bb20608-ba3f-4515-8689-da9a5d9ebdec)
    - 从实现方式上看：
      - select采用的是将所有已连接的socket放入FD_SET（文件描述符集合中），然后`select`函数将该集合**拷贝**到内核中，内核利用O(n)时间来检测事件，然后标记事件可读或可写，再将FD_SET**拷贝**回用户空间，由用户自行检测事件，也是O(n)时间。
      - epoll采用红黑树实现
        - 第⼀点，epoll在内核⾥使⽤红⿊树来跟踪进程所有待检测的⽂件描述字，把需要监控的 socket 通过epoll_ctl() 函数加⼊内核中的红⿊树⾥，红⿊树是个⾼效的数据结构，增删查⼀般时间复杂度是O(logn) ，通过对这棵⿊红树进⾏操作，这样就不需要像 select/poll 每次操作时都传⼊整个 socket 集合，只需要传⼊⼀个待检测的 socket，减少了内核和⽤户空间⼤量的数据拷⻉和内存分配。
        - 第⼆点， epoll 使⽤事件驱动的机制，内核⾥维护了⼀个链表来记录就绪事件，当某个 socket 有事件发⽣时，通过**回调函数**内核会将其加⼊到这个就绪事件列表中，当⽤户调⽤ epoll_wait() 函数时，只会返回有事件发⽣的⽂件描述符的个数，不需要像 select/poll 那样轮询扫描整个 socket 集合，⼤⼤提⾼了检测的效率。

    - 从支持的最大连接数上看
      - select 使⽤固定⻓度的 BitsMap，表示⽂件描述符集合，⽽且所⽀持的⽂件描述符的个数是有限制的，在Linux 系统中，由内核中的FD_SETSIZE限制，默认最⼤值为1024，只能监听0~1023的⽂件描述符。
      - poll/epoll受到系统能打开的文件描述符数量的限制
13. 父类和子类之间的构造函数、析构函数的调用顺序
14. 程序从执行到结束发生了什么


