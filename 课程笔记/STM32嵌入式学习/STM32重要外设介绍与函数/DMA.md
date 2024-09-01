# DMA 工作模式
正常模式：传输完一个缓冲区的数据后便停止，若需要再传输一次需要重新启动 DMA 传输。

循环模式：循环模式指的是启动缓冲区的数据传输后将循环执行这个 DMA 数据传输任务


传输完成中断 DMA_IT_TC

[STM32的DMA使用详解](https://blog.csdn.net/weixin_44584198/article/details/119351052)