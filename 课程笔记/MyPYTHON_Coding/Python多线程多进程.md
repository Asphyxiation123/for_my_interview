# 引入
## 全局解释性锁 GIL
Python 慢 - 1. 动态语言，边解释边进行；2. GIL 使 Python 无法使用多线程
![[Pasted image 20231227162354.png]]
# 多线程
## 线程池
线程生命周期
![[Pasted image 20231228165530.png]]

## 线程池
```python
import concurrent.futures
import time

def task(n):
    print(f"开始任务 {n}")
    time.sleep(2)
    print(f"完成任务 {n}")
    return n * 2

def main():
    # 创建一个线程池，最大线程数为4
    with concurrent.futures.ThreadPoolExecutor(max_workers=4) as executor:
        # 使用map方法将任务分配给线程池中的线程
        results = executor.map(task, range(1, 6))

    # 打印结果
    for result in results:
        print(f"任务结果: {result}")

if __name__ == "__main__":
    main()

```
线程池可以根据系统分配资源、终止线程

使用线程池的好处：
 使用线程池的好处主要有以下几点：

1. 提高性能：线程池可以重复利用已经创建的线程，避免频繁创建和销毁线程的开销，从而提高性能。
2. 减少资源消耗：线程池可以限制线程的数量，避免创建过多的线程，从而减少系统资源的使用。
3. 提高线程的可管理性：线程池可以对线程进行统一的调度和管理，方便对线程进行控制和管理。
4. 提高线程的复用性：线程池可以提高线程的复用性，避免频繁创建和销毁线程，从而提高性能。
5. 提高线程的稳定性：线程池可以对线程的异常进行捕获和处理，避免异常导致程序崩溃。
6. 提高线程的可扩展性：线程池可以方便地添加或删除线程，以适应程序的需求。

# 多进程
![[多进程与多线程相关函数.png]]

## 多进程原理
#### multiprocessing 原理之 Process ，fork 和 spawn 分别是如何实现多进程的
