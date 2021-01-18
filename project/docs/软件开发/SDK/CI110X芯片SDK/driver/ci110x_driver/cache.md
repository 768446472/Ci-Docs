# 高速缓冲存储器(CACHE)

***

## 简介

Cache是位于CPU与内存之间的缓存空间，其容量比内存小，但访问速度比内存快。Cache数据是内存中的一小部分，当CPU需要访问的数据位于Cache时，就可从相对较快的Cache中读取，而不用访问相对较慢的内存，从而提高访问速度。其工作原理是利用计算机程序对存储器访问行为的局部性原理，主要分两方面：1、时间局部性，即程序会在一个比较短的时间窗口内频繁访问同一个内存地址；2、空间局部性，即程序会倾向于访问一组数据或相邻的数据；按功能通常分为只读的指令Cache和可读可写的数据Cache。

***

## API

<center>

| 函数名             | 描述                 |
| ------------------ | -------------------- |
| cache_enable_auto  | 自动模式打开CACHE    |
| cache_disable_auto | 自动模式关闭CACHE    |
| get_hit_miss       | 获取CACHE的hit和miss |
| cache_alias_mode   | 配置CACHE的别名模式  |
| i_cache_tcm_enable | 配置ICACHE的TCM      |
| s_cache_tcm_enable | 配置SCACHE的TCM      |

</center>

注意：Cache只能在Cache处于关闭状态时才能配置。

***

## 示例

以下代码实现将Cache映射到PSRAM，最后跳转到PSRAM执行代码。

```c
/* Cache配置 */
cache_enable_auto(ICACHE,0x1FDB0000,0x1FFB0000);
cache_enable_auto(SCACHE,0x70200000,0x70800000);
/* PSRAM运行代码使能 */
Unlock_Ckconfig();
SCU->SYS_CTRL |= (0x1 << 20);
Lock_Ckconfig();
/* 跳转执行 */
mprintf("\033[32m Jump To PSRAM! \033[0m\n");
JmpAddr(0x1FDB0000);
```

***

## 常见问题

问：CI110X芯片有几个Cache每个有多大空间？

答：在CI110X芯片内只有两个CACHE，空间大小和读写属性如下表：

<center>

| Cache  | 大小 | 读写属性      |
| ------ | ---- | ------------- |
| ICache | 32K  | 只读指令Cache |
| SCache | 16K  | 读写数据Cache |

</center>

问：Cache对映射地址有没有对齐要求？

答：`Cache`对映射起始地址与映射终止地址都有对齐要求；两个地址都应当与`cacheline`的大小保持对齐，如下表：

<center>

| Cacheline | 大小   |
| --------- | ------ |
| 4         | 16byte |
| 5         | 32byte |
| 6         | 64byte |

</center>

注意：`cacheline`默认为4。
