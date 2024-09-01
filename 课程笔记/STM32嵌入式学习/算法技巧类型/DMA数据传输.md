```C
unsigned char UartTxBuf[128]; 
void Usart1Printf(const char *format,...)
{
	
	uint16_t len;
	va_list args;	
	va_start(args,format);
	len = vsnprintf((char*)UartTxBuf,sizeof(UartTxBuf)+1,(char*)format,args);
	va_end(args);
	HAL_UART_Transmit_DMA(&huart1, UartTxBuf, len);
}
```