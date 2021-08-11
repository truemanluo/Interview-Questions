### TCP和UDP的区别

1. 连接
2. 服务对象：1对1和1对多
3. 可靠性
4. 拥塞控制、流量控制
5. 首部开销
   
   UDP固定8字节，TCP有20字节+可选项
6. 传输方式
7. 分片不同


### 三次握手

Client      Server

close       close

syn_sent    syn_rcvd

established established

### 一些术语

- MSS：Maximum Segment Size（TCP数据长度，以太网通常为1460字节）

- MTU：链路层最大传输单元，以太网为1500字节

- MSL：Maximum Segment Lifetime

- TTL：Time-to-live，IP包被路由器丢弃之前允许通过的最大网段数量（跳数）

- RTT：Round-Trip Time，往返时延

- RTO：Retransmission Timeout，超时重传时间


### TIME_WAIT状态

- 为什么是2MSL
  > 网络中可能存在来自发起方的数据段，当这些发起方的数据段被服务端处理后又会向客户端发送响应，所以一来一回需要等待 2 倍的时间

- 为什么要有TIME_WAIT状态
  - 防止旧连接的数据包
  - 保证被动关闭连接的一方能被正确关闭

    > 经过 2MSL 这个时间， ⾜以让两个⽅向上的数据包都被丢弃，使得原来连接的数据包在⽹络中都⾃然消失，再出现的数据包⼀定都是新建⽴连接所产⽣的。


### 分片

### TCP可靠传输如何实现

- 序列号
- 重传机制
  - 超时重传
  - 快速重传
  - SACK（选择性确认）
    > 图中连续三次传回相同的ack，称为duplicated ack
    ![](https://gblobscdn.gitbook.com/assets%2F-MRP-_mTHVixnnXPCTr3%2F-MfReE9dS5oW5D2Ax_mo%2F-MfReHmxHGrO-1FnIDLo%2Fimage.png?alt=media&token=c7f082d5-2082-46ee-9dda-c61b9af10b60)
    > 这种⽅式需要在 TCP 头部「选项」字段⾥加⼀个 SACK 的东⻄，它可以将缓存的地图发送给发送⽅，这样发送⽅就可以知道哪些数据收到了，哪些数据没收到，知道了这些信息，就可以只重传丢失的数据。
- 确认应答
- 拥塞控制
  - 慢启动
  - 拥塞避免
  - 快重传
- 流量控制



  