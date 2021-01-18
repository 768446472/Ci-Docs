# 独立看门狗(IWDG)

***

## 简介

IWDG(独立看门狗)看门狗定时器是一种硬件定时电路，主要用于检测系统是否发生故障。独立看门狗模块基于一个32bit的递减计数器，使用独立于PLCK的时钟计数，当计数值达到0时，产生中断请求计数器重载初值再次进行递减计数，再次当计数值到0时若中断未被清除，将产生复位请求，同时计数器停止计数。

***

## API

<center>

| 函数名     | 描述           |
| ---------- | -------------- |
| iwdg_init  | 初始化IWDG设备 |
| iwdg_open  | 启动IWDG设备   |
| iwdg_close | 停止IWDG设备   |
| iwdg_feed  | IWDG设备喂狗   |

</center>

***

## 示例

以下代码配置并启动IWDG喂狗时间为1S

```c
NVIC_EnableIRQ(IWDG_IRQn);
/* 打开IWDG的复位配置 */
Scu_Iwdg_RstSys_Config();
/* 配置IWDG并启动 */
iwdg_init_t init;
init.irq = iwdg_irqen_enable;
init.res = iwdg_resen_enable;
init.count = get_ipcore_clk() / 16;
iwdg_init(IWDG,init);
iwdg_open(IWDG);
```
