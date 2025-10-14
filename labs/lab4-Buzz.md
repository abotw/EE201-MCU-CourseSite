---
title: "Lab4: Buzz"
layout: page
parent: Labs
---

# Lab4: Buzz

## 蜂鸣器

```c
#include <8052.h>

__sbit __at (0x96) BUZZ;  //蜂鸣器控制引脚

void main()
{
    EA = 1;       //使能总中断
    TMOD = 0x01;  //设置T0为模式1
    TH0  = 0xFC;  //为T0赋初值0xFC67，定时1ms
    TL0  = 0x67;
    ET0  = 1;     //使能T0中断
    TR0  = 1;     //启动T0
    BUZZ = 0;     //启动蜂鸣器鸣叫
    
    while (1);
}

/* T0中断服务函数，用于控制蜂鸣器发声 */
void InterruptTimer0() __interrupt(1)
{
    static unsigned int tmr = 0;

    TH0 = 0xFC;   //重新加载重载值
    TL0 = 0x67;
    tmr++;
    if (BUZZ == 0)
    {
        if (tmr >= 500)  //蜂鸣器鸣叫0.5s
        {
            BUZZ = 1;
            tmr = 0;
        }
    }
    else
    {
        if (tmr >= 1500)  //蜂鸣器关闭1.5s
        {
            BUZZ = 0;
            tmr = 0;
        }
    }
}
```

## 按键控制

```c
#include <8052.h>

__sbit __at (0x96) BUZZ; // P1.6
__sbit __at (0xA4) KEY1; // P2.4

void main()
{
    while (1) {
        P2 = 0b11110111;   // Set P2 outputs high except P2.3 low
        if (KEY1 == 0) {   // Check if KEY1 is pressed
            BUZZ = 0;      // Activate buzzer
        } else {
            BUZZ = 1;      // Deactivate buzzer
        }
    }
}
```

