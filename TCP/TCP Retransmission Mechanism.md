# TCP 重传机制

## 为什么 TCP 要重传？

TCP 提供一种面向连接的、可靠的字节流服务。如何保证可靠，当 TCP 发出一个段后，它启动一个**定时器**，等待目的端确认收到这个报文段。如果不能及时收到一个确认，将重传这个报文段。

所以，TCP 要保证所有的数据包都能到达，就必须要有重传机制。

## 怎么去确定超时时间 Timeout？

* 当 Timeout 时间设置长些，就会导致重发就慢，效率差，性能差。
* 当 Timeout 时间设置短些，就会导致没有丢就重发，重发快，会增加网络拥塞，导致更多的超时，从而导致更多的重发。

由于路由器和网络流量均会变化，所以超时时间在不同的网络情况下，也不一样。从而，TCP 引入了一个给定连接的往返时间(RTT)的测量。`RTT(Round Trip Time)`表示一个数据包从发出去到回来的时间。这样发送端就可以知道需要多长时间，从而方便设置 Timeout(`RTO, Retransmission TimeOut`)，让重传机制更加高效。

> RTT 算法

* RFC973 经典算法

  公式：

  `SRTT = (α * SRTT) + ((1-α) * RTT)`  (加权移动平均)

  `RTO = min [ UBOUND,  max[LBOUND, (β * SRTT)] ]`
  
  注：RTT 表示采集 RTT 时间；SRTT 表示做平滑计算的 RTT；α 表示 一个推荐值为 0.9 的平滑因子；UBOUND 表示最大的 Timeout 时间，上限值；LBOUND 表示最小 的 Timeout 时间，下限值；β 表示一个推荐值为 2 的时延离散因子。

  **RFC973 经典算法的缺点：当 RTT 变化范围很大时，使用这个方法无法跟上这种变化，从而引起不必要的重传，增加网络的负载。**

* Karn/Partridge 算法

  对`RFC973 经典算法`选取 RTT 样本值进行改进，该算法的最大特点是**忽略重传，不把重传的 RTT 做采样，当只要一发生重传，就对现有的 RTO 值翻倍**，但是这种估计比较准确的 RTT 也不靠谱。

* Jacobson/Karels 算法

  前面两种算法用的都是“加权移动平均”，当 RTT 波动很大，会被平滑掉，不能被发现。

  公式：(这个算法引入了最新的 RTT 的采样和平滑过的 SRTT 的差距做因子来计算)

  `SRTT = SRTT + α * (RTT – SRTT)`   计算平滑RTT

  `DevRTT = (1-β) * DevRTT + β * (|RTT-SRTT|)`   计算平滑RTT和真实的差距(加权移动平均)

  `RTO = µ * SRTT + ∂ * DevRTT`  

  注：在Linux下，α = 0.125，β = 0.25， μ = 1，∂ = 4

**总结：TCP 超时与重传中最重要的部分就是对一个给定连接的往返时间 RTT 的测量。**

## TCP 重传的机制

* 超时重传
  
  + 仅重传 Timeout 的包，这种方式，会节省带宽，但是慢。
  + 重传 Timeout 后的所有数据，这种方式会浪费带宽。

**超时重传：归根结底还是确定 Timeout 时间。**

* 快速重传(Fast Retransmit)
  + 不以时间驱动，而以数据驱动重传，不用等 Timeout 再重传。
  + 当发送方连续收到 3 次相同的 ack，就重传。

**快速重传：解决了 Timeout，但是无法确认到底重传谁更好。**

* SACK

`Selective Acknowledgment (SACK)`：优化 Fast Retransmit 算法，在 TCP 头加一个 SACK选项，SACK 是汇报收到的数据。由接收端发出，发送端就可以根据回传的 SACK 知道哪些数据到了，哪些没有到。

**SACK：当一个攻击者给数据发送方发一堆 SACK 选项，这会导致发送方开始要重传甚至遍历已经发出的数据，会消耗发送端的资源。**

* Duplicate SACK

Duplicate SACK，又称 D-SACK，其主要使用 SACK 来告诉发送方有哪些数据被重复接收了。
