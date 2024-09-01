# 1 CAN 总线简介
CAN（Controller Area Network），是 ISO 国际标准化的串行通信协议。
低速 CAN(ISO11519)通信速率10-125Kbps，总线长度可达1000米；高速 CAN(ISO11898)通信速率125Kbps-1Mbps，总线长度≤40米；CAN FD 通信速率可达5Mbps，并且兼容经典 CAN，遵循 ISO 11898-1 做数据收发。

![[Pasted image 20231209104534.png]]

异步通信方式，采用差分信号传输，一般用双绞线提升信号抗干扰能力
![[Pasted image 20231209104654.png]]

![[Pasted image 20231209105056.png]]

![[Pasted image 20231209105147.png]]

![[Pasted image 20231209105228.png|CAN的帧类型]]
# CAN 帧格式
![[Pasted image 20231213200155.png]]
# CAN 的 HAL 库编程

HAL 库关于 CAN 的所有函数定义和结构体定义分别在 `Drivers/STM 32 F 4 xx_HAL_Driver` 文件夹下 `stm32f4xx_hal_can.c` 和 `stm32f4xx_hal_spi.h` 中

下面列举一些基本的和常用的


## CAN 基本结构体
### 发送报文定义结构体 （CAN_TxHeaderTypeDef）
```C
Typedef struct
{
  Uint32_t StdId;    /*! 标准 ID。参数值只能是 0 到 0 x 7 FF (二进制的 11 位 1) */

  Uint32_t ExtId;    /*! 扩展 ID。参数值只能是 0 到 0 x 1 FFFFFFF (二进制的 29 位 1)*/

  Uint32_t IDE;      /*! IDE。HAL 库编程时，标准帧填写 CAN_ID_STD，扩展帧填写CAN_ID_EXT */

  Uint32_t RTR;      /*! RTR。HAL 库编程时，数据帧填写 CAN_RTR_DATA，遥控帧填写 CAN_RTR_REMOTE */

  Uint32_t DLC;      /*! DLC。数据长度，参数值只能是 0 到 8 */

  FunctionalState TransmitGlobalTime; /*!< Specifies whether the timestamp counter value captured on start
                          Of frame transmission, is sent in DATA 6 and DATA 7 replacing pData[6] and pData[7].
                          @note: Time Triggered Communication Mode must be enabled.
                          @note: DLC must be programmed as 8 bytes, in order these 2 bytes are sent.
                          This parameter can be set to ENABLE or DISABLE. */

} CAN_TxHeaderTypeDef;
```
### 接收报文定义结构体 （CAN_RxHeaderTypeDef）
```C
typedef struct
{
  uint32_t StdId;   
  uint32_t ExtId;    
  uint32_t IDE;      
  uint32_t RTR;      
  uint32_t DLC;      
  uint32_t Timestamp; /*!< Specifies the timestamp counter value captured on start of frame reception.
@note: Time Triggered Communication Mode must be enabled.This parameter must be a number between Min_Data = 0 and Max_Data = 0xFFFF. */

  uint32_t FilterMatchIndex; /*!< Specifies the index of matching acceptance filter element.This parameter must be a number between Min_Data = 0 and Max_Data = 0xFF. */

} CAN_RxHeaderTypeDef;
```

这段代码定义了一个结构体 CAN_RxHeaderTypeDef，用于存储 CAN 接收到的消息的头部信息。这个结构体包含了以下字段：
1. StdId：标准标识符，用于标识消息类型。
2. ExtId：扩展标识符，用于在标准标识符冲突时使用。
3. IDE：标识符类型，0 表示标准标识符，1 表示扩展标识符。
4. RTR：请求帧类型，0 表示数据帧，2 表示远程帧。
5. DLC：数据长度，表示消息中数据的字节数。
6. Timestamp：时间戳，用于记录消息接收的开始时间。
7. FilterMatchIndex：过滤器匹配索引，表示消息匹配的过滤器索引。

这个结构体用于存储 CAN 接收到的消息的头部信息，方便后续处理消息时使用。
接收过滤器类型定义结构体（CAN_FilterTypeDef）

```C
typedef struct
{
  uint32_t FilterIdHigh;          /*!过滤器验证码ID高16位，只能填０到０ｘFFFF　中间的数值 */

  uint32_t FilterIdLow;           /*!过滤器ID低16位，只能填０到０ｘFFFF　中间的数值*/

  uint32_t FilterMaskIdHigh;      /*!过滤器掩码ID高16位，只能填０到０ｘFFFF中间的数值 */

  uint32_t FilterMaskIdLow;       /*!过滤器掩码ID低16位，只能填０到０ｘFFFF中间的数值 */

  uint32_t FilterFIF(x)Assignment;  /*!将通过的报文放入哪个FIFOx中，填写FIFO(x) */

  uint32_t FilterBank;            /*!使用过滤器编号。使用一个CAN，则可选 0~13；使用两个CAN可选                                        0~27 */

  uint32_t FilterMode;            /*!过滤器模式选择。掩码模式填写 CAN_FILTERMODE_IDMASK，列表模式填写 CAN_FILTERMODE_IDLIST */

  uint32_t FilterScale;           /*!过滤器位宽，32位是CAN_FILTERSCALE_32BIT，
                                               16位是CAN_FILTERSCALE_16BIT */

  uint32_t FilterActivation;      /*!是否使能过滤器，DISABLE 或 ENABLE */

  uint32_t SlaveStartFilterBank;  /*!< Select the start filter bank for the slave CAN instance.
For single CAN instances, this parameter is meaningless.
For dual CAN instances, all filter banks with lower index are assigned to master
CAN instance, whereas all filter banks with greater index are assigned to slave
CAN instance.
This parameter must be a number between Min_Data = 0 and Max_Data = 27. */
} CAN_FilterTypeDef;

```


注意：FilterIdHigh、FilterIdLow、FilterMaskIdLow、FilterMaskIdLow 这四个成员不是它的名字那么简单，

​ 他们的功能取决于使用的过滤器模式。若为列表模式，则不管是 FilterId 还是 FilterMaskId 都表示的是列

​ 表成员。

​ 上述四个成员都是 16 位的，一定注意下面说的移位操作

​ 在 32 位过滤器中，一组 High 和 Low 检验同一个 CAN ID；16 位过滤器，则可分别独立检验一个 ID
过滤器寄存器如下：（使用 HAL 库可以不必深挖具体的寄存器，只需注意 Mapping 每一位的内容）


- ​32 位列表模式
![[Pasted image 20231213201901.png]]
- ​16 位列表模式
![[Pasted image 20231213201915.png]]
- ​32 位掩码模式
- 16 位掩码模式
![[Pasted image 20231213201923.png]]

标准帧用 16 位过滤器表示：FilterIdHigh、FilterIdLow、FilterMaskIdLow、FilterMaskIdLow 各可以表示一个列表成员。由于标准帧的 ID 是 11 位的，所以需要将其 ID 左移５位

标准帧用 32 位过滤器表示：FilterIdHigh、FilterIdLow 共同表示一个 ID，FilterMaskIdLow、FilterMaskIdLow 共同表示一个 ID。由于标准帧 ID 是 11 位的，故表示时其 ID 需左移５位存入 High，且 LOW = 0 | CAN_ID_STD （意思是将 IDE 位置为0）

扩展帧用 32 位过滤器表示：扩展帧的 ID 是 29 位的，所以将扩展帧 ID 先左移 3 位与 32 位按高位对齐，再右移 16 位并取低 16 位，存入 High；将扩展帧 ID 左移 3 位与 32 位对齐，再取低 16 位存入 Low。具体操作如下
```C
CAN_FilterTypeDef sFilterConfig;//定义过滤器设置结构体变量
Uint32_t StdId =0 x 321;				           //一个标准 CAN ID
Uint32_t ExtId =0 x 1800 f 001;			       //一个扩展 CAN ID

SFilterConfig.FilterIdHigh = StdId<<5;			   //标准 ID 放入 FilterIdHigh
SFilterConfig.FilterIdLow = 0|CAN_ID_STD;		   //标准 ID，故设置 IDE 位为0

sFilterConfig.FilterMaskIdHigh = ((ExtId<<3)>>16)&0 xffff;       //扩展帧 ID 高 16 位放入FilterMaskIdHigh
SFilterConfig.FilterMaskIdLow = (ExtId<<3)&0 xffff|CAN_ID_EXT;	//扩展帧 ID 高 16 位放入FilterMaskIdLow，并设置IDE位为1
```

关于验证码和掩码：假设有一个关注列表，里面全是感兴趣的报文。验证码可以是关注列表中任意一个成员的 ID（经上述处理），而掩码是所有感兴趣的成员的同或的结果

综上，两种位宽，两种模式，共四种过滤模式，特点总结如下：

模式特点
16 位列表可包含四个列表成员
32 位列表可包含两个列表成员
16 位掩码可检验两对验证码和掩码
32 位掩码可检验一对验证码和掩码

## CAN 基本函数
控制函数如下表


| 函数名称 | 功能 |
| --- | --- |
| HAL_CAN_Start | 开启 CAN 通讯 |
| HAL_CAN_Stop | 关闭 CAN 通讯 |
| HAL_CAN_RequestSleep | 尝试进入休眠模式 |
| HAL_CAN_WakeUp | 从休眠模式中唤醒 |
| HAL_CAN_IsSleepActive | 检查是否成功进入休眠模式 |
| HAL_CAN_AddTxMessage | 向 Tx 邮箱中增加一个消息, 并且激活对应的传输请求 |
| HAL_CAN_AbortTxRequest | 请求中断传输 |
| HAL_CAN_GetTxMailboxesFreeLevel | Return Tx mailboxes free level |
| HAL_CAN_IsTxMessagePending | 检查是否有传输请求在指定的 Tx 邮箱上等待 |
| HAL_CAN_GetRxMessage | 从 Rx FIFO 收取一个 CAN 帧 |
| HAL_CAN_GetRxFifoFillLevel | Return Rx FIFO fill level |
### 函数功能详细介绍
`HAL_StatusTypeDef HAL_CAN_Start (CAN_HandleTypeDef *hcan)`

功能：开启 CAN，一般一开始就要打开
参数：CAN 句柄指针，&hcan 1 或 &hcan2
返回值：返回值：HAL 状态

`HAL_StatusTypeDef HAL_CAN_ConfigFilter (CAN_HandleTypeDef *hcan, CAN_FilterTypeDef *sFilterConfig)`


功能：配置 CAN 过滤器
参数：第一个，CAN 句柄指针，&hcan 1 或 &hcan2
第二个，CAN 配置结构体的指针
返回值：返回值：HAL 状态

`HAL_StatusTypeDef HAL_CAN_ActivateNotification (CAN_HandleTypeDef *hcan, uint 32_t ActiveITs)`

功能：使能中断
参数：第一个，CAN 句柄指针，&hcan 1 或 &hcan2
第二个，使能哪个中断，在 stm 32 f 4 xx_HAL_Driver. H 中，搜索 Receive Interrupts 可以查到各宏定义
返回值：返回值：HAL 状态


`Void HAL_CAN_RxFifo 0 MsgPendingCallback (CAN_HandleTypeDef *hcan)（根据使用 0 可能替换成 1）`
功能：接收回调函数，CAN 回调后进行的操作。一般在此接收数据
参数：第一个，CAN 句柄指针
返回值：void


`HAL_StatusTypeDef HAL_CAN_AddTxMessage (CAN_HandleTypeDef *hcan, CAN_TxHeaderTypeDef *pHeader, uint 8_t aData[], uint 32_t *pTxMailbox)`
功能：增加一个消息到第一个空闲的 Tx 邮箱，并且激活对应的传输请求

参数：第一个，CAN 句柄指针，&hcan 1 或 &hcan2
第二个，发送报文定义结构体的指针
​ 第三个，数据帧数组的内容，长度不能超过定义的长度
​ 第四个，Tx 邮箱，可填 (uint 32_t*) CAN_TX_MAILBOX 0 ，CAN_TX_MAILBOX 1 ， CAN_TX_MAILBOX3
返回值：HAL 状态


`HAL_StatusTypeDef HAL_CAN_GetRxMessage (CAN_HandleTypeDef *hcan, uint 32_t RxFifo, CAN_RxHeaderTypeDef *pHeader, uint 8_t aData[])`

功能：从 Rx FIFO 收取一个 CAN 帧
参数：第一个，CAN 句柄指针，&hcan 1 或 &hcan 2​
第二个，Rx FIFO，可填 CAN_RX_FIFO 0， CAN_RX_FIFO 1
​ 第三个，接收报文定义结构体的指针
​ 第四个，接受数据数组
返回值：HAL 状态

`HAL_StatusTypeDef HAL_CAN_ConfigFilter (CAN_HandleTypeDef *hcan, CAN_FilterTypeDef *sFilterConfig)`

功能：设置接收过滤器
参数：第一个，CAN 句柄指针，&hcan 1 或 &hcan2
第二个，过滤器类型定义结构体指针
返回值：HAL 状态

## CAN 编程步骤示例
定义接收过滤器类型定义结构体（CAN_FilterTypeDef）变量

配置 CAN 接收过滤器 HAL_CAN_ConfigFilter

开启 CAN，HAL_CAN_Start ()

发送，HAL_CAN_AddTxMessage

接收，需要先开启中断 HAL_CAN_ActivateNotification，第二个参数设置为 CAN_IT_RX_FIFO 0_MSG_PENDING

接收中断回调函数 HAL_CAN_RxFifo 0 MsgPendingCallback，里面定义接收报文定义结构体（CAN_RxHeaderTypeDef）结构体变量。用接收函数 HAL_CAN_GetRxMessage 接收

## CAN 轮询接收信息示例
```C
// CAN_TestPoll函数用于发送和接收CAN消息
void CAN_TestPoll(uint8_t msgID, uint8_t frameType)
{
 // 1. 发送消息
 uint8_t txdata[8]; // 定义一个长度为8的数组，用于存储发送的消息数据
 txdata[0] = msgID; // 将msgID存储到数组的第一个元素
 txdata[1] = msgID + 11; // 将msgID+11存储到数组的第二个元素

 CAN_TxHeaderTypeDef txheader; // 定义一个CAN_TxHeaderTypeDef结构体，用于存储发送消息的头部信息
 txheader.StdId = msgID; // 将msgID存储到txheader的StdId成员
 txheader.RTR = frameType; // 将frameType存储到txheader的RTR成员
 txheader.IDE = CAN_ID_STD; // 将CAN_ID_STD存储到txheader的IDE成员
 txheader.DLC = 2; // 将2存储到txheader的DLC成员，表示消息数据长度为2个字节
 txheader.TransmitGlobalTime = DISABLE; // 将DISABLE存储到txheader的TransmitGlobalTime成员，表示不使用全局时间

 while (HAL_CAN_GetTxMailboxesFreeLevel(&hcan1) < 1) // 如果发送缓冲区中可用空间不足1个，则进入循环
   ;

 uint32_t txmailbox; // 定义一个uint32_t变量，用于存储发送消息时分配的缓冲区编号

 if (HAL_CAN_AddTxMessage(&hcan1, &txheader, txdata, &txmailbox) != HAL_OK) // 如果添加发送消息到缓冲区失败，则输出错误信息并返回
 {
   printf("Send to mailbox %d failed\r\n", txmailbox);
   return;
 }
 uint8_t rxdata[8]; // 定义一个长度为8的数组，用于存储接收到的消息数据
 CAN_RxHeaderTypeDef rxheader; // 定义一个CAN_RxHeaderTypeDef结构体，用于存储接收到的消息头部信息
 printf("Send to mailbox %d\r\n", txmailbox); // 输出发送消息时分配的缓冲区编号

 while (HAL_CAN_GetTxMailboxesFreeLevel(&hcan1) != 3) // 如果发送缓冲区中可用空间不足3个，则进入循环
   ;

 // 以轮询方式接收消息
 HAL_Delay(1); // 等待1ms
 if (HAL_CAN_GetRxFifoFillLevel(&hcan1, CAN_RX_FIFO0) != 1) // 如果接收缓冲区0中没有消息，则进入循环
 {
   printf("No message received\r\n");
   return;
 }
 else
 {
   printf("Received message\r\n");
 }

 if (HAL_CAN_GetRxMessage(&hcan1, CAN_RX_FIFO0, &rxheader, rxdata) == HAL_OK) // 如果从接收缓冲区0中读取消息成功，则进入循环
 {
   printf("StdId: %d\r\n", rxheader.StdId); // 输出接收到的消息ID
   printf("RTR:(0=Data,2=Remote) = %d\r\n", rxheader.RTR); // 输出接收到的消息类型
   printf("IDE:(0=Std,4=EXT) = %d\r\n", rxheader.IDE); // 输出接收到的消息类型
   printf("DLC(DataLenth): %d\r\n", rxheader.DLC); // 输出接收到的消息数据长度
   if (rxheader.RTR == CAN_RTR_DATA) // 如果接收到的消息类型为数据帧
   {
     printf("Received message data: rx[0]=%d, rx[1]=%d\r\n", rxdata[0], rxdata[1]); // 输出接收到的消息数据
   }
 }