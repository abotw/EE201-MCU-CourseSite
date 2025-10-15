---
title: "Lab3: Keys"
layout: page
parent: Labs
---

# Lab3: Keys
{:.no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 矩阵按键电路

<img src="labs/attachments/Pasted%20image%2020251014102242.png" alt="" srcset="{{ site.baseurl }}/labs/attachments/Pasted%20image%2020251014102242.png">

## P2

<img src="labs/attachments/Pasted%20image%2020251014102144.png" alt="" srcset="{{ site.baseurl }}/labs/attachments/Pasted%20image%2020251014102144.png">

```c
/* P2 */
__sbit __at (0xA0) P2_0 ; // KeyOut4
__sbit __at (0xA1) P2_1 ; // KeyOut3
__sbit __at (0xA2) P2_2 ; // KeyOut2
__sbit __at (0xA3) P2_3 ; // KeyOut1
__sbit __at (0xA4) P2_4 ; // KeyIn1
__sbit __at (0xA5) P2_5 ; // KeyIn2
__sbit __at (0xA6) P2_6 ; // KeyIn3
__sbit __at (0xA7) P2_7 ; // KeyIn4
```

## 按下即亮

```c
#include <8052.h>

// === Pin definitions ===
// P1.0 ~ P1.3: Address lines
__sbit __at (0x90) ADDR0; // P1.0
__sbit __at (0x91) ADDR1; // P1.1
__sbit __at (0x92) ADDR2; // P1.2
__sbit __at (0x93) ADDR3; // P1.3
__sbit __at (0x94) ENLED; // P1.4

// P0.4 ~ P0.7: LEDs
__sbit __at (0x87) LED9; // P0.7
__sbit __at (0x86) LED8; // P0.6
__sbit __at (0x85) LED7; // P0.5
__sbit __at (0x84) LED6; // P0.4

// P2.4 ~ P2.7: Keys
__sbit __at (0xA4) KEY1; // P2.4
__sbit __at (0xA5) KEY2; // P2.5
__sbit __at (0xA6) KEY3; // P2.6
__sbit __at (0xA7) KEY4; // P2.7

void main(void)
{
    ENLED = 0;         // Enable LEDs
    ADDR3 = 1;
    ADDR2 = 1;
    ADDR1 = 1;
    ADDR0 = 0;

    P2 = 0b11110111;   // Set P2 outputs high except P2.3 low

    while (1) {
        LED9 = KEY1;
        LED8 = KEY2;
        LED7 = KEY3;
        LED6 = KEY4;
    }
}
```

## 按键计数

```c
#include <8052.h>

__sbit __at (0x90) ADDR0; // P1.0
__sbit __at (0x91) ADDR1; // P1.1
__sbit __at (0x92) ADDR2; // P1.2
__sbit __at (0x93) ADDR3; // P1.3
__sbit __at (0x94) ENLED; // P1.4

__sbit __at (0xA4) KEY1; // P2.4
__sbit __at (0xA5) KEY2; // P2.5
__sbit __at (0xA6) KEY3; // P2.6
__sbit __at (0xA7) KEY4; // P2.7

__code unsigned char LedChar[ ] = {
    0xC0, 0xF9, 0xA4, 0xB0, 0x99, 0x92, 0x82, 0xF8,
    0x80, 0x90, 0x88, 0x83, 0xC6, 0xA1, 0x86, 0x8E
};

void delay() {
    unsigned int i = 1000;
    while (i--);
}

void main(void)
{
    __bit keybuf = 1;
    __bit backup = 1;
    unsigned char cnt = 0;

    ENLED = 0;
    ADDR3 = 1;
    ADDR2 = 0;
    ADDR1 = 0;
    ADDR0 = 0;

    P2 = 0xF7;
    P0 = LedChar[cnt];

    while (1) {
        keybuf = KEY4;
        if (keybuf != backup) {
            delay();
            if (keybuf == KEY4) {
                if (backup == 0) {
                    cnt++;
                    if (cnt > 9) {
                        cnt = 0;
                    }
                    P0 = LedChar[cnt];
                }
                backup = keybuf;
            }
        }
    }
}
```

## 矩阵键盘扫描 + 控制LED

```c
#include <reg52.h>

#define LED_PORT P0   // LED9-14 对应 P0.0~P0.5
#define KEY_PORT P2   // P2.0~P2.3 为列输出，P2.4~P2.7 为行输入

// 简单延时
void delay_ms(unsigned int ms)
{
    unsigned int i, j;
    for(i = 0; i < ms; i++)
        for(j = 0; j < 120; j++);
}

// 扫描矩阵键盘，返回按键编号（0~15），无按键返回0xFF
unsigned char key_scan()
{
    unsigned char col, row;
    unsigned char key_value = 0xFF;

    // 所有列输出高电平（未选中）
    KEY_PORT |= 0x0F;   // 低四位列线为输出
    for(col = 0; col < 4; col++)
    {
        // 设置列输出：当前列低，其余列高
        KEY_PORT = (KEY_PORT & 0xF0) | (~(1 << col) & 0x0F);
        delay_ms(2);  // 稳定时间

        // 读取行输入
        row = (KEY_PORT & 0xF0) >> 4;
        if(row != 0x0F)   // 检测到低电平
        {
            delay_ms(10); // 防抖
            row = (KEY_PORT & 0xF0) >> 4;
            if(row != 0x0F)
            {
                // 判断哪一行按下
                if((row & 0x01) == 0) key_value = 0 * 4 + col; // 第1行
                else if((row & 0x02) == 0) key_value = 1 * 4 + col; // 第2行
                else if((row & 0x04) == 0) key_value = 2 * 4 + col; // 第3行
                else if((row & 0x08) == 0) key_value = 3 * 4 + col; // 第4行

                while(((KEY_PORT & 0xF0) >> 4) != 0x0F);  // 等待松手
                return key_value;
            }
        }
    }

    return 0xFF; // 无按键
}

void main()
{
    unsigned char key;
    LED_PORT = 0xFF;  // 所有LED灭（假设低电平点亮）

    while(1)
    {
        key = key_scan();

        if(key != 0xFF)
        {
            // 根据行列号计算对应LED
            switch(key)
            {
                case 0: LED_PORT = ~(1 << 0); break; // R1C1 -> LED9 (P0.0)
                case 1: LED_PORT = ~(1 << 1); break; // R1C2 -> LED10 (P0.1)
                case 2: LED_PORT = ~(1 << 2); break; // R1C3 -> LED11 (P0.2)
                case 3: LED_PORT = ~(1 << 3); break; // R1C4 -> LED12 (P0.3)
                case 4: LED_PORT = ~(1 << 4); break; // R2C1 -> LED13 (P0.4)
                case 5: LED_PORT = ~(1 << 5); break; // R2C2 -> LED14 (P0.5)
                default: LED_PORT = 0xFF; break;     // 其他键不处理
            }
        }
    }
}

```



