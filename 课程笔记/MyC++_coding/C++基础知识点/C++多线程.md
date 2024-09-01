# 1. C++11 Thead 线程库的基本使用

本文详细介绍C++11 Thead线程库的基本使用，包括如何创建线程、启动线程、等待线程完成以及如何分离线程等。  

## **创建线程**

要创建线程，我们需要一个可调用的函数或函数对象，作为线程的入口点。在C++11中，我们可以使用函数指针、函数对象或lambda表达式来实现。创建线程的基本语法如下：

  
```C++
#include <thread>std::thread t(function_name, args...);
```
`function_name`是线程入口点的函数或可调用对象

`args...`是传递给函数的参数

创建线程后，我们可以使用`t.join()`等待线程完成，或者使用`t.detach()`分离线程，让它在后台运行。  

例如，下面的代码创建了一个线程，输出一条消息：
```C++
#include <iostream>
#include <thread>
void print_message(const std::string& message) {   
	std::cout << message << std::endl;
}

void increment(int& x) {  
	++x;
}

int main() {    
	std::string message = "Hello, world!";
	std::thread t(print_message, message);
	t.join();    
	int x = 0;  
	std::thread t2(increment, std::ref(x));  
	t2.join(); 
	std::cout << x << std::endl;  
	return 0;
}
```
  
在这个例子中，我们定义了一个名为 `print_message` 的函数，它输出一条消息。然后，我们创建了一个名为 `t` 的线程，将 `print_message` 函数作为入口点。最后，我们使用 `t.join()` 等待线程完成。  

## **传递参数**
我们可以使用多种方式向线程传递参数，例如使用函数参数、全局变量、引用等。如：
在第一个例子中，我们使用了一个字符串作为函数参数，传递给线程。在第二个例子中，我们使用了一个引用来传递一个整数变量。需要注意的是，当我们使用引用传递参数时，我们需要使用`std::ref`来包装引用，否则编译器会报错。

## **等待线程完成**

当我们创建一个线程后，我们可能需要等待它完成，以便获取线程的执行结果或执行清理操作。我们可以使用`t.join()`方法来等待线程完成。例如，下面的代码创建了两个线程，等待它们完成后输出一条消息：

  ```C++

#include <iostream>
#include <thread>
void print_message(const std::string& message) { 
std::cout << message << std::endl;
}
int main() {  
	std::thread t1(print_message, "Thread 1"); 
	std::thread t2(print_message, "Thread 2");  
	t1.join();   
	t2.join();   
	std::cout << "All threads joined" << std::endl; 
	return 0;
}
```
  

在这个例子中，我们创建了两个线程`t1`和`t2`，它们都调用`print_message`函数输出一条消息。然后，我们使用`t1.join()`和`t2.join()`等待它们完成。最后，我们输出一条消息，表示所有线程都已经完成。  

  

## **分离线程**

  

有时候我们可能不需要等待线程完成，而是希望它在后台运行。这时候我们可以使用`t.detach()`方法来分离线程。例如，下面的代码创建了一个线程，分离它后输出一条消息：

  
```C++
#include <iostream>
#include <thread>
void print_message(const std::string& message) { 
	std::cout << message << std::endl;
	}
int main() {   
	std::thread t(print_message, "Thread 1");  
	t.detach(); 
	std::cout << "Thread detached" << std::endl; 
	return 0;
}
```
  

在这个例子中，我们创建了一个名为`t`的线程，调用`print_message`函数输出一条消息。然后，我们使用`t.detach()`方法分离线程，让它在后台运行。最后，我们输出一条消息，表示线程已经被分离。

  

需要注意的是，一旦线程被分离，就不能再使用`t.join()`方法等待它完成。而且，我们需要确保线程不会在主线程结束前退出，否则可能会导致未定义行为。

  
## **joinable()**

`joinable()`方法返回一个布尔值，如果线程可以被join()或detach()，则返回true，否则返回false。如果我们试图对一个不可加入的线程调用join()或detach()，则会抛出一个std::system_error异常。

下面是一个使用joinable()方法的例子：

  
```C++
#include <iostream>
#include <thread>
void foo() {
    std::cout << "Thread started" << std::endl;
}
int main() {
    std::thread t(foo);
    if (t.joinable()) {
        t.join();
    }
    std::cout << "Thread joined" << std::endl;
    return 0;
}
```
  

## 常见错误（将在后续文章中详解以下错误的解决方案）
在使用C++11线程库时，有一些常见的错误需要注意。例如：
- 忘记等待线程完成或分离线程：如果我们创建了一个线程，但没有等待它完成或分离它，那么在主线程结束时，可能会导致未定义行为。
- 访问共享数据时没有同步：如果我们在多个线程中访问共享数据，但没有使用同步机制，那么可能会导致数据竞争、死锁等问题。
- 异常传递问题：如果在线程中发生了异常，但没有处理它，那么可能会导致程序崩溃。因此，我们应该在线程中使用try-catch块来捕获异常，并在适当的地方处理它。
# 总结
C++11提供了一个强大的线程库，即std:: thread。它可以在C++程序中创建和管理线程，提供了一种更加现代化的方式来处理多线程编程。在本文中，我们介绍了std::thread库的基本使用，包括如何创建、启动和管理线程，以及如何等待线程完成和分离线程。同时，我们也提到了一些常见的错误，需要注意避免。

# 多线程
多线程只读的共享变量，永远不会出现任何冲突。多线程各自写各自的变量，也不会有任何冲突。只有出现共享的可写变量时，才会发生冲突！这就是写独占定律。本期课程中，首先区分了并发与并行的区别，共享可写变量，需要用互斥锁（mutex）保护，避免重入。为了避免忘记释放锁，以及在异常时也能正常释放锁，建议用符合RAII思想的unique_lock。如果想要实现一个线程写入后，让另一个线程接收，还需要配合条件变量（condition_variable）来保证顺序。这种“基于共享变量的同步”，容易出错，特别是如果一次获取多个锁，可能出现死锁。为了避免麻烦和危险，小彭老师又提出“基于消息的同步”，为你打造一个多线程安全的队列封装类（见课程源码的mt_queue.hpp），支持许多常用操作，包括阻塞的pop，定时的try_pop_for等。还介绍了命令模式的思想，通过队列传递函数对象，完全避免共享的同时又能准确，你可以把这个小彭老师封装好的头文件下载下来自己用。顺便还介绍了C++20新增的std::barrier，std::counting_semaphere等。但在并发编程中，都不如多线程队列来的万能。