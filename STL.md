
## 1. 六大组件
- 容器：类模板
- 算法：sort、search、copy、erase、next_permutation、partitio  函数模板
- 迭代器：泛型指针 operator* operator-> operator++ 
- 仿函数： 算法策略，operator()
- 容器配接器：queue、stack
- 空间配置器：内存管理

## 2. 各容器特点
1. vector: 底层数字，快速随机访问
- vector就是一个动态数组，由一片连续的内存组成，当空间放不下时，会申请另外一片更大的空间，一般是当前的50%（vs）或者100%（GCC），把原来的数据拷贝过去。当删除clear()的时候空间并不会释放只是清空了里面的数据。
- 内存移动后，对于原来的迭代器会失效。
- reserve和resize
  - reserve：能够增大capacity，但是不能改变size，并且不会创建对象，需要push_back添加元素，reserve小于capacity时不会减小capacity，不会减小size。reversize在扩大capacity时相对于resize能够精准控制增大的capacity数。
  - resize：能够增大capacity，同时改变size，因为添加了对象，参数可以指定添加具体对象值。resize不能减小capacity，但是能减小size，相对于reverse不能精准控制capacity的增大量(按编译器扩容)。
- 通过swap代替clear来释放空间, 相当于clear后再shrink_to_fit()
  - clear 清空内容，不释放内存
  - shrink_to_fit(): 请求降低capacity和size匹配
- 非线程安全

2. list：底层双向链表，支持快速增删
```cpp
list.push_back(elem)	在尾部加入一个数据
list.pop_back()	        删除尾部数据
list.push_front(elem)	在头部插入一个数据
list.pop_front()	    删除头部数据
list.size()	            返回容器中实际数据的个数
list.sort()             排序，默认由小到大 
list.unique()           移除数值相同的连续元素
list.back()             取尾部迭代器
list.erase(iterator)    删除一个元素，参数是迭代器，返回的是删除迭代器的下一个位置
```

3. deque：双端队列，收尾快速增删，支持随机访问。一个中央控制器和多个缓冲区
4. priority_queue：优先队列。堆实现。
```cpp
priority_queue<int, vector<int>, greater<int>> pq;   最小堆
priority_queue<int, vector<int>, less<int>> pq;      最大堆
pq.empty()   如果队列为空返回真
pq.pop()     删除对顶元素
pq.push(val) 加入一个元素
pq.size()    返回优先队列中拥有的元素个数
pq.top()     返回优先级最高的元素
```
5. array：固定大小数组，不能添加删除元素
6. map、set、multiset、multimap： 底层实现都是红黑树，map和set的增删改查速度为都是logn；map和set的插入删除效率比其他序列容器高，而且每次insert之后，以前保存的iterator不会失效，因为存储的是结点，不需要内存拷贝和内存移动。
   -  空间占用率高，因为map内部实现了红黑树，虽然提高了运行效率，但是因为每一个节点都需要额外保存父节点、孩子节点和红/黑性质，使得每一个节点都占用大量的空间
7. unordered_map、unordered_set：底层是一个防冗余的哈希表（采用除留余数法）。哈希表最大的优点，就是把数据的存储和查找消耗的时间大大降低，时间复杂度为O(1)；而代价仅仅是消耗比较多的内存。
- unordered_map 查找速度会比map快，而且查找速度基本和数据数据量大小，属于常数级别;而map的查找速度是log(n)级别。
- 哈希表的建立比较耗费时间, 内存消耗

## 其他
1. STL容器不保证线程安全，读是线程安全的，需要手动保证写线程安全。
2. 红黑树有什么性质？
```
1）每个结点是红色或者黑色。
2）根结点为黑色。
3）叶结点为黑色的NULL结点。
4）如果结点为红，其子节点必须为黑。
5）任一结点到NULL的任何路径，所含黑结点数必须相同。
```
