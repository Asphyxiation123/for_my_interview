# 0 某些无关紧要的知识
出现对部分 Widget 的方法没有 connnet 方法提示时可以使用安装下面两个库以提供代码提示
```
pip install IceSpringPySideStubs-PySide6
pip install PySide6-stubs
```

# 1. QT 窗体布局
## QmianWindow


## QWidget

## QDialog
Modal - 把窗体始终放在最前面


# 上下文菜单
功能类似于右键菜单，分为三步
1. 设置窗体上下文菜单策略
2. 
```Python
# 设置右键菜单
self.setContextMenuPolicy(Qt.ContextMenuPolicy.ActionsContextMenu)
# 创建打开文件和关闭文件动作
self.openFile = QAction('打开文件')
self.closeFile = QAction('关闭文件')
# 将动作添加到右键菜单中
self.addActions([self.openFile, self.closeFile])
```


# 资源加载
## 给控件加入图标
#### Pyside 6 自带标准图标

#### 
# 界面美化
直接套用他人的 QSS
# PySide 6 高级控件

## QListwidget
### 添加元素
```python
self.listWidget.addItems(["Person %c" % chr(ord('A')+i) for i in range(10)])
```
其中推理表达式的定义如下
```Python
[表达式 for 变量 in 可迭代对象 if 条件]
```
### 插入元素
```Python
self.listWidget.insertItems(3, ["Apple", "Banana", "Cherry"])
```
### 删除元素
```python
self.listWidget.takeItem(2)
```
### 修改元素
```python
# 修改元素
banana->Orangeself.listWidget.item(2).setText("Orange")
```

```Python
# 创建一个 QListWidget 对象，用于显示列表
self.listWidget = QListWidget()
        # 创建一个列表，将字符串 "Person A" 到 "Person J" 添加到列表中
        self.listWidget.addItems(["Person %c" % chr(ord('A')+i) for i in range(10)])
        
        # 创建一个列表，将字符串 "Apple"，"Banana"，"Cherry" 添加到列表中
        self.listWidget.insertItems(3, ["Apple", "Banana", "Cherry"])
        # 创建一个 QVBoxLayout 对象，用于垂直布局
        self.layout = QVBoxLayout()
        # 将 QListWidget 对象添加到 QVBoxLayout 对象中
        self.layout.addWidget(self.listWidget)
        # 将 QVBoxLayout 对象添加到 QMainWindow 对象中
        self.setLayout(self.layout)

```
### QlistWidge 方法一览
QListWidget 是一个常用的 Qt 数据可视化控件，可以用来显示项目列表。以下是 QListWidget 的一些重要方法：
```
 QListWidget是一个常用的列表视图控件，提供了丰富的功能和信号，以下是一些重要方法与信号及其使用频率的排序：

1. addItem(QListWidgetItem* item)：向列表中添加一个项目。
2. addItems(QList<QListWidgetItem*> items)：向列表中添加一组项目。
3. insertItem(int index, QListWidgetItem* item)：在指定索引处插入一个项目。
4. insertItems(int index, QList<QListWidgetItem*> items)：在指定索引处插入一组项目。
5. removeItem(QListWidgetItem* item)：从列表中移除一个项目。
6. takeItem(int index)：从列表中移除指定索引的项目。
7. item(int index)：返回指定索引的项目。
8. count()：返回列表中的项目数量。
9. indexOf(QListWidgetItem* item)：返回指定项目的索引。
10. sortItems(Qt::SortOrder order=Qt::AscendingOrder)：对列表中的项目进行排序。
11. clear()：清除列表中的所有项目。
12. setSortingEnabled(bool enabled)：设置是否启用项目排序功能。
13. isSortingEnabled()：判断是否启用项目排序功能。
14. setSelectionMode(QAbstractItemView::SelectionMode mode)：设置列表的选中模式。
15. selectionMode()：返回列表的选中模式。
16. setSelectionBehavior(QAbstractItemView::SelectionBehavior behavior)：设置列表的选中行为。
17. selectionBehavior()：返回列表的选中行为。
18. setFlow(QListWidget::Flow flow)：设置列表的布局方式。
19. flow()：返回列表的布局方式。
20. setMovement(QListWidget::Movement movement)：设置列表的项目移动方式。
21. movement():返回列表的项目移动方式。
22. setResizeMode(QListWidget::ResizeMode mode)：设置列表的项目缩放方式。
23. resizeMode()：返回列表的项目缩放方式。
24. setViewMode(QListWidget::ViewMode mode)：设置列表的视图模式。
25. viewMode()：返回列表的视图模式。

从排序结果可以看出，使用率高的方法在前，重要性高的方法在后。其中，addItem、addItems、insertItem、insertItems、removeItem、takeItem等方法在实际开发中使用频率较高，而sortItems、setSortingEnabled、isSortingEnabled、setSelectionMode、selectionMode、setSelectionBehavior、selectionBehavior等方法在排序和选中操作中使用频率较高。

这些方法可以用来操作QListWidget中的项目，实现数据可视化控件的各种功能。
```
#### 信号一览
除了重要方法外，QListWidget 还提供了许多信号，用于在列表项发生改变时触发相应的操作。以下是一些常见的信号：
```
1. currentItemChanged(QListWidgetItem* current, QListWidgetItem* previous)：当前项目发生变化时触发。
2. itemClicked(QListWidgetItem* item)：当列表项被单击时触发。
3. itemDoubleClicked(QListWidgetItem* item)：当列表项被双击时触发。
4. itemSelectionChanged():当列表项的选中状态发生变化时触发。
5. itemActivated(QListWidgetItem* item)：当列表项被激活（通常是按下Enter键）时触发。
6. layoutAboutToBeChanged():在列表项布局即将改变时触发。
7. layoutChanged():列表项布局已更改时触发。
8. rowsAboutToBeInserted(int parent, int start, int end)：在列表项将被插入时触发。
9. rowsInserted(int parent, int start, int end)：列表项已插入时触发。
10. rowsAboutToBeRemoved(int parent, int start, int end)：在列表项将被移除时触发。
11. rowsRemoved(int parent, int start, int end)：列表项已移除时触发。
12. rowsAboutToBeMoved(int sourceParent, int sourceStart, int sourceEnd, int destinationParent, int destinationStart)：在列表项将被移动时触发。
13. rowsMoved(int sourceParent, int sourceStart, int sourceEnd, int destinationParent, int destinationStart)：列表项已移动时触发。

这些信号可以用来监听列表项的变化，并根据需要执行相应的操作。
```

# MVC 架构
 MVC (Model-View-Controller)是一种软件架构设计模式, 用于简化代码结构和管理应用程序的数据。MVC 中的模型层是指负责处理数据的部分, 通常是应用程序中的数据结构和业务逻辑。
## 模型层
在 MVC 模型中, 模型层通常包含以下几个部分:

1. 数据结构: 模型层使用数据结构来表示应用程序中的数据, 例如使用对象、数组或数据库表来存储数据。

2. 业务逻辑: 模型层包含处理数据的方法和函数, 例如验证数据、计算结果、执行数据库查询等。

3. 数据访问层: 模型层与数据访问层进行交互, 以获取和存储数据。数据访问层通常是应用程序的底层组件, 负责与数据库或其他数据存储系统进行交互。

在实际应用中, 模型层通常是一个类或一组类, 其中包含数据结构和业务逻辑。例如, 一个用户管理应用程序可能包含一个名为 User 的模型类, 其中包含用户的数据结构、用户注册和登录等功能。

MVC 模型中的模型层主要关注数据的处理和业务逻辑, 而不直接面向用户界面。这使得应用程序更加灵活和易于维护。

## 视图层
在 MVC 模型中, 视图层通常包含以下几个部分:

1. 模板: 模板是指用于显示数据的一种方式, 例如 HTML、XML 或 JSON。模板通常包含显示数据的标签和指令。

2. 前端代码: 视图层包含前端代码, 例如 JavaScript、CSS 或 HTML。前端代码负责与用户进行交互, 例如接收用户输入、显示数据、处理用户操作等。

3. 后端代码: 视图层包含后端代码, 例如服务器端脚本或控制器。后端代码负责处理数据并将数据发送到视图层。

在实际应用中, 视图层通常是一个类或一组类, 其中包含模板、前端代码和后端代码。例如, 一个用户管理应用程序可能包含一个名为 UserView 的视图类, 其中包含显示用户列表、用户详情和用户操作的模板和代码。

MVC 模型中的视图层主要关注显示数据, 而不直接参与数据处理和业务逻辑。这使得应用程序更加灵活和易于维护。
## 控制层

控制层（Controller）是 MVC 模式中的核心部分，其主要任务是将请求接收并转换为模型中的数据，然后将数据传递给视图进行处理，最后将视图的响应返回给请求方。控制层负责处理业务逻辑，而视图层负责呈现用户界面。

在 MVC 代码架构中，控制层通常包含以下几个部分：

1. 请求处理：接收用户请求，并根据请求类型进行处理。
2. 数据验证：对用户输入的数据进行验证，确保其合法性和正确性。
3. 数据操作：根据业务逻辑对模型数据进行操作，例如查询、添加、更新或删除数据。
4. 数据传递：将操作后的模型数据传递给视图进行处理。
5. 响应生成：根据视图的响应类型，生成相应的响应内容。
6. 响应发送：将响应发送给请求方，完成整个请求的处理过程。

控制层通常是一个类，该类负责处理请求、数据操作和响应生成等业务逻辑。它与模型层和视图层进行交互，将数据请求转换为模型数据，然后将模型数据传递给视图进行处理，最后将视图的响应返回给请求方。控制层通常不关注用户界面，而是关注业务逻辑的处理。

总之，控制层是 MVC 模式中的核心部分，其主要任务是将请求接收并转换为模型中的数据，然后将数据传递给视图进行处理，最后将视图的响应返回给请求方。控制层负责处理业务逻辑，而视图层负责呈现用户界面。

# Qt 与多线程
[Python并发编程](https://python-parallel-programmning-cookbook.readthedocs.io/zh-cn/latest/chapter2/06_Thread_synchronization_with_Lock_and_Rlock.html)
## QTimer
```Shell
#实例化timer

#绑定timeout信号

#开启定时器
timer.start(1000)  # 每隔1000毫秒执行一次update_time函数
```
 `QTimer` 是 PySide 6 中用于实现定时操作的类。在 PySide 6 应用程序中，`QTimer` 是一个非常常用的计时器类，用于在指定时间间隔内执行特定操作。以下是使用率最高的 10 个 `QTimer` 方法，并给出一些例子：

1. `start(interval)`：启动计时器，并设置时间间隔。`interval` 参数表示时间间隔的毫秒数。

例子：
```python
from PySide6.QtCore import QTimer, QTime

def update_time():
    current_time = QTime.currentTime()
    print(current_time.toString("hh:mm:ss"))

timer = QTimer()
timer.timeout.connect(update_time)
timer.start(1000)  # 每隔1000毫秒执行一次update_time函数
```
2. `stop()`：停止计时器。

例子：
```python
from PySide6.QtCore import QTimer

def update_time():
    print("Time elapsed:", timer.elapsed())

timer = QTimer()
timer.timeout.connect(update_time)
timer.start(1000)

# 等待3秒
QTimer.singleShot(3000, timer.stop)
```
3. `isActive()`：检查计时器是否处于活动状态。

例子：
```python
from PySide6.QtCore import QTimer

def update_time():
    print("Timer is active:", timer.isActive())

timer = QTimer()
timer.timeout.connect(update_time)
timer.start(1000)

# 等待3秒
QTimer.singleShot(3000, timer.stop)
```
4. `setInterval(interval)`：设置计时器的时间间隔。

例子：
```python
from PySide6.QtCore import QTimer

def update_time():
    print("Timer interval:", timer.interval())

timer = QTimer()
timer.timeout.connect(update_time)
timer.start(1000)

# 等待3秒
QTimer.singleShot(3000, timer.stop)
```
5. `setSingleShot(singleShot)`：设置计时器是否为单次触发。如果 `singleShot` 为 `True`，则计时器在触发一次后就会停止。

例子：
```python
from PySide6.QtCore import QTimer

def update_time():
    print("Timer is single shot:", timer.isSingleShot())

timer = QTimer()
timer.timeout.connect(update_time)
timer.start(1000)

# 等待3秒
QTimer.singleShot(3000, timer.stop)
```
6. `timeout()`：槽函数，当计时器超时时触发。

例子：
```python
from PySide6.QtCore import QTimer

def update_time():
    print("Time elapsed:", timer.elapsed())

timer = QTimer()
timer.timeout.connect(update_time)
timer.start(1000)

# 等待3秒
QTimer.singleShot(3000, timer.stop)
```
7. `interval()`：返回计时器的时间间隔。

例子：
```python
from PySide6.QtCore import QTimer

def update_time():
    print("Timer interval:", timer.interval())

timer = QTimer()
timer.timeout.connect(update_time)
timer.start(1000)

# 等待3秒
QTimer.singleShot(3000, timer.stop)
```
8. `startTimer(interval)`：启动计时器，并设置时间间隔。`interval` 参数表示时间间隔的毫秒数。

例子：
```python
from PySide6.QtCore import QTimer, QTime

def update_time():
    current_time = QTime.currentTime()
    print(current_time.toString("hh:mm:ss"))

timer = QTimer()
timer.timeout.connect(update_time)
```
### pocessEvents ()
该函数用于将控制权交给窗体

## 多线程实验
 PySide 6 中的 QThread 提供了许多重要的方法，其中有

1. `QThread.__init__(self, parent=None)`: 构造函数，初始化线程对象。
2. `QThread.start()`: 启动线程。
3. `QThread.quit()`: 请求线程退出。
4. `QThread.wait()`: 等待线程完成。
5. `QThread.terminate()`: 终止线程。
6. `QThread.isRunning()`: 检查线程是否正在运行。
7. `QThread.setPriority(priority)`: 设置线程的优先级。
8. `QThread.priority()`: 返回线程的优先级。
9. `QThread.sleep(msecs)`: 使线程休眠指定毫秒数。
10. `QThread.usleep(microsecs)`: 使线程休眠指定微秒数。

这些方法可以用来控制线程的启动、退出、优先级等，从而实现线程之间的协作和同步。
```python
# pyside6基础框架  
import sys  
from PySide6.QtWidgets import QApplication, QMainWindow, QPushButton, QLabel, QVBoxLayout, QWidget  
from PySide6.QtCore import QThread, Signal  
import time  
  
  
class workerThread(QThread):  
    # 自定义一个信号，用于在后台线程中发送消息  
    update_signal = Signal(str)  
  
    def __init__(self):  
        super().__init__()  
  
        print("run")  
  
    def run(self):  
        # 在这里执行你的任务  
        for i in range(5):  
            # 模拟一些工作  
            self.sleep(1)  
            # 自定义信号发送消息到主线程  
            self.update_signal.emit(str(i))  
  
  
class MainWindow(QWidget):  
    def __init__(self):  
        super().__init__()  
  
        # 设置窗口标题  
        self.setWindowTitle("multiThread")  
        # 设置窗口大小  
        self.setGeometry(100, 100, 300, 200)  
  
        self.lb = QLabel("当前的值为：0")  
  
        self.workThread = workerThread()  
        self.workThread.update_signal.connect(lambda x: self.lb.setText(f'当前的值为:{x}'))  
        self.workThread.started.connect(lambda: print("started"))  
        self.workThread.finished.connect(lambda: print("finished"))  
        # 删除该线程  
        self.workThread.finished.connect(self.workThread.deleteLater)  
  
        self.workThread.start()  
  
        # 创建一个 QVBoxLayout 对象，用于垂直布局  
        self.layout = QVBoxLayout()  
        self.layout.addWidget(self.lb)  
        self.setLayout(self.layout)  
  
  
if __name__ == "__main__":  
    app = QApplication(sys.argv)  
    mainWin = MainWindow()  
    mainWin.show()  
    sys.exit(app.exec())
```
### 多线程信号传递
1. 在线程内自定义信号
2. 在主线程内将信号与槽函数相连接

### 线程阻塞
```python
self.thread.wait()
```
只有该线程运行完成再进行下一步


## 线程安全介绍
 线程安全的概念是指在多线程并发访问的情况下，程序能够正确地保持其状态不变，并且不会出现竞态条件或数据不一致的情况。实现线程安全的方法有很多，其中一种常用的方法是使用同步机制，例如锁（Lock）、原子操作（Atomic）和信号量（Semaphore）等。

Qt 的线程安全机制基于信号和槽机制，以及锁机制。在 PySide 6 中，可以使用 QThread 类创建线程，并使用 Signal 和 Slot 机制来发送和接收信号。当多个线程并发访问同一个对象时，可以使用 QMutex 或 QReadWriteLock 等锁机制来保证线程安全。

PySide 6 还提供了原子操作类，例如 QAtomicInt、QAtomicLong、QAtomicReference 等，这些类可以确保多线程并发访问时不会出现问题。
 PySide 6 是 Python 的一个跨平台的 UI 框架，它提供了丰富的库，如 QtWidgets、QtGui、QtCore 等。其中，QtCore 库提供了线程安全的容器和类，可以用于多线程编程。

PySide 6 的线程安全主要依赖于 QtCore 库中的线程同步机制。QtCore 库提供了以下线程安全容器和类：


1. QMutex：互斥锁，用于保护临界区，防止多线程同时访问同一个资源。包括 lock 也是同样的功能
2. QSemaphore：信号量，用于限制并发访问的数量，防止资源过快被消耗。
3. QReadWriteLock：读写锁，用于确保在多线程环境下，同一时刻只有一个线程可以访问共享资源。
4. QWaitCondition：等待条件，用于等待某个事件发生。
5. QMutexLocker：互斥锁封装器，用于简化线程同步代码。

下面是一些使用 PySide 6 线程安全的例子：

1. 使用 QMutex 保护临界区：

```python
from PySide6.QtCore import QMutex, QThread

class MyThread(QThread):
    def __init__(self):
        super().__init__()
        self.mutex = QMutex()

    def run(self):
        with QMutexLocker(self.mutex):
            # 临界区代码
            print("线程1：", self.currentThread())

class MyThread2(QThread):
    def __init__(self):
        super().__init__()
        self.mutex = QMutex()

    def run(self):
        with QMutexLocker(self.mutex):
            # 临界区代码
            print("线程2：", self.currentThread())

if __name__ == "__main__":
    thread1 = MyThread()
    thread2 = MyThread2()
    thread1.start()
    thread2.start()
```

2. 使用 QSemaphore 限制并发访问数量：

```python
from PySide6.QtCore import QSemaphore, QThread

class MyThread(QThread):
    def __init__(self, semaphore):
        super().__init__()
        self.semaphore = semaphore

    def run(self):
        self.semaphore.acquire()
        print("线程：", self.currentThread())
        self.semaphore.release()

if __name__ == "__main__":
    semaphore = QSemaphore(2)
    thread1 = MyThread(semaphore)
    thread2 = MyThread(semaphore)
    thread3 = MyThread(semaphore)
    thread1.start()
    thread2.start()
    thread3.start()
```

3. 使用 QReadWriteLock 确保同一时刻只有一个线程可以访问共享资源：

```python
from PySide6.QtCore import QReadWriteLock, QThread

class MyThread(QThread):
    def __init__(self, lock):
        super().__init__()
        self.lock = lock

    def run(self):
        with self.lock.readLocker():
            # 读操作
            print("线程：", self.currentThread())

        with self.lock.writeLocker():
            # 写操作
            print("线程：", self.currentThread())

if __name__ == "__main__":
    lock = QReadWriteLock()
    thread1 = MyThread(lock)
    thread2 = MyThread(lock)
    thread1.start()
    thread2.start()
```

4. 使用 QWaitCondition 等待某个事件发生：

```python
from PySide6.QtCore import QWaitCondition, QMutex, QThread

class Producer(QThread):
    def __init__(self, condition, mutex):
        super().__init__()
        self.condition = condition
        self.mutex = mutex

    def run(self):
        for i in range(5):
            with self.mutex:
                print("生产者：", self.currentThread())
                self.condition.wait()
                print("生产者：", self.
```

### 锁的使用
#### with
 `with` 是一个 Python 关键字，用于提供一种简洁的方式来管理上下文（如锁、文件句柄等）。它通常与 `as` 一起使用，用于指定要使用的对象。

`with` 语句的工作原理如下：

1. 进入 `with` 语句时，它会调用与 `with` 语句关联的对象的 `__enter__` 方法。这通常是一个获取资源（如锁、文件句柄等）的方法。
2. 在 `with` 语句块中，您可以执行所需的操作。
3. 当离开 `with` 语句块时，它会自动调用与 `with` 语句关联的对象的 `__exit__` 方法。这通常是一个释放资源（如锁、文件句柄等）的方法。

使用 `with` 语句可以确保资源在使用完毕后被正确释放，避免资源泄漏和其他问题。例如，在使用文件时，可以使用 `with` 语句来确保文件在操作完成后被正确关闭。

总之，`with` 语句提供了一种简洁的方式来管理上下文，可以确保资源在使用完毕后被正确释放。
#### 使用 Qt 自带的锁
在 Pyside 6 中，锁可以通过继承自 `QMutex` 或 `QReadWriteLock` 的类来创建。这些类提供了锁的接口，可以在多个线程之间共享数据。

`QMutex` 是一个互斥锁，用于保护对共享资源的访问，确保同一时间只有一个线程可以访问该资源。它提供了一个 `lock` 方法，用于获取锁，并在离开代码块时自动调用 `unlock` 方法释放锁。

`QReadWriteLock` 是一个读写锁，用于保护对共享资源的访问，允许多个线程同时读取共享资源，但只允许一个线程写入共享资源。它提供了一个 `readLock` 方法，用于获取读锁，以及一个 `writeLock` 方法，用于获取写锁。在读取和写入共享资源时，需要首先获取相应的锁，并在离开代码块时释放锁。

下面是一个使用 `QMutex` 的简单示例：
```python
from PySide6.QtCore import QMutex, QThread

class MyThread(QThread):
    def __init__(self):
        super().__init__()
        self.mutex = QMutex()

    def run(self):
        with QMutexLocker(self.mutex):
            # 临界区，确保同一时间只有一个线程可以执行此代码
            print("Hello from thread")

if __name__ == "__main__":
    thread = MyThread()
    thread.start()
    thread.wait()
```
在这个示例中，我们创建了一个名为 `MyThread` 的线程类，它继承自 `QThread`。在 `run` 方法中，我们使用 `QMutexLocker` 来获取 `mutex` 锁，以确保在临界区中只有一个线程可以执行代码。

总之，Pyside 6 提供了锁的概念，用于保护共享资源的访问，确保在不同线程之间同步数据。

## 进程间通信
在 Python 中，进程间通信（IPC）可以通过多种方式实现，包括：

1. 文件管道：使用文件描述符进行通信。

2. 套接字：使用套接字进行通信。

3. 消息队列：使用消息队列进行通信。

4. 共享内存：使用共享内存进行通信。

5. 信号量：使用信号量进行通信。

下面是一些使用这些方法的示例：

1. 文件管道：
```python
import os
import sys

# 创建一个管道文件
pipe_file = "pipe.txt"

# 写入数据到管道文件
with open(pipe_file, "w") as f:
    f.write("Hello, World!")

# 从管道文件中读取数据
with open(pipe_file, "r") as f:
    data = f.read()
    print("Received data:", data)
```
2. 套接字：

```python
import socket

# 创建一个套接字
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 绑定到一个地址和端口
server_address = ("localhost", 10000)
sock.bind(server_address)

# 监听连接
sock.listen(1)

# 接受连接
connection, client_address = sock.accept()

# 读取数据
data = connection.recv(1024)
print("Received data:", data)

# 发送数据
connection.sendall(b"Hello, World!")

# 关闭连接
connection.close()
sock.close()
```
3. 消息队列：

```python
import queue

# 创建一个消息队列
message_queue = queue.Queue()

# 向队列中添加数据
message_queue.put("Hello, World!")

# 从队列中获取数据
data = message_queue.get()
print("Received data:", data)
```
4. 共享内存：

```python
import multiprocessing

# 创建一个共享内存对象
shared_memory = multiprocessing.Value("i", 0)

# 向共享内存中写入数据
shared_memory.value = 42

# 从共享内存中读取数据
data = shared_memory.value
print("Received data:", data)
```
5. 信号量：

```python
import threading

# 创建一个信号量
semaphore = threading.Semaphore(0)

# 等待信号量
semaphore.acquire()

# 释放信号量
semaphore.release()
```

这些示例展示了如何在 Python 中使用不同的方法进行进程间通信。在实际应用中，可以根据需要选择合适的方法。

