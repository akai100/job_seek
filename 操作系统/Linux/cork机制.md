## 介绍
Cork机制是TCP协议中的一种数据传输优化技术，通过阻塞小包发送、积累数据至合理大小再发送，以提升网络吞吐量和效率。

## 核心原理
### 避免小包问题
+ Cork机制像“塞子”一样阻塞数据发送，强制积累多个小数据包，直到达到MSS（最大分段大小） 或超时（通常200ms）才发送
+ 
