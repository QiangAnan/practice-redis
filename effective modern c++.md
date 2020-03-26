
## 第一章：类型推导 
c++98类型推导：函数模板
c++11：auto、decltype
c++14：decltype(auto)  ^_^

### 条款1：理解模板类型推导 -- **<font color = red>需要多看两边记忆</font>**
```cpp
template<typename T>
void f(ParamType param);
f(expr);  // 描述expr推导T和ParamType的关系
```
- 模板T是普通引用
```cpp
template<typename T>
void f(T &param) {}
// T可以推导成普通类型和引用类型 int &param/ const int &param
```
- 模板T是通用引用
```cpp
template<typename T>
void f(T &&param) {}
// T可以推导成int, int&, const int& ==> int &param, int &&param, const int &param; 具体涉及到万能个引用，见条款24
```
- 模板T既不是指针也不是引用: 这就意味着，无论传入的是什么，param 都将生成一个拷贝
```cpp
template<typename T>
void f(T param) {}
// 普通的值传递，不具备&/const的推导，但是可以推导成一个char *,或者const char *；不能为char const *, 因为推导成指针时，param本身不能有const属性。
```
- 数组与指针的转换关系与类型推导
  - 数组引用也可以作为参数
```cpp

const char tt[22] = {0};

template<typename T>
void f1(T para){};
template<typename &T>
void f2(T &para){};

f1(tt); // T = const char *, 数组退化成指针
f2(tt); // T = const char(&)[], 数组的引用，并且带const属性

// ***利用数组引用的推导，可以在编译器通过模板推导得出数组的大小***
template<typename T, std::size_t N>
constexpr std::size_t arraySize(T(&)[N]) noexcept { // 返回值是constexpr
    return N;// 传入数组，推导出数组引用类型和数组大小
}
// 使用： 
int keyVlaue[] = {2, 3, 4, 5, 6};
arraySize(keyValue); // 推导出T是int，N是5

```
- 函数名与函数指针的转换
```cpp
// 模板函数
template<typename T>
void f(T param) {};
template<typename T>
void f2(T &param) {};
// 函数
void tt(int);
// 函数作为参数传入模板
f(tt); // T推导为 void (*)(int) 函数指针
f2(tt); // param ==> void (&)(int)  函数引用
```

### 条款2：理解auto类型推导
```cpp
auto x = 27; // auto -- int
const auto cx = x; // auot -- int or int&
const auto &rx = x; // auto -- int
auto &&ref1 = x; // auto -- int&
auto &&ref2 = cx;  // auto --const int &
auto &&ref3 = 27; // auto -- int

const char name[] = "xxxx";
auto arr1 = name; // auto -- const char *
auto &arr2 = name // auto -- const char (*)[]

void func(int);
auto f1 = func; // auto -- void (*)(int)
auto &f2 = func2; // auto -- void (&)(int)

// std::initializer_list<int>
auto x = 23; // auto -- int
auot x2(23); // same

auto x3 = {23}; // auot -- std::initializer_list<int>
auto x4{23}; // same
```
- <font color = red>模板类型推导和auto的区别</font>  -- std::initializer_list<T>的推导
  auto可以推导initializer_list<T>， 而 T param不行。但是当auto的作用和T作用类似时，不能推导成initializer_list<T>：如下函数返回类型，函数参数、lambda不行。
```cpp
auto func() {
    return {1, 2, 3}; // error auto 出错
}

void func(auto f) {  // error当成模板推导了
    return;
}

auto resetV = [&v](const auto& newValue) { v = newValue; }; // C++14…
resetV({ 1, 2, 3 }); // error! can't deduce type
```



--- 

## 第七章：并发API

- c++11提供的并发api是的c++第一次通过标准库实现跨平台。
- 提供的并发操作由 tasks、futures、threads、mutexes、condition、variables、atomic、objects等
  
### 条款35 优先使用task-based编程而非thread-based
