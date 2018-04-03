---
title: redis在项目中使用场景
date: 2018-01-25 19:48:32
tags: [Redis]
categories: work
---

在我们商城中首页中有大量的分类广告，并且网站的首页会有很大的访问量，如果每次访问都查询数据库，数据库的压力会变的很大，性能也会大大降低，经常会有卡顿。所以需要引入redis持久化缓存方案！

<!-- more -->

## 一、pom依赖和配置
[参见前面spring-data-redis的使用](https://leahshi.github.io/2018/01/18/spring-data-redis%E4%BD%BF%E7%94%A8/)；需要配置jedis连接池、连接工厂、redisTemplate模板等

## 二、redis缓存方案（以首页广告为例）
### 1、明确存储数据类型：
因为首页广告有很多分类，并且每个分类中有很多条广告，所以采用的是Hash数据结构,Hash的key是广告的分类id,存储在redis中key命名为content.主要思想就是查询的时候做缓存，执行增删改从操作时候清空缓存。


### 2、查询：
在根据分类广告的查询方法中，首先判断redis缓存中是否存在key为content的value，如果存在直接返回即可，如果不存在走数据库查询。注意使用redisTemplate连接redis，需要try...catch...否则页面中为空，影响用户体验！
```
@Override
public List<TbContent> findContentByCategoryId(Long categoryId) {
    // 判断缓存是否为空，不为空优先从缓存中取
    List<TbContent> contents = null;
    try {
        contents = (List<TbContent>) redisTemplate.boundHashOps("content").get(categoryId);
        // 如果不为空 直接从缓存中取
        if(contents!=null){
            return contents;
        }
    } catch (Exception e) {
        e.printStackTrace();
    }

    if(contents==null){
        // 缓存为空从数据库中取
        contents = contentMapper.findContentByCategoryId(categoryId);
    }

    try {
        redisTemplate.boundHashOps("content").put(categoryId,contents);
    } catch (Exception e) {
        e.printStackTrace();
    }

    return contents;
}
```


### 3、修改：
在后台管理系统修改了广告的信息后，需要考虑两个方面，一是广告的分类id没有被修改，另方面分类id没有变化。

```
@Override
public void update(TbContent content){
    // 判断是否修改了广告分类
    Long categoryIdPresist = contentMapper.selectByPrimaryKey(content.getId()).getCategoryId();
    // 修改了分类
    if(categoryIdPresist.longValue() != content.getCategoryId().longValue()){
        // 因为两个分类都有变化 所以需要都清除
        redisTemplate.boundHashOps("content").delete(content.getCategoryId());
    }

    // 无论是否修改了分类，都需要将原来分类数据清空
    redisTemplate.boundHashOps("content").delete(categoryIdPresist);

    contentMapper.updateByPrimaryKey(content);
}
```

### 4、删除和新增
按照广告分类id，将缓存清空即可，注意执行删除操作时候，需要将清空缓存操作放在前面，否则分类id无法被获取到！

## 三、redis存储时效性数据
在有些系统中用户注册完毕之后，需要向用户邮箱发送激活账户的邮件，如果三天之内没有验证，就会失效，重新验证用户信息。这是可以使用到redis存储值的时效性做到，将用户手机号和随机激活码作为key和value，并设置失效时间即可做到

## 四、计数器
redis是单线程的服务器，并且可以处理50万的并发量（并不是同时进行，而是类似队列快速运行），redis中提供了incr和decr的方法，在原有的数值上+1和-1操作，这两个方法在一些场景中很有用，比如在数据库某些表数据上万条级别时，经常需要进行分片操作，数据库分片之后对于主键的控制为了避免主键并发重复可以使用redis来控制；
同时在商城的秒杀模块，需要记录商品的剩余量，可以将商品剩余量存到redis中，利用redis的单线程特性，即使存在并发抢单的情况下，redis使用decr方法来记录剩余量。注意：redis是内存服务器，需要将被持久化的数据持久化到硬盘中！！






