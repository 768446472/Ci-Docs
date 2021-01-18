# 存储控制(FLASH CTL)

***

## 1. 概述

在CI112X系列芯片中，flash用于多种功能，包括固件存储、用户数据存储，通常情况下，用户数据存储应使用NVdata组件相关API完成，如需在开发调试过程中快速读写flash数据以及开发bootload程序时，则可能需要直接读写flash，但由于flash器件被m4内核和dnn硬件单元同时访问，故不能直接读写flash，因此这里提供了FLASH控制读写接口，通过这组接口可以安全可靠的获取总线控制，进而读写flash。

***

## 2. 使用说明

| 函数接口          | 说明              |
| ----------------- | ----------------- |
| post_write_flash  | 请求写flash       |
| post_read_flash   | 请求读flash       |
| post_erase_flash  | 请求擦除flash     |
| requset_flash_ctl | 请求flash总线使用 |
| release_flash_ctl | 释放flash总线使用 |

!!! warning "警告"
    一般情况下使用post_write_flash、post_read_flash、post_erase_flash接口进行操作即可完成flash数据相关需求，不推荐使用requset_flash_ctl、release_flash_ctl接口去控制flash，因为长期占有flash控制权会影响dnn进而影响识别效果。切记如需保存用户nv数据请使用NVdata组件相关API而非本组件。

使用示例：

```c
#define WAVE_ADDR  0x1000
char wav_format_head[44];

/* 读flash0x1000地址44个字节到wav_format_head内 */
post_read_flash(wav_format_head, WAVE_ADDR, sizeof(wav_format_head));
```
