# TCP 重传机制
## 超时重传
当TCP 发送方发出一个数据段后，如果在预定时间内没有收到 ACK（确认），就会触发超时重传。这个时间称为**重传超时时间 RTO**。

![](https://i-blog.csdnimg.cn/direct/44e2b42f2dd64de8a772b0ccaf3f66aa.png)

RTO的计算通常使用 RTT（往返时间）预测算法，结合抖动值进行加权调整。

## 快速重传
快速重传避免了等待 RTO 超时时间。当接收方收到三个相同的 ACK 时，发送发立刻重传对应的段。任务改段可能丢失；

## SACK 方法
SACK（选择性确认）机制允许接收方高速方法方：我收到了哪些数据段，即便这些段是不连续的。

![](https://i-blog.csdnimg.cn/direct/5d9523a406644655a33feec33f5d702a.png)

## Duplicate SACK
一种对 SACK 的增强，用于进一步标明哪些段被重复收到了，有助于快速判断拥塞与丢包状态。
### ACK包丢失
![](https://i-blog.csdnimg.cn/direct/0843160b20e348b29518c4a45cdc1790.png)

![](https://i-blog.csdnimg.cn/direct/9f6e2e3d7c204861b25d5eedac243347.png)

# 滑动窗口机制
滑动窗口用于控制发送方可以连续发送多少字节而无需等待 ACK。其大小取决于接收方通告窗口（rwnd）和拥塞窗口（cwnd）中较小的值。

![](https://i-blog.csdnimg.cn/direct/8259127725fa45b8b5d5c24121bcab3d.png)

## 工作原理
+ 每次发送新的数据后，窗口向右滑动；
+ 收到 ACK 后，窗口继续向右滑动；
+ 丢包或者拥塞时，窗口收缩；

![](https://i-blog.csdnimg.cn/direct/d8be837c7a9549f8bf57bf43b9d4f2ac.png)

## 流量控制
### 基本思想
流量控制主要是为了防止发送方发太快，而接收方“接不过来”。这依赖于接收方通告窗口（rwnd）,它告诉发送“我还能接收多少数据”。
![](https://i-blog.csdnimg.cn/direct/b00d722b6e2d41058d10e65e5e3e5823.png)

### 缓冲区与窗口的关系
接收方在操作系统内核中分配一段 缓冲区 存储TCP数据。RWND就是这个缓冲区剩余的反映。

![](https://i-blog.csdnimg.cn/direct/0b5185d6fb5c48b39ed286ccf200904e.png)

为了防止这种情况发生，TCP规定是不允许同时减少缓存又收缩窗口的，而是采用先收缩窗口，过段时间再减少缓存，这样就可以避免了丢包情况。

### 窗口关闭

当 rwnd=0时，TCP会暂时关闭窗口，不再发送数据。发送方会周期性发送“窗口探测包”询问接收方缓冲是否已空。
![](https://i-blog.csdnimg.cn/direct/4d0a35be0ee1478ab106ef77f3f4a040.png "窗口关闭")


### 糊涂窗口综合症
当接收方的缓冲区空闲很少就通告窗口，或发送方每次只发一点点数据，这会导致网络效率极低。这种现象称为“糊涂窗口综合症”。
![](https://i-blog.csdnimg.cn/direct/5d4dccdcc0814b9f8661d3bd7f043e17.png)

解决方案：
+ 接收方策略：不通告过小窗口；
+ 发送方策略：累积数据后再发送；

## 拥塞控制

防止发送方过快发送数据导致网络拥塞。

### 慢启动
初始时 cwnd 很小 （1 MSS），每收到一个 ACK 就将 cwnd 加倍（指数增长）。直到达阈值或出现丢包为止。

![](https://i-blog.csdnimg.cn/direct/218d9e0e27bd4d53913d22002841fb60.png)

### 拥塞避免

当 cwnd > ssthresh 后，进入线性增长阶段，即每个 RTT 只增长 1 MSS，避免过快增大窗口。

![](https://i-blog.csdnimg.cn/direct/84480e8380bc45ddbc9e4858705edc7c.png)

### 拥塞发生（例如快速重传触发）

+ cwnd 减半（乘法减小）
+ ssthresh 设置为一半；
+ 重回慢启动或进入快速恢复；

![](https://i-blog.csdnimg.cn/direct/dd922830756249bb895573d8a3de9175.png)

### 快速恢复
当触发快速重传时，不进入慢启动，而是认为网络并未严重阻塞，因此采用快速恢复算法；
+ 将 cwnd 设置为 ssthresh；
+ 继续进入拥塞避免阶段；

![](https://i-blog.csdnimg.cn/direct/211a68a57bc2468790eb52498d6f45bf.png)

