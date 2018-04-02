---
title: redis五种数据类型及应用场景
date: 2018-01-15 19:48:32
tags: [Redis]
categories: work
---

redis作为nosql数据库的一员，主要是以key-value方式存储，尽管key只能是字符串类型，但是value可以有五种类型，分别是String、Hash、List、Set、zset，此篇主要总结五种数据类型存储方式，以及应用场景、常见命令等。

<!-- more-->

## 五种数据类型的特点
- String类型
- Hash类似map集合，小key（value中的key）不能重复，适用于json对象
- List类似list集合，有序可以重复，但是值得注意的是，List类型分为左压栈和右压栈，左压栈相当于栈的数据结构，有压栈相当于队列的数据结构，所以这种特性决定了存储的先后的，取值的顺序不同。比如左压栈存的是1,2,3,4 取的是4,3,2,1;而右压栈存的是1,2,3取的是1,2,3
- Set类型，类似hashset集合，无序，不可重复；只是Set类型只能放字符串
- ZSet类型，拥有Set和List的特点，有序，不可重复


## 常见命令
 
- 字符串操作  
    set/get								存值取值，单值操作
    mset/mget							存取多个值
    incr/decr key                       加一减一操作【常用分表分片主键生成策略等】
    setrange/getrange key offset value     setrange 方法 如果被替换的字符串长度没有被替换的长，原来被替换的剩余部分将会被保存
                                        username abcdefgh; setrange username 7 "a"        //abcdefah
    getrange key start end 				获取key从索引start到end之间的字符串
			
- list 操作  
    链表底层是数组所以使用push:
    lpush key value1 value2...		//在指定链表key中的前面插入值
    rpush key value1 value2			//在后面插入值
    
    lrange key 0 -1 				//获取所有集合数据
    lpushx key value 				//只有key存在，且对应的key中没有插入的value时 返回插入 链表前面元素的数量，否则失败返回0
    rpushx key value
    
    lpop key						//删除链表数据的第一个并返回，如果没有key就返回nil
    rpop key						//删除最后个并返回
    
    llen key 						//返回链表数据的个数，如果没有指定的key返回0
    lrem key count value			//删除链表中count个value值
    lset key index value 			//在链表key中指定索引index位置插入value
    lindex key index 				//返回链表key索引为index的值
    ltrim key start stop 			//保留链表key 索引为start-stop之间的值【包括start和stop】
    
    rpoplpush list01 list02			//删除list01中最后个元素插入到链表list02头中
        
- hash 操作 键值对
    hset student sid 001
    hget student
    hmset/hmget
    hsetnx
    hexists
    hlen
    hincrby
    hgetall/hkeys/hvals				//获取所有键值对、获取所有key、获取所有值
    hdel key field..				//一次性删除多个字段
        
- set 操作	 无序去重、【聚合】
    sadd key value...					//向key中插入值
    scard key							//返回成员数量
    smembers key						//获取所有成员
    sismembers key member				//判断member是否为key中的值
    spop key							//删除key中第一个值并返回
    srem key member member..			//批量删除多个值
    srandmember key count				//count可省  随机获取key中count个成员
    smove set01 set02 member			//将member从set01移动到set02
    
    【聚合】
        求差集和并集时，key1和key2位置不空结果不同
    sdiff   key1 key2...  				//差集  
    sunion  key1 key2...  				//并集
    sinter  key1 key2...  				//交集
    
    sdiffstore newkey key1 key2...		//将差集存储到新的newkey中
        
- ZSet 
    ZADD key score member [score] [member]
    ZINCRBY key increment member
    ZCARD key
    ZCOUNT key min max	
    ZRANGE key start stop [WITHSCORES]
    ZREVRANGE key start stop [WITHSCORES]
    ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]
    ZREVRANGEBYSCORE key max min [WITHSCORES] [LIMIT offset count]
    ZRANK key member
    ZREVRANK key member
    ZSCORE key member
    ZREM key member [member ...]
    ZREMRANGEBYRANK key start stop 
    ZREMRANGEBYSCORE key min max
        
- 其他
    keys * 						//查找redis数据库中所有的key值
    del key1 key2... 			//批量删除多个值
    exists key  				//判断值是否存在 1 存在 0 不存在
    rename key newkey			//修改名称

    expire key seconds 			//为key设置过期时间 s为单位 
    ttl key 					//查看key过期剩余时间 当 key 不存在时,返回 -2。当 key 存在但没有设置剩余生存时间时,返回 -1 
    persist key					//持久化存储，消除过期时间
    
    MOVE key db 				//redis.conf配置文件中定义了redis默认库为16个
    randomkey					//随机返回一个key
    type key					//返回key类型 string list hash set sortedSet
    
- 事务
    开启事务 multi
    取消事务 discard
    退出	exec






