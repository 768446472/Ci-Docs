# 脉冲宽度调制输出(PWM)

***

## 简介

PWM(Pulse Width Modulation,脉冲宽度调制)是一种对模拟信号电平进行数字编码的方式，通过不同频率的脉冲，以及使用不同占空比的方波来对一个具体的模拟信号进行电平编码，使输出端得到一系列幅值相等的脉冲，用这些脉冲来代替所需要波形的设备。

***

## API

<center>

| 函数名       | 描述          |
| ------------ | ------------- |
| pwm_init     | 初始化PWM设备 |
| pwm_start    | 启动PWM设备   |
| pwm_stop     | 停止PWM设备   |
| pwm_set_duty | 设置PWM占空比 |

</center>

***

## 示例

以下代码配置并启动PWM0，%50占空比，1000频率

```c
Scu_SetDeviceGate(HAL_PWM0_BASE,ENABLE);
Scu_SetIOReuse(PWM0_PAD,SECOND_FUNCTION);
Scu_SetIOPull(PWM0_PAD,DISABLE);//关闭IO内部上下拉
pwm_init_t init;
init.freq = 1000;
init.duty = 50;
init.duty_max = 100;
pwm_init(PWM0,init);
pwm_stop(PWM0);
pwm_start(PWM0);
```

***

## 常见问题

- PWM频率与PWM最大占空比的关系

* 当freq为8200000Hz时，此时duty_max最大可配置为10
* 当freq为820000Hz时，此时duty_max最大可配置为100
* 当freq为82000Hz时，此时duty_max最大可配置为1000
