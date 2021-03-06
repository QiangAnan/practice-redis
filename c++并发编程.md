
# <<c++并发编程>>

## 第一章: 你好，C++的并发世界

- 并发：两个或更多独立的活动同时发生。
  - 快速切换假并发：多个任务快速切换，快的看起来像是并发进行，实质某一刻只有一个任务被执行
  - 硬件并发：多核cpu并行多个任务。真实并发
  - **多进程并发**：`独立的进程可以通过进程间常规的通信渠道传递讯息(信号、套接字、文件、管道等等)。`不过，这种进程之间的通信通常不是设置复杂，就是速度慢，这是因为`操作系统会在进程间提供了一定的保护措施，以避免一个进程去修改另一个进程的数据`。还有一个缺点是，运行`多个进程所需的固定开销：需要时间启动进程，操作系统需要内部资源来管理进程，同时进程间切换也需要开销 等等`； 另外进程的好处在于`操作系统在进程间提供附加的保护操作和更高级别的通信机制`，意味着可以更容易编写安全的并发代码, 同时也支持`跨机并发`编程。
  - **多线程并发** : 每个线程相互独立运行，且线程可以在不同的指令序列中运行。但是，进程中的`所有线程都共享地址空间`，并且所有线程都能访问到大部分数据。地址空间共享，以及`缺少线程间数据的保护`，使得操作系统的记录工作量减小，所以使用多线程相关的开销远远小于使用多个进程。共享内存的多线程切换、操作系统对线程的管理开销虽然减小了，但是多线程间的数据使用时的互斥保证增加了线程的使用难度。
  
- 为什么使用并发？
  - 分离关注点：视频播放时影视和声音是两个线程并发执行。
  - 性能：任务并行、数据并行

- c++的并发和多线程
  - c++11以前，没有语言级别的跨平台多线程库，只能通过编译器提供api如posix标准的pthread，windows的Windows API、mfc框架等以及第三方库如boost、ace，导致c++多线程程序没有固定的标准，跨平台也不易。
  - c++11增加了`管理线程`, `保护共享数据`， `线程间同步操作`，`原子操作`， c++14增加了新的互斥量类型，c++17添加了一整套的并行算法

- 一个例子：
```cpp
#include <iostream>
#include <thread>  // 管理线程的函数和类
void hello()  
{
  std::cout << "Hello Concurrent World\n";
}
int main()
{
  std::thread t(hello);  // 启动一个新线程
  t.join();  // 主线程等待新线程结束 
}
```

---
## 第二章：线程管理
std::thread


`std::thread my_thread(do_some_work);` 声明对象即启动新线程，新线程启动地址为do_some_work
- 参数do_some_work可以是普通函数，也可以是函数对象(函数调用方式辅助记忆)
  - 传入函数对象时注意编译器可能认为是函数声明，如下
```cpp
class Test {
public:
    void opreator()() {
        do_something();
    }
};

Test t;
thread th(t); // 函数对象
thread th2(Test()); // 此时编译器可能解析成函数th2，返回thread，参数时函数指针,避免方式如下
thread th3(Test{}); // initialize方式初始化
```


`my_thread.join()`
- 如果一个thread对象在析构前没有调用join()或者detach(), 析构函数会自动调用std::terminate()触发异常。
- join
- detach
  - 注意detach后线程不要访问无效的局部变量。此时应该慎用指针和引用。
