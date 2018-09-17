# Unix Network Programming

1. 每个UDP数据报都有一定长度，如果数据报最终正确到达目的地（即分组到达目的地且校验和正确），数据报的长度将传递给接收方的应用进程。而TCP是字节流协议，无边界记录。
2. UDP提供无连接的服务，一个UDP客户端可以用同一个套接口给不同的服务器发送数据，一个UDP服务器也可以用同一个套接口从不同的客户端接收数据。
3. TCP提供可靠性。当TCP向另一端发送数据时，它要求对方返回一个确认。如果没有收到确认，TCP自动重传并等待更长时间。在数次重传失败后，TCP才放弃。
4. TCP含有用于动态估算客户到服务器往返所花时间RTT（round-trip time）的算法，对于同一个连接，RTT也会变化，因为网络传输的拥挤情况是在不断变化的。
5. TCP提供流量控制，UDP不提供流量控制，UDP按发送方得速率发送数据，而不管接收方的缓冲区是否装得下。
6. TCP是全双工的，UDP可以是全双工的
7. TCP的三部握手
    1. 客户端发送SYN
    2. 服务器以单个分节发送ACK和SYN（ACK和SYN必须一起发送）
    3. 客户端确认SYN
8. 常用TCP选项
    1. MSS选项：最大分节大小（maximum segment size),通常为1460（1500（MTU）-20（IP首部）-20（TCP首部）），可以通过TCP_MAXSEG获取和设置
    2. 窗口规模选项
        1. 因为TCP首部中窗口大小为16位，因此能通知对方的最大窗口大小是65535，为了获取更大的吞吐量，这个新选项制定TCP头部的广告窗口必须扩大（即左移）的位数（0-14），因此所提供的最大窗口是65535*2^14字节。
        2. 两端必须都支持该选项才可以扩大窗口规模
        3. 可以通过SO_RCVBUF选项获取相关信息
        4. 一般地，TCP可以作为主动打开的一方随它的SYN发送该选项，但只有对方也随它的SYN发送该选项时才能扩大窗口规模。服务器的TCP只有接收到随客户端的SYN来的该选项时才能发送该选项。
9. TIME_WAIT
    1. 执行主动关闭的一端能进入到TIME_WAIT状态，持续时间是2MSL。RFC1122建议MSL的值为2分钟，而源自Berkeley的实现传统上使用的值是30s。
    2. TIME_WAIT状态存在的理由
        1. 实现终止TCP全双工连接的可靠性，维护状态信息以重发最终的ACK
        2. 允许老的重复分解在网络中消失。2MSL足够让某个方向上的分组最多存活MSL秒即被丢弃，另一个方向上的应答最多存活MSL秒也被丢弃。
10. Accept出来的df跟listen的fd使用相同的端口，并且一旦连接建立，多IP服务器上accept出来的fd的IP已经确定。在Linux的实现上，accept出来的fd不继承listen的fd的状态标识，如O_NONBLOCK和O_ASYNC等。TCP无法仅仅通过查看目的端口号来分离外来的分节，它必须查看套接口对的所有四个元素才能确定由哪个端点接收到达的分节。
11. IP分片
    1. MTU由硬件规定，以太网的MTU通常为1500字节。在两台主机间的路径上的最小MTU称为路径MTU，路径MTU在不同的方向可以不相同，因为在网络中路由是非对称的。
    2. IP数据报将从某个接口发出时，如果它的大小超过相应链路的MTU，IPv4和IPv6都会执行分片。各片段在到达目的地前不会重组。IPv4主机对其产生的数据报执行分片，IPv4路由器对其转发的数据报也执行分片。但IPv6只在数据报产生的主机执行分片，IPv6路由器对其转发的数据报不执行分片。
    3. 如果IPv4头部的DF（不分片）位被设置，那么不管是发送主机还是转发路由器都不能对本数据报分片。当路由器接收到一个超过其外出链路MTU大小且设置了DF位的IPv4数据报时，它将产生一个ICMPv4出错消息。
    4. 因为IPv6路由器不执行分片，因此IPv6数据报隐含设置了DF位。IPv4的DF位和IPv6的隐含DF位可以用于路径MTU的发现。路径MTU的发现对于IPv4是可选的，对于IPv6是必须的。
    5. IPv4和IPv6都定义了最小重组缓冲大小（minimum reassembly buffer size）：任何实现都必须支持的最小数据报大小。对于IPv4其值为576字节，IPv6其值为1500字节。对于IPv4，我们不能确定目的主机是否接受577字节的数据报，因此很多使用UDP的应用程序避免产生大于这个大小的数据报。
    6. TCP有一个MSS用于向对方TCP通告对方在每个分节中能发送的最大TCP数据量。SYN分解中带有MSS选项。MSS的目的是告诉对方其重组缓冲区大小的实际值，从而避免分片。MSS经常被设置成MTU减去IP和TCP头部的固定长度。
12. TCP输出
    1. 每一个TCP套接口有一个发送缓冲区，可以用SO_SNDBUF套接口选项改变发送缓冲区的大小。
    2. TCP以MSS大小或更小的块发送数据给IP（它同时在每个数据块上安上TCP头部以构成分节）。其中MSS是由对方通告的，当对方未通告时就是用536这个值。IP给每个数据块安上IP头部以构成数据报，查找器目的IP地址的路由表项以确定外出接口，然后把数据报传递给相应的数据链路。IP可能先将数据分片，再传递给链路层。MSS选项的目的是避免分片，新的实现使用路径MTU发现功能。每个链路有一个输出队列，如果队列满，则分组丢弃，并沿协议栈向上返回一个错误，从链路层到IP层，再从IP层到TCP层。TCP注意到这个错误，并在以后某个时刻重传这个分节，应用程序并不知道这种暂时情况。
13. UDP的输出
    1. UDP套接口有发送缓冲区大小（可用SO_SNDBUF套接口选项修改），但是它仅仅是写到套接口的UDP数据报的大小上限。如果一个进程写一大于套接口发送缓冲区的数据报，则返回EMSGSIZE错误，数据在沿协议栈向下传递时，以某种形式拷贝到内核的缓冲区，当链路层把数据传出后这个拷贝就丢弃。
    2. UDP简单的安上它的8个字节的头部以构成数据报并把它传递给IP。IP给数据安上IP头部，执行路由确定外出接口，然后或直接把数据报加入到链路层输出队列（如果适合于MTU），或是分片后再把每个片段加入数据链路层的输出队列。
    3. 如果UDP发送一个大数据包，它比TCP应用程序更有可能分片，因为TCP会把应用程序数据划分成MSS大小的块，但UDP没有对等的手段。
    4. 从UDP套接口的write调用成功返回表示数据报或所有片段已经加入链路层的输出队列。如果输出队列没有足够的空间存放数据报或它的某个分节，UDP将返回应用进程ENOBUFS错误。（有些UDP实现简单丢弃数据并不返回错误。）
14. 通用套接口地址

    ```
    #include <sys/socket.h>

    struct sockaddr {
        uint8_t           sa_len;
        sa_family_t sa_family;
        char        sa_data[14];
    };
    ```
15. IPv4套接口地址

    ```
    #include <netinet/in.h>


    struct sockaddr_in {
        uint8_t        sin_len;
        sa_family_t    sin_family;
        in_port_t      sin_port;
        struct in_addr sin_addr;
        char           sin_zero[8];
    };
    ```
    1. `uint8_t`等整型的定义在有文件`<sys/types.h>`中
    2. `sa_family_t`套接口地址结构的地址族，在`<sys/socket.h>`中定义，IPv4为`AF_INET`
    3. `socklen_t`套接口地址的长度，一般为`uint32_t`,在`<sys/socket.h>`中定义
    4. `struct in_addr`的定义

        ```
        #include <netinet/in.h>
        struct in_addr {
            in_addr_t s_addr;
        };
        ```
    5. `in_addr_t` IPv4地址，一般为`uint32_t`, 在`<netinet/in.h>`中定义
    6. `in_port_t` TCP或UDP端口，一般为`uint16_t`, 在`<netinet/in.h>`中定义。
16. 地址的传递
17. 字节序
18. 地址转换函数
19. `isfdtype`测试描述字
20. 套接口协议族
21. 套接口协议类型
22. `connect`函数
23. TCP发送RST的情况
24. 对于
25. 可以使用`tcpkill`命令终止一个进行中的TCP连接
26. bind函数
27. listen函数
28. accept函数
29. fork和exec函数
30. close函数
31. getsockname和getpeername函数
32. wait函数
33. accept返回前连接夭折
34. SIGPIPE信号
35. 五个IO模型
36. 同步IP与异步IO
37. select函数
38. shutdown函数
39. pselect
40. poll
41. getsockopt和setsockopt函数
42. 某些
43. SO_KEEPALIVE选项
44. TCP_KEEPALIVE选项
45. SO_LINGER选项
46. SO_RECVBUF和SO_SNDBUF选项
47. SO_RCVLOWWAT和SO_SNDLOWWAT
48. SO_RCVTIMEO和SO_SNDTIMEO
49. SO_REUSEADDR
50. SO_TYPE
51. IP_TOS
52. IP_TTL
53. TCP_MAXRT
54. TCP_MAXSEG
55. TCP_NODELAY
56. fcntl
57. sendto 和 recvfrom
58. UDP
59. UDP的connect函数
60. 域名解析
61. uname
62. gethostname
63. 与网络相关的
64. 套接口超时
65. recv和send
66. readv和writev
67. recvmsg和sendmsg
68. IO函数的差异
69. 辅助数据
70. 使用MSG_PEEK标志
71. 标准IO用于套接口
72. T/TCP
73. Unix域套接口
74. Unix域套接口结构
75. socketpair函数
76. Unix域套接口函数
77. 非阻塞IO
78. 非阻塞connect
79. 非阻塞accept
80. ioctl操作
81. 广播
82. 多播
83. 信号驱动IO
84. 原始套接口
