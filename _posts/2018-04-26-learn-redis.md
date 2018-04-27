---
layout: post
title:  "Redis实战学习摘录"
date:   2018-04-26 22:00:00 +0800
categories: Redis
---

最近在看《redis实战》，记录了书中的一些内容方便查阅。

## redis的适用场景

- 取最新 N 个数据的操作
- 排行榜应用，取 TOP N 操作
- 需要精准设定过期时间的应用
- 计数器应用
- Uniq 操作，获取某段时间所有数据排重值
- 实时系统，反垃圾系统
- Pub/Sub 构建实时消息系统
- 构建队列系统
- 缓存

## 记录

> setnx，如果 key 已经存在，返回 0，nx 是 not exist 的意思。

```
redis 127.0.0.1:6379> get name
"HongWan"
redis 127.0.0.1:6379> setnx name HongWan_new
(integer) 0
redis 127.0.0.1:6379> get name
"HongWan"
redis 127.0.0.1:6379>
```



> setex，设置 key 对应的值为 string 类型的 value，并指定此键值对应的有效期。

```
redis 127.0.0.1:6379> setex haircolor 10 red
OK
redis 127.0.0.1:6379> get haircolor
"red"
redis 127.0.0.1:6379> get haircolor
(nil)
redis 127.0.0.1:6379>
```

> setrange，设置指定 key 的 value 值的子字符串。
> 例如我们希望将 HongWan 的 126 邮箱替换为 gmail 邮箱，那么我们可以这样做:（其中的 8 是指从下标为 8（包含 8）的字符开始替换）

```
redis 127.0.0.1:6379> get name
"HongWan@126.com"
redis 127.0.0.1:6379> setrange name 8 gmail.com
(integer) 17
redis 127.0.0.1:6379> get name
"HongWan@gmail.com"
redis 127.0.0.1:6379>
```

> mset，一次设置多个 key 的值，成功返回 ok 表示所有的值都设置了，失败返回 0 表示没有任何值
> 被设置。

```
redis 127.0.0.1:6379> mset key1 HongWan1 key2 HongWan2
OK
redis 127.0.0.1:6379> get key1
"HongWan1"
redis 127.0.0.1:6379> get key2
"HongWan2"
redis 127.0.0.1:6379>
```

> getset，设置 key 的值，并返回 key 的旧值。

```
redis 127.0.0.1:6379> get name
"HongWan"
redis 127.0.0.1:6379> getset name HongWan_new
"HongWan"
redis 127.0.0.1:6379> get name
"HongWan_new"
redis 127.0.0.1:6379>
```

> incr，对 key 的值做加加操作,并返回新的值。注意 incr 一个不是 int 的 value 会返回错误，incr 一个不存在的 key，则设置 key 为 1

```
redis 127.0.0.1:6379> set age 20
OK
redis 127.0.0.1:6379> incr age
(integer) 21
redis 127.0.0.1:6379> get age
"21"
redis 127.0.0.1:6379>
```

> decr，对 key 的值做的是减减操作，decr 一个不存在 key，则设置 key 为-1

```
redis 127.0.0.1:6379> get age
"26"
redis 127.0.0.1:6379> decr age
(integer) 25
redis 127.0.0.1:6379> get age
"25"
redis 127.0.0.1:6379>
```

> append，给指定 key 的字符串值追加 value,返回新字符串值的长度。
> 例如我们向 name 的值追加一个@126.com 字符串，那么可以这样做:

```
redis 127.0.0.1:6379> append name @126.com
(integer) 15
redis 127.0.0.1:6379> get name
"HongWan@126.com"
redis 127.0.0.1:6379>
```

> strlen，取指定 key 的 value 值的长度。

```
redis 127.0.0.1:6379> get name
"HongWan_new"
redis 127.0.0.1:6379> strlen name
(integer) 11
redis 127.0.0.1:6379> get age
"15"
redis 127.0.0.1:6379> strlen age
(integer) 2
redis 127.0.0.1:6379>
```

## 常用命令

> keys *，返回满足给定 pattern 的所有 key

```
redis 127.0.0.1:6379> keys *
1) "myzset2"
2) "myzset3"
3) "mylist"
4) "myset2"
5) "myset3"
6) "myset4"
7) "k_zs_1"
8) "myset5"
9) "myset6"
10) "myset7"
11) "myhash"
12) "myzset"
13) "age"
14) "myset"
15) "mylist5"
16) "mylist6"
17) "mylist7"
18) "mylist8"
redis 127.0.0.1:6379> keys mylist*
1) "mylist"
2) "mylist5"
3) "mylist6"
4) "mylist7"
5) "mylist8"
redis 127.0.0.1:6379>
```

> exists，确认一个 key 是否存在

```
redis 127.0.0.1:6379> exists HongWan
(integer) 0
redis 127.0.0.1:6379> exists age
(integer) 1
redis 127.0.0.1:6379>
```

> del，删除一个 key

```
redis 127.0.0.1:6379> del age
(integer) 1
redis 127.0.0.1:6379> exists age
(integer) 0
redis 127.0.0.1:6379>
```

> expire，设置一个 key 的过期时间(单位:秒)

```
redis 127.0.0.1:6379> expire addr 10
(integer) 1
redis 127.0.0.1:6379> ttl addr
(integer) 8
redis 127.0.0.1:6379> ttl addr
(integer) 1
redis 127.0.0.1:6379> ttl addr
(integer) -1
redis 127.0.0.1:6379>
```

> move，将当前数据库中的 key 转移到其它数据库中

```
redis 127.0.0.1:6379> select 0
OK
redis 127.0.0.1:6379> set age 30
OK
redis 127.0.0.1:6379> get age
"30"
redis 127.0.0.1:6379> move age 1
(integer) 1
redis 127.0.0.1:6379> get age
(nil)
redis 127.0.0.1:6379> select 1
OK
redis 127.0.0.1:6379[1]> get age
"30"
redis 127.0.0.1:6379[1]>
```

> persist，移除给定 key 的过期时间

```
redis 127.0.0.1:6379[1]> expire age 300
(integer) 1
redis 127.0.0.1:6379[1]> ttl age
(integer) 294
redis 127.0.0.1:6379[1]> persist age
(integer) 1
redis 127.0.0.1:6379[1]> ttl age
(integer) -1
redis 127.0.0.1:6379[1]>
```

> randomkey，随机返回 key 空间的一个 key

```
redis 127.0.0.1:6379> randomkey
"mylist7"
redis 127.0.0.1:6379> randomkey
"mylist5"
redis 127.0.0.1:6379>
```

> rename，重命名 key

```
redis 127.0.0.1:6379[1]> keys *
1) "age"
redis 127.0.0.1:6379[1]> rename age age_new
OK
redis 127.0.0.1:6379[1]> keys *
1) "age_new"
redis 127.0.0.1:6379[1]>
```

> type，返回值的类型

```
redis 127.0.0.1:6379> type addr
string
redis 127.0.0.1:6379> type myzset2
zset
redis 127.0.0.1:6379> type mylist
list
redis 127.0.0.1:6379>
```