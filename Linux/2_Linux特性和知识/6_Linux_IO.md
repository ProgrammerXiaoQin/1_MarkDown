1. 常见IO模型有:
	- 阻塞模型
	- 非阻塞模型
	- IO多路复用
	- 异步IO

2. 网络IO指的是在网络中进行数据的读 , 写操作 , 本是上就是一个socket套接字读取 , socket套接字在Linux系统中被抽象为流的概念 . 网络IO就是对数据流的处理

3. 阻塞与非阻塞模型
	- 阻塞IO会一直阻塞对应进程 , 知道数据操作完毕
	- 非阻塞IO是在内核空间准备数据的阶段立即返回

4. 同步IO和异步IO区别
	- 同步IO在进行IO操作的时候 , 进程会被阻塞
	- 异步IO实在进程发起IO操作后 , 内核立即返回 , 直到内核发送型号 , 告诉进程IO操作完成了, 整个进程是没有阻塞的

##### 1. 同步阻塞模型
1. Linux默认情况下 , 所有的socket套接字操作都是阻塞的 , 阻塞是指进程在等待中 , cpu此时去干别的事去了 , 其优缺点如下
	- 阻塞IO能及时返回数据 , 无延迟
	- 对于开发人员负担较低
	- 对于用户不友好 , 性能较低

2. 同步阻塞IO的特点是 , IO执行的两个阶段都是阻塞的
	- 用户空间发起调用 , 内核准备数据时 , 阻塞
	- 内核拷贝数据到用户空间时 , 阻塞

##### 2. 同步非阻塞模型
1. 进程每隔一会就会检索一次 , 进行轮询调用方式 , 查询数据是否准备好 , 其优缺点如下
	- 等待时能进行其他任务 , 也包括继续提交新的任务
	- 但是任务完成延迟比较大 , 需要多次的发起系统轮询调用操作 , 并且有可能在轮询过程中数据已经准备完毕 , 造成延迟 , 对系统吞吐性能降低


##### 3. IO多路复用
1. select模型  同时监听多个链接 , 等待链接可读


##### 4. 异步非阻塞IO模型
