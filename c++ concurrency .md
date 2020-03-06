
c++11  thread library
  process concurrency 
  thread concurrency
  
  why concurrency  
    不同关注点
    性能
    
  why not concurrency 
    线程并发收益比不上成本
    
  c++11以前是通过平台相关的api如posix标准中的pthread、windowd下的api，或者通过与平台无关的如ACE、boost实现并发多线程编程
  c++11后提供thread统一跨平台机制支持多线程、原子操作库
  
  #include<thread>  管理线程
  
  
  ### 线程管理： 
  - 启动线程
    每个线程有一个启动函数; 线程在终止之前必须以后join或者detach动作，否则线程析构的时候回调用std::terminate.
    
  - join线程
    一个线程只能join一次，join后joinable()返回false
  - detach线程
    线程detach后可能访问局部临时变量，这个时候要注意; 
    如果使用一个可调用的对象作为线程函数，这个对象就会复制到线程中，而后原始对象就会立即销毁。但对于对象中包含的指针和引用还需谨慎
  
  ```cpp
  std::thread my_thread(background_task()); // background_task为函数
  将函数对象传入thread构造函数时，有可能语法解析成 定义一个函数my_thread,参数为一个函数指针（函数指针指向没有参数并返回background_task对象的函数），使用下面方式初始化可以避免
  std::thread my_thread{background_task()};
  ```
  `确保thread对象被正确join，防止因异常的其他情况而未正常调用线程的join函数`
  ```cpp
  class thread_guard
{
  std::thread& t;
public:
  explicit thread_guard(std::thread& t_):
    t(t_)
  {}
  ~thread_guard()
  {
    if(t.joinable()) // 1
    {
      t.join();      // 2
    }
  }
  thread_guard(thread_guard const&)=delete;   // 3
  thread_guard& operator=(thread_guard const&)=delete;
};
  ```
    
