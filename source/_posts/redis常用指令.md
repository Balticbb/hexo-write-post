---
title: redis常用指令
date: 2018-03-08 13:49:40
tags:
---

##String 类型

1,设置值
        
        set key value
        
2,获取值

        get key
3,获取所有的key

        keys *
        
4,创建String

        setNx key value
        
5,删除

        del key
        
6,设置过期

        expire 时间戳
        
7,设置时间点过期

        expireAt 时间点
        
8,一次设置多个值

        mset key value key value
        
9,一次获取多个值

        mget key1 key2
##List

1,创建list或新增值

        lpush key value

2,获取list列表

        lrange key 0 -1
        
3,获取list某个下标值

        lindex key index
        
4,更新某个下标的值

        lset key index value
        
5,删除某个value

        lrem key count value   count>0从左往右第一个  count<0从右往左第一个 count=0匹配的所有value
        
##hashs
1,获取所有filed value
        
          hgetall key
           
2,判断某个field是否存在
        
        hexists key filed
        
3，获取所有的field       
        
         hkeys key
         
4,获取所有的value

        hvals key
        
5,hget hset hmget hmset
    
6,查看有多少字段

        hlen key
        
      
        
       

        