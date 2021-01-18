# 通用串行外设接口（SPI）

***

## 简介

SPI是串行外设接口（Serial Peripheral Interface）的缩写，是一种高速的，全双工，同步的通信总线，并且在芯片的管脚上只占用四根线。通常由一个主模块和一个或多个从模块组成，主模块选择一个从模块进行同步通信，从而完成数据的交换。SPI是一个环形结构，通信时需要至少4根线（事实上在单向传输时3根线也可以）。它们是MISO（主设备数据输入）、MOSI（主设备数据输出）、SCLK（时钟）、CS（片选）。SPI主要特点有：可以同时发出和接收串行数据；可以当作主机或从机工作；提供频率可编程时钟；发送结束中断标志；写冲突保护；总线竞争保护等。

***

## API

<center>

| 函数名                              | 描述                                  |
| ------------------                  | --------------------                 |
|      normal_spi_hw_init             |   初始化通用SPI控制器                  |
|  normal_spi_clear_interrupt         |   清除SPI中断                         |
|   normal_spi_set_tx_level           |   设置SPI发送FIFO触发深度              |
|   wait_normal_spi_translate_flag    |     等待传输完成                       |
|    nomal_spi_interrupt_transfer     |     读取接收FIFO                       |
|  normal_spi_read_byte               |     从RX FIFO读取1字节数据             |
|   normal_spi_write_byte             |     向TX FIFO填充1字节数据             |
|   normal_spi_sr_status              |     查询SPI状态                        |
|  normal_spi_mask_intr               |     禁用对应中断使能                    |
|  normal_spi_rx_sample_delay         |     配置RX延迟采样                     |
|     normal_spi_dma_enable           |     DMA传输使能                        |
|  normal_spi_rxfifo_threshold_set    |     配置RX FIFO 触发深度               |
|  normal_spi_txfifo_threshold_set    |     配置TX FIFO 触发深度               |
|  normal_spi_set_cr0                 |     配置寄存器0                        |
|  normal_spi_slave_select            |     使能SLAVE外设                      |
|  normal_spi_set_clk                 |     配置分频                           |
|  normal_spi_enable                  |     使能/禁用通用SPI模块                |
|  normal_spi_set_cs_en_level                  |     SPI片选脚使能电平为高/低                |

</center>

***

## 示例
```c
/* 1、spi初始化 */
static void spi_init(void)
{
    uint32_t cr0;
    *(volatile unsigned int *)(0x40010000 + 0x8c) &= ~(0xf << 20);
    *(volatile unsigned int *)(0x40010000 + 0x8c) |= ((0x1 << 20) | (0x1 <<22));

    Scu_SetIOReuse(SPI1_CS_PAD,SECOND_FUNCTION);
    Scu_SetIOReuse(SPI1_DIN_PAD,SECOND_FUNCTION);
    Scu_SetIOReuse(SPI1_DOUT_PAD,SECOND_FUNCTION);
    Scu_SetIOReuse(SPI1_CLK_PAD,SECOND_FUNCTION);
    Scu_SetDeviceGate(HAL_SPI1_BASE,ENABLE);

    normal_spi_enable(NORMAL_SPI,0);
    normal_spi_set_clk(NORMAL_SPI,40000);    //时钟频率40K
    normal_spi_slave_select(NORMAL_SPI);

    cr0 = (DATA_FRAME_SIZE_8_BIT) | (SPI_MODE << NORMAL_SPI_FRF_OFFSET)
        | (SPI_MODE_0 << NORMAL_SPI_MODE_OFFSET) | (NORMAL_SPI_TMOD_TR << NORMAL_SPI_TMOD_OFFSET)
        | (0 << NORMAL_SPI_SLVOE_OFFSET) | (0 << NORMAL_SPI_SRL_OFFSET) | (1 << NORMAL_SPI_SSTE_OFFSET);            //SPI_MODE_0 为CPHA = 0, CPOL = 0。

    normal_spi_set_cr0(NORMAL_SPI,cr0);
    normal_spi_set_tx_level(NORMAL_SPI,0);
    /*normal_spi_rx_sample_delay(NORMAL_SPI,0x1); 若所接SPI设备时序有采样延时，需要进行延时，这里参数表示延时一个CLK。*/
    normal_spi_mask_intr(NORMAL_SPI,NORMAL_SPI_INT_MASK_ALL);
    normal_spi_enable(NORMAL_SPI,1);
}

/* 2、SPI写数据，默认MSB在前，若要LSB在前，转一下 */
uint8_t spi_io_writebyte(uint8_t Data)
{
    uint8_t tmp;
    /* Send the byte */
    while(!(normal_spi_sr_status(NORMAL_SPI) & (1 << 1)));
    normal_spi_write_byte(NORMAL_SPI,Data);
    while((normal_spi_sr_status(NORMAL_SPI) & (1 << 3)) == 0);
    tmp = (uint8_t)normal_spi_read_byte(NORMAL_SPI);
    return tmp;
}

/* 3、SPI读数据 */
uint8_t spi_io_readbyte()
{
    uint8_t tmp;
    tmp = spi_io_writebyte(0xFF);
    return tmp;
}

/* 4、测试 */

void spi1_test(void)
{
    uint8_t counter;
    spi_init();
    normal_spi_set_cs_en_level(1);     /*SPI片选脚使能电平为高*/
    for(;;)
    {
        for (counter = 0; counter <= 10; counter++)
        {                   
            spi_io_writebyte(counter);
        }
        vTaskDelay(pdMS_TO_TICKS(500));
    }
}
```


注意：更多示例可参考SDK下文件

driver\ci110x_chip_driver\src\ci110x_normal_spi_sd.c

driver\ci110x_chip_driver\src\ci110x_normal_spi_m01239.c