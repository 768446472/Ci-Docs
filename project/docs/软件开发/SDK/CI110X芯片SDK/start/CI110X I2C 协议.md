# CI110X I2C 协议

***

## 1. 概述

本文档为CI(chipintelli) 标准语音模块I2C协议设计说明书，
方便开发快速开发代码和评审，维护人员了解代码框架。

### 1.1. 功能

* 完整传输包：寄存器地址，数据，校验，数据尾。

* 本司语音芯片IIC支持标准模式100 kbit/s、快速模式最高达400 kbit/s。该协议默认传输速率：100 kbit/s。

* CI110X系列芯片有2组IIC总线，分别为IIC0、IIC1。该协议默认使用IIC1总线。

* 默认引脚使用UART2_RX_PAD作SCL、UART2_RX_PAD作SDA。

* 本司语音模块的IIC总线地址（本司芯片做slave的IIC地址）默认均为0x64，如想修改，请配置对应总线编号的寄存器，0x64或上读写位后值为0xC8（写），0xC9（读）。

* 可更改配置选用不同的传输速率、总线编号和引脚、IIC总线地址。文档后续会介绍如何修改相关参数。

* 支持的功能：获取命令词id（8位，默认只支持非0命令词ID），用命令词id播放本地播报音，获取语义id（32位，默认只支持非0语义ID）），用语义id播放本地播报音。

* 约定本司语音模块为slave，发起请求的用户设备为master。

### 1.2. 性能

接收采用中断方式处理，输出采用中断方式处理。

***

## 2. 协议简介

### 2.1. 获取命令词id功能数据包定义

|    数据方向  |   字节数   |   byte1     |  byte2   |   byte3     |
| --------    |------------|------------ | -------- | --------   |
| master发送  | 1          |寄存器值    |          |             |
|  slave回复  | 3          |命令词id信息    |  校验和   | 数据尾      |

* 寄存器值：0x02
* 命令词id信息：命令词ID
* 校验和：寄存器值 + 命令词id信息 
* 数据尾：0x5a

!!! note "注意"
        默认只支持非0命令词ID，没有识别结果时，读取到的命令词id为0x00。

### 2.2. 用命令词id播放本地播报音功能数据包定义

|    数据方向  |  字节数   |   byte1    |  byte2  |   byte3  |  byte4   |
| ----------- |--------- |-------------| ------- |---------|-----------|
| master发送 |  4      | 寄存器值   | 命令词id信息|  校验和 |数据尾     |
| slave响应播报|          |             |         |         |          |

* 寄存器值：0x03
* 命令词id信息：获取命令词id数据包中解析出来的命令词id
* 校验和：寄存器值 + 命令词id信息 
* 数据尾：0x5a

### 2.3. 获取语义id功能数据包定义

|    数据方向  |   字节数   |   byte1     |  byte2   |   byte3    |   byte4   |   byte5    |   byte6  |
| --------    |------------|------------ | --------  | --------   |--------   |--------   |--------   |
| master发送  | 1          |寄存器值    |           |            |           |            |           |
|  slave回复  | 6          |语义id-1    |语义id-2   |语义id-3    |  语义id-4  | 校验和     |     数据尾 |

* 寄存器值：0x04
* 语义id_1 ：语义ID的第0 - 7位
* 语义id_2 ：语义ID的第8 - 15位
* 语义id_3 ：语义ID的第16 - 23位
* 语义id_4 ：语义ID的第24 - 31位
* 校验和：寄存器0x04 + 语义id_1 + 语义id_2 + 语义id_3 + 语义id_4 
* 数据尾：0x5a

!!! note "注意"
         默认只支持非0语义ID，没有识别结果时，读取到的语义id为0x00000000。

### 2.4. 用语义id播放本地播报音功能数据包定义

|    数据方向  |  字节数 |   byte1 | byte2   |   byte3    |   byte4   |   byte5    |   byte6  |   byte7  |
| --------    |------------|------------ | --------  | --------   |--------   |--------   |--------   |--------   |
| master发送  | 7          |寄存器值  |  语义id_1  |  语义id_2  |  语义id_3  |   语义id_4  |    校验和  |  数据尾   |
|  slave响应播报  |      |   |   |   |    |     |    |          |

* 寄存器值：0x05
* 语义id_1 ：语义ID的第0 - 7位
* 语义id_2 ：语义ID的第8 - 15位
* 语义id_3 ：语义ID的第16 - 23位
* 语义id_4 ：语义ID的第24 - 31位
* 校验和：寄存器值0x05 + 语义id_1 + 语义id_2 + 语义id_3 + 语义id_4
* 数据尾：0x5a

***

## 3. 配置相关

### 3.1. 修改协议开关宏

建议开发者在自己工程路径下进行以下配置，不要修改sdk_default_config.h文件。
打开SDK\sample\internal\sample_1102\src\user_config.h，把MSG_USE_I2C_EN配置为1。sample_1103工程同理。

```c
#define MSG_USE_I2C_EN                   1
/*引脚选择请参考表1-1*/
#define I2C_PROTOCOL_SCL_PAD             (UART2_RX_PAD)
#define I2C_PROTOCOL_SDA_PAD             (UART2_TX_PAD)
/*总线编号请参考表1-1，由引脚决定*/
#define I2C_PROTOCOL_HAL_NUM             (IIC1)    
/*标准模式100 Kbit/s，快速模式400 Kbit/s*/       
#define I2C_PROTOCOL_SPEED               (100)            
```

表 1-1
>| HAL_NUM |   SCL_PAD    | SDA_PAD       | 芯片型号         |
| --------|------------- | ------------- | ----------------|
|  IIC0   | AIN0_PAD     |  AIN1_PAD     | 1102、1103、1106 |
|  IIC0   | I2C0_SCL_PAD |  I2C0_SDA_PAD | 1102、1103、1106 |
|  IIC1   | UART1_RX_PAD |  UART1_TX_PAD | 1102、1103、1106 |
|  IIC1   | UART2_RX_PAD |  UART2_TX_PAD | 1102、1103、1106 |
|  IIC1   | PWM5_PAD     |  PWM4_PAD     | 1102、1103、1106 |
|  IIC1   | I2C1_SCL_PAD |  I2C1_SDA_PAD | 1106             |

!!! note "注意"
        HAL_NUM、SCL_PAD、SDA_PAD必须配对。修改时需确认引脚是否被占用。
        SDK里默认UART1被用作串口协议接口，如使用UART1用作IIC功能，需把下列宏置0。

```c
#define MSG_COM_USE_UART_EN       0   //1：开启CI串口协议，0：关闭
```

### 3.2. 修改功能代码宏

（1）、默认关闭调试语句，如需打开，修改i2c_protocol_module.c的调试宏为1。
```c
/* 0：关闭打印，1 ：打开打印 */
#define  IIC_PROTOCOL_DEBUG   1  
```

（2）、默认唤醒词命令词ID、语义ID定义如下，如需修改，打开i2c_protocol_module.c。
```c
#define  IIC_WAKEUP_WORD_CMDID     0x01          /* 唤醒词命令词ID：1个字节 */
#define  IIC_WAKEUP_WORD_SECID     0x01e05501    /* 唤醒词语义ID：4个字节*/
```

（3）、默认只开启命令词ID协议功能，关闭语义ID协议功能。如需打开语义ID协议功能，修改i2c_protocol_module.h的对应宏为1。两种协议可以同时使用，最快支持每隔30ms发起一次请求。
```c
/* 0：关闭命令词ID协议功能，1：开启命令词ID协议功能 */
#define  IIC_PROTOCOL_CMD_ID     1    
/* 0：关闭语义ID协议功能，1：开启语义ID的协议功能 */      
#define  IIC_PROTOCOL_SEC_ID     1            
```

（4）、默认主动播报唤醒词、欢迎词、退出唤醒词、其他命令词。如想屏蔽主动播报，使用协议数据被动播报，修改user_config.h相关宏为0。默认协议被动播报，只支持唤醒词和其他命令词。

```c
// 播报音配置
#define PLAY_WELCOME_EN        1 //欢迎词播报     =1是 =0否
#define PLAY_ENTER_WAKEUP_EN   0 //唤醒词播报     =1是 =0否
#define PLAY_EXIT_WAKEUP_EN    1 //退出唤醒播报   =1是 =0否
#define PLAY_OTHER_CMD_EN      0 //命令词播报     =1是 =0否          
```