---
title: Lab3
layout: page
parent: Labs
---

# Lab3

![](labs/attachments/p1.png)

## P2

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

![](labs/attachments/Pasted%20image%2020251014102144.png)

![](labs/attachments/Pasted%20image%2020251014102242.png)

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

__code unsigned char LedChar[ ] = { //数码管显示字符转换表
    0xC0, 0xF9, 0xA4, 0xB0, 0x99, 0x92, 0x82, 0xF8,
    0x80, 0x90, 0x88, 0x83, 0xC6, 0xA1, 0x86, 0x8E
};

void main(void)
{
    __bit backup = 1;
    unsigned char cnt = 0;
    ENLED = 0;
    ADDR3 = 1;
    ADDR2 = 0;
    ADDR1 = 0;
    ADDR0 = 0;

    P2 = 0b11110111;   // Set P2 outputs high except P2.3 low
    P0 = LedChar[0]; // Display '0' on 7-segment

    while (1) {
        if (KEY4 != backup) {
            if (backup == 0) { // Key pressed
                cnt++;
                if (cnt >= 10) {
                    cnt = 0;
                }
                P0 = LedChar[cnt]; // Update display
            }
            backup = KEY4;
        }
    }
}
```



