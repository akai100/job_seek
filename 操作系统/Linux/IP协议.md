# 1. IP协议基础
## 1.1 协议核心原理
### 1.1.1 报文结构
版本（4bit） + 报头长度（4bit）+ 服务类型（8bit）+ 总长度（16bit）

标识（16bit）+ 保留（1bit） + DF（1bit）+ MF（1bit）+ 分段偏移量（13bit）

存活时间（8bit）+ 协议（8bit）+ 报文校验和（16bit）

源地址（32bit）

目的地址（32bit）

+ 版本
  
  协议的版本
+ 报头长度
  
  报头的长度，以32位为单位
+ 服务类型（TOS）
+ 总长度
  
  封包长度（包括报头），以字节为单位；
+ 标识
+ DF

  不分段标记
+ MF

  还有其他分段
+ 分段偏移量
+ 存活时间（Time To Live, TTL）
+ 协议
+ 报文校验和
+ 源地址
+ 目的地址
+ 选项

### 1.1.2 地址管理
### 1.1.3 路径MTU发现
路径MTU发现用于发现封包传输至指定目的地地址而不用被分段的最大尺寸。

路径MTU 功能配置：

+ IP_PMTUDISC_DONT
  
  绝不使用报文头中设定了 DF 标志的IP封包，因此，不使用路径MTU发现功能；
+ IP_PMTUDISC_DO
  
  总是在本地节点所产生的封包的报文头中设定 DF 标志，试图在每次传输时都找出最佳的PMTU；
+ IP_PMTUDISC_WANT
  
  按每条路径决定是否使用路径MTU发现功能。这是默认行为。

开启路径MTU发现功能，下面两种情况可能锁定PMTU, 无法被更改：

+ ip route 添加时，通过关键字 lock 锁定MTU
  
  ```ip route add 10.10.1.0/24 via 100.100.100.1 mtu lock 750```

+ 收到 ICMP FRAGMENTATION NEEDED 消息使得你要用的PMTU小于最小容许值，则PMTU会设成最小值，然后锁住。
  ```/proc/net/ipv4/route/min_pmtu```

## 1.2 路由基础

