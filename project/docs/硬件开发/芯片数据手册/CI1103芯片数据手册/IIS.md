# IIS

## 功能介绍

CI1103带1路IIS1，IIS1可以用来对接外部16/24/32位立体声数字音频信号编解码电路。它能从外部audio device接收音频数据(如降噪芯片)，并将音频数据发送到芯片外部的audio device(如音频codec芯片)。

* 接收端特征如下：
    * 支持APB配置和AHB数据传输（总线和发送端共用）
    * 最大支持4声道数据的接收，每个通道均有通道使能用来选择是否进行接收数据
    * 支持16/24/32bit数据宽度
    * 支持IIS, MSB Justified, LSB Justified数据格式
    * 支持MSB first input接收
    * 支持DMA方式数据传输
    * 支持128/192/256/384FS
 * FIFO深度128，触发等级可配置

接收端和发送端共用MCLK，LRCK和SCK信号，所以要求接收端的数据宽度、采样率和过采样率和发送端一致。
 * 发送端特征如下：
    * 支持APB配置和AHB数据传输
    * 支持16/24/32bit数据宽度
    * 支持IIS, MSB Justified, LSB Justified数据格式
    * 支持MSB first output发送
    * 支持128/192/256/384FS
    * 支持DMA方式数据传输
    * 深度为16，宽度为32bit的发送FIFO
    * 支持立体声发送

IIS传输有专用的DMA通道，此处不进行详细描述，芯片配套开发包中将该功能完成，用户直接使用即可。

## 寄存器映射

IIS1寄存器映射的基地址分别为为0x40046000，详细的寄存器映射见表17。

<div align=center>表17 IISx寄存器映射</div>

<center>

偏移量 | 名称 | 位宽 | 类型 | 复位值 | 描述
:--: | :--: | :--: | :--: | :--: | :--:
0x00 | IISTXCTRL | 32 | R/W | 0x00000000 | 接收控制寄存器，配置接收通道的工作模式
0x04 | IISRXCTRL | 32 | R/W | 0x00000000 | 全局控制寄存器，配置控制器的工作模式
0x08 | IISGBCTRL | 32 | R/W | 0x00000000 | 发送控制寄存器，配置发送通道的工作模式
0x0C | IISCHSEL | 32 | R/W | 0x00000000 | 通道选择寄存器

</center>

## 发送控制寄存器（IISTXCTRL）

偏移量：0x00

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:7 | Reserved | 0x0000000 | R/W | 保留
6:4 | TXCHFL | 0x0 | R/W | 发送数据宽度，<br>000 = 16bit，<br>001 = 24bit，<br>010 = 32bit，<br>Other = Reversed，
3 | TXFFTRL | 0 | R/W | 发送FIFO触发等级配置<br>0 = 1/2空，1 = 1/4空
2:1 | TXCHNUM | 0x0 | R/W | 发送通道数配置<br>00 = 两声道
0 | TXEN | 0 | R/W | 发送通道使能<br>0 = 禁止，1 = 使能

</center>

## 接收控制寄存器（IISRXCTRL）

偏移量：0x04

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--
31:17 | Reserved | 0 | R/W | 保留
16 | RX_MODE  | 0 | R/W | 0 stereo/ 1 mono
15:9 | Reserved | 0 | R/W | 保留
8:6 | RXCHFL | 0x0 | R/W | 数据源数据宽度，<br>000 = 16bit，<br>001 = 24bit，<br>010 = 32bit，<br>Other = Reversed，
5:4 | RXFFTRL | 0x0 | R/W | 接收FIFO触发等级配置<br>00 = 1/4满，<br>01 = 1/8满<br>10 = 1/16满<br>11 = 1/32满
3:0 | RXEN | 0x0 | R/W | 接收通道使能<br>0000 = 所有接收通道均disable，对应的bit为1则对应通道被使能。例如：<br>Bit0对应channel 0。

</center>

## 全局控制寄存器（IISGBCTRL）

偏移量：0x08

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--
31:6 | Reserved | 0 | R/W | 保留
5 | BUSWID | 0 | R/W | IIS总线上SCK与LRCK的比例关系（master和slave都有效）<br>1-----SCK = 64*LRCK<br>0-----SCK = 32*LRCK
4:3 | RXDF | 0x0 | R/W | 接收数据格式选择，<br>00 = IIS格式，LRCK低为左声道，高为右声道；<br>01 = MSB Justified，10 = LSB Justified，这两种模式均是LRCK低为右声道，高为左声道。
2:1 | TXDF | 0x0 | R/W | 00 = IIS格式，LRCK低为左声道，高为右声道；<br>01 = MSB Justified，10 = LSB Justified，这两种模式均是LRCK低为右声道，高为左声道。
0 | GBEN | 0 | R/W | IIS控制器使能，<br>0 = 禁止，1 = 使能

</center>

## 通道选择寄存器（IISCHSEL）

偏移量：0x0C

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--
31:4 | Reserved | 0 | R/W | 保留
3 | INTER_EXTRX | 0 | R/W | 外部音频编解码器输入<br>1 = 选通外部音频设备的serial input接收<br>0 = 不使能外部音频设备的serial input接收
2 | Reserved | 0 | R/W | 保留
1 | INTER_EXTTX | 0 | R/W | 外部音频设备播放<br>1 = 选通外部音频设备进行音频播放<br>0 = 不将音频数据输出到外部音频设备
0 | Reserved | 0 | R/W | 保留

</center>