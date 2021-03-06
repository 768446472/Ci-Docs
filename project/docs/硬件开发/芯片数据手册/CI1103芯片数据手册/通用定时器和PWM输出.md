# 通用定时器和PWM输出

## 功能介绍

CI1103的通用定时器可产生PWM波输出以及定时器中断信号，两个定时器单元可独立作为单独的定时器工作也可以组合成一个级联的定时器。定时器单元进行32位定时器的递减计数，可产生周期性的中断或者PWM波形，两个定时器单元进行级联工作时，需将TIMER_UNIT_0的周期性的中断作为TIMER_UNIT_1的计数时钟。定时器单元从寄存器TIMER_SC递减TIMER_SPWMC时，PWM输出置高，递减到0时PWM输出置低，同时产生可配宽度的中断信号，每个定时器单元具有如下一些特征：

* 多种计数方式：单周期、自动重新开始以及自由计数模式
* PWM输出
* 计数时钟分频
* 级联模式
* 可产生周期性中断

CI1103有四个专用TIMER（TIMER0到TIMER3），六个专用PWM（PWM0到PWM5）。

## 寄存器映射

TIMER0 / 1 / 2 / 3寄存器映射的基地址分别为0x40036000、0x40037000、0x40038000、0x40039000，PWM0 / 1 / 2 / 3 / 4 / 5寄存器映射的基地址分别为0x40030000、0x40031000、0x40032000、0x40033000、0x40034000、0x40035000，具体的寄存器映射见表12。

<div align=center>表12 TIMER和PWM寄存器映射</div>

<center>

偏移量 | 名称 | 位宽 | 类型 | 复位值 | 描述
:--: | :--: | :--: | :--: | :--: | :--
0x00 | TIMER_CFG | 32 | R/W | 0x00000000 | 配置寄存器
0x04 | TIMER_CFG1 | 32 | R/W | 0x00000010 | 配置寄存器1
0x08 | TIMER_EW | 32 | R/W | 0x00000000 | 事件寄存器
0x0C | TIMER_SC | 32 | R/W | 0x00000000 | 周期寄存器
0x10 | TIMER_CC | 32 | RO | 0x00000000 | 计数值寄存器
0x14 | TIMER_SPWMC | 32 | R/W | 0x00000000 | PWM周期寄存器（PWM专用）
0x18 | TIMER_CFG0 | 32 | R/W | 0x00000000 | 配置寄存器0

</center>

## 配置寄存器（TIMER_CFG）

偏移量：0x00

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--
31:9 | Reserved | 0x00_0000 | R/W | 保留
8:7 | TM | 0x0 | R/W | 定时器中断信号宽度：<br>0x0：由TIMER_CFG1[CT]清除<br>0x1：2个时钟周期<br>0x2：4个时钟周期<br>0x3：8个时钟周期
6 | TP | 0 | R/W | 定时器中断极性：<br>0：高有效<br>1：低有效
5 | CS | 0 | R/W | 计数时钟源：<br>0：PCLK<br>1：EXT_CLK（专用PWM接PCLK）
4:2 | CM | 0x0 | R/W | 计数模式：<br>0x0：单周期模式<br>0x1：自动重新计数模式<br>0x2：自由计数模式<br>0x3：事件计数模式<br>0x4-0x7：预留
1:0 | TS | 0x0 | R/W | 计数时钟分频：<br>0x0：不分频<br>0x1：2分频<br>0x2：4分频<br>0x3：16分频

</center>

## 配置寄存器1（TIMER_CFG1）

偏移量：0x04

复位值：0x00000010

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--
31:4 | Reserved | 0x0000001 | R/W | 保留
3 | RU | 0 | R/W | TIMER_CC寄存器所保存的值：<br>0：该位置位前的计数值<br>1：当前计数值
2 | CT | 0 | R | 清除定时器中断：<br>0：无影响<br>1：清除定时器中断
1 | PC | 0 | R/W | 暂停计数：<br>0：正常计数<br>1：暂停计数
0 | RES | 0 | R | 重新计数：<br>0：无影响<br>1：从TIMER_SPWMC和TIMER_SC重载，递减计数

</center>

## 事件寄存器（TIMER_EW）

偏移量：0x08

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--
31:1 | Reserved | 0x0000000 | R/W | 保留
0 | EW | 0 | R/W | 事件计数重载，一个时钟周期后自清该位<br>事件计数模式：<br>0：无影响<br>1：计数器减1

</center>

## 周期寄存器（TIMER_SC）

偏移量：0x0C

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--
31:0 | TIMER_SC | 0x0000000 | R/W | 定时器周期值

</center>

## 计数值寄存器（TIMER_CC）

偏移量：0x10

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--
31:0 | TIMER_CC | 0x0000000 | R/W | 当前计数值

</center>

## PWM周期寄存器（TIMER_SPWMC）

偏移量：0x14

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--
31:0 | TIMER_SPWMC | 0x0000000 | R/W | PWM周期值

</center>

## 配置寄存器0（TIMER_CFG0）

偏移量：0x18

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--
31:3 | Reserved | 0x0000000 | R/W | 保留
2 | TRU_EN | 0 | R/W | 输入信号TIMER_RU使能：<br>0：TIMER_RU无效<br>1：TIMER_RU为1时，TIMER_CC更新
1 | Reversed | 0 | R/W | 保留
0 | TSEL_CLK | 0 | R/W | 计数时钟选择：<br>0：PCLK或者EXT_CLK<br>1：级联时钟

</center>