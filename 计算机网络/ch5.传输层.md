# 计算机网络Notes  

## 传输层协议  

### 传输层的功能

1. 提供应用进程之间的逻辑通信（即端到端的通信）。与网络层的区别是，网络层提供的是主机之间的逻辑通信。  
2. 复用和分用。复用是指发送方不同的应用进程都可以使用同一个传输层协议传送数据。分用是指接收方的传输层在剥去报文的首部后能够把这些数据正确交付到目的应用进程。  
3. 传输层还要对收到的报文进行差错检测（首部和数据部分）。而网络层只检查IP数据报的首部，不检验数据部分是否出错。  
4. 提供两种不同的传输协议，即面向连接的TCP和无连接的UDP。而网络层无法同时实现者两种协议。  

### TCP和UDP  

+ TCP提供面向连接的服务，在传送数据之前必须先建立连接，数据传送结束后要释放连接，TCP不提供广播或组播服务。由于TCP提供面向连接的可靠的传输服务，因此不可避免地增加了许多开销，如确认、流量控制、计时器以及连接管理等。这不仅使协议数据单元的头部增大很多，还要占用许多的处理机资源。因此TCP主要适用于可靠性更重要的场合，如文件传输FTP、超文本传输协议HTTP、远程登录TELNET等。  
+ UDP是一个无连接的非可靠的传输层协议。它在IP之上仅提供两个附加服务：多路复用和对数据的错误检查。IP知道怎样把分组投递给一台主机，但不知道怎样把他们投递给主机上的具体的应用。UDP在传送数据之前不需要先建立连接，远程主机的传输层收到UDP报文后，不需要给出任何确认。由于UDP比较简单，其执行速度就比较快、实时性好。使用UDP的应用主要包括小文件传送协议（TFTP）、DNS、SNMP和实时协议（RTP）。  

### UDP协议 

1. UDP无需建立连接。  
2. 无连接状态。  
3. 分组首部开销小。  
4. 应用层能更好地控制要发送的数据和发送时间。  

#### UDP的首部格式  

UDP首部8个字节，4个字段，每个字段两个字节。

1. 源端口， 在需要对方回信时选用。不需要时可用全0。  
2. 目的端口。 
3. 长度 UDP数据报的长度，其最小值是8. 4. 校验和。  

### TCP协议  

TCP主要解决传输的可靠、有序、无丢失和不重复的问题。主要特点有：  

1. TCP是面向连接的传输层协议  
2. 每一条TCP连接只能有两个端点，每一条TCP连接稚嫩恶搞是点对点的（一对一）。  
3. TCP提供可靠的交付服务，保证传送的数据无差错、不丢失、不重复且有序。  
4. TCP提供全双工通信，TCP允许通信双方的应用进程在任何时候都能发送数据，为此TCP连接的连段都设有发送缓存和接收缓存，用来临时存放双向通信的数据。  
5. TCP是面向字节流的。  

#### TCP报文段  

TCP报文段的首部最短为20字节，后面有4N字节是根据需要而增加的选项，通常长度为4字节的整数倍。  

各字段意义如下： 

1. 源端口和目的端口字段： 各占2字节。
2. 序号字段 占4字节，TCP是面向字节流的，所以会给每一个字节都编上一个序号。
3. 确认号字段： 占4字节，是期望收到对方的下一个报文段的数据的第一个字节的序号，是期望收到对方的下一个报文段的数据的第一个字节的序号，若确认号位N，则表明到序号N-1为止的所有数据都已收到。  
4. 数据偏移（即首部长度）占4位。  
5. 保留字段 占6位
6. 紧急位URG， 当URG=1时，表明紧急指针资源有效，应尽快传送。  
7. 确认位ACK 只有当ACK=1时确认号字段才有效  
8. 推送位PSH，接受到PSH=1的报文段，就尽快地交付接受应用进程。  
9. 复位位RST（Reset）， 当RST=1时，表明连接中出现严重差错，必须释放连接，然后再重新建立运输连接。  
10. 同步位（SYN） 同步SYN=1表示这是一个连接请求或连接接受报文。  
11. 终止位FIN。FIN=1表明此报文段的发送方的数据已发送完毕，并要求释放传输连接。  
12. 窗口字段 占2字节，它指出了现在允许对方发送的数据量，接收方的数据缓存空间是有限的，故用窗口值作为接收方让发送方设置其发送窗口的依据，单位为字节。  
13. 检验和 占2字节，需要添加伪首部，第4个字段即协议字段的17改为6，检验首部+数据部分。  
14. 紧急指针字段 占16位，指出本报文段中紧急数据共有多少个字节。  
15. 选项字段，长度可变。 
16. 填充字段 这是为了使整个首部长度是4字节的整数倍。  

#### 连接管理 

三个阶段：连接建立、数据传送和连接释放。  
解决三个问题：  

1. 要使每一方都能够确知对方的存在。
2. 要允许双方协商一些参数
3. 能够对运输实体资源（缓存大小等）。  

##### TCP连接的建立

“三次握手”：  

1. 客户机的TCP首先向服务器的TCP发送一个请求报文段。这个特殊的报文段不含应用层数据，其首部中的SYN标志位被置为1。另外，客户机会随机选择一个起始序号seq=x(连接请求报文不携带数据，但要消耗掉一个序号)。 
2. 服务器的TCP收到连接请求报文段后，如同意建立连接，就像客户机发回确认，并为该TCP连接分配TCP缓存和变量。在确认报文段中，SYN和ACK位都被置为1，确认号字段的值为x+1，并且服务器随机产生起始序号seq=y（消耗一个序号）。确认报文段同样不包含应用层数据。  
3. 当客户机收到确认报文段后，还要向服务器给出确认，并且也要给该链接分配缓存和变量。这个报文段的ACK标志被置为1，序号字段为x+1,确认号字段ack=y+1。该报文段可以携带数据，如果不携带数据则不消耗序号。  

##### TCP连接的释放  

“四次挥手”：  

1. 客户机打算关闭连接，就向其TCP发送一个连接释放报文段，并停止再发送数据，主动关闭TCP链接，该报文段的FIN标志位被置1，seq=u，它等于前面已传送的数据的最后一个字节的序号加1（FIN报文段即使不携带数据，也要消耗掉一个序号）。  
2. 服务器收到连接释放报文段后即发出确认，确认后是ack=u+1，而这个报文段自己的序号是v，等于它前面已发送过的珊瑚橘的最后一个字节的序号加1。  
3. 若服务器已经没有要向客户机发送的数据，就通知TCP释放连接，此时发出FIN=1的连接释放报文段。
4. 客户机收到连接释放报文段后，必须发出确认。在确认报文段中，ACK字段被置为1，确认号ack=w+1,序号seq=u+1。此时TCP连接还没有释放掉，必须经过时间等待计时器设置的时间2MSL后，A才进入到连接关闭状态。  

#### 可靠传输  

1. 序号  
2. 确认 
3. 重传  
    有两种事件会导致TCP对报文段进行重传：超时和冗余ACK。  
    （1）超时  
    TCP保留了RTT的一个加权平均往返时间$RTT_s$,当第一次测量RTT样本时，RTT值就为所测量到的RTT样本的值。但以后每测量一个新的RTT样本，就按下式重新计算一次RTT：
    $$新的RTT_s = (1-\alpha)\times(旧的RTT_s)+\alpha \times (新的RTT样本）$$ 
    若$\alpha$很接近于零，表示新的$RTT_s$和旧的$RTT_S$值相比变化不大，而受新的RTT样本影响不大。反之则表示RTT值更新较快。推荐的$\alpha$值为0.125。  
    显然，超时计时器的重传时间（Retransmission Time-Out, RTO)应略大于上面得出的加权平均往返时间$RTT_s$。使用下式计算：  
    $$RTO = RTT_s + 4\times RTT_D$$ 
    式中，$RTT_D$是$RTT$的偏差的加权平均值，它与$RTT_S$和新的$RTT$样本之差有关。当第一次测量时，$RTT_D$取为测量到的$RTT$样本值的一般，以后测量中，使用下式计算：  
    $$新的RTT_D = (1-\beta)\times(旧的RTT_D)+\beta \times |RTT_S - 新的RTT样本|$$  
   这里$\beta$的推荐值是0.25。  
   （2） 冗余ACK  
   TCP规定每当比期望序号大的失序报文段到达时，发送一个冗余确认ACK，指明下一个期待字节的序号。TCP规定当发送方收到对同一个报文段的3个冗余ACK时，就可以认为跟在这个被确认报文段之后的报文段已经丢失。此时可立即发送重传这个技术叫做快速重传。  

#### 滑动窗口协议  

##### 停止等待协议

发送方每发送一帧，都要等待接收方的应答信号，之后才能发送下一帧；接收方每接收一帧，都要反馈一个应答信号，表示k可接收下一帧，如果接收方不反馈应答信号，则发送方必须一致等待。每次只允许发送一帧，然后就陷入等待接收方确认信息的过程中，因而传输效率很低。  

##### 滑动窗口流量控制基本原理



