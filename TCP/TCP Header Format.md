## TCP Header Format

![](https://github.com/steveLauwh/TCP-IP/raw/master/TCP/image/TCP-header.png)

* Source Port 和 Destination Port ：分别占用 16 位(2个字节)，表示源端口号和目的端口号，用来区别主机中的不同进程。
  而 IP 地址是用来区分不同的主机，源端口号和目的端口号配合上 IP 首部中的源 IP 地址和目的 IP 地址就能唯一的确定一个 TCP 连接。
  
* Sequence Number ：32位无符号数，包的序号，用来标识从 TCP 发端向 TCP 收端发送的数据字节流，它表示在这个报文段中的第一个数据字节。
  **主要用来解决网络包乱序(reordering)的问题**。

* Acknowledgment Number ：32位，确认序列号 包含发送确认的一端所期望收到的下一个序号；因此，确认序号应当是上次已成功收到数据字节序号加1。
  只有当标志位中的 ACK 标志为 1 时，该确认序列号的字段才有效。**主要用来解决不丢包的问题**。
  
* Offset ：4位(TCP头长度)，给出首部中 32位 字的数目，需要这个值是因为任选字段的长度是可变的。最多能表示 15 个 32位的字，即 4x15=60 个字节的首部长度；
  因此 TCP 最多有 60 个字节的首部。当没有任选字段，正常的长度是 20 个字节。
  
* TCP Flags ：包的类型，TCP 首部中有 6 个标志比特，它们中的多个可同时被设置为 1，**主要是用于操控 TCP 的状态机**，依次是 URG，ACK，PSH，RST，SYN，   FIN。
  
  + URG ：此标志表示 TCP 包的紧急指针域有效，用来保证 TCP 连接不被中断，并且督促中间层设备要尽快处理这些数据。
  + ACK ：此标志表示应答域有效，TCP 应答号将会包含在 TCP 数据包中。
  + PSH ：此标志表示 Push 操作，在数据包到达接收端以后，立即传送给应用程序，而不是在缓冲区中排队。
  + RST ：此标志表示连接复位请求，用来复位那些产生错误的连接，也被用来拒绝错误和非法的数据包。
  + SYN ：此标志表示同步序号，用来建立连接。SYN 标志位和 ACK 标志位搭配使用，当连接请求的时候，SYN=1，ACK=0；当连接被响应的时候，SYN=1，ACK=1。
  这个标志的数据包经常被用来进行端口扫描。
  + FIN ：此标志表示发送端已经达到数据末尾，也就是说双方的数据传送完成，没有数据可以传送了，发送 FIN 标志位的 TCP 数据包后，连接将被断开。

* Window ：16位，滑动窗口(Sliding Window)，**用来进行流量控制**。

* Checksum ：16位，校验和，对整个 TCP 报文段，即 TCP 头部和 TCP 数据进行校验和计算，并由目的端进行验证。

* Urgent Pointer ：16位，紧急数据偏移量。

* TCP Options ：可选项

