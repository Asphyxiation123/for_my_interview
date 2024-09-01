# usart 2 UDP
STM 32 发送数据格式
```c
  uint16_t data[40];  // 40个整数数据

  // 填充数据数组
  for (int i = 0; i < 40; i++) {
    data[i] = i;
  }

  // 发送数据
  HAL_UART_Transmit(&huart2, (uint8_t*)data, sizeof(data), HAL_MAX_DELAY);
```
Arduino 接收数据格式
```C
void setup() {
  Serial.begin(115200);
}

void loop() {
  uint16_t data[40];  // 40个整数数据

  if (Serial.available() >= sizeof(data)) {
    // 接收数据
    Serial.readBytes((uint8_t*)data, sizeof(data));
    
    // 处理接收到的数据
    for (int i = 0; i < 40; i++) {
      Serial.println(data[i]);
    }
  }
}
```
UDP 配置
![[Pasted image 20231206202236.png]]

# 帧格式定义
```C
#include <ESP32C3.h>

#define FRAME_HEADER 0xABCD
#define FRAME_TAIL 0xEFGH

typedef struct {
    uint32_t data[40];
    uint16_t frameHeader;
    uint16_t frameTail;
} DataPacket;

DataPacket dataPacket;

void setup() {
    Serial.begin(115200);
    while (!Serial) {
        ; // 等待串口连接
    }

    // 初始化数据包
    dataPacket.frameHeader = FRAME_HEADER;
    dataPacket.frameTail = FRAME_TAIL;
}

void loop() {
    if (Serial.available() >= sizeof(DataPacket)) {
        // 读取数据包
        Serial.readBytes((uint8_t *)&dataPacket, sizeof(DataPacket));

        // 判断帧头和帧尾
        if (dataPacket.frameHeader == FRAME_HEADER && dataPacket.frameTail == FRAME_TAIL) {
            // 处理数据
            for (int i = 0; i < 40; i++) {
                Serial.print("Data ");
                Serial.print(i);
                Serial.print(": ");
                Serial.println(dataPacket.data[i]);
            }
        } else {
            Serial.println("Invalid frame header or frame tail");
        }
    }
}

```


```C
#include <stdint.h>

typedef struct {
  uint8_t frame_header;
  uint8_t frame_type;
  uint32_t data[40];
  uint8_t frame_tail;
} DataPacket;

DataPacket packet;

void setup() {
  Serial1.begin(9600);
}

void loop() {
  if (Serial1.available() > 0) {
    uint8_t byte = Serial1.read();

    if (byte == packet.frame_header) {
      // 开始接收数据包
      packet.frame_type = 0;
    } else if (byte == packet.frame_tail) {
      // 结束接收数据包
      break;
    } else {
      // 处理数据
      packet.data[packet.frame_type] = byte;
      packet.frame_type++;
    }
  }

  // 处理数据
  switch (packet.frame_type) {
    case 0:
      // 处理帧类型0的数据
      break;
    case 1:
      // 处理帧类型1的数据
      break;
    // ...
  }

  // 延时，避免过快地接收数据
  delay(10);
}

```

# SPI 中继
```C++
#include <SPI.h>
#include <ESP32C3.h>

// SPI引脚配置
#define SPI_SCK 18
#define SPI_MISO 19
#define SPI_MOSI 23
#define SPI_SS 5

// 初始化SPI
SPIClass spi(SPI_SCK, SPI_MISO, SPI_MOSI, SPI_SS);

void setup() {
  // 初始化ESP32C3
  Serial.begin(115200);
  delay(100);
  ESP32C3.init();

  // 配置SPI
  spi.begin();
  spi.setDataMode(SPI_MODE0);
  spi.setClockDivider(SPI_CLOCK_DIV16);
  spi.setBitOrder(SPI_BIT_ORDER_MSBFIRST);

  // 发送初始化命令
  spi.transfer(0x00);
}

void loop() {
  // 读取SPI数据
  uint8_t data = spi.transfer(0x00);

  // 处理数据
  Serial.print("Received data: 0x");
  Serial.println(data, HEX);

  delay(1000);
}

```

## 代码 2
```C++
#include <SPI.h>
#include <ESP32C3.h>

SPIConfig spiConfig;

void handleData(int data) {
  Serial.println("Received data: " + String(data));
}

void setup() {
  Serial.begin(115200);
  SPI.begin();
  SPI.setClockDivider(SPI_CLOCK_DIV16);
  SPI.setDataMode(SPI_MODE0);
  SPI.setChipSelect(0);
}

void loop() {
  while (true) {
    int data = SPI.transfer(0);
    if (data != -1) {
      handleData(data);
    }
  }
}

```

```
#include <Wire.h>

void setup() {
  Serial.begin(9600);
  Wire.begin();
  Wire.onReceive(receiveEvent); // 设置接收中断处理函数
  Wire.onRequest(requestEvent); // 设置请求中断处理函数
}

void loop() {
  // 你的主程序代码
}

void receiveEvent(int howMany) {
  while (Wire.available()) {
    char c = Wire.read();
    // 处理接收到的数据
  }
}

void requestEvent() {
  // 发送数据
  Wire.write('A');
}

```