## sock_common
```c
struct sock_common {
    union {
    };
}
```
## sock
```c
struct sock {
    struct sock_common __sk_common;
}
```
## inet_connection_sock

## tcp_sock
```C
struct tcp_sock {
    struct inet_connection_sock inet_conn;
    u16 tcp_header_len;
    u16 gso_segs;
    __be32	pred_flags;
}
```
### tcp_header_len
定义了TCP头部的长度。
+ 三次连接过程中，如果在SYN_SENT状态收到服务端发动的报文中携带时间戳，那么TCP头部长度要加上时间戳长度。

### gso_segs
记录单个大尺寸数据包（SKB）通过 GSO 分片后生成的子段数量，从而在网卡硬件或驱动层高效处理分段，减轻 CPU 负载。

### pred_flags
