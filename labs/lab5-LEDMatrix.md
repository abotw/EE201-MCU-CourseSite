---
title: "Lab5: LED Matrix"
layout: page
parent: Labs
---

# Lab5: LED Matrix
{:.no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Key Terms

- 动态扫描
- 动画

## Quick links

- [788BS datasheet](attachments/troyka-led-matrix_788bs_datasheet.pdf)
- [Control an 8x8 matrix of LEDs.](https://docs.arduino.cc/built-in-examples/display/RowColumnScanning/)
- [波特律动](https://led.baud-dance.com/)
- [LED Matrix Editor](https://xantorohara.github.io/led-matrix-editor/)

<img src="labs/attachments/Pasted%20image%2020251014140039.png" alt="" srcset="{{ site.baseurl }}/labs/attachments/Pasted%20image%2020251014140039.png">

## 8 * 8 LED Matrix Digits

```
0xC1,0x9C,0x8C,0x84,0x90,0x98,0xC1,0xFF, // 0
0xF3,0xF1,0xF3,0xF3,0xF3,0xF3,0xC0,0xFF, // 1
0xE1,0xCE,0xCF,0xE3,0xF9,0xCC,0xC0,0xFF, // 2
0xE1,0xCC,0xCF,0xE3,0xCF,0xCC,0xE1,0xFF, // 3
0xC7,0xC3,0xC9,0xCC,0x80,0xCF,0x87,0xFF, // 4
0xC0,0xFC,0xE0,0xCF,0xCF,0xCC,0xE1,0xFF, // 5
0xE3,0xF9,0xFC,0xE0,0xCC,0xCC,0xE1,0xFF, // 6
0xC0,0xCC,0xCF,0xE7,0xF3,0xF3,0xF3,0xFF, // 7
0xE1,0xCC,0xCC,0xE1,0xCC,0xCC,0xE1,0xFF, // 8
0xE1,0xCC,0xCC,0xC1,0xCF,0xE7,0xF1,0xFF, // 9
```

## 点亮一盏

```c
#include <8052.h>

__sbit __at (0x90) ADDR0; // P1.0
__sbit __at (0x91) ADDR1; // P1.1
__sbit __at (0x92) ADDR2; // P1.2
__sbit __at (0x93) ADDR3; // P1.3
__sbit __at (0x94) ENLED; // P1.4
__sbit __at (0x80) LED0;  // P0.0

void main(void)
{
    ENLED = 0;
    ADDR3 = 0;
    ADDR2 = 0;
    ADDR1 = 0;
    ADDR0 = 0;
    LED0 = 0;
    while (1);
}
```

## 全亮

```c
#include <8052.h>

__sbit __at (0x90) ADDR0; // P1.0
__sbit __at (0x91) ADDR1; // P1.1
__sbit __at (0x92) ADDR2; // P1.2
__sbit __at (0x93) ADDR3; // P1.3
__sbit __at (0x94) ENLED; // P1.4

void main(void)
{
    EA = 1; // Enable global interrupts
    ENLED = 0; // Enable 3-8 decoder
    ADDR3 = 0;
    TMOD = 0x01; // Timer 0, mode 1
    TH0 = 0xFC; // Load high byte for 1ms
    TL0 = 0x66; // Load low byte for 1ms
    ET0 = 1; // Enable Timer 0 interrupt
    TR0 = 1; // Start Timer 0
    while(1);
}

void timer0_ISR(void) __interrupt(1)
{
    static unsigned char i = 0;
    TH0 = 0xFC; // Reload high byte for 1ms
    TL0 = 0x66; // Reload low byte for 1ms
    P0 = 0xFF;
    switch (i) {
        case 0:
            ADDR2 = 0; ADDR1 = 0; ADDR0 = 0; i++; P0 = 0x00; break;
        case 1:
            ADDR2 = 0; ADDR1 = 0; ADDR0 = 1; i++; P0 = 0x00; break;
        case 2:
            ADDR2 = 0; ADDR1 = 1; ADDR0 = 0; i++; P0 = 0x00; break;
        case 3:
            ADDR2 = 0; ADDR1 = 1; ADDR0 = 1; i++; P0 = 0x00; break;
        case 4:
            ADDR2 = 1; ADDR1 = 0; ADDR0 = 0; i++; P0 = 0x00; break;
        case 5:
            ADDR2 = 1; ADDR1 = 0; ADDR0 = 1; i++; P0 = 0x00; break;
        case 6:
            ADDR2 = 1; ADDR1 = 1; ADDR0 = 0; i++; P0 = 0x00; break;
        case 7:
            ADDR2 = 1; ADDR1 = 1; ADDR0 = 1; i = 0; P0 = 0x00; break;
        default: break;
    }
}
```

## 点阵爱心

```c
#include <8052.h>

__sbit __at (0x90) ADDR0; // P1.0
__sbit __at (0x91) ADDR1; // P1.1
__sbit __at (0x92) ADDR2; // P1.2
__sbit __at (0x93) ADDR3; // P1.3
__sbit __at (0x94) ENLED; // P1.4

__code unsigned char image[] = { //图片的字模表
    0xFF, 0x99, 0x00, 0x00,
    0x00, 0x81, 0xC3, 0xE7
};

void main(void)
{
    EA = 1; // Enable global interrupts
    ENLED = 0; // Enable 3-8 decoder
    ADDR3 = 0;
    TMOD = 0x01; // Timer 0, mode 1
    TH0 = 0xFC; // Load high byte for 1ms
    TL0 = 0x66; // Load low byte for 1ms
    ET0 = 1; // Enable Timer 0 interrupt
    TR0 = 1; // Start Timer 0
    while(1);
}

void timer0_ISR(void) __interrupt(1)
{
    static unsigned char i = 0;
    TH0 = 0xFC; // Reload high byte for 1ms
    TL0 = 0x66; // Reload low byte for 1ms
    P0 = 0xFF;
    switch (i) {
        case 0:
            ADDR2 = 0; ADDR1 = 0; ADDR0 = 0; i++; P0 = image[0]; break;
        case 1:
            ADDR2 = 0; ADDR1 = 0; ADDR0 = 1; i++; P0 = image[1]; break;
        case 2:
            ADDR2 = 0; ADDR1 = 1; ADDR0 = 0; i++; P0 = image[2]; break;
        case 3:
            ADDR2 = 0; ADDR1 = 1; ADDR0 = 1; i++; P0 = image[3]; break;
        case 4:
            ADDR2 = 1; ADDR1 = 0; ADDR0 = 0; i++; P0 = image[4]; break;
        case 5:
            ADDR2 = 1; ADDR1 = 0; ADDR0 = 1; i++; P0 = image[5]; break;
        case 6:
            ADDR2 = 1; ADDR1 = 1; ADDR0 = 0; i++; P0 = image[6]; break;
        case 7:
            ADDR2 = 1; ADDR1 = 1; ADDR0 = 1; i = 0; P0 = image[7]; break;
        default: break;
    }
}
```

## ILoveU 纵向动画

```c
#include <8052.h>

__sbit __at (0x90) ADDR0; // P1.0
__sbit __at (0x91) ADDR1; // P1.1
__sbit __at (0x92) ADDR2; // P1.2
__sbit __at (0x93) ADDR3; // P1.3
__sbit __at (0x94) ENLED; // P1.4

__code unsigned char image [ ] = {  //图片的字模表
    0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF,
    0xC3, 0xE7, 0xE7, 0xE7, 0xE7, 0xE7, 0xC3, 0xFF,
    0x99, 0x00, 0x00, 0x00, 0x81, 0xC3, 0xE7, 0xFF,
    0x99, 0x99, 0x99, 0x99, 0x99, 0x81, 0xC3, 0xFF,
    0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF
};

void main(void)
{
    EA = 1; // Enable global interrupts
    ENLED = 0; // Enable 3-8 decoder
    ADDR3 = 0;
    TMOD = 0x01; // Timer 0, mode 1
    TH0 = 0xFC; // Load high byte for 1ms
    TL0 = 0x66; // Load low byte for 1ms
    ET0 = 1; // Enable Timer 0 interrupt
    TR0 = 1; // Start Timer 0
    while(1);
}

void timer0_ISR(void) __interrupt(1)
{
    static unsigned char i = 0;
    static unsigned char tmr = 0;
    static unsigned char index = 0;
    TH0 = 0xFC; // Reload high byte for 1ms
    TL0 = 0x66; // Reload low byte for 1ms
    P0 = 0xFF;
    switch (i) {
        case 0:
            ADDR2 = 0; ADDR1 = 0; ADDR0 = 0; i++; P0 = image[index+0]; break;
        case 1:
            ADDR2 = 0; ADDR1 = 0; ADDR0 = 1; i++; P0 = image[index+1]; break;
        case 2:
            ADDR2 = 0; ADDR1 = 1; ADDR0 = 0; i++; P0 = image[index+2]; break;
        case 3:
            ADDR2 = 0; ADDR1 = 1; ADDR0 = 1; i++; P0 = image[index+3]; break;
        case 4:
            ADDR2 = 1; ADDR1 = 0; ADDR0 = 0; i++; P0 = image[index+4]; break;
        case 5:
            ADDR2 = 1; ADDR1 = 0; ADDR0 = 1; i++; P0 = image[index+5]; break;
        case 6:
            ADDR2 = 1; ADDR1 = 1; ADDR0 = 0; i++; P0 = image[index+6]; break;
        case 7:
            ADDR2 = 1; ADDR1 = 1; ADDR0 = 1; i = 0; P0 = image[index+7]; break;
        default: break;
    }
    tmr++;
    if (tmr >= 125) { // Update image every 250ms
        tmr = 0;
        index++;
        if (index >= sizeof(image)) {
            index = 0;
        }
    }
}
```

## ILoveU 横向动画

```c
#include <8052.h>

__sbit __at (0x90) ADDR0; // P1.0
__sbit __at (0x91) ADDR1; // P1.1
__sbit __at (0x92) ADDR2; // P1.2
__sbit __at (0x93) ADDR3; // P1.3
__sbit __at (0x94) ENLED; // P1.4

__code unsigned char image [30] [8] = {
    {0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF,0xFF},  //动画帧 1
    {0xFF,0x7F,0xFF,0xFF,0xFF,0xFF,0xFF,0x7F}, //动画帧 2
    {0xFF,0x3F,0x7F,0x7F,0x7F,0x7F,0x7F,0x3F}, //动画帧 3
    {0xFF,0x1F,0x3F,0x3F,0x3F,0x3F,0x3F,0x1F}, //动画帧 4
    {0xFF,0x0F,0x9F,0x9F,0x9F,0x9F,0x9F,0x0F}, //动画帧 5
    {0xFF,0x87,0xCF,0xCF,0xCF,0xCF,0xCF,0x87}, //动画帧 6
    {0xFF,0xC3,0xE7,0xE7,0xE7,0xE7,0xE7,0xC3}, //动画帧 7
    {0xFF,0xE1,0x73,0x73,0x73,0xF3,0xF3,0xE1}, //动画帧 8
    {0xFF,0x70,0x39,0x39,0x39,0x79,0xF9,0xF0}, //动画帧 9
    {0xFF,0x38,0x1C,0x1C,0x1C,0x3C,0x7C,0xF8}, //动画帧 10
    {0xFF,0x9C,0x0E,0x0E,0x0E,0x1E,0x3E,0x7C}, //动画帧 11
    {0xFF,0xCE,0x07,0x07,0x07,0x0F,0x1F,0x3E}, //动画帧 12
    {0xFF,0x67,0x03,0x03,0x03,0x07,0x0F,0x9F}, //动画帧 13
    {0xFF,0x33,0x01,0x01,0x01,0x03,0x87,0xCF}, //动画帧 14
    {0xFF,0x99,0x00,0x00,0x00,0x81,0xC3,0xE7}, //动画帧 15
    {0xFF,0xCC,0x80,0x80,0x80,0xC0,0xE1,0xF3}, //动画帧 16
    {0xFF,0xE6,0xC0,0xC0,0xC0,0xE0,0xF0,0xF9}, //动画帧 17    
    {0xFF,0x73,0x60,0x60,0x60,0x70,0x78,0xFC}, //动画帧 18
    {0xFF,0x39,0x30,0x30,0x30,0x38,0x3C,0x7E}, //动画帧 19
    {0xFF,0x9C,0x98,0x98,0x98,0x9C,0x1E,0x3F}, //动画帧 20
    {0xFF,0xCE,0xCC,0xCC,0xCC,0xCE,0x0F,0x1F}, //动画帧 21
    {0xFF,0x67,0x66,0x66,0x66,0x67,0x07,0x0F}, //动画帧 22
    {0xFF,0x33,0x33,0x33,0x33,0x33,0x03,0x87}, //动画帧 23
    {0xFF,0x99,0x99,0x99,0x99,0x99,0x81,0xC3}, //动画帧 24
    {0xFF,0xCC,0xCC,0xCC,0xCC,0xCC,0xC0,0xE1}, //动画帧 25
    {0xFF,0xE6,0xE6,0xE6,0xE6,0xE6,0xE0,0xF0}, //动画帧 26
    {0xFF,0xF3,0xF3,0xF3,0xF3,0xF3,0xF0,0xF8}, //动画帧 27
    {0xFF,0xF9,0xF9,0xF9,0xF9,0xF9,0xF8,0xFC}, //动画帧 28
    {0xFF,0xFC,0xFC,0xFC,0xFC,0xFC,0xFC,0xFE}, //动画帧 29
    {0xFF,0xFE,0xFE,0xFE,0xFE,0xFE,0xFE,0xFF} //动画帧 30  
};

void main(void)
{
    EA = 1; // Enable global interrupts
    ENLED = 0; // Enable 3-8 decoder
    ADDR3 = 0;
    TMOD = 0x01; // Timer 0, mode 1
    TH0 = 0xFC; // Load high byte for 1ms
    TL0 = 0x66; // Load low byte for 1ms
    ET0 = 1; // Enable Timer 0 interrupt
    TR0 = 1; // Start Timer 0
    while(1);
}

void timer0_ISR(void) __interrupt(1)
{
    static unsigned char i = 0;
    static unsigned char tmr = 0;
    static unsigned char index = 0;
    TH0 = 0xFC; // Reload high byte for 1ms
    TL0 = 0x66; // Reload low byte for 1ms
    P0 = 0xFF;
    switch (i) {
        case 0:
            ADDR2 = 0; ADDR1 = 0; ADDR0 = 0; i++; P0 = image[index][0]; break;
        case 1:
            ADDR2 = 0; ADDR1 = 0; ADDR0 = 1; i++; P0 = image[index][1]; break;
        case 2:
            ADDR2 = 0; ADDR1 = 1; ADDR0 = 0; i++; P0 = image[index][2]; break;
        case 3:
            ADDR2 = 0; ADDR1 = 1; ADDR0 = 1; i++; P0 = image[index][3]; break;
        case 4:
            ADDR2 = 1; ADDR1 = 0; ADDR0 = 0; i++; P0 = image[index][4]; break;
        case 5:
            ADDR2 = 1; ADDR1 = 0; ADDR0 = 1; i++; P0 = image[index][5]; break;
        case 6:
            ADDR2 = 1; ADDR1 = 1; ADDR0 = 0; i++; P0 = image[index][6]; break;
        case 7:
            ADDR2 = 1; ADDR1 = 1; ADDR0 = 1; i = 0; P0 = image[index][7]; break;
        default: break;
    }
    tmr++;
    if (tmr >= 125) { // Update image every 250ms
        tmr = 0;
        index++;
        if (index >= sizeof(image)) {
            index = 0;
        }
    }
}
```

