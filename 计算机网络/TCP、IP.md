# TCP 重传机制
## 超时重传
当TCP 发送方发出一个数据段后，如果在预定时间内没有收到 ACK（确认），就会触发超时重传。这个时间称为**重传超时时间 RTO**。
![](https://i-blog.csdnimg.cn/direct/44e2b42f2dd64de8a772b0ccaf3f66aa.png)
