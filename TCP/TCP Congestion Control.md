## TCP 拥塞控制

### 为什么需要 TCP 拥塞控制机制？

我们知道 TCP 通过一个定时器（timer）采样了 RTT 并计算 RTO，但是，如果网络上的延时突然增加，
那么，TCP 对这个事做出的应对只有重传数据，然而重传会导致网络的负担更重，于是会导致更大的延迟以及更多的丢包，
这就导致了恶性循环，最终形成“网络风暴” —— TCP 的拥塞控制机制就是用于应对这种情况。

### TCP 拥塞控制机制

为了在发送端调节所要发送的数据量，定义了一个“拥塞窗口”（Congestion Window），
在发送数据时，将拥塞窗口的大小与接收端 ack 的窗口大小做比较，取较小者作为发送数据量的上限。

拥塞控制主要是四个算法：

* 慢启动：刚刚加入网络的连接，一点一点地提速，不要一上来就把路占满。
* 拥塞避免：当拥塞窗口 cwnd 达到一个阈值时，窗口大小不再呈指数上升，而是以线性上升，避免增长过快导致网络拥塞。
* 拥塞发生：当发生丢包进行数据包重传时，表示网络已经拥塞。
* 快速恢复：至少收到了 3 个 Duplicated Acks，说明网络也不那么糟糕，可以快速恢复。
