## 介绍
TCP 快速重传（Fast Retransmit）是 TCP 协议中用于快速检测和恢复数据包丢失的核心机制，通过冗余 ACK（重复确认）触发重传，避免依赖超时重传（RTO）的延迟。

## 原理
（1）三次冗余 ACK 规则
当发送方连续收到 3 个相同的 ACK（如 ACK=2）时，认为该 ACK 对应的数据包（如 Seq2）已丢失，立即重传而非等待超时。

## 实现
