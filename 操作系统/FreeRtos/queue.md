# 数据结构
## 队列
### 成员
+ int8_t * pcHead
指向队列存储区域起始地址的指针，具体功能包括：
  + 数据存储管理：指向队列中第一个数据项的起始位置；
  + 队列操作基准：入队（xQueueSend）和出队（xQueueReceive）操作均以 pcHead 为基准计算数据位置；
+ int8_t * pcWriteTo
+ List_t xTasksWaitingToSend
  用于管理因队列满而阻塞的待发送任务。以下是其详细解析；
  + 当任务尝试向已满队列发送数据时，若指定了超时时间，该任务会被挂起都改列表；
  + 当队列有空间时（例如其他任务从队列读取数据），调度器会从该列表唤醒优先级最高的任务继续发送操作；
+ List_t xTasksWaitingToReceive
  专门用于管理因队列空而阻塞的待接收任务；
  + 阻塞任务管理：当任务尝试从空队列读取数据且指定阻塞时间时，任务会被挂起到 xTasksWaitingToReceive 列表；
  + 数据到达唤醒：当新数据入队（如其他任务调用 xQueueSend），系统会从该列表唤醒优先级最高的任务继续接收操作；
+ volatile UBaseType_t uxMessagesWaiting
  用于实时跟踪队列中当前存储的消息数量；
  + 消息计数器：记录队列中当前有效的数据项数量；
  + 空/满判断依据
  + 性能优化：避免频繁计算 pcWriteTo 和 pcReadFrom 的相对位置；
+ UBaseType_t uxLength
  用于定义队列的容量和行为上限
  + 容量定义：指定队列能够存储的最大数据项数量（非字节数）
  + 内存分配基准：与 uxItemSize 共同决定队列存储区大小（uxLength * uxItemSize）
  + 流控关键参数：影响任务阻塞行为（当 uxMessagesWaiting == uxLength 时队列满）
+ UBaseType_t uxItemSize
+ volatile int8_t cRxLock
  用于接收端（Read）的线程安全控制;
  + 中断安全锁：保护队列接收操作（如 xQueueReceive）在中断与任务间的原子性;
  + 性能优化：减少临界区范围，通过计数锁替代全局中断禁用;
  + 先级继承：与调度器协作防止优先级反转;
+ volatile int8_t cTxLock
  + 
用于管理数据入队（写入）的位置。
# 创建队列
## xQueueGenericCreate
### 参数
### 功能
### 实现
步骤 1：如果队列长度大于0并且
