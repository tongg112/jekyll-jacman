---
layout: post
title:  "Redis实战学习摘录"
date:   2018-04-26 22:00:00 +0800
categories: Redis
---

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