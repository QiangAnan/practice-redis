redis
### set key value
	set key value nx  不覆盖
	set key value xx  
	incr key   针对int类型value +1，原子操作
	incrby key incrValue  针对value +incrValue
	decr key
	decrby key decrValue
	
	getset key newValue 设置新值，返回老值
	
	mset key1 value1 key2 value2 key3 value3  multi设置key-value
	mget  key1 key2 key3 multi获取key-value
	
	exists key 存在key返回1，否则返回0
	del key 删除key
	type key 返回value类型
	
### expire
	expire key timelen key超时时间 
	pexpire key miliTimeLen 设置毫秒
	set key value ex timelen  设置key的同时指定超时时间
	ttl key 查看当前超时信息
	pttl key 可以查看毫秒
	persist key 超时失效失败，不进行超时失效处理

### list 通过链表实现，添加元素始终是固定的花销。可以模仿队列
	lpush mylist value 在链表mylist的头部插入value
	rpush mylist value 在链表mylist的尾部插入value
		lpush 和 rpush接收的value值可以是多个，这样可以同时调加多个元素进入list
	// 只能获取一个元素
	lrange mylist indexStart indexEnd 列出链表从start--end的元素
	rpop mylist 从list尾部弹出一个元素 
	lpop mylist 从list头部弹出一个元素
	ltrim mylist startIndex endIndex 从mylist中截取start--end中的元素
	brpop mylist seconds: block  rpop 如果list中没有元素，会阻塞seconds秒
	
### hash
	hset keyName field value
	hmset keyName field1 value1 dield2 value2...
	hget keyName field 
	hmget keyName field1 field2 
	hgetall keyName 

	hincrby keyName field incrValue  没有hincr
	
### set： unordered collections of strings
	sadd setName value1 value2 ...
	smembers setName 
	sismember setName value 
	spop setName 
	scard setName 求set长度
	srandmember setName valueNum  随机取出valueName个元素
		
### sorted set: unique, non-repeating string elements
	zadd setName score value ： score为排序的key值，如果key相同，则按照value顺序排，key和value相同时，插入失败
	zrange setName startIndx endIndex : zrange myset 0 -1 遍历整个set，只会返回value
	zrange setName startIndx endIndex withscores
	zrangebyscore setName -inf 1950 ： score小于1950的值
	zremrangebyscore setName scoreValue1 scoreValue2 : 删除score在scoreValue1和scoreValue2之间的数据个数
	zrank setName value : 返回value的rank排位
		

### 其他通用操作：
	del 
	llen
	type
	exists 当容器中没有元素时exist为0

	
	
	
---
	string 
		key -- value ： set key value / get key/ mset/mget
	list
		link set : lpush listName value ，rpush，lrange listName startIndex endIndex/ lpop/ rpop/ ltrim
	
	
	