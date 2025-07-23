## 保活机制介绍

## LINUX 内核实现
### 重置保活定时器
```mermaid
graph TD
    A[tcp_v4_do_rcv]
    B[tcp_rcv_state_process]
    C{TCP_SYN_SENT?}
    D[tcp_rcv_synsent_state_process]
    E[tcp_finish_connect]
    F[inet_csk_reset_keepalive_timer]
    G[sk_reset_timer]
    H{FIN_WAIT1}
    I[tcp_set_keepalive]
    A --> B
    B --> C
    C -->|是| D
    D -->|收到ACK| E
    E -->|进入ESTABLISH态| F
    F -->|重置保活定时器| G
    C -->|否| H
    H --> F
    I --> F
```

### 保活定时器
当保活定时器超时调用 ```tcp_keepalive_timer```函数
```mermaid
graph TD
    A[tcp_keepalive_timer]
    B[sock_put]
    C[结束]
    D{LISTEN?}
    E{FIN_WAIT2?}
    F{SOCK_DEAD?}
    G{立即终止WAIT_2态？}
    A --> D
    D -->|是| B
    B --> C
    D -->|否|E
    E -->|是|F
    F -->|是|G

```
