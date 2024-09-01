# 1. FreeRTOS 源码概述
## 1.1 源码架构
![[FreeRTOS代码简介.png]]
其中主要涉及2个目录：
- Demo
    - Demo目录下是工程文件，以"芯片和编译器"组合成一个名字
    - 比如：CORTEX_STM32F103_Keil
- Source
    - 根目录下是核心文件，这些文件是通用的
    - portable目录下是移植时需要实现的文件
        - 目录名为：[compiler]/[architecture]
        - 比如：RVDS/ARM_CM3，这表示cortexM3架构在RVDS工具上的移植文件

## 1.2 核心文件

FreeRTOS的最核心文件只有2个：

- FreeRTOS/Source/tasks.c
- FreeRTOS/Source/list.c

其他文件的作用也一起列表如下：

|FreeRTOS/Source/下的文件|作用|
|---|---|
|tasks.c|必需，任务操作|
|list.c|必须，列表|
|queue.c|基本必需，提供队列操作、信号量(semaphore)操作|
|timer.c|可选，software timer|
|event_groups.c|可选，提供event group功能|
|croutine.c|可选，过时了|

## 1.3 移植时涉及的文件

移植FreeRTOS时涉及的文件放在`FreeRTOS/Source/portable/[compiler]/[architecture]`目录下，

比如：RVDS/ARM_CM3，这表示cortexM3架构在RVDS或Keil工具上的移植文件。

里面有2个文件：

- port.c
- portmacro.h

## 1.4 头文件相关

### 1.4.1 头文件目录

FreeRTOS需要3个头文件目录：

- FreeRTOS本身的头文件：FreeRTOS/Source/include
- 移植时用到的头文件：FreeRTOS/Source/portable/[compiler]/[architecture]
- 含有配置文件FreeRTOSConfig.h的目录

### 1.4.2 头文件

列表如下：

|头文件|作用|
|---|---|
|FreeRTOSConfig.h|FreeRTOS的配置文件，比如选择调度算法：configUSE_PREEMPTION  <br>每个demo都必定含有FreeRTOSConfig.h  <br>建议去修改demo中的FreeRTOSConfig.h，而不是从头写一个|
|FreeRTOS.h|使用FreeRTOS API函数时，必须包含此文件。  <br>在FreeRTOS.h之后，再去包含其他头文件，比如：  <br>task.h、queue.h、semphr.h、event_group.h|

## 1 .5 内存管理

文件在`FreeRTOS/Source/portable/MemMang`下，它也是放在`portable`目录下，表示你可以提供自己的函数。

源码中默认提供了5个文件，对应内存管理的5种方法。

参考文章：[FreeRTOS说明书吐血整理【适合新手+入门】在新窗口打开](https://blog.csdn.net/qq_43212092/article/details/104845158)

后续章节会详细讲解。

|文件|优点|缺点|
|---|---|---|
|heap_1.c|分配简单，时间确定|只分配、不回收|
|heap_2.c|动态分配、最佳匹配|碎片、时间不定|
|heap_3.c|调用标准库函数|速度慢、时间不定|
|heap_4.c|相邻空闲内存可合并|可解决碎片问题、时间不定|
|heap_5.c|在heap_4基础上支持分隔的内存块|可解决碎片问题、时间不定|

## 1.6 数据类型和编程规范

### 1.6.1 数据类型

每个移植的版本都含有自己的`portmacro.h`头文件，里面定义了2个数据类型：

- **TickType_t**：
    - FreeRTOS配置了一个周期性的时钟中断：Tick Interrupt
    - 每发生一次中断，中断次数累加，这被称为tick count
    - tick count这个变量的类型就是TickType_t
    - TickType_t可以是16位的，也可以是32位的
    - FreeRTOSConfig.h中定义configUSE_16_BIT_TICKS时，TickType_t就是uint16_t
    - 否则TickType_t就是uint32_t
    - **对于32位架构，建议把TickType_t配置为uint32_t**
- **BaseType_t**：
    - 这是该架构最高效的数据类型
    - 32位架构中，它就是uint32_t
    - 16位架构中，它就是uint16_t
    - 8位架构中，它就是uint8_t
    - BaseType_t通常用作简单的返回值的类型，还有逻辑值，比如`pdTRUE/pdFALSE`
### 1.6.2 变量名
变量名有前缀：

|变量名前缀|含义|
|---|---|
|c|char|
|s|int16_t，short|
|l|int32_t，long|
|x|BaseType_t，  <br>其他非标准的类型：结构体、task handle、queue handle等|
|u|unsigned|
|p|指针|
|uc|uint8_t，unsigned char|
|pc|char 指针|

### 1.6.3 函数名
函数名的前缀有2部分：返回值类型、在哪个文件定义。

|函数名前缀|含义|
|---|---|
|vTaskPrioritySet|返回值类型：void  <br>在task.c中定义|
|xQueueReceive|返回值类型：BaseType_t  <br>在queue.c中定义|
|pvTimerGetTimerID|返回值类型：pointer to void  <br>在tmer.c中定义|

### 1.6.4 宏的名
宏的名字是大小，可以添加小写的前缀。前缀是用来表示：宏在哪个文件中定义。

|宏的前缀|含义：在哪个文件里定义|
|---|---|
|port (比如portMAX_DELAY)|portable.h或portmacro.h|
|task (比如taskENTER_CRITICAL())|task.h|
|pd (比如pdTRUE)|projdefs.h|
|config (比如configUSE_PREEMPTION)|FreeRTOSConfig.h|
|err (比如errQUEUE_FULL)|projdefs.h|

通用的宏定义如下：

|宏|值|
|---|---|
|pdTRUE|1|
|pdFALSE|0|
|pdPASS|1|
|pdFAIL|0|

# 2 内存管理


# 3 任务管理

在本章中，会涉及如下内容：

- FreeRTOS如何给每个任务分配CPU时间
- 如何选择某个任务来运行
- 任务优先级如何起作用
- 任务有哪些状态
- 如何实现任务
- 如何使用任务参数
- 怎么修改任务优先级
- 怎么删除任务
- 怎么实现周期性的任务
- 如何使用空闲任务

## 3.1 基本概念
对于整个单片机程序，我们称之为application，应用程序。
使用FreeRTOS时，我们可以在application中创建多个任务(task)，有些文档把任务也称为线程(thread)。

![](http://photos.100ask.net/rtos-docs/FreeRTOS/simulator/chapter-3/01_mother_do_jobs.png)

以日常生活为例，比如这个母亲要同时做两件事：

- 喂饭：这是一个任务
- 回信息：这是另一个任务

这可以引入很多概念：

- 任务状态(State)：
    - 当前正在喂饭，它是running状态；另一个"回信息"的任务就是"not running"状态
    - "not running"状态还可以细分：
        - ready：就绪，随时可以运行
        - blocked：阻塞，卡住了，母亲在等待同事回信息
        - suspended：挂起，同事废话太多，不管他了
- 优先级(Priority)
    - 我工作生活兼顾：喂饭、回信息优先级一样，轮流做
    - 我忙里偷闲：还有空闲任务，休息一下
    - 厨房着火了，什么都别说了，先灭火：优先级更高
- 栈(Stack)
    - 喂小孩时，我要记得上一口喂了米饭，这口要喂青菜了
    - 回信息时，我要记得刚才聊的是啥
    - 做不同的任务，这些细节不一样
    - 对于人来说，当然是记在脑子里
    - 对于程序，是记在栈里
    - 每个任务有自己的栈
- 事件驱动
    - 孩子吃饭太慢：先休息一会，等他咽下去了、等他提醒我了，再喂下一口
- 协助式调度(Co-operative Scheduling)
    - 你在给同事回信息
        - 同事说：好了，你先去给小孩喂一口饭吧，你才能离开
        - 同事不放你走，即使孩子哭了你也不能走
    - 你好不容易可以给孩子喂饭了
        - 孩子说：好了，妈妈你去处理一下工作吧，你才能离开
        - 孩子不放你走，即使同事连发信息你也不能走-第三章任务管理

## 3.2 任务创建与删除

### 3.2.1 什么是任务

在FreeRTOS中，任务就是一个函数，原型如下：
```C
void ATaskFunction( void *pvParameters );
```
要注意的是：

- 这个函数不能返回
- 同一个函数，可以用来创建多个任务；换句话说，多个任务可以运行同一个函数
- 函数内部，尽量使用局部变量：
    - 每个任务都有自己的栈
    - 每个任务运行这个函数时
        - 任务A的局部变量放在任务A的栈里、任务B的局部变量放在任务B的栈里
        - 不同任务的局部变量，有自己的副本
    - 函数使用全局变量、静态变量的话
        - 只有一个副本：多个任务使用的是同一个副本
        - 要防止冲突(后续会讲)

下面是一个示例：

```C
void ATaskFunction( void *pvParameters )
{
	/* 对于不同的任务，局部变量放在任务的栈里，有各自的副本 */
	int32_t lVariableExample = 0;
	
    /* 任务函数通常实现为一个无限循环 */
	for( ;; )
	{
		/* 任务的代码 */
	}

    /* 如果程序从循环中退出，一定要使用vTaskDelete删除自己
     * NULL表示删除的是自己
     */
	vTaskDelete( NULL );
    
    /* 程序不会执行到这里, 如果执行到这里就出错了 */
}
```

### 3.2.2 创建任务

创建任务时使用的函数如下：

```C
BaseType_t xTaskCreate( 
		TaskFunction_t pxTaskCode, // 函数指针, 任务函数
        const char * const pcName, // 任务的名字
        const configSTACK_DEPTH_TYPE usStackDepth, // 栈大小,单位为word,10表示40字节
        void * const pvParameters, // 调用任务函数时传入的参数
        UBaseType_t uxPriority,    // 优先级
        TaskHandle_t * const pxCreatedTask ); // 任务句柄, 以后使用它来操作这个任务
```
参数说明：

|参数|描述|
|---|---|
|pvTaskCode|函数指针，可以简单地认为任务就是一个C函数。  <br>它稍微特殊一点：永远不退出，或者退出时要调用"vTaskDelete(NULL)"|
|pcName|任务的名字，FreeRTOS内部不使用它，仅仅起调试作用。  <br>长度为：configMAX_TASK_NAME_LEN|
|usStackDepth|每个任务都有自己的栈，这里指定栈大小。  <br>单位是word，比如传入100，表示栈大小为100 word，也就是400字节。  <br>最大值为uint16_t的最大值。  <br>怎么确定栈的大小，并不容易，很多时候是估计。  <br>精确的办法是看反汇编码。|
|pvParameters|调用pvTaskCode函数指针时用到：pvTaskCode(pvParameters)|
|uxPriority|优先级范围：0~(configMAX_PRIORITIES – 1)  <br>数值越小优先级越低，  <br>如果传入过大的值，xTaskCreate会把它调整为(configMAX_PRIORITIES – 1)|
|pxCreatedTask|用来保存xTaskCreate的输出结果：task handle。  <br>以后如果想操作这个任务，比如修改它的优先级，就需要这个handle。  <br>如果不想使用该handle，可以传入NULL。|
|返回值|成功：pdPASS；  <br>失败：errCOULD_NOT_ALLOCATE_REQUIRED_MEMORY(失败原因只有内存不足)  <br>注意：文档里都说失败时返回值是pdFAIL，这不对。  <br>pdFAIL是0，errCOULD_NOT_ALLOCATE_REQUIRED_MEMORY是-1。|

### 3.2.3 示例1: 创建任务

代码为：`FreeRTOS_01_create_task`

使用2个函数分别创建2个任务。

任务1的代码：

```C
void vTask1( void *pvParameters )
{
	const char *pcTaskName = "T1 run\r\n";
	volatile uint32_t ul; /* volatile用来避免被优化掉 */
	
	/* 任务函数的主体一般都是无限循环 */
	for( ;; )
	{
		/* 打印任务1的信息 */
		printf( pcTaskName );
		
		/* 延迟一会(比较简单粗暴) */
		for( ul = 0; ul < mainDELAY_LOOP_COUNT; ul++ )
		{
		}
	}
}
```

任务2的代码：

```C
void vTask2( void *pvParameters )
{
	const char *pcTaskName = "T2 run\r\n";
	volatile uint32_t ul; /* volatile用来避免被优化掉 */
	
	/* 任务函数的主体一般都是无限循环 */
	for( ;; )
	{
		/* 打印任务1的信息 */
		printf( pcTaskName );
		
		/* 延迟一会(比较简单粗暴) */
		for( ul = 0; ul < mainDELAY_LOOP_COUNT; ul++ )
		{
		}
	}
}
```

main函数：
```C
int main( void )
{
	prvSetupHardware();
	
	xTaskCreate(vTask1, "Task 1", 1000, NULL, 1, NULL);
	xTaskCreate(vTask2, "Task 2", 1000, NULL, 1, NULL);

	/* 启动调度器 */
	vTaskStartScheduler();

	/* 如果程序运行到了这里就表示出错了, 一般是内存不足 */
	return 0;
}
```

运行结果如下：

![image-20210729170906116](http://photos.100ask.net/rtos-docs/FreeRTOS/simulator/chapter-3/04_create_two_task.png)

注意：

- task 2先运行！
- 要分析xTaskCreate的代码才能知道原因：更高优先级的、或者后面创建的任务先运行。

任务运行图：

- 在t1：Task2进入运行态，一直运行直到t2
- 在t2：Task1进入运行态，一直运行直到t3；在t3，Task2重新进入运行态

![image-20210729172213224](http://photos.100ask.net/rtos-docs/FreeRTOS/simulator/chapter-3/05_task_schedul.png)

### 3.2.4 示例2: 使用任务参数

代码为：`FreeRTOS_02_create_task_use_params`

我们说过，多个任务可以使用同一个函数，怎么体现它们的差别？

- 栈不同
- 创建任务时可以传入不同的参数

我们创建2个任务，使用同一个函数，代码如下：

```C
void vTaskFunction( void *pvParameters )
{
	const char *pcTaskText = pvParameters;
	volatile uint32_t ul; /* volatile用来避免被优化掉 */
	
	/* 任务函数的主体一般都是无限循环 */
	for( ;; )
	{
		/* 打印任务的信息 */
		printf(pcTaskText);
		
		/* 延迟一会(比较简单粗暴) */
		for( ul = 0; ul < mainDELAY_LOOP_COUNT; ul++ )
		{
		}
	}
}
```

上述代码中的`pcTaskText`来自参数`pvParameters`，`pvParameters`来自哪里？创建任务时传入的。

代码如下：

- 使用xTaskCreate创建2个任务时，第4个参数就是pvParameters
- 不同的任务，pvParameters不一样

```C
static const char *pcTextForTask1 = "T1 run\r\n";
static const char *pcTextForTask2 = "T2 run\r\n";

int main( void )
{
	prvSetupHardware();
	
	xTaskCreate(vTaskFunction, "Task 1", 1000, (void *)pcTextForTask1, 1, NULL);
	xTaskCreate(vTaskFunction, "Task 2", 1000, (void *)pcTextForTask2, 1, NULL);

	/* 启动调度器 */
	vTaskStartScheduler();

	/* 如果程序运行到了这里就表示出错了, 一般是内存不足 */
	return 0;
}
```

### 3.2.5 任务的删除

删除任务时使用的函数如下：

```C
void vTaskDelete( TaskHandle_t xTaskToDelete );
```

参数说明：

|参数|描述|
|---|---|
|pvTaskCode|任务句柄，使用xTaskCreate创建任务时可以得到一个句柄。  <br>也可传入NULL，这表示删除自己。|

怎么删除任务？举个不好的例子：

- 自杀：`vTaskDelete(NULL)`
- 被杀：别的任务执行`vTaskDelete(pvTaskCode)`，pvTaskCode是自己的句柄
- 杀人：执行`vTaskDelete(pvTaskCode)`，pvTaskCode是别的任务的句柄

### 3.2.6 示例3: 删除任务

代码为：`FreeRTOS_03_delete_task`

本节代码会涉及优先级的知识，可以只看vTaskDelete的用法，忽略优先级的讲解。

我们要做这些事情：

- 创建任务1：任务1的大循环里，创建任务2，然后休眠一段时间
- 任务2：打印一句话，然后就删除自己

任务1的代码如下：

```C
void vTask1( void *pvParameters )
{
	const TickType_t xDelay100ms = pdMS_TO_TICKS( 100UL );		
	BaseType_t ret;
	
	/* 任务函数的主体一般都是无限循环 */
	for( ;; )
	{
		/* 打印任务的信息 */
		printf("Task1 is running\r\n");
		
		ret = xTaskCreate( vTask2, "Task 2", 1000, NULL, 2, &xTask2Handle );
		if (ret != pdPASS)
			printf("Create Task2 Failed\r\n");
		
		// 如果不休眠的话, Idle任务无法得到执行
		// Idel任务会清理任务2使用的内存
		// 如果不休眠则Idle任务无法执行, 最后内存耗尽
		vTaskDelay( xDelay100ms );
	}
```

任务2的代码如下：

```C
void vTask2( void *pvParameters )
{	
	/* 打印任务的信息 */
	printf("Task2 is running and about to delete itself\r\n");

	// 可以直接传入参数NULL, 这里只是为了演示函数用法
	vTaskDelete(xTask2Handle);
}
```

main函数代码如下：

```C
int main( void )
{
	prvSetupHardware();
	
	xTaskCreate(vTask1, "Task 1", 1000, NULL, 1, NULL);

	/* 启动调度器 */
	vTaskStartScheduler();

	/* 如果程序运行到了这里就表示出错了, 一般是内存不足 */
	return 0;
}
```

运行结果如下：

![image-20210731110531625](http://photos.100ask.net/rtos-docs/FreeRTOS/simulator/chapter-3/04_delete_task.png)

任务运行图：

- main函数中创建任务1，优先级为1。任务1运行时，它创建任务2，任务2的优先级是2。
- 任务2的优先级最高，它马上执行。
- 任务2打印一句话后，就删除了自己。
- 任务2被删除后，任务1的优先级最高，轮到任务1继续运行，它调用`vTaskDelay()` 进入Block状态
- 任务1 Block期间，轮到Idle任务执行：它释放任务2的内存(TCB、栈)
- 时间到后，任务1变为最高优先级的任务继续执行。
- 如此循环。

![image-20210731111929008](http://photos.100ask.net/rtos-docs/FreeRTOS/simulator/chapter-3/06_task_schedul_for_delete.png)

在任务1的函数中，如果不调用vTaskDelay，则Idle任务用于没有机会执行，它就无法释放创建任务2是分配的内存。

而任务1在不断地创建任务，不断地消耗内存，最终内存耗尽再也无法创建新的任务。

现象如下：

![image-20210731112826679](http://photos.100ask.net/rtos-docs/FreeRTOS/simulator/chapter-3/07_create_task_fail.png)

任务1的代码中，需要注意的是：xTaskCreate的返回值。

- 很多手册里说它失败时返回值是pdFAIL，这个宏是0
- 其实失败时返回值是errCOULD_NOT_ALLOCATE_REQUIRED_MEMORY，这个宏是-1
- 为了避免混淆，我们使用返回值跟pdPASS来比较，这个宏是1

## [#](https://rtos.100ask.net/zh/FreeRTOS/simulator/chapter3.html#_3-3-%E4%BB%BB%E5%8A%A1%E4%BC%98%E5%85%88%E7%BA%A7%E5%92%8Ctick)3.3 任务优先级和Tick

### [#](https://rtos.100ask.net/zh/FreeRTOS/simulator/chapter3.html#_3-3-1-%E4%BB%BB%E5%8A%A1%E4%BC%98%E5%85%88%E7%BA%A7)3.3.1 任务优先级

在上个示例中我们体验过优先级的使用：高优先级的任务先运行。

优先级的取值范围是：0~(configMAX_PRIORITIES – 1)，数值越大优先级越高。

FreeRTOS的调度器可以使用2种方法来快速找出优先级最高的、可以运行的任务。使用不同的方法时，configMAX_PRIORITIES 的取值有所不同。

- 通用方法 使用C函数实现，对所有的架构都是同样的代码。对configMAX_PRIORITIES的取值没有限制。但是configMAX_PRIORITIES的取值还是尽量小，因为取值越大越浪费内存，也浪费时间。 configUSE_PORT_OPTIMISED_TASK_SELECTION被定义为0、或者未定义时，使用此方法。
- 架构相关的优化的方法 架构相关的汇编指令，可以从一个32位的数里快速地找出为1的最高位。使用这些指令，可以快速找出优先级最高的、可以运行的任务。 使用这种方法时，configMAX_PRIORITIES的取值不能超过32。 configUSE_PORT_OPTIMISED_TASK_SELECTION被定义为1时，使用此方法。

在学习调度方法之前，你只要初略地知道：

- FreeRTOS会确保最高优先级的、可运行的任务，马上就能执行
- 对于相同优先级的、可运行的任务，轮流执行

这无需记忆，就像我们举的例子：

- 厨房着火了，当然优先灭火
- 喂饭、回复信息同样重要，轮流做

### [#](https://rtos.100ask.net/zh/FreeRTOS/simulator/chapter3.html#_3-3-2-tick)3.3.2 Tick

对于同优先级的任务，它们“轮流”执行。怎么轮流？你执行一会，我执行一会。

"一会"怎么定义？

人有心跳，心跳间隔基本恒定。

FreeRTOS中也有心跳，它使用定时器产生固定间隔的中断。这叫Tick、滴答，比如每10ms发生一次时钟中断。

如下图：

- 假设t1、t2、t3发生时钟中断
- 两次中断之间的时间被称为时间片(time slice、tick period)
- 时间片的长度由configTICK_RATE_HZ 决定，假设configTICK_RATE_HZ为100，那么时间片长度就是10ms

![image-20210731130348561](http://photos.100ask.net/rtos-docs/FreeRTOS/simulator/chapter-3/08_time_tick.png)

相同优先级的任务怎么切换呢？请看下图：

- 任务2从t1执行到t2
- 在t2发生tick中断，进入tick中断处理函数：
    - 选择下一个要运行的任务
    - 执行完中断处理函数后，切换到新的任务：任务1
- 任务1从t2执行到t3
- 从下图中可以看出，任务运行的时间并不是严格从t1,t2,t3哪里开始

![image-20210731130720669](http://photos.100ask.net/rtos-docs/FreeRTOS/simulator/chapter-3/09_tick_interrtups.png)

有了Tick的概念后，我们就可以使用Tick来衡量时间了，比如：

```
vTaskDelay(2);  // 等待2个Tick，假设configTICK_RATE_HZ=100, Tick周期时10ms, 等待20ms

// 还可以使用pdMS_TO_TICKS宏把ms转换为tick
vTaskDelay(pdMS_TO_TICKS(100));	 // 等待100ms
```

注意，基于Tick实现的延时并不精确，比如`vTaskDelay(2)`的本意是延迟2个Tick周期，有可能经过1个Tick多一点就返回了。

如下图：

![image-20210731133559155](http://photos.100ask.net/rtos-docs/FreeRTOS/simulator/chapter-3/10_taskdelay.png)

使用vTaskDelay函数时，建议以ms为单位，使用pdMS_TO_TICKS把时间转换为Tick。

这样的代码就与configTICK_RATE_HZ无关，即使配置项configTICK_RATE_HZ改变了，我们也不用去修改代码。

### [#](https://rtos.100ask.net/zh/FreeRTOS/simulator/chapter3.html#_3-3-3-%E7%A4%BA%E4%BE%8B4-%E4%BC%98%E5%85%88%E7%BA%A7%E5%AE%9E%E9%AA%8C)3.3.3 示例4: 优先级实验

代码为：`FreeRTOS_04_task_priority`

本程序会创建3个任务：

- 任务1、任务2：优先级相同，都是1
- 任务3：优先级最高，是2

任务1、2代码如下：

```
void vTask1( void *pvParameters )
{
	/* 任务函数的主体一般都是无限循环 */
	for( ;; )
	{
		/* 打印任务的信息 */
		printf("T1\r\n");				
	}
}

void vTask2( void *pvParameters )
{	
	/* 任务函数的主体一般都是无限循环 */
	for( ;; )
	{
		/* 打印任务的信息 */
		printf("T2\r\n");				
	}
}
```

任务3代码如下：

```
void vTask3( void *pvParameters )
{	
	const TickType_t xDelay3000ms = pdMS_TO_TICKS( 3000UL );		
	
	/* 任务函数的主体一般都是无限循环 */
	for( ;; )
	{
		/* 打印任务的信息 */
		printf("T3\r\n");				

		// 如果不休眠的话, 其他任务无法得到执行
		vTaskDelay( xDelay3000ms );
	}
}
```

main函数代码如下：

```
{
	prvSetupHardware();
	
	xTaskCreate(vTask1, "Task 1", 1000, NULL, 1, NULL);
	xTaskCreate(vTask2, "Task 2", 1000, NULL, 1, NULL);
	xTaskCreate(vTask3, "Task 3", 1000, NULL, 2, NULL);

	/* 启动调度器 */
	vTaskStartScheduler();

	/* 如果程序运行到了这里就表示出错了, 一般是内存不足 */
	return 0;
}
```

运行情况如下图所示：

- 任务3优先执行，直到它调用vTaskDelay主动放弃运行
- 任务1、任务2：轮流执行

![image-20210731140405148](http://photos.100ask.net/rtos-docs/FreeRTOS/simulator/chapter-3/11_priority_result.png)

调度情况如下图所示：

![image-20210731143751639](http://photos.100ask.net/rtos-docs/FreeRTOS/simulator/chapter-3/12_priority_scheduler.png)

### [#](https://rtos.100ask.net/zh/FreeRTOS/simulator/chapter3.html#_3-3-4-%E7%A4%BA%E4%BE%8B5-%E4%BF%AE%E6%94%B9%E4%BC%98%E5%85%88%E7%BA%A7)3.3.4 示例5: 修改优先级

本节代码为：`FreeRTOS_05_change_priority`。

使用uxTaskPriorityGet来获得任务的优先级：

```
UBaseType_t uxTaskPriorityGet( const TaskHandle_t xTask );
```

使用参数xTask来指定任务，设置为NULL表示获取自己的优先级。

使用vTaskPrioritySet 来设置任务的优先级：

```
void vTaskPrioritySet( TaskHandle_t xTask,
                       UBaseType_t uxNewPriority );
```

使用参数xTask来指定任务，设置为NULL表示设置自己的优先级； 参数uxNewPriority表示新的优先级，取值范围是0~(configMAX_PRIORITIES – 1)。

main函数的代码如下，它创建了2个任务：任务1的优先级更高，它先执行：

```
int main( void )
{
	prvSetupHardware();
	
	/* Task1的优先级更高, Task1先执行 */
	xTaskCreate( vTask1, "Task 1", 1000, NULL, 2, NULL );
	xTaskCreate( vTask2, "Task 2", 1000, NULL, 1, &xTask2Handle );

	/* 启动调度器 */
	vTaskStartScheduler();

	/* 如果程序运行到了这里就表示出错了, 一般是内存不足 */
	return 0;
}
```

任务1的代码如下：

```
void vTask1( void *pvParameters )
{
	UBaseType_t uxPriority;
	
	/* Task1,Task2都不会进入阻塞或者暂停状态
	 * 根据优先级决定谁能运行
	 */
	
	/* 得到Task1自己的优先级 */
	uxPriority = uxTaskPriorityGet( NULL );
	
	for( ;; )
	{
		printf( "Task 1 is running\r\n" );

		printf("About to raise the Task 2 priority\r\n" );
		
		/* 提升Task2的优先级高于Task1
		 * Task2会即刻执行
 		 */
		vTaskPrioritySet( xTask2Handle, ( uxPriority + 1 ) );
		
		/* 如果Task1能运行到这里，表示它的优先级比Task2高
		* 那就表示Task2肯定把自己的优先级降低了
 		 */
	}
}
```

任务2的代码如下：

```
void vTask2( void *pvParameters )
{
	UBaseType_t uxPriority;

	/* Task1,Task2都不会进入阻塞或者暂停状态
	 * 根据优先级决定谁能运行
	 */
	
	/* 得到Task2自己的优先级 */
	uxPriority = uxTaskPriorityGet( NULL );
	
	for( ;; )
	{
		/* 能运行到这里表示Task2的优先级高于Task1
		 * Task1提高了Task2的优先级
		 */
		printf( "Task 2 is running\r\n" );
		
		printf( "About to lower the Task 2 priority\r\n" );

		/* 降低Task2自己的优先级，让它小于Task1
		 * Task1得以运行
 		 */
		vTaskPrioritySet( NULL, ( uxPriority - 2 ) );
	}
}
```

调度情况如下图所示：

- 1：一开始Task1优先级最高，它先执行。它提升了Task2的优先级。
- 2：Task2的优先级最高，它执行。它把自己的优先级降低了。
- 3：Task1的优先级最高，再次执行。它提升了Task2的优先级。
- 如此循环。
- 注意：Task1的优先级一直是2，Task2的优先级是3或1，都大于0。所以Idel任务没有机会执行。

![image-20210731220350206](http://photos.100ask.net/rtos-docs/FreeRTOS/simulator/chapter-3/15_change_priority.png)

## [#](https://rtos.100ask.net/zh/FreeRTOS/simulator/chapter3.html#_3-4-%E4%BB%BB%E5%8A%A1%E7%8A%B6%E6%80%81)3.4 任务状态

以前我们很简单地把任务的状态分为2中：运行(Runing)、非运行(Not Running)。

对于非运行的状态，还可以继续细分，比如前面的`FreeRTOS_04_task_priority`中：

- Task3执行vTaskDelay后：处于非运行状态，要过3秒种才能再次运行
- Task3运行期间，Task1、Task2也处于非运行状态，但是它们**随时可以运行**
- 这两种"非运行"状态就不一样，可以细分为：
    - 阻塞状态(Blocked)
    - 暂停状态(Suspended)
    - 就绪状态(Ready)

### 3.4.1 阻塞状态(Blocked)

在日常生活的例子中，母亲在电脑前跟同事沟通时，如果同事一直没回复，那么母亲的工作就被卡住了、被堵住了、处于阻塞状态(Blocked)。重点在于：母亲在**等待**。

在`FreeRTOS_04_task_priority`实验中，如果把任务3中的vTaskDelay调用注释掉，那么任务1、任务2根本没有执行的机会，任务1、任务2被"饿死"了(starve)。

在实际产品中，我们不会让一个任务一直运行，而是使用"事件驱动"的方法让它运行：

- 任务要等待某个事件，事件发生后它才能运行
- 在等待事件过程中，它不消耗CPU资源
- 在等待事件的过程中，这个任务就处于阻塞状态(Blocked)

在阻塞状态的任务，它可以等待两种类型的事件：

- 时间相关的事件
    - 可以等待一段时间：我等2分钟
    - 也可以一直等待，直到某个绝对时间：我等到下午3点
- 同步事件：这事件由别的任 务，或者是中断程序产生
    - 例子1：任务A等待任务B给它发送数据
    - 例子2：任务A等待用户按下按键
    - 同步事件的来源有很多(这些概念在后面会细讲)：
        - 队列(queue)
        - 二进制信号量(binary semaphores)
        - 计数信号量(counting semaphores)
        - 互斥量(mutexes)
        - 递归互斥量、递归锁(recursive mutexes)
        - 事件组(event groups)
        - 任务通知(task notifications)

在等待一个同步事件时，可以加上超时时间。比如等待队里数据，超时时间设为10ms：

- 10ms之内有数据到来：成功返回
- 10ms到了，还是没有数据：超时返回

### 3.4.2 暂停状态(Suspended)

在日常生活的例子中，母亲正在电脑前跟同事沟通，母亲可以暂停：

- 好烦啊，我暂停一会
- 领导说：你暂停一下

FreeRTOS中的任务也可以进入暂停状态，唯一的方法是通过vTaskSuspend函数。函数原型如下：

```C
void vTaskSuspend( TaskHandle_t xTaskToSuspend );
```

参数xTaskToSuspend表示要暂停的任务，如果为NULL，表示暂停自己。

要退出暂停状态，只能由**别人**来操作：

- 别的任务调用：vTaskResume
- 中断程序调用：xTaskResumeFromISR
 
实际开发中，暂停状态用得不多。

### 3.4.3 就绪状态(Ready)

这个任务完全准备好了，随时可以运行：只是还轮不到它。这时，它就处于就绪态(Ready)。

### 3.4.4 完整的状态转换图

![image-20210731155223985](http://photos.100ask.net/rtos-docs/FreeRTOS/simulator/chapter-3/13_full_task_state_machine.png)

## [#](https://rtos.100ask.net/zh/FreeRTOS/simulator/chapter3.html#_3-5-delay%E5%87%BD%E6%95%B0)3.5 Delay函数

### [#](https://rtos.100ask.net/zh/FreeRTOS/simulator/chapter3.html#_3-5-1-%E4%B8%A4%E4%B8%AAdelay%E5%87%BD%E6%95%B0)3.5.1 两个Delay函数

`FreeRTOS` 有两个 Delay 函数：
- vTaskDelay：至少等待指定个数的Tick Interrupt才能变为就绪状态
- vTaskDelayUntil：等待到指定的绝对时刻，才能变为就绪态。
用于将任务从阻塞态切换为就绪态

这2个函数原型如下：

```C
void vTaskDelay( const TickType_t xTicksToDelay ); /* xTicksToDelay: 等待多少给Tick */

/* pxPreviousWakeTime: 上一次被唤醒的时间
 * xTimeIncrement: 要阻塞到(pxPreviousWakeTime + xTimeIncrement)
 * 单位都是Tick Count
 */
BaseType_t xTaskDelayUntil( TickType_t * const pxPreviousWakeTime,
                            const TickType_t xTimeIncrement );
```

下面画图说明：

- 使用 vTaskDelay(n)时，进入、退出 vTaskDelay 的时间间隔至少是 n 个 Tick 中断

一般会使用 `xTaskGetTickCount()` 函数来获取当前时间戳
- 使用xTaskDelayUntil(&Pre, n)时，前后两次退出xTaskDelayUntil的时间至少是n个Tick中断
    - 退出xTaskDelayUntil时任务就进入的就绪状态，一般都能得到执行机会
    - 所以可以使用xTaskDelayUntil来让任务周期性地运行

![image-20210731205236939](http://photos.100ask.net/rtos-docs/FreeRTOS/simulator/chapter-3/14_delay_functions.png)

### 3.5.2 示例6: Delay
 
本节代码为：`FreeRTOS_06_taskdelay`。

本程序会创建2个任务：

- Task1：
    - 高优先级
    - 设置变量flag为1，然后调用`vTaskDelay(xDelay50ms);`或`vTaskDelayUntil(&xLastWakeTime, xDelay50ms);`
- Task2：
    - 低优先级
    - 设置变量flag为0

main函数代码如下：

```C
int main( void )
{
	prvSetupHardware();
	
	/* Task1的优先级更高, Task1先执行 */
	xTaskCreate( vTask1, "Task 1", 1000, NULL, 2, NULL );
	xTaskCreate( vTask2, "Task 2", 1000, NULL, 1, NULL );

	/* 启动调度器 */
	vTaskStartScheduler();

	/* 如果程序运行到了这里就表示出错了, 一般是内存不足 */
	return 0;
}
```

Task1的代码中使用条件开关来选择Delay函数，把`#if 1`改为`#if 0`就可以使用`vTaskDelayUntil`，代码如下：

```C
void vTask1( void *pvParameters )
{
	const TickType_t xDelay50ms = pdMS_TO_TICKS( 50UL );
	TickType_t xLastWakeTime;
	int i;
	
	/* 获得当前的Tick Count */
	xLastWakeTime = xTaskGetTickCount();
			
	for( ;; )
	{
		flag = 1;
		
		/* 故意加入多个循环，让程序运行时间长一点 */
		for (i = 0; i <5; i++)
			printf( "Task 1 is running\r\n" );

#if 1		
		vTaskDelay(xDelay50ms);
#else		
		vTaskDelayUntil(&xLastWakeTime, xDelay50ms);
#endif		
	}
}
```

Task2的代码如下：

```C
void vTask2( void *pvParameters )
{
	for( ;; )
	{
		flag = 0;
		printf( "Task 2 is running\r\n" );
	}
}
```

使用Keil的逻辑分析观察flag变量的bit波形，如下：

- flag为1时表示Task1在运行，flag为0时表示Task2在运行，也就是Task1处于阻塞状态
- vTaskDelay：指定的是阻塞的时间
- vTaskDelayUntil：指定的是任务执行的间隔、周期

![image-20210731233309265](http://photos.100ask.net/rtos-docs/FreeRTOS/simulator/chapter-3/16_delay_time.png)

## 9.6 空闲任务及其钩子函数

### 9.6.1 介绍

空闲任务(Idle任务)的作用之一：**释放被删除的任务的内存**。

除了上述目的之外，为什么必须要有空闲任务？一个良好的程序，它的任务都是事件驱动的：平时大部分时间处于阻塞状态。有可能我们自己创建的所有任务都无法执行，但是调度器必须能找到一个可以运行的任务：所以，我们要提供空闲任务。在使用`vTaskStartScheduler()`函数来创建、启动调度器时，这个函数内部会创建空闲任务：

- 空闲任务优先级为0：它不能阻碍用户任务运行
- 空闲任务要么处于就绪态，要么处于运行态，永远不会阻塞

空闲任务的优先级为0，这意味着一旦某个用户的任务变为就绪态，那么空闲任务马上被切换出去，让这个用户任务运行。在这种情况下，我们说用户任务"抢占"(pre-empt)了空闲任务，这是由调度器实现的。

*一个任务运行完要想退出，只能自杀或者他杀，如果程序正常运行完返回，则会进入到空闲函数中，kill 掉所有任务*
要注意的是：如果使用 `vTaskDelete()`来删除任务，那么你就要确保空闲任务有机会执行，否则就无法释放被删除任务的内存。


我们可以添加一个空闲任务的钩子函数(Idle Task Hook Functions)，空闲任务的循环每执行一次，就会调用一次钩子函数。钩子函数的作用有这些：

- 执行一些低优先级的、后台的、需要连续执行的函数
- 测量系统的空闲时间：空闲任务能被执行就意味着所有的高优先级任务都停止了，所以测量空闲任务占据的时间，就可以算出处理器占用率。
- 让系统进入省电模式：空闲任务能被执行就意味着没有重要的事情要做，当然可以进入省电模式了。
- 空闲任务的钩子函数的限制：
- 不能导致空闲任务进入阻塞状态、暂停状态
- 如果你会使用`vTaskDelete()`来删除任务，那么钩子函数要非常高效地执行。如果空闲任务移植卡在钩子函数里的话，它就无法释放内存。

### 9.6.2 使用钩子函数的前提

在FreeRTOS\Source\tasks.c中，可以看到如下代码，所以前提就是：

- 把这个宏定义为1：configUSE_IDLE_HOOK
- 实现vApplicationIdleHook函数

![](http://photos.100ask.net/rtos-docs/FreeRTOS/DShanMCU-F103/chapter-9/image16.png)

## 9.7 调度算法

### 9.7.1 重要概念

这些知识在前面都提到过了，这里总结一下。

正在运行的任务，被称为"正在使用处理器"，它处于运行状态。在单处理系统中，任何时间里只能有一个任务处于运行状态。

非运行状态的任务，它处于这3中状态之一：阻塞(Blocked)、暂停(Suspended)、就绪(Ready)。就绪态的任务，可以被调度器挑选出来切换为运行状态，调度器永远都是挑选最高优先级的就绪态任务并让它进入运行状态。

阻塞状态的任务，它在等待"事件"，当事件发生时任务就会进入就绪状态。事件分为两类：时间相关的事件、同步事件。所谓时间相关的事件，就是设置超时时间：在指定时间内阻塞，时间到了就进入就绪状态。使用时间相关的事件，可以实现周期性的功能、可以实现超时功能。同步事件就是：某个任务在等待某些信息，别的任务或者中断服务程序会给它发送信息。怎么"发送信息"？方法很多，有：任务通知(task notification)、队列(queue)、事件组(event group)、信号量(semaphoe)、互斥量(mutex)等。这些方法用来发送同步信息，比如表示某个外设得到了数据。
#### 任务调度算法原理
创建大小56  `tasklist` 数组，数组 `tasklist[n]` 中存放的是对应优先级的所有处于就绪态和运行态的任务，任务之间通过链表相连。RTOS 启动时，最后创建的任务先运行

### 9.7.2 配置调度算法

所谓调度算法，就是怎么确定哪个就绪态的任务可以切换为运行状态。

通过配置文件FreeRTOSConfig.h的两个配置项来配置调度算法：configUSE_PREEMPTION、configUSE_TIME_SLICING。

还有第三个配置项：configUSE_TICKLESS_IDLE，它是一个高级选项，用于关闭Tick中断来实现省电，后续单独讲解。现在我们假设configUSE_TICKLESS_IDLE被设为0，先不使用这个功能。 调度算法的行为主要体现在两方面：高优先级的任务先运行、同优先级的就绪态任务如何被选中。调度算法要确保同优先级的就绪态任务，能"轮流"运行，策略是"轮转调度"(Round Robin Scheduling)。轮转调度并不保证任务的运行时间是公平分配的，我们还可以细化时间的分配方法。 从3个角度统一理解多种调度算法：

- 可否抢占？高优先级的任务能否优先执行(配置项: configUSE_PREEMPTION)
    - 可以：被称作"可抢占调度"(Pre-emptive)，高优先级的就绪任务马上执行，下面再细化。
    - 不可以：不能抢就只能协商了，被称作"合作调度模式"(Co-operative Scheduling)
        - 当前任务执行时，更高优先级的任务就绪了也不能马上运行，只能等待当前任务主动让出CPU资源。
        - 其他同优先级的任务也只能等待：更高优先级的任务都不能抢占，平级的更应该老实点
- 可抢占的前提下，同优先级的任务是否轮流执行(配置项：configUSE_TIME_SLICING)
    - 轮流执行：被称为"时间片轮转"(Time Slicing)，同优先级的任务轮流执行，你执行一个时间片、我再执行一个时间片
    - 不轮流执行：英文为"without Time Slicing"，当前任务会一直执行，直到主动放弃、或者被高优先级任务抢占
- 在"可抢占"+"时间片轮转"的前提下，进一步细化：空闲任务是否让步于用户任务(配置项：configIDLE_SHOULD_YIELD)
    - 空闲任务低人一等，每执行一次循环，就看看是否主动让位给用户任务
    - 空闲任务跟用户任务一样，大家轮流执行，没有谁更特殊 列表如下：

|**配置项**|**A**|**B**|**C**|**D**|**E**|
|---|---|---|---|---|---|
|configUSE_PREEMPTION|1|1|1|1|0|
|configUSE_TIME_SLICING|1|1|0|0|x|
|configIDLE_SHOULD_YIELD|1|0|1|0|x|
|说明|常用|很少用|很少用|很少用|几乎不用|

注：

- A：可抢占+时间片轮转+空闲任务让步
- B：可抢占+时间片轮转+空闲任务不让步
- C：可抢占+非时间片轮转+空闲任务让步
- D：可抢占+非时间片轮转+空闲任务不让步
- E：合作调度

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter9.html#_9-7-3-%E7%A4%BA%E4%BE%8B6-%E8%B0%83%E5%BA%A6)9.7.3 示例6: 调度

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter9.html#_9-7-4-%E5%AF%B9%E6%AF%94%E6%95%88%E6%9E%9C-%E6%8A%A2%E5%8D%A0%E4%B8%8E%E5%90%A6)9.7.4 对比效果: 抢占与否

在 **FreeRTOSConfig.h** 中，定义这样的宏，对比逻辑分析仪的效果：

```C
// 实验1：抢占
##define configUSE_PREEMPTION		1
##define configUSE_TIME_SLICING      1
##define configIDLE_SHOULD_YIELD		1

// 实验2：不抢占
##define configUSE_PREEMPTION		0
##define configUSE_TIME_SLICING      1
##define configIDLE_SHOULD_YIELD		1
```

对比结果为：

- 抢占时：高优先级任务就绪时，就可以马上执行
- 不抢占时：优先级失去意义了，既然不能抢占就只能协商了，图中任务1一直在运行(一点都没有协商精神)，其他任务都无法执行。即使任务3的vTaskDelay已经超时、即使它的优先级更高，都没办法执行。

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter9.html#_9-7-5-%E5%AF%B9%E6%AF%94%E6%95%88%E6%9E%9C-%E6%97%B6%E9%97%B4%E7%89%87%E8%BD%AE%E8%BD%AC%E4%B8%8E%E5%90%A6)9.7.5 对比效果: 时间片轮转与否

在 **FreeRTOSConfig.h** 中，定义这样的宏，对比逻辑分析仪的效果：

```
// 实验1：时间片轮转
##define configUSE_PREEMPTION		1
##define configUSE_TIME_SLICING      1
##define configIDLE_SHOULD_YIELD		1

// 实验2：时间片不轮转
##define configUSE_PREEMPTION		1
##define configUSE_TIME_SLICING      0
##define configIDLE_SHOULD_YIELD		1
```

从下面的对比图可以知道：

- 时间片轮转：在Tick中断中会引起任务切换
- 时间片不轮转：高优先级任务就绪时会引起任务切换，高优先级任务不再运行时也会引起任务切换。可以看到任务3就绪后可以马上执行，它运行完毕后导致任务切换。其他时间没有任务切换，可以看到任务1、任务2都运行了很长时间。

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter9.html#_9-7-6-%E5%AF%B9%E6%AF%94%E6%95%88%E6%9E%9C-%E7%A9%BA%E9%97%B2%E4%BB%BB%E5%8A%A1%E8%AE%A9%E6%AD%A5)9.7.6 对比效果: 空闲任务让步

在 **FreeRTOSConfig.h** 中，定义这样的宏，对比逻辑分析仪的效果：

```C++
// 实验1：空闲任务让步
##define configUSE_PREEMPTION		1
##define configUSE_TIME_SLICING      1
##define configIDLE_SHOULD_YIELD		1

// 实验2：空闲任务不让步
##define configUSE_PREEMPTION		1
##define configUSE_TIME_SLICING      1
##define configIDLE_SHOULD_YIELD		0
```

从下面的对比图可以知道：

- 让步时：在空闲任务的每个循环中，会主动让出处理器，从图中可以看到flagIdelTaskrun的波形很小
- 不让步时：空闲任务跟任务1、任务2同等待遇，它们的波形宽度是差不多的

### 估算任务大小栈
栈包括：
1. 返回地址 （LR 寄存器）等->函数调用深度
2. 局部变量
3. 现场大小 $16*4=64 byte$
估算方案：选取最复杂的调用关系，估算用到的栈大概有多少
$层数*（4*9）$，其中 4 为每个寄存器大小，9 为需要保存的寄存器值

# 4 同步互斥与通信
同一时间只能有一个人使用的资源，被称为临界资源。比如任务A、B都要使用串口来打印，串口就是临界资源。如果A、B同时使用串口，那么打印出来的信息就是A、B混杂，无法分辨。所以使用串口时，应该是这样：A用完，B再用；B用完，A再用
## 4. 1 互斥通信各类方法的对比

能实现同步、互斥的内核方法有：任务通知(task notification)、队列(queue)、事件组(event group)、信号量(semaphoe)、互斥量(mutex)。

它们都有类似的操作方法：获取/释放、阻塞/唤醒、超时。比如：

- A获取资源，用完后A释放资源
- A获取不到资源则阻塞，B释放资源并把A唤醒
- A获取不到资源则阻塞，并定个闹钟；A要么超时返回，要么在这段时间内因为B释放资源而被唤醒。

这些内核对象五花八门，记不住怎么办？我也记不住，通过对比的方法来区分它们。

- 能否传信息？只能传递状态？
- 为众生？只为你？
- 我生产，你们消费？
- 我上锁，只能由我开锁

|内核对象|生产者|消费者|数据/状态|说明|
|---|---|---|---|---|
|队列|ALL|ALL|数据：若干个数据  <br>谁都可以往队列里扔数据，  <br>谁都可以从队列里读数据|用来传递数据，  <br>发送者、接收者无限制，  <br>一个数据只能唤醒一个接收者|
|事件组|ALL|ALL|多个位：或、与  <br>谁都可以设置(生产)多个位，  <br>谁都可以等待某个位、若干个位|用来传递事件，  <br>可以是N个事件，  <br>发送者、接受者无限制，  <br>可以唤醒多个接收者：像广播|
|信号量|ALL|ALL|数量：0~n  <br>谁都可以增加一个数量，  <br>谁都可消耗一个数量|用来维持资源的个数，  <br>生产者、消费者无限制，  <br>1个资源只能唤醒1个接收者|
|任务通知|ALL|只有我|数据、状态都可以传输，  <br>使用任务通知时，  <br>必须指定接受者|N对1的关系：  <br>发送者无限制，  <br>接收者只能是这个任务|
|互斥量|只能A开锁|A上锁|位：0、1  <br>我上锁：1变为0，  <br>只能由我开锁：0变为1|就像一个空厕所，  <br>谁使用谁上锁，  <br>也只能由他开锁|

使用图形对比如下：

- 队列：
    - 里面可以放任意数据，可以放多个数据
    - 任务、ISR都可以放入数据；任务、ISR都可以从中读出数据
- 事件组：
    - 一个事件用一bit表示，1表示事件发生了，0表示事件没发生
    - 可以用来表示事件、事件的组合发生了，不能传递数据
    - 有广播效果：事件或事件的组合发生了，等待它的多个任务都会被唤醒
- 信号量：
    - 核心是"计数值"
    - 任务、ISR释放信号量时让计数值加1
    - 任务、ISR获得信号量时，让计数值减1
- 任务通知：
    - 核心是任务的TCB里的数值
    - 会被覆盖
    - 发通知给谁？必须指定接收任务
    - 只能由接收任务本身获取该通知
- 互斥量：
    - 数值只有0或1
    - 谁获得互斥量，就必须由谁释放同一个互斥量

队列包括：
>环形 Buffer
>两个链表：
>	1. Sender list
>	2. Receiver list


# 5. 队列通信
```C
#include "FreeRTOS.h"
#include "queue.h"
```
使用队列通信往往要用到以上两个文件


队列使用：
>创建队列->写队列->其他任务读取队列数据
创建队列生成了一个句柄（对象），之后读队列和写队列都是对这个对象进行操作
## 5.1 队列的特性
队列(queue)可以用于"任务到任务"、"任务到中断"、"中断到任务"直接传输信息。
### 5.1.1 常规操作

队列的简化操如入下图所示，从此图可知：

- 队列可以包含若干个数据：队列中有若干项，这被称为"长度"(length)
- 每个数据大小固定
- 创建队列时就要指定长度、数据大小
- 数据的操作采用先进先出的方法(FIFO，First In First Out)：写数据时放到尾部，读数据时从头部读
- 也可以强制写队列头部：覆盖头部数据

![](http://photos.100ask.net/rtos-docs/FreeRTOS/DShanMCU-F103/chapter-11/image1.png)

更详细的操作入下图所示：

![](http://photos.100ask.net/rtos-docs/FreeRTOS/DShanMCU-F103/chapter-11/image2.png)

### 5.1.2 传输数据的两种方法

使用队列传输数据时有两种方法：

- 拷贝：把数据、把变量的值复制进队列里
- 引用：把数据、把变量的地址复制进队列里

FreeRTOS使用拷贝值的方法，这更简单：

- 局部变量的值可以发送到队列中，后续即使函数退出、局部变量被回收，也不会影响队列中的数据
- 无需分配buffer来保存数据，队列中有buffer
- 局部变量可以马上再次使用
- 发送任务、接收任务解耦：接收任务不需要知道这数据是谁的、也不需要发送任务来释放数据
- 如果数据实在太大，你还是可以使用队列传输它的地址
- 队列的空间有FreeRTOS内核分配，无需任务操心
- 对于有内存保护功能的系统，如果队列使用引用方法，也就是使用地址，必须确保双方任务对这个地址都有访问权限。使用拷贝方法时，则无此限制：内核有足够的权限，把数据复制进队列、再把数据复制出队列。

### 5.1.3 队列的阻塞访问

**只要知道队列的句柄，谁都可以读、写该队列。**任务、ISR都可读、写队列。可以多个任务读写队列。

任务读写队列时，简单地说：如果读写不成功，则阻塞；可以指定超时时间。口语化地说，就是可以定个闹钟：如果能读写了就马上进入就绪态，否则就阻塞直到超时。

某个任务读队列时，如果队列没有数据，则该任务可以进入阻塞状态：还可以指定阻塞的时间。如果队列有数据了，则该阻塞的任务会变为就绪态。如果一直都没有数据，则时间到之后它也会进入就绪态。

既然读取队列的任务个数没有限制，那么当多个任务读取空队列时，这些任务都会进入阻塞状态：有多个任务在等待同一个队列的数据。当队列中有数据时，哪个任务会进入就绪态？

- 优先级最高的任务
- 如果大家的优先级相同，那等待时间最久的任务会进入就绪态

跟读队列类似，一个任务要写队列时，如果队列满了，该任务也可以进入阻塞状态：还可以指定阻塞的时间。如果队列有空间了，则该阻塞的任务会变为就绪态。如果一直都没有空间，则时间到之后它也会进入就绪态。

既然写队列的任务个数没有限制，那么当多个任务写"满队列"时，这些任务都会进入阻塞状态：有多个任务在等待同一个队列的空间。当队列中有空间时，哪个任务会进入就绪态？

- 优先级最高的任务
- 如果大家的优先级相同，那等待时间最久的任务会进入就绪态

## 5.2 队列函数

使用队列的流程：创建队列、写队列、读队列、删除队列。

### 5.2.1 创建

队列的创建有两种方法：动态分配内存、静态分配内存，

- 动态分配内存：xQueueCreate，队列的内存在函数内部动态分配

函数原型如下：

```C
QueueHandle_t xQueueCreate( UBaseType_t uxQueueLength, UBaseType_t uxItemSize );
```

|**参数**|**说明**|
|---|---|
|uxQueueLength|队列长度，最多能存放多少个数据(item)|
|uxItemSize|每个数据(item)的大小：以字节为单位|
|返回值|非0：成功，返回句柄，以后使用句柄来操作队列 NULL：失败，因为内存不足|

- 静态分配内存：xQueueCreateStatic，队列的内存要事先分配好

函数原型如下：

```C
QueueHandle_t xQueueCreateStatic(*
              		UBaseType_t uxQueueLength,*
              		UBaseType_t uxItemSize,*
              		uint8_t *pucQueueStorageBuffer,*
              		StaticQueue_t *pxQueueBuffer*
           		 );
```

|**参数**|**说明**|
|---|---|
|uxQueueLength|队列长度，最多能存放多少个数据(item)|
|uxItemSize|每个数据(item)的大小：以字节为单位|
|pucQueueStorageBuffer|如果uxItemSize非0，pucQueueStorageBuffer必须指向一个uint8_t数组， 此数组大小至少为"uxQueueLength * uxItemSize"|
|pxQueueBuffer|必须执行一个StaticQueue_t结构体，用来保存队列的数据结构|
|返回值|非0：成功，返回句柄，以后使用句柄来操作队列 NULL：失败，因为pxQueueBuffer为NULL|

示例代码：

```C
// 示例代码
 #define QUEUE_LENGTH 10
 #define ITEM_SIZE sizeof( uint32_t )
 
 // xQueueBuffer用来保存队列结构体
 StaticQueue_t xQueueBuffer;

// ucQueueStorage 用来保存队列的数据

// 大小为：队列长度 * 数据大小
 uint8_t ucQueueStorage[ QUEUE_LENGTH * ITEM_SIZE ];

 void vATask( void *pvParameters )
 {
	QueueHandle_t xQueue1;

	// 创建队列: 可以容纳QUEUE_LENGTH个数据，每个数据大小是ITEM_SIZE
	xQueue1 = xQueueCreateStatic( QUEUE_LENGTH,
							ITEM_SIZE,
                            ucQueueStorage,
                            &xQueueBuffer ); 
  }
```

### 5.2.2 复位

队列刚被创建时，里面没有数据；使用过程中可以调用 **xQueueReset()** 把队列恢复为初始状态，此函数原型为：

```C
/*  pxQueue : 复位哪个队列;
 * 返回值: pdPASS(必定成功)
*/
BaseType_t xQueueReset( QueueHandle_t pxQueue);
```

### 5.2.3 删除

删除队列的函数为 **vQueueDelete()** ，只能删除使用动态方法创建的队列，它会释放内存。原型如下：

```
void vQueueDelete( QueueHandle_t xQueue );
```

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter11.html#_11-2-4-%E5%86%99%E9%98%9F%E5%88%97)5.2.4 写队列

可以把数据写到队列头部，也可以写到尾部，这些函数有两个版本：在任务中使用、在ISR中使用。函数原型如下：

```C
/* 等同于xQueueSendToBack
 * 往队列尾部写入数据，如果没有空间，阻塞时间为xTicksToWait
 */
BaseType_t xQueueSend(
                                QueueHandle_t    xQueue,
                                const void       *pvItemToQueue,
                                TickType_t       xTicksToWait
                            );

/* 
 * 往队列尾部写入数据，如果没有空间，阻塞时间为xTicksToWait
 */
BaseType_t xQueueSendToBack(
                                QueueHandle_t    xQueue,
                                const void       *pvItemToQueue,
                                TickType_t       xTicksToWait
                            );


/* 
 * 往队列尾部写入数据，此函数可以在中断函数中使用，不可阻塞
 */
BaseType_t xQueueSendToBackFromISR(
						  QueueHandle_t xQueue,
						  const void *pvItemToQueue,
						  BaseType_t *pxHigherPriorityTaskWoken
                                   );

/* 
 * 往队列头部写入数据，如果没有空间，阻塞时间为xTicksToWait
 */
BaseType_t xQueueSendToFront(
						QueueHandle_t    xQueue,
						const void       *pvItemToQueue,
						TickType_t       xTicksToWait
                            );

/* 
 * 往队列头部写入数据，此函数可以在中断函数中使用，不可阻塞
 */
BaseType_t xQueueSendToFrontFromISR(
						  QueueHandle_t xQueue,
						  const void *pvItemToQueue,
						  BaseType_t *pxHigherPriorityTaskWoken
                                   );
```

这些函数用到的参数是类似的，统一说明如下：

|参数|说明|
|---|---|
|xQueue|队列句柄，要写哪个队列|
|pvItemToQueue|数据指针，这个数据的值会被复制进队列， 复制多大的数据？在创建队列时已经指定了数据大小|
|xTicksToWait|如果队列满则无法写入新数据，可以让任务进入阻塞状态， xTicksToWait表示阻塞的最大时间(Tick Count)。 如果被设为0，无法写入数据时函数会立刻返回； 如果被设为portMAX_DELAY，则会一直阻塞直到有空间可写|
|返回值|pdPASS：数据成功写入了队列 errQUEUE_FULL：写入失败，因为队列满了。|

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter11.html#_11-2-5-%E8%AF%BB%E9%98%9F%E5%88%97)5.2.5 读队列

使用 **xQueueReceive()** 函数读队列，读到一个数据后，队列中该数据会被移除。这个函数有两个版本：在任务中使用、在ISR中使用。函数原型如下：

```C
BaseType_t xQueueReceive( QueueHandle_t xQueue,
                          void * const pvBuffer,
                          TickType_t xTicksToWait );

BaseType_t xQueueReceiveFromISR(
                                    QueueHandle_t    xQueue,
                                    void             *pvBuffer,
                                    BaseType_t       *pxTaskWoken
                                );
```

参数说明如下：

|**参数**|**说明**|
|---|---|
|xQueue|队列句柄，要读哪个队列|
|pvBuffer|bufer指针，队列的数据会被复制到这个buffer 复制多大的数据？在创建队列时已经指定了数据大小|
|xTicksToWait|果队列空则无法读出数据，可以让任务进入阻塞状态， xTicksToWait表示阻塞的最大时间(Tick Count)。 如果被设为0，无法读出数据时函数会立刻返回； 如果被设为portMAX_DELAY，则会一直阻塞直到有数据可写|
|返回值|pdPASS：从队列读出数据入 errQUEUE_EMPTY：读取失败，因为队列空了。|

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter11.html#_11-2-6-%E6%9F%A5%E8%AF%A2)5.2.6 查询

可以查询队列中有多少个数据、有多少空余空间。函数原型如下：

```C
/*
 * 返回队列中可用数据的个数
 */
UBaseType_t uxQueueMessagesWaiting( const QueueHandle_t xQueue );

/*
 * 返回队列中可用空间的个数
 */
UBaseType_t uxQueueSpacesAvailable( const QueueHandle_t xQueue );
```

### 5.2.7 覆盖/偷看

当队列长度为1时，可以使用 **xQueueOverwrite()** 或 **xQueueOverwriteFromISR()** 来覆盖数据。

注意，队列长度必须为1。当队列满时，这些函数会覆盖里面的数据，这也以为着这些函数不会被阻塞。

函数原型如下：

```C
/* 覆盖队列
 * xQueue: 写哪个队列
 * pvItemToQueue: 数据地址
 * 返回值: pdTRUE表示成功, pdFALSE表示失败
 */
BaseType_t xQueueOverwrite(
                           QueueHandle_t xQueue,
                           const void * pvItemToQueue
                      );

BaseType_t xQueueOverwriteFromISR(
                           QueueHandle_t xQueue,
                           const void * pvItemToQueue,
                           BaseType_t *pxHigherPriorityTaskWoken
                      );
```

如果想让队列中的数据供多方读取，也就是说读取时不要移除数据，要留给后来人。那么可以使用"窥视"，也就是**xQueuePeek()**或**xQueuePeekFromISR()**。这些函数会从队列中复制出数据，但是不移除数据。这也意味着，如果队列中没有数据，那么"偷看"时会导致阻塞；一旦队列中有数据，以后每次"偷看"都会成功。

函数原型如下：

```C
/* 偷看队列
 * xQueue: 偷看哪个队列
 * pvItemToQueue: 数据地址, 用来保存复制出来的数据
 * xTicksToWait: 没有数据的话阻塞一会
 * 返回值: pdTRUE表示成功, pdFALSE表示失败
 */
BaseType_t xQueuePeek(
                          QueueHandle_t xQueue,
                          void * const pvBuffer,
                          TickType_t xTicksToWait
                      );

BaseType_t xQueuePeekFromISR(
                                 QueueHandle_t xQueue,
                                 void *pvBuffer,
                             );
```

## [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter11.html#_11-3-%E7%A4%BA%E4%BE%8B-%E9%98%9F%E5%88%97%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8)5.3 示例: 队列的基本使用

本节代码为：13_queue_game。以前使用环形缓冲区传输红外遥控器的数据，本程序改为使用队列。

## [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter11.html#_11-4-%E7%A4%BA%E4%BE%8B-%E4%BD%BF%E7%94%A8%E9%98%9F%E5%88%97%E5%AE%9E%E7%8E%B0%E5%A4%9A%E8%AE%BE%E5%A4%87%E8%BE%93%E5%85%A5)5.4 示例: 使用队列实现多设备输入

本节代码为：14_queue_game_multi_input。

## [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter11.html#_11-5-%E7%A4%BA%E4%BE%8B-%E4%BC%A0%E8%BE%93%E5%A4%A7%E5%9D%97%E6%95%B0%E6%8D%AE)5.5 示例: 传输大块数据

本节代码为：**FreeRTOS_10_queue_bigtransfer**。

FreeRTOS的队列使用拷贝传输，也就是要传输uint32_t时，把4字节的数据拷贝进队列；要传输一个8字节的结构体时，把8字节的数据拷贝进队列。

如果要传输1000字节的结构体呢？写队列时拷贝1000字节，读队列时再拷贝1000字节？不建议这么做，影响效率！

这时候，我们要传输的是这个巨大结构体的地址：把它的地址写入队列，对方从队列得到这个地址，使用地址去访问那1000字节的数据。

使用地址来间接传输数据时，这些数据放在RAM里，对于这块RAM，要保证这几点：

- RAM的所有者、操作者，必须清晰明了 这块内存，就被称为"共享内存"。要确保不能同时修改RAM。比如，在写队列之前只有由发送者修改这块RAM，在读队列之后只能由接收者访问这块RAM。
- RAM要保持可用

这块RAM应该是全局变量，或者是动态分配的内存。对于动然分配的内存，要确保它不能提前释放：要等到接收者用完后再释放。另外，不能是局部变量。

**FreeRTOS_10_queue_bigtransfer**程序会创建一个队列，然后创建1个发送任务、1个接收任务：

- 创建的队列：长度为1，用来传输"char *"指针
- 发送任务优先级为1，在字符数组中写好数据后，把它的地址写入队列
- 接收任务优先级为2，读队列得到"char *"值，把它打印出来

这个程序故意设置接收任务的优先级更高，在它访问数组的过程中，接收任务无法执行、无法写这个数组。

main函数中创建了队列、创建了发送任务、接收任务，代码如下：

```C++
/* 定义一个字符数组 */
static char pcBuffer[100];

/* vSenderTask被用来创建2个任务，用于写队列
 * vReceiverTask被用来创建1个任务，用于读队列
 */
static void vSenderTask( void *pvParameters );
static void vReceiverTask( void *pvParameters );

/*-----------------------------------------------------------*/

/* 队列句柄, 创建队列时会设置这个变量 */
QueueHandle_t xQueue;

int main( void )
{
	prvSetupHardware();
	
    /* 创建队列: 长度为1，数据大小为4字节(存放一个char指针) */
    xQueue = xQueueCreate( 1, sizeof(char *) );

	if( xQueue != NULL )
	{
		/* 创建1个任务用于写队列
		 * 任务函数会连续执行，构造buffer数据，把buffer地址写入队列
		 * 优先级为1
		 */
		xTaskCreate( vSenderTask, "Sender", 1000, NULL, 1, NULL );

		/* 创建1个任务用于读队列
		 * 优先级为2, 高于上面的两个任务
		 * 这意味着读队列得到buffer地址后，本任务使用buffer时不会被打断
		 */
		xTaskCreate( vReceiverTask, "Receiver", 1000, NULL, 2, NULL );

		/* 启动调度器 */
		vTaskStartScheduler();
	}
	else
	{
		/* 无法创建队列 */
	}

	/* 如果程序运行到了这里就表示出错了, 一般是内存不足 */
	return 0;
}
```

发送任务的函数中，现在全局大数组pcBuffer中构造数据，然后把它的地址写入队列，代码如下：

```C++
static void vSenderTask( void *pvParameters )
{
	BaseType_t xStatus;
	static int cnt = 0;
	
	char *buffer;

	/* 无限循环 */
	for( ;; )
	{
		sprintf(pcBuffer, "www.100ask.net Msg %d\r\n", cnt++);
		buffer = pcBuffer; // buffer变量等于数组的地址, 下面要把这个地址写入队列
		
		/* 写队列
		 * xQueue: 写哪个队列
		 * pvParameters: 写什么数据? 传入数据的地址, 会从这个地址把数据复制进队列
		 * 0: 如果队列满的话, 即刻返回
		 */
		xStatus = xQueueSendToBack( xQueue, &buffer, 0 ); /* 只需要写入4字节, 无需写入整个buffer */

		if( xStatus != pdPASS )
		{
			printf( "Could not send to the queue.\r\n" );
		}
	}
}
```

接收任务的函数中，读取队列、得到buffer的地址、打印，代码如下：

```C++
static void vReceiverTask( void *pvParameters )
{
	/* 读取队列时, 用这个变量来存放数据 */
	char *buffer;
	const TickType_t xTicksToWait = pdMS_TO_TICKS( 100UL );	
	BaseType_t xStatus;

	/* 无限循环 */
	for( ;; )
	{
		/* 读队列
		 * xQueue: 读哪个队列
		 * &xReceivedStructure: 读到的数据复制到这个地址
		 * xTicksToWait: 没有数据就阻塞一会
		 */
		xStatus = xQueueReceive( xQueue, &buffer, xTicksToWait); /* 得到buffer地址，只是4字节 */

		if( xStatus == pdPASS )
		{
			/* 读到了数据 */
			printf("Get: %s", buffer);
		}
		else
		{
			/* 没读到数据 */
			printf( "Could not receive from the queue.\r\n" );
		}
	}
}
```

运行结果如下图所示：

![](http://photos.100ask.net/rtos-docs/FreeRTOS/DShanMCU-F103/chapter-11/image3.png)

## [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter11.html#_11-6-%E7%A4%BA%E4%BE%8B-%E9%82%AE%E7%AE%B1-mailbox)5.6 示例: 邮箱(Mailbox)

本节代码为：**FreeRTOS_11_queue_mailbox**。

FreeRTOS的邮箱概念跟别的RTOS不一样，这里的邮箱称为"橱窗"也许更恰当：

- 它是一个队列，队列长度只有1
- 写邮箱：新数据覆盖旧数据，在任务中使用**xQueueOverwrite()**，在中断中使用**xQueueOverwriteFromISR()**。既然是覆盖，那么无论邮箱中是否有数据，这些函数总能成功写入数据。
- 读邮箱：读数据时，数据不会被移除；在任务中使用**xQueuePeek()**，在中断中使用**xQueuePeekFromISR()**。

这意味着，第一次调用时会因为无数据而阻塞，一旦曾经写入数据，以后读邮箱时总能成功。

main函数中创建了队列(队列长度为1)、创建了发送任务、接收任务：

- 发送任务的优先级为2，它先执行
- 接收任务的优先级为1

代码如下：

```C
/* 队列句柄, 创建队列时会设置这个变量 */
QueueHandle_t xQueue;

int main( void )
{
	prvSetupHardware();
	
    /* 创建队列: 长度为1，数据大小为4字节(存放一个char指针) */
    xQueue = xQueueCreate( 1, sizeof(uint32_t) );

	if( xQueue != NULL )
	{
		/* 创建1个任务用于写队列
		 * 任务函数会连续执行，构造buffer数据，把buffer地址写入队列
		 * 优先级为2
		 */
		xTaskCreate( vSenderTask, "Sender", 1000, NULL, 2, NULL );

		/* 创建1个任务用于读队列
		 * 优先级为1
		 */
		xTaskCreate( vReceiverTask, "Receiver", 1000, NULL, 1, NULL );

		/* 启动调度器 */
		vTaskStartScheduler();
	}
	else
	{
		/* 无法创建队列 */
	}

	/* 如果程序运行到了这里就表示出错了, 一般是内存不足 */
	return 0;
}
```

发送任务、接收任务的代码和执行流程如下：

- A：发送任务先执行，马上阻塞
- BC：接收任务执行，这是邮箱无数据，打印"Could not ..."。在发送任务阻塞过程中，接收任务多次执行、多次打印。
- D：发送任务从阻塞状态退出，立刻执行、写队列
- E：发送任务再次阻塞
- FG、HI、……：接收任务不断"偷看"邮箱，得到同一个数据，打印出多个"Get: 0"
- J：发送任务从阻塞状态退出，立刻执行、覆盖队列，写入1
- K：发送任务再次阻塞
- LM、……：接收任务不断"偷看"邮箱，得到同一个数据，打印出多个"Get: 1"

![](http://photos.100ask.net/rtos-docs/FreeRTOS/DShanMCU-F103/chapter-11/image4.png)

运行结果如下图所示：

![](http://photos.100ask.net/rtos-docs/FreeRTOS/DShanMCU-F103/chapter-11/image5.png)

## 5.7 队列集

假设有2个输入设备：红外遥控器、旋转编码器，它们的驱动程序应该专注于“产生硬件数据”，不应该跟“业务有任何联系”。比如：红外遥控器驱动程序里，它只应该把键值记录下来、写入某个队列，它不应该把键值转换为游戏的控制键。在红外遥控器的驱动程序里，不应该有游戏相关的代码，这样，切换使用场景时，这个驱动程序还可以继续使用。

把红外遥控器的按键转换为游戏的控制键，应该在游戏的任务里实现。

要支持多个输入设备时，我们需要实现一个“InputTask”，它读取各个设备的队列，得到数据后再分别转换为游戏的控制键。

InputTask如何及时读取到多个队列的数据？要使用队列集。

队列集的本质也是队列，只不过里面存放的是“队列句柄”。使用过程如下：

a. 创建队列A，它的长度是n1 b. 创建队列B，它的长度是n2 c. 创建队列集S，它的长度是“n1+n2” d. 把队列A、B加入队列集S e. 这样,写队列A的时候，会顺便把队列A的句柄写入队列集S f. 这样,写队列B的时候，会顺便把队列B的句柄写入队列集S g. InputTask先读取队列集S，它的返回值是一个队列句柄，这样就可以知道哪个队列有有数据了 然后InputTask再读取这个队列句柄得到数据。

#### 使用方式
>1. 创建队列 A、B
>2. 创建队列集 S
>3. 将 A、B 句柄 加入 S
>4. 在 Input_Task 中读队列集拿到句柄，然后做后续处理
```C
while(1){
	queueHandle = read(队列集)//读队列集->得到队列句柄;
	data = read(queueHandle)//->读拿到的队列句柄得到数据
	task();//处理数据
}
```
### 5.7.1 创建

函数原型如下：

```C
QueueSetHandle_t xQueueCreateSet( const UBaseType_t uxEventQueueLength )
```

|**参数**|**说明**|
|---|---|
|uxQueueLength|队列集长度，最多能存放多少个数据(队列句柄)|
|返回值|非0：成功，返回句柄，以后使用句柄来操作队列 NULL：失败，因为内存不足|

### 5.7.2 把队列加入队列集

函数原型如下：

```C
BaseType_t xQueueAddToSet( QueueSetMemberHandle_t xQueueOrSemaphore,
                               QueueSetHandle_t xQueueSet );
```

|**参数**|**说明**|
|---|---|
|xQueueOrSemaphore|队列句柄，这个队列要加入队列集|
|xQueueSet|队列集句柄|
|返回值|pdTRUE：成功 pdFALSE：失败|

### 5.7.3读取队列集

函数原型如下：

```C
QueueSetMemberHandle_t xQueueSelectFromSet( QueueSetHandle_t xQueueSet,
                                                TickType_t const xTicksToWait );
```

|**参数**|**说明**|
|---|---|
|xQueueSet|队列集句柄|
|xTicksToWait|如果队列集空则无法读出数据，可以让任务进入阻塞状态，xTicksToWait表示阻塞的最大时间(Tick Count)。如果被设为0，无法读出数据时函数会立刻返回；如果被设为portMAX_DELAY，则会一直阻塞直到有数据可写|
|返回值|NULL：失败，队列句柄：成功|

### 队列集思考
在抽象出各个硬件驱动并写分别写队列时，某个应用层的程序想要同时检测这三个驱动，故有以下方式
>1. 使用队列集
>2. 采用轮询的方式在任务中依次读三个独立队列，但超时时间尽量不要设置太大。

队列集使用方法


# 第12章信号量(semaphore)

前面介绍的队列(queue)可以用于传输数据：在任务之间、任务和中断之间。
消息队列用于传输多个数据，但是有时候我们只需要传递状态，这个状态值需要用一个数值表示，比如：

- 卖家：做好了1个包子！做好了2个包子！做好了3个包子！
- 买家：买了1个包子，包子数量减1
- 这个停车位我占了，停车位减1
- 我开车走了，停车位加1

在这种情况下我们只需要维护一个数值，使用信号量效率更高、更节省内存 本章涉及如下内容：

- 怎么创建、删除信号量
- 怎么发送、获得信号量
- 什么是计数型信号量？什么是二进制信号量？
>信号量操作：
>Give: 归还信号量
>Take：拿信号量
>拥有信号量的任务才能运行，进而实现互斥
## [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter12.html#_12-1-%E4%BF%A1%E5%8F%B7%E9%87%8F%E7%9A%84%E7%89%B9%E6%80%A7)12.1 信号量的特性

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter12.html#_12-1-1-%E4%BF%A1%E5%8F%B7%E9%87%8F%E7%9A%84%E5%B8%B8%E8%A7%84%E6%93%8D%E4%BD%9C)12.1.1 信号量的常规操作

信号量这个名字很恰当：

- 信号：起通知作用
- 量：还可以用来表示资源的数量
    - 当"量"没有限制时，它就是"计数型信号量"(Counting Semaphores)
    - 当"量"只有0、1两个取值时，它就是"二进制信号量"(Binary Semaphores)
- 支持的动作："give"给出资源，计数值加1；"take"获得资源，计数值减1

计数型信号量的典型场景是：

- 计数：事件产生时"give"信号量，让计数值加1；处理事件时要先"take"信号量，就是获得信号量，让计数值减1。
- 资源管理：要想访问资源需要先"take"信号量，让计数值减1；用完资源后"give"信号量，让计数值加1。 信号量的"give"、"take"双方并不需要相同，可以用于生产者-消费者场合：
- 生产者为任务A、B，消费者为任务C、D
- 一开始信号量的计数值为0，如果任务C、D想获得信号量，会有两种结果：
    - 阻塞：买不到东西咱就等等吧，可以定个闹钟(超时时间)
    - 即刻返回失败：不等
- 任务A、B可以生产资源，就是让信号量的计数值增加1，并且把等待这个资源的顾客唤醒
- 唤醒谁？谁优先级高就唤醒谁，如果大家优先级一样就唤醒等待时间最长的人

二进制信号量跟计数型的唯一差别，就是计数值的最大值被限定为1。

![](http://photos.100ask.net/rtos-docs/FreeRTOS/DShanMCU-F103/chapter-12/image1.png)

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter12.html#_12-1-2-%E4%BF%A1%E5%8F%B7%E9%87%8F%E8%B7%9F%E9%98%9F%E5%88%97%E7%9A%84%E5%AF%B9%E6%AF%94)12.1.2 信号量跟队列的对比
差异列表如下：

|队列|信号量|
|---|---|
|可以容纳多个数据， 创建队列时有2部分内存: 队列结构体、存储数据的空间|只有计数值，无法容纳其他数据。 创建信号量时，只需要分配信号量结构体|
|生产者：没有空间存入数据时可以阻塞|生产者：用于不阻塞，计数值已经达到最大时返回失败|
|消费者：没有数据时可以阻塞|消费者：没有资源时可以阻塞|

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter12.html#_12-1-3-%E4%B8%A4%E7%A7%8D%E4%BF%A1%E5%8F%B7%E9%87%8F%E7%9A%84%E5%AF%B9%E6%AF%94)12.1.3 两种信号量的对比

信号量的计数值都有限制：限定了最大值。如果最大值被限定为1，那么它就是二进制信号量；如果最大值不是1，它就是计数型信号量。

差别列表如下：

|二进制信号量|技术型信号量|
|---|---|
|被创建时初始值为0|被创建时初始值可以设定|
|其他操作是一样的|其他操作是一样的|

## 12.2 信号量函数

使用信号量时，先创建、然后去添加资源、获得资源。使用句柄来表示一个信号量。

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter12.html#_12-2-1-%E5%88%9B%E5%BB%BA) 12.2.1 创建信号量

使用信号量之前，要先创建，得到一个句柄；使用信号量时，要使用句柄来表明使用哪个信号量。 对于二进制信号量、计数型信号量，它们的创建函数不一样：

|二进制信号量|计数型信号量|
|---|---|---|
|动态创建|xSemaphoreCreateBinary 计数值初始值为0|xSemaphoreCreateCounting|
|静态创建|xSemaphoreCreateBinaryStatic|xSemaphoreCreateCountingStatic|

创建二进制信号量的函数原型如下：

```C++
/* 创建一个二进制信号量，返回它的句柄。
 * 此函数内部会分配信号量结构体 
 * 返回值: 返回句柄，非NULL表示成功
 */
SemaphoreHandle_t xSemaphoreCreateBinary( void );

/* 创建一个二进制信号量，返回它的句柄。
 * 此函数无需动态分配内存，所以需要先有一个StaticSemaphore_t结构体，并传入它的指针
 * 返回值: 返回句柄，非NULL表示成功
 */
SemaphoreHandle_t xSemaphoreCreateBinaryStatic( StaticSemaphore_t *pxSemaphoreBuffer );
```

创建计数型信号量的函数原型如下：

```C++
/* 创建一个计数型信号量，返回它的句柄。
 * 此函数内部会分配信号量结构体 
 * uxMaxCount: 最大计数值
 * uxInitialCount: 初始计数值
 * 返回值: 返回句柄，非NULL表示成功
 */
SemaphoreHandle_t xSemaphoreCreateCounting(UBaseType_t uxMaxCount, UBaseType_t uxInitialCount);

/* 创建一个计数型信号量，返回它的句柄。
 * 此函数无需动态分配内存，所以需要先有一个StaticSemaphore_t结构体，并传入它的指针
 * uxMaxCount: 最大计数值
 * uxInitialCount: 初始计数值
 * pxSemaphoreBuffer: StaticSemaphore_t结构体指针
 * 返回值: 返回句柄，非NULL表示成功
 */
SemaphoreHandle_t xSemaphoreCreateCountingStatic( UBaseType_t uxMaxCount, 
                                                 UBaseType_t uxInitialCount, 
                                                 StaticSemaphore_t *pxSemaphoreBuffer );
```

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter12.html#_12-2-2-%E5%88%A0%E9%99%A4)12.2.2 删除

对于动态创建的信号量，不再需要它们时，可以删除它们以回收内存。

vSemaphoreDelete可以用来删除二进制信号量、计数型信号量，函数原型如下：

```C++
/*
 * xSemaphore: 信号量句柄，你要删除哪个信号量
 */
void vSemaphoreDelete( SemaphoreHandle_t xSemaphore );
```

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter12.html#_12-2-3-give-take)12.2.3 give/take

二进制信号量、计数型信号量的give、take操作函数是一样的。这些函数也分为2个版本：给任务使用，给ISR使用。列表如下：

|方法|在任务中使用|在 ISR 中使用|
|---|---|---|
|give|xSemaphoreGive|xSemaphoreGiveFromISR|
|take|xSemaphoreTake|xSemaphoreTakeFromISR|

xSemaphoreGive的函数原型如下：

```C++
BaseType_t xSemaphoreGive( SemaphoreHandle_t xSemaphore );
```

xSemaphoreGive函数的参数与返回值列表如下：

|参数|说明|
|---|---|
|xSemaphore|信号量句柄，释放哪个信号量|
|返回值|pdTRUE表示成功, 如果二进制信号量的计数值已经是1，再次调用此函数则返回失败； 如果计数型信号量的计数值已经是最大值，再次调用此函数则返回失败|

pxHigherPriorityTaskWoken的函数原型如下：

```C++
BaseType_t xSemaphoreGiveFromISR(
                        SemaphoreHandle_t xSemaphore,
                        BaseType_t *pxHigherPriorityTaskWoken
                    );
```

xSemaphoreGiveFromISR函数的参数与返回值列表如下：

|参数|说明|
|---|---|
|xSemaphore|信号量句柄，释放哪个信号量|
|pxHigherPriorityTaskWoken|如果释放信号量导致更高优先级的任务变为了就绪态， 则*pxHigherPriorityTaskWoken = pdTRUE|
|返回值|pdTRUE表示成功, 如果二进制信号量的计数值已经是1，再次调用此函数则返回失败； 如果计数型信号量的计数值已经是最大值，再次调用此函数则返回失败|

xSemaphoreTake的函数原型如下：

```C++
BaseType_t xSemaphoreTake(
                   SemaphoreHandle_t xSemaphore,
                   TickType_t xTicksToWait
               );
```

xSemaphoreTake函数的参数与返回值列表如下：

|参数|说明|
|---|---|
|xSemaphore|信号量句柄，获取哪个信号量|
|xTicksToWait|如果无法马上获得信号量，阻塞一会： 0：不阻塞，马上返回 portMAX_DELAY: 一直阻塞直到成功 其他值: 阻塞的Tick个数，可以使用*pdMS_TO_TICKS()*来指定阻塞时间为若干ms|
|返回值|pdTRUE表示成功|

xSemaphoreTakeFromISR的函数原型如下：

```C++
BaseType_t xSemaphoreTakeFromISR(
                        SemaphoreHandle_t xSemaphore,
                        BaseType_t *pxHigherPriorityTaskWoken
                    );
```

xSemaphoreTakeFromISR函数的参数与返回值列表如下：

|参数|说明|
|---|---|
|xSemaphore|信号量句柄，获取哪个信号量|
|pxHigherPriorityTaskWoken|如果获取信号量导致更高优先级的任务变为了就绪态， 则*pxHigherPriorityTaskWoken = pdTRUE|

## 优先级反转现象
当有任务（低优先级）没有使用信号量，而高优先级的任务使用的信号量且被其他拿了信号量的任务阻塞了，可以使用互斥量来解决。
>解决方案，使用互斥量来为两个任务惊醒提供互斥操作，当一个任务完成后释放信号量便会让高优先级任务工作
# 第13章互斥量(mutex)

使用队列、信号量，都可以实现互斥访问，以信号量为例：

- 信号量初始值为1
- 任务A想上厕所，"take"信号量成功，它进入厕所
- 任务B也想上厕所，"take"信号量不成功，等待
- 任务A用完厕所，"give"信号量；轮到任务B使用

这需要有2个前提：

- 任务B很老实，不撬门(一开始不"give"信号量)
- 没有坏人：别的任务不会"give"信号量

可以看到，使用信号量确实也可以实现互斥访问，但是不完美。

使用互斥量可以解决这个问题，互斥量的名字取得很好：

- 量：值为0、1
- 互斥：用来实现互斥访问

它的核心在于：谁上锁，就只能由谁开锁。

很奇怪的是，FreeRTOS的互斥锁，并没有在代码上实现这点：

- 即使任务A获得了互斥锁，任务B竟然也可以释放互斥锁。
- 谁上锁、谁释放：只是约定。


## [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter13.html#_13-1-%E4%BA%92%E6%96%A5%E9%87%8F%E7%9A%84%E4%BD%BF%E7%94%A8%E5%9C%BA%E5%90%88)13.1 互斥量的使用场合

在多任务系统中，任务A正在使用某个资源，还没用完的情况下任务B也来使用的话，就可能导致问题。

比如对于串口，任务A正使用它来打印，在打印过程中任务B也来打印，客户看到的结果就是A、B的信息混杂在一起。

这种现象很常见：

- 访问外设：刚举的串口例子
- 读、修改、写操作导致的问题

对于同一个变量，比如int a，如果有两个任务同时写它就有可能导致问题。 对于变量的修改，C代码只有一条语句，比如：a=a+8;，它的内部实现分为3步：读出原值、修改、写入。

![](http://photos.100ask.net/rtos-docs/FreeRTOS/DShanMCU-F103/chapter-13/image1.png)

我们想让任务A、B都执行add_a函数，a的最终结果是1+8+8=17。

假设任务A运行完代码①，在执行代码②之前被任务B抢占了：现在任务A的R0等于1。

任务B执行完add_a函数，a等于9。

任务A继续运行，在代码②处R0仍然是被抢占前的数值1，执行完②③的代码，a等于9，这跟预期的17不符合。

- 对变量的非原子化访问

修改变量、设置结构体、在16位的机器上写32位的变量，这些操作都是非原子的。也就是它们的操作过程都可能被打断，如果被打断的过程有其他任务来操作这些变量，就可能导致冲突。

- 函数重入

"可重入的函数"是指：多个任务同时调用它、任务和中断同时调用它，函数的运行也是安全的。可重入的函数也被称为"线程安全"(thread safe)。

每个任务都维持自己的栈、自己的CPU寄存器，如果一个函数只使用局部变量，那么它就是线程安全的。

函数中一旦使用了全局变量、静态变量、其他外设，它就不是"可重入的"，如果该函数正在被调用，就必须阻止其他任务、中断再次调用它。

上述问题的解决方法是：任务A访问这些全局变量、函数代码时，独占它，就是上个锁。这些全局变量、函数代码必须被独占地使用，它们被称为临界资源。

互斥量也被称为互斥锁，使用过程如下：

- 互斥量初始值为1
- 任务A想访问临界资源，先获得并占有互斥量，然后开始访问
- 任务B也想访问临界资源，也要先获得互斥量：被别人占有了，于是阻塞
- 任务A使用完毕，释放互斥量；任务B被唤醒、得到并占有互斥量，然后开始访问临界资源
- 任务B使用完毕，释放互斥量

正常来说：在任务A占有互斥量的过程中，任务B、任务C等等，都无法释放互斥量。 但是FreeRTOS未实现这点：任务A占有互斥量的情况下，任务B也可释放互斥量。

## [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter13.html#_13-2-%E4%BA%92%E6%96%A5%E9%87%8F%E5%87%BD%E6%95%B0)13.2 互斥量函数

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter13.html#_13-2-1-%E5%88%9B%E5%BB%BA)13.2.1 创建

互斥量是一种特殊的二进制信号量。

使用互斥量时，先创建、然后去获得、释放它。使用句柄来表示一个互斥量。

创建互斥量的函数有2种：动态分配内存，静态分配内存，函数原型如下：

```C
/* 创建一个互斥量，返回它的句柄。
 * 此函数内部会分配互斥量结构体 
 * 返回值: 返回句柄，非NULL表示成功
 */
SemaphoreHandle_t xSemaphoreCreateMutex( void );

/* 创建一个互斥量，返回它的句柄。
 * 此函数无需动态分配内存，所以需要先有一个StaticSemaphore_t结构体，并传入它的指针
 * 返回值: 返回句柄，非NULL表示成功
 */
SemaphoreHandle_t xSemaphoreCreateMutexStatic( StaticSemaphore_t *pxMutexBuffer );
```

要想使用互斥量，需要在配置文件FreeRTOSConfig.h中定义：

```C
#define configUSE_MUTEXES 1
```

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter13.html#_13-2-2-%E5%85%B6%E4%BB%96%E5%87%BD%E6%95%B0)13.2.2 其他函数

要注意的是，互斥量不能在ISR中使用。

各类操作函数，比如删除、give/take，跟一般是信号量是一样的。

```C
/*
 * xSemaphore: 信号量句柄，你要删除哪个信号量, 互斥量也是一种信号量
 */
void vSemaphoreDelete( SemaphoreHandle_t xSemaphore );

/* 释放 */
BaseType_t xSemaphoreGive( SemaphoreHandle_t xSemaphore );

/* 释放(ISR版本) */
BaseType_t xSemaphoreGiveFromISR(
                       SemaphoreHandle_t xSemaphore,
                       BaseType_t *pxHigherPriorityTaskWoken
                   );

/* 获得 */
BaseType_t xSemaphoreTake(
                   SemaphoreHandle_t xSemaphore,
                   TickType_t xTicksToWait
               );
/* 获得(ISR版本) */
xSemaphoreGiveFromISR(
                       SemaphoreHandle_t xSemaphore,
                       BaseType_t *pxHigherPriorityTaskWoken
                   );
```

# 第14章事件组(event group)

学校组织秋游，组长在等待：

- 张三：我到了
- 李四：我到了
- 王五：我到了
- 组长说：好，大家都到齐了，出发！

秋游回来第二天就要提交一篇心得报告，组长在焦急等待：张三、李四、王五谁先写好就交谁的。

在这个日常生活场景中：

- 出发：要等待这3个人都到齐，他们是"与"的关系
- 交报告：只需等待这3人中的任何一个，他们是"或"的关系

在FreeRTOS中，可以使用事件组(event group)来解决这些问题。

本章涉及如下内容：

- 事件组的概念与操作函数
- 事件组的优缺点
- 怎么设置、等待、清除事件组中的位
- 使用事件组来同步多个任务

## [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter14.html#_14-1-%E4%BA%8B%E4%BB%B6%E7%BB%84%E6%A6%82%E5%BF%B5%E4%B8%8E%E6%93%8D%E4%BD%9C) 14.1 事件组概念与操作
事件，实际上是一种任务间通信的机制，主要用于实现多任务间的同步，其只能是事件类型的通信，无数据传输。与信号量不同的是，**它可以实现一对多，多对多的同步**。即可以是任意一个事件发生时唤醒任务进行事件处理；也可以是几个事件都发生后才唤醒任务进行事件处理；同样，也可以是多个任务同步多个事件。


### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter14.html#_14-1-1-%E4%BA%8B%E4%BB%B6%E7%BB%84%E7%9A%84%E6%A6%82%E5%BF%B5)14.1.1 事件组的概念

事件组可以简单地认为就是一个整数：

- 的每一位表示一个事件
- 每一位事件的含义由程序员决定，比如：Bit0表示用来串口是否就绪，Bit1表示按键是否被按下
- 这些位，值为1表示事件发生了，值为0表示事件没发生
- 一个或多个任务、ISR都可以去写这些位；一个或多个任务、ISR都可以去读这些位
- 可以等待某一位、某些位中的任意一个，也可以等待多位

![](http://photos.100ask.net/rtos-docs/FreeRTOS/DShanMCU-F103/chapter-14/image1.png)

事件组用一个整数来表示，其中的高8位留给内核使用，只能用其他的位来表示事件。那么这个整数是多少位的？

- 如果configUSE_16_BIT_TICKS是1，那么这个整数就是16位的，低8位用来表示事件
- 如果configUSE_16_BIT_TICKS是0，那么这个整数就是32位的，低24位用来表示事件
- configUSE_16_BIT_TICKS是用来表示Tick Count的，怎么会影响事件组？这只是基于效率来考虑
    - 如果configUSE_16_BIT_TICKS是1，就表示该处理器使用16位更高效，所以事件组也使用16位
    - 如果configUSE_16_BIT_TICKS是0，就表示该处理器使用32位更高效，所以事件组也使用32位

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter14.html#_14-1-2-%E4%BA%8B%E4%BB%B6%E7%BB%84%E7%9A%84%E6%93%8D%E4%BD%9C)14.1.2 事件组的操作

事件组和队列、信号量等不太一样，主要集中在2个地方：

- 唤醒谁？
    - 队列、信号量：事件发生时，只会唤醒一个任务
    - 事件组：事件发生时，会唤醒所有符号条件的任务，简单地说它有"广播"的作用
- 是否清除事件？
    - 队列、信号量：是消耗型的资源，队列的数据被读走就没了；信号量被获取后就减少了
    - 事件组：被唤醒的任务有两个选择，可以让事件保留不动，也可以清除事件

以上图为列，事件组的常规操作如下：

- 先创建事件组
- 任务C、D等待事件：
    - 等待什么事件？可以等待某一位、某些位中的任意一个，也可以等待多位。简单地说就是"或"、"与"的关系。
    - 得到事件时，要不要清除？可选择清除、不清除。
- 任务A、B产生事件：设置事件组里的某一位、某些位

## [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter14.html#_14-2-%E4%BA%8B%E4%BB%B6%E7%BB%84%E5%87%BD%E6%95%B0)14.2 事件组函数

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter14.html#_14-2-1-%E5%88%9B%E5%BB%BA)14.2.1 创建

使用事件组之前，要先创建，得到一个句柄；使用事件组时，要使用句柄来表明使用哪个事件组。

有两种创建方法：动态分配内存、静态分配内存。函数原型如下：

```C
/* 创建一个事件组，返回它的句柄。
 * 此函数内部会分配事件组结构体 
 * 返回值: 返回句柄，非NULL表示成功
 */
EventGroupHandle_t xEventGroupCreate( void );

/* 创建一个事件组，返回它的句柄。
 * 此函数无需动态分配内存，所以需要先有一个StaticEventGroup_t结构体，并传入它的指针
 * 返回值: 返回句柄，非NULL表示成功
 */
EventGroupHandle_t xEventGroupCreateStatic( StaticEventGroup_t * pxEventGroupBuffer );
```

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter14.html#_14-2-2-%E5%88%A0%E9%99%A4)14.2.2 删除

对于动态创建的事件组，不再需要它们时，可以删除它们以回收内存。

**vEventGroupDelete**可以用来删除事件组，函数原型如下：

```C
/*
 * xEventGroup: 事件组句柄，你要删除哪个事件组
 */
void vEventGroupDelete( EventGroupHandle_t xEventGroup )
```

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter14.html#_14-2-3-%E8%AE%BE%E7%BD%AE%E4%BA%8B%E4%BB%B6)14.2.3 设置事件

可以设置事件组的某个位、某些位，使用的函数有2个：

- 在任务中使用**xEventGroupSetBits()**
- 在ISR中使用**xEventGroupSetBitsFromISR()**

有一个或多个任务在等待事件，如果这些事件符合这些任务的期望，那么任务还会被唤醒。

函数原型如下：

```C
/* 设置事件组中的位
 * xEventGroup: 哪个事件组
 * uxBitsToSet: 设置哪些位? 
 *              如果uxBitsToSet的bitX, bitY为1, 那么事件组中的bitX, bitY被设置为1
 *              可以用来设置多个位，比如 0x15 就表示设置bit4, bit2, bit0
 * 返回值: 返回原来的事件值(没什么意义, 因为很可能已经被其他任务修改了)
 */
EventBits_t xEventGroupSetBits( EventGroupHandle_t xEventGroup,
                                    const EventBits_t uxBitsToSet );

/* 设置事件组中的位
 * xEventGroup: 哪个事件组
 * uxBitsToSet: 设置哪些位? 
 *              如果uxBitsToSet的bitX, bitY为1, 那么事件组中的bitX, bitY被设置为1
 *              可以用来设置多个位，比如 0x15 就表示设置bit4, bit2, bit0
 * pxHigherPriorityTaskWoken: 有没有导致更高优先级的任务进入就绪态? pdTRUE-有, pdFALSE-没有
 * 返回值: pdPASS-成功, pdFALSE-失败
 */
BaseType_t xEventGroupSetBitsFromISR( EventGroupHandle_t xEventGroup,
									  const EventBits_t uxBitsToSet,
									  BaseType_t * pxHigherPriorityTaskWoken );
```

值得注意的是，ISR中的函数，比如队列函数**xQueueSendToBackFromISR**、信号量函数**xSemaphoreGiveFromISR**，它们会唤醒某个任务，最多只会唤醒1个任务。

但是设置事件组时，有可能导致多个任务被唤醒，这会带来很大的不确定性。所以**xEventGroupSetBitsFromISR**函数不是直接去设置事件组，而是给一个FreeRTOS后台任务(daemon task)发送队列数据，由这个任务来设置事件组。

如果后台任务的优先级比当前被中断的任务优先级高，**xEventGroupSetBitsFromISR**会设置***pxHigherPriorityTaskWoken**为pdTRUE。

如果daemon task成功地把队列数据发送给了后台任务，那么**xEventGroupSetBitsFromISR**的返回值就是pdPASS。

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter14.html#_14-2-4-%E7%AD%89%E5%BE%85%E4%BA%8B%E4%BB%B6)14.2.4 等待事件

使用**xEventGroupWaitBits**来等待事件，可以等待某一位、某些位中的任意一个，也可以等待多位；等到期望的事件后，还可以清除某些位。

函数原型如下：

```C
EventBits_t xEventGroupWaitBits( EventGroupHandle_t xEventGroup,
                                 const EventBits_t uxBitsToWaitFor,
                                 const BaseType_t xClearOnExit,
                                 const BaseType_t xWaitForAllBits,
                                 TickType_t xTicksToWait );
```

先引入一个概念：unblock condition。一个任务在等待事件发生时，它处于阻塞状态；当期望的时间发生时，这个状态就叫"unblock condition"，非阻塞条件，或称为"非阻塞条件成立"；当"非阻塞条件成立"后，该任务就可以变为就绪态。

函数参数说明列表如下：

|**参数**|**说明**|
|---|---|
|xEventGroup|等待哪个事件组？|
|uxBitsToWaitFor|等待哪些位？哪些位要被测试？|
|xWaitForAllBits|怎么测试？是"AND"还是"OR"？ pdTRUE: 等待的位，全部为1; pdFALSE: 等待的位，某一个为1即可|
|xClearOnExit|函数提出前是否要清除事件？ pdTRUE: 清除uxBitsToWaitFor指定的位 pdFALSE: 不清除|
|xTicksToWait|如果期待的事件未发生，阻塞多久。 可以设置为0：判断后即刻返回； 可设置为portMAX_DELAY：一定等到成功才返回； 可以设置为期望的Tick Count，一般用*pdMS_TO_TICKS()*把ms转换为Tick Count|
|返回值|返回的是事件值， 如果期待的事件发生了，返回的是"非阻塞条件成立"时的事件值； 如果是超时退出，返回的是超时时刻的事件值。|

举例如下：

|事件组的值|uxBitsToWaitFor|xWaitForAllBits|说明|
|---|---|---|---|
|0100|0101|pdTRUE|任务期望bit0,bit2都为1， 当前值只有bit2满足，任务进入阻塞态； 当事件组中bit0,bit2都为1时退出阻塞态|
|0100|0110|pdFALSE|任务期望bit0,bit2某一个为1， 当前值满足，所以任务成功退出|
|0100|0110|pdTRUE|任务期望bit1,bit2都为1， 当前值不满足，任务进入阻塞态； 当事件组中bit1,bit2都为1时退出阻塞态|

你可以使用*xEventGroupWaitBits()_等待期望的事件，它发生之后再使用_xEventGroupClearBits()*来清除。但是这两个函数之间，有可能被其他任务或中断抢占，它们可能会修改事件组。

可以使用设置_xClearOnExit_为pdTRUE，使得对事件组的测试、清零都在*xEventGroupWaitBits()*函数内部完成，这是一个原子操作。

### [#](https://rtos.100ask.net/zh/FreeRTOS/DShanMCU-F103/chapter14.html#_14-2-5-%E5%90%8C%E6%AD%A5%E7%82%B9)14.2.5 同步点

有一个事情需要多个任务协同，比如：

- 任务A：炒菜
- 任务B：买酒
- 任务C：摆台
- A、B、C做好自己的事后，还要等别人做完；大家一起做完，才可开饭

使用 **xEventGroupSync()** 函数可以同步多个任务：

- 可以设置某位、某些位，表示自己做了什么事
- 可以等待某位、某些位，表示要等等其他任务
- 期望的时间发生后， **xEventGroupSync()** 才会成功返回。
- **xEventGroupSync**成功返回后，会清除事件

**xEventGroupSync** 函数原型如下：

```
EventBits_t xEventGroupSync(    EventGroupHandle_t xEventGroup,
                                const EventBits_t uxBitsToSet,
                                const EventBits_t uxBitsToWaitFor,
                                TickType_t xTicksToWait );
```

参数列表如下：

|**参数**|**说明**|
|---|---|
|xEventGroup|哪个事件组？|
|uxBitsToSet|要设置哪些事件？我完成了哪些事件？ 比如0x05(二进制为0101)会导致事件组的bit0,bit2被设置为1|
|uxBitsToWaitFor|等待那个位、哪些位？ 比如0x15(二级制10101)，表示要等待bit0,bit2,bit4都为1|
|xTicksToWait|如果期待的事件未发生，阻塞多久。 可以设置为0：判断后即刻返回； 可设置为portMAX_DELAY：一定等到成功才返回； 可以设置为期望的Tick Count，一般用*pdMS_TO_TICKS()*把ms转换为Tick Count|
|返回值|返回的是事件值， 如果期待的事件发生了，返回的是"非阻塞条件成立"时的事件值； 如果是超时退出，返回的是超时时刻的事件值。|

参数列表如下：

|**参数**|**说明**|
|---|---|
|xEventGroup|哪个事件组？|
|uxBitsToSet|要设置哪些事件？我完成了哪些事件？ 比如0x05(二进制为0101)会导致事件组的bit0,bit2被设置为1|
|uxBitsToWaitFor|等待那个位、哪些位？ 比如0x15(二级制10101)，表示要等待bit0,bit2,bit4都为1|
|xTicksToWait|如果期待的事件未发生，阻塞多久。 可以设置为0：判断后即刻返回； 可设置为portMAX_DELAY：一定等到成功才返回； 可以设置为期望的Tick Count，一般用*pdMS_TO_TICKS()*把ms转换为Tick Count|
|返回值|返回的是事件值，如果期待的事件发生了，返回的是"非阻塞条件成立"时的事件值；如果是超时退出，返回的是超时时刻的事件值。|
