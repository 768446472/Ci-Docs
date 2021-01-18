# DMA

DMA实现了一种无需CPU参与、完全依靠硬件的在外设及/或存储器之间传递数据的工作方式，从而极大地解放了CPU，提升了效率。通过DMA，系统可以在外设与存储器之间、存储器与存储器之间快速传输数据，无需CPU的任何干涉。

## 功能介绍

DMA控制器主要特征如下：

* 1个DMA通道，4个DMA请求，每个通道只支持单向传输
* 支持single请求和burst请求
* 支持存储器-存储器、存储器-外设、外设-存储器传输
* 通过使用链表，支持分散/连续地址的DMA传输
* 硬件DMA通道优先级，通道0具有最高优先级，通道2具有最低优先级
* 两个AHB总线master
* 支持DMA源地址与目的地址递增或不递增
* DMA burst size可配置
* 每个通道内部具有4字的FIFO
* 支持8-bit、16-bit以及32-bit宽度的传输
* DMA传输完成或者DMA传输错误产生中断请求
* DMA中断请求可屏蔽
* DMA屏蔽前中断请求状态可查询

为使DMA正常工作，软件配置时需满足下列配置顺序：

1. 配置DMACCxSrcAddr、DMACCxDestAddr、DMACCxLLI、DMACCxControl、DMACCxConfiguration等通道寄存器
2. 使能DMA通道
3. 使能DMA控制器

CI1122芯片的DMA支持1个通道，通道的source和destination可根据传输的方向配置，每个请求传输数据时使用哪个DMA通道由软件根据DMA控制器来配置决定。

DMA请求为burst请求，相关寄存器为DMACBREQ[15:0]。DMA请求可以通过软件（DMACSoftBReq、DMACSoftSReq）和硬件来产生。硬件DMA请求的分配如表8所示。

<div align=center>表8 DMA burst传输请求分配</div>

<center>

偏移量 | 名称 | 描述
:--: | :--: | :--:
0-3 | Reserved | 保留
4 | UART0_RX | UART0接收
5 | UART0_TX | UART0发送
6 | UART1_RX | UART1接收
7 | UART1_TX | UART1发送
8-15 | Reserved | 保留

</center>

## 寄存器映射

DMA控制器DMAC的寄存器映射基地址为0x40011000，详见表9。

<div align=center>表9 系统控制单元寄存器映射</div>

<center>

偏移量 | 名称 | 位宽 | 类型 | 复位值 | 描述
:--: | :--: | :--: | :--: | :--: | :--:
0x000 | DMACIntStatus | 8 | RO | 0x00 | 中断状态寄存器
0x004 | DMACIntTCStatus | 8 | RO | 0x00 | 传输计数中断状态寄存器
0x008 | DMACIntTCClear | 8 | WO | 0x00 | 传输计数中断清除寄存器
0x00C | DMACIntErrorStatus | 8 | RO | 0x00 | 传输错误中断状态寄存器
0x010 | DMACIntErrClr | 8 | WO | 0x00 | 传输错误中断清除寄存器
0x014 | DMACRawIntTCStatus | 8 | RO | 0x00 | 传输计数原始中断状态寄存器
0x018 | DMACRawIntErrorStatus | 8 | RO | 0x00 | 传输错误原始中断状态寄存器
0x01C | DMACEnbldChns | 8 | RO | 0x00 | 通道使能状态寄存器
0x030 | DMACConfiguration | 3 | R/W | 0x0 | 配置寄存器
0x100 | DMACCxSrcAddr | 32 | R/W | 0x00000000 | 通道源地址寄存器
0x104 | DMACCxDestAddr | 32 | R/W | 0x00000000 | 通道目的地址寄存器
0x108 | DMACCxLLI | 32 | R/W | 0x00000000 | 通道链表寄存器
0x10C | DMACCxControl | 32 | R/W | 0x00000000 | 通道控制寄存器
0x110 | DMACCxConfiguration | 20 | R/W | 0x00000 | 通道配置寄存器 

</center>

## 中断状态寄存器（DMACIntStatus）

偏移量：0x000

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:8 | Reserved | 0 | R | Reserved
7:0 | IntStatus | 0x00 | R | 掩蔽后DMA中断的状态，低3位有效，1表示发生中断

</center>

## 传输计数中断状态寄存器（DMACIntTCStatus）

偏移量：0x004

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:8 | Reserved | 0 | R | Reserved
7:0 | IntTCStatus | 0x00 | R | 中断终端计数请求状态，低3位有效，1表示发生传输计数中断

</center>

## 传输计数中断清除寄存器（DMACIntTCClear）

偏移量：0x008

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:8 | Reserved | 0 | W | Reserved
7:0 | IntTCClear | 0x00 | W | 终端计数请求清除，低3位有效，写1清除传输计数中断状态

</center>

## 传输错误中断状态寄存器（DMACIntErrorStatus）

偏移量：0x00C

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:8 | Reserved | 0 | R | Reserved
7:0 | IntErrorStatus | 0x00 | R | 中断错误状态，低3位有效，1表示发生传输错误中断

</center>

## 传输错误中断清除寄存器（DMACIntErrClr）

偏移量：0x010

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:8 | Reserved | 0 | W | Reserved
7:0 | IntErrClr | 0x00 | W | 中断错误清除，低3位有效，写1表示清除传输错误中断

</center>

## 传输计数原始中断状态寄存器（DMACRawIntTCStatus）

偏移量：0x014

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:8 | Reserved | 0 | R | Reserved
7:0 | RawIntTCStatus | 0x00 | R | 屏蔽前终端计数中断的状态，低3位有效，1表示发生传输计数原始中断

</center>

## 传输错误原始中断状态寄存器（DMACRawIntErrorStatus）

偏移量：0x018

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:8 | Reserved | 0 | R | Reserved
7:0 | RawIntErrorStatus | 0x00 | R | 掩蔽前错误中断的状态，低3位有效，1表示发生传输错误原始中断

</center>

## 通道使能状态寄存器（DMACEnbldChns）

偏移量：0x01C

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:8 | Reserved | 0 | R | Reserved
7:0 | EnabledChannels | 0x00 | R | 通道使能状态，低3位有效，1表示对应通道使能

</center>

## 配置寄存器（DMACConfiguration）

偏移量：0x030

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:1 | Reserved | 0 | R/W | Reserved
0 | E | 0 | R/W | DMAC使能:<br>0 = 不使能<br>1 = 使能<br>该位重置为0，禁用DMAC可降低功耗。

</center>

## 通道源地址寄存器（DMACCxSrcAddr）

偏移量：0x100

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:0 | SrcAddr | 0 | R/W | DMA源地址

</center>

## 通道目的地址寄存器（DMACCxDestAddr）

偏移量：0x104

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:0 | DestAddr | 0 | R/W | DMA目的地址

</center>

## 通道链表寄存器（DMACCxLLI）

偏移量：0x108

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:2 | LLI | 0x00000000 | R/W | 链接列表项。下一个LLI地址的位[31:2]，地址位[1:0]为0。
1 | Reserved | 0 | R/W | Reserved
0 | LM | 0 | R/W | AHB主选择用于加载下一个LLI<br>LM=0=AHB主1<br>LM=1=AHB主2

</center>

## 通道控制寄存器（DMACCxControl）

偏移量：0x10C

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31 | I | 0 | R/W | 终端计数中断启用位。它控制当前LLI是否会触发终端计数中断。
30:28 | Prot | 0x0 | R/W | 保护
27 | DI | 0 | R/W | 终端计数中断启用位。它控制当前LLI是否预期触发终端计数中断。目的地增量：每次传输后，目标地址都会递增。
26 | SI | 0 | R/W | 源增量。设置后，源地址在每次传输后递增。
25 | D | 0 | R/W | 目的地AHB主机选择：<br>0=为目的地传输选择AHB主机1<br>1=为目的地传输选择AHB主机2
24 | S | 0 | R/W | 源AHB主机选择：<br>0=源传输选择AHB主机1<br>1=源传输选择AHB主机2
23:21 | DWidth | 0x0 | R/W | 目标传输宽度。传输宽度超过AHB主总线宽度是非法的。源和目标宽度可以彼此不同。硬件会在需要时自动打包和解包数据。
20:18 | SWidth | 0x0 | R/W | 源传输宽度。传输宽度超过AHB主总线宽度是非法的。源和目标宽度可以彼此不同。硬件会在需要时自动打包和解包数据。
17:15 | DBSize | 0x0 | R/W | 源传输大小。传输大小超过AHB主总线大小是非法的。源和目标大小可以彼此不同。硬件会在需要时自动打包和解包数据。
14:12 | SBSize | 0x0 | R/W | 源突发大小。指示组成源突发的传输数。必须将此值设置为源外围设备的突发大小，如果源是内存，则设置为内存边界大小。
11:0 | TransferSize | 0x000 | R/W | 传输大小。当DMAC是流量控制器时，写入此字段可设置传输的大小。<br>从该字段读取的数据表示在目标总线上完成的传输数。当通道处于活动状态时读取寄存器并不能提供有用的信息，因为当软件处理读取的值时，通道可能已经进行了处理。您应该只在通道启用然后禁用时使用它。

</center>

## 通道配置寄存器（DMACCxConfiguration）

偏移量：0x110

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:19 | EN | 0x0000 | R/W | POR power down控制位<br>0：开启<br>1：关闭，POR_RESET=POR_VDD
18 | H | 0 | R/W | 暂停：<br>0=启用DMA请求<br>1=忽略额外的源DMA请求<br>通道FIFO的内容被排出。您可以将此值与活跃位和通道使能位一起使用，以完全禁用DMA通道。
17 | A | 0 | R | 活跃：<br>0=FIFO通道中没有数据<br>1=通道的FIFO有数据<br>您可以将此值与暂停位和通道使能位一起使用，以完全禁用DMA通道。
16 | L | 0 | R/W | 锁定：设置为1时，此位启用锁定传输。
15 | ITC | 0 | R/W | 终端计数中断掩码。清除时，该位屏蔽相关通道的终端计数中断。
14 | IE | 0 | R/W | 中断错误掩码。清除时，该位掩盖了相关通道的错误中断。
13:11 | FlowCntrl | 0x0 | R/W | 流量控制和传输类型。此值表示流量控制器和传输类型。流量控制器可以是DMAC、源外围设备或目标外围设备。传输类型可以是存储器到存储器、存储器到外围设备、外围设备到存储器或外围设备到外围设备。
10 | Reserved | 0 | R/W | Reserved
9:6 | DestPeripheral | 0x0 | R/W | 目标外围设备。此值选择DMA目标请求外设。如果传输的目标是内存，则忽略此字段。
5 | Reserved | 0 | R/W | Reserved
4:1 | SrcPeripheral | 0x0 | R/W | 目标外围设备。此值选择DMA目标请求外设。如果传输的目标是内存，则忽略此字段。
0 | E | 0 | R/W | 通道使能位。读取此位表示通道当前是否使能或不使能：<br>0=通道不使能<br>1=通道使能

</center>