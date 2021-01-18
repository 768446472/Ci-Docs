# 通用定时器(TIMER)

***

## 简介

TIMER(定时器)功能是在指定的时间间隔内反复触发指定窗口的定时器事件。

***

## API

<center>

| 函数名            | 描述                  |
| ----------------- | --------------------- |
| timer_init        | 初始化TIMER设备       |
| timer_start       | 启动TIMER设备         |
| timer_stop        | 停止TIMER设备         |
| timer_set_mode    | 设置TIMER设备计数模式 |
| timer_event_start | TIMER设备触发事件计数 |
| timer_set_count   | TIMER设备触发事件计数 |
| timer_get_count   | TIMER设备获取计数值   |
| timer_cascade_set | TIMER设备设置级联模式 |

</center>

***

## 示例

以下代码配置并启动TIMER0，1S来一次定时器中断

```c
void timer_s(uint32_t s)
{
    NVIC_EnableIRQ(TIMER0_IRQn);
    Scu_SetDeviceGate(HAL_TIMER0_BASE,ENABLE);
    timer_init_t init;
    init.mode = timer_count_mode_auto;
    init.div = timer_clk_div_0;
    init.width = timer_iqr_width_2;
    init.count = get_apb_clk() * s;
    timer_init(TIMER0,init);
    timer_start(TIMER0);
}
```

以下代码配置并启动TIMER0，1ms来一次定时器中断

```c
void timer_ms(uint32_t ms)
{
    NVIC_EnableIRQ(TIMER0_IRQn);
    Scu_SetDeviceGate(HAL_TIMER0_BASE,ENABLE);
    timer_init_t init;
    init.mode = timer_count_mode_auto;
    init.div = timer_clk_div_0;
    init.width = timer_iqr_width_2;
    init.count = （get_apb_clk() / 1000） * ms;
    timer_init(TIMER0,init);
    timer_start(TIMER0);
}
```

以下代码配置并启动TIMER0，1us来一次定时器中断

```c
void timer_us(uint32_t us)
{
    NVIC_EnableIRQ(TIMER0_IRQn);
    Scu_SetDeviceGate(HAL_TIMER0_BASE,ENABLE);
    timer_init_t init;
    init.mode = timer_count_mode_auto;
    init.div = timer_clk_div_0;
    init.width = timer_iqr_width_2;
    init.count = (get_apb_clk() / 1000000) * us;
    timer_init(TIMER0,init);
    timer_start(TIMER0);
}
```

!!! warning "警告"
    CI110x芯片内置32位计数器，计数范围：0x0 - 0xFFFFFFFF；如果需要更长时间的定时设置，需要对定时器进行分频操作，或使用级联模式。

***

## 常见问题

- TIMER定时范围(TIMER模块基础时钟CLK_S = 82000000Hz)

分频系数：DIV（取值范围：1，2，4，16）

计数时钟：CLK = CLK_S / DIV 				   单位：Hz

最小定时：MIN = 1s / CLK						  单位：S

最大定时：MAX = 0xFFFFFFFF * MIN		单位：S

以上做法，最大定时13分钟左右，若需要更长时间定时，请使用定时器级联模式。以下对级联模式进行描述：

<center>

| 基础定时器 | 级联定时器|
|------------|----------|
| TIMER0 | TIMER1\2\3 |
| TIMER1 | TIMER2\3 |
| TIMER2 | TIMER3 |

</center>

举个例子：
我们现在将TIMER0作为基础定时器，将时钟分频系数配置为16（这里追求定时最大化），TIMER1、TIMER2、TIMER3都配置为级联模式，且TIMER1、TIMER2、TIMER3的计数值都配置为最大值（0xFFFFFFFF）,那么每一个TIMER定时是多久呢？

<center>

| 定时器 | 定时时间 |
|--------|---------|
| TIMER0 | 13分钟左右 |
| TIMER1 | 0xFFFFFFFF * 13分钟 |
| TIMER2 | 0xFFFFFFFF * 0xFFFFFFFF * 13分钟 |
| TIMER3 | 0xFFFFFFFF * 0xFFFFFFFF * 0xFFFFFFFF * 13分钟 |

</center>
