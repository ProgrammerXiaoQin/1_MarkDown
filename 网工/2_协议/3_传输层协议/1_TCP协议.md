#### 1. TCP 协议
TCP 协议给使用者提供了两种服务，分别是面向连接的服务和可靠的数据传输服务，我们简单介绍一下。简单来说面向连接的含义是客户机和服务器之间需要建立连接，在位于应用层的数据开始交互之前，TCP 协议首先要先让客户机和服务器进行一次握手。这么做相当于告知彼此接下来需要进行数据的交互，请双方做好准备，具体的实现方式为双方在传输层交互信息。握手阶段结束之后，就能够建立起 TCP 连接，双方应用程序的数据交互完毕之后，还需要切断这个连接。  
所谓 TCP 连接是一种逻辑上的连接，它可以提供全双工服务，即应用层数据可以在建立连接的进程之间相互传输。TCP 连接也是点对点的，因为这是针对单个发送方和单个接收方之间的连接。  
接下来再看看何谓可靠的数据传输服务，即双方的数据交互在 TCP 协议的保障下，彼此发出的数据能够无差别、按照正确顺序传递给对方。即 TCP 会将发送方通过套接字传出的字节流，在保证字节不发生丢失和出现多余的情况下，将字节流交付给接收方的套接字

#### 2. TCP报文结构
![[1774310-20200710173946013-1095280594.png]]
##### 字段解析
 - 源端口, 目的端口: 各占两字节, 端口是传输层与应用层的服务接口 
 - 序列号（Sequence Number）: 占4字节，标识数据发送方所发出数据的编号；
 - 确认号（Acknowledgment Number）: 4bits ，标识数据接收方期望下次收到的数据编号；
 - 报头长度（Header Length）: 4bits，标识TCP报头长度（以 4 字节为计算单位，也就是TCP报头最大发为60字节，最小为20字节）
 - 保留：占 6 位，保留为今后使用，但目前应置为 0
 - 窗口大小：16bits，用于流量控制，指示接收方愿意接收的字节数量，单位为字节 ， 代表了数据接收方当前的接收缓存池的大小
 - 检验和（Checksum）：占 2 字节。用于TCP头部+应用层数据的差错校验。
 - 紧急指针（Urgent Pointer）：16bits，标明紧急数据的字节数，与序列号搭配使用
 - **选项**：长度可变。TCP 最初只规定了最大报文段长度 MSS，表示缓存所能接收的报文段的数据字段的最大长度是 MSS 个字节
##### 标志字段
- **紧急 URG**：当 URG = 1 时，表明紧急指针字段有效。它告诉系统此报文段中有紧急数据，应尽快传送(相当于高优先级的数据)。  
- **确认 ACK**：只有当 ACK = 1   时确认号字段有效，当 ACK = 0 时确认号无效。  
- **推送 PSH(PuSH)**：接收 TCP 收到 PSH = 1 的报文段，就尽快地交付接收应用进程，不再等到整个缓存都填满了后再向上交付。  
- **复位 RST(ReSeT)**：当 RST = 1 时，表明 TCP 连接中出现严重差错（如由于主机崩溃或其他原因）必须释放连接，然后再重新建立运输连接。  
- **同步 SYN**：同步 SYN = 1 表示这是一个连接请求或连接接受报文。  
- **终止 FIN(FINish)**：用来释放一个连接。FIN = 1 表明此报文段的发送端的数据已发送完毕，并要求释放运输连接。

#### 3. TCP三次握手
1. 客户端 生成随机序列号（SN，相对SN=0），发送TCP报文给服务端（SYN=1）
2. 服务端 生成随机序列号（相对SN=0），发送TCP报文可客户端，确认号为客户端SN号+1，ACK=1，SYN=1
3. 客户端发送TCP报文给服务端，序列号·为客户端初始序列号+1，确认号为服务端序列号+1，ACK=1
>注：一般的，TCP报文的序列号为上一条自己发送的报文序列号+携带的字节数（建立连接时为随机生成，三次握手时被视为携带1字节数据），确认号为上一条对方发送的报文的序列号+携带字节数（三次握手时被视为携带1字节数据）

