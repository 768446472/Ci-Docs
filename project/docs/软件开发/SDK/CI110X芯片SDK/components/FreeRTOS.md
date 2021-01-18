# 实时操作系统(FreeRTOS)

***

## 1. 概述

FreeRTOS是一款著名的开源免费实时操作系统，目前已作为CI110XSDK的默认OS使用。其内核轻巧，稳定，接口丰富，市场占有率较高。CI110X集成了最新的FreeRTOSV10版本，相较以前的V8版本，新增了邮箱、数据缓冲流等新组件接口。

!!! important "提示"
    更多FreeRTOS信息请查看 ☞[FreeRTOS官网](https://www.freertos.org/index.html)获取帮助。

***

## 2. 使用说明

在使用FreeRTOS的APi函数时，需要包含FreeRTOS.h和对应的模块头文件，在CI110SDK中应添加一级路径，如下所示：

```c
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
```

FreeRTOSConfig.h文件内包含OS内核的一些配置项，这里针对具有开发需求配置做以说明：
