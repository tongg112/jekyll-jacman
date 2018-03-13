---
layout: post
title:  "php-fpm优化分享"
date:   2018-03-13 18:18:09 +0800
---
|                                          | Static                                   | Dynamic                                  | Ondemand                                 |
| ---------------------------------------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| php-fpm的工作模式和nginx类似，都是一个master，多个worker模型。 | php-fpm启动采用固定大小数量的worker，在运行期间也不会扩容，由pm.max_children指定固定的子进程数 | 在php-fpm启动时，会初始启动一些worker，在运行过程中动态调整worker数量，worker的数量受限于pm.max_children配置，同时受限全局配置process.max | 在php-fpm启动的时候，不会给这个pool启动任何一个worker，是按需启动，当有连接过来才会启动。 |

> pm = dynamic  
>
> m.process_idle_timeout = 120s  
>
>
> m.max_children = 360 子进程最大数  
>
> m.start_servers = 180 启动时的进程数  
>
> m.min_spare_servers = 120 保证空闲min  
>
> m.max_spare_servers = 240 保证空闲max  
>
> m.max_requests = 40960  



- 具体的业务需要反复的调整才能找到对应的最优
- 压测时，曾经发现每个php-fpm子进程占用内存高达500m，那么就是需要优化程序逻辑了。
- 原则上，得出每个进程占用的最大内存值，然后再计算出参数的值，进行压测，反复调整得出满意的结果。
- 可分配给php-fpm的内存/每个php-fpm子进程占用的内存=最大子进程数目




参考链接：https://secure.php.net/manual/zh/install.fpm.configuration.php
