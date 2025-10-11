---
layout: page
title: Lab2
---
# Lab 2

## 数码管真值表

|字符|0|1|2|3|4|5|6|7|
|---|---|---|---|---|---|---|---|---|
|数值|0xC0|0xF9|0xA4|0xB0|0x99|0x92|0x82|0xF8|
|字符|8|9|A|B|C|D|E|F|
|数值|0x80|0x90|0x88|0x83|0xC6|0xA1|0x86|0x8E|

```c
// 7-segment display character map for hexadecimal digits 0-F
__code unsigned char LedChar[] = {
    0xC0, 0xF9, 0xA4, 0xB0,
    0x99, 0x92, 0x82, 0xF8,
    0x80, 0x90, 0x88, 0x83,
    0xC6, 0xA1, 0x86, 0x8E
};
```

## 数码管的静态显示

### Windows

```c
/*
 * File:   7segment_display.c
 * Author: Matt Lee
 * Description: Main program for LED control on 8052 microcontroller
 * Date:   2025-10-11
 */

#include <reg52.h>

// Define control pins for 7-segment display
sbit ADDR0 = P1^0;
sbit ADDR1 = P1^1;
sbit ADDR2 = P1^2;
sbit ADDR3 = P1^3;
sbit ENLED = P1^4;

// 7-segment display character map for hexadecimal digits 0-F
unsigned char code LedChar[] = {
    0xC0, 0xF9, 0xA4, 0xB0,
    0x99, 0x92, 0x82, 0xF8,
    0x80, 0x90, 0x88, 0x83,
    0xC6, 0xA1, 0x86, 0x8E
};

void main()
{
    unsigned char cnt = 0;  // Record T0 overflow count
    unsigned char sec = 0;  // Record seconds

    ENLED = 0;      // Enable U3
    ADDR3 = 1;      // Select U3
    // Select LEDS0
    ADDR2 = 0;
    ADDR1 = 0;
    ADDR0 = 0;
    TMOD = 0x01;    // Timer0, Mode 1 (16-bit)
    // Timer0 initial value for ~20ms at 11.0592MHz
    TH0 = 0xB8;
    TL0 = 0x00;
    TR0 = 1;        // Start Timer0

    while (1) {
        if (TF0) { // Check Timer0 overflow flag
            cnt++;
            TF0 = 0; // Clear overflow flag
            // Reload Timer0 initial value
            TH0 = 0xB8;
            TL0 = 0x00;
            if (cnt >= 50) {        // ~1 second
                cnt = 0;
                P0 = LedChar[sec];  // Switch LEDS0 state
                sec++;
                if (sec >= 16) {
                    sec = 0;
                }
            }
        }
    }
}
```

### macOS

```c
/*
 * File:   7segment_display.c
 * Author: Matt Lee
 * Description: Main program for LED control on 8052 microcontroller
 * Date:   2025-10-11
 */

#include <8052.h>

// Define control pins for 7-segment display
__sbit __at (0x90) ADDR0;   // P1^0
__sbit __at (0x91) ADDR1;   // P1^1
__sbit __at (0x92) ADDR2;   // P1^2
__sbit __at (0x93) ADDR3;   // P1^3
__sbit __at (0x94) ENLED;   // P1^4

// 7-segment display character map for hexadecimal digits 0-F
__code unsigned char LedChar[] = {
    0xC0, 0xF9, 0xA4, 0xB0,
    0x99, 0x92, 0x82, 0xF8,
    0x80, 0x90, 0x88, 0x83,
    0xC6, 0xA1, 0x86, 0x8E
};

void main()
{
    unsigned char cnt = 0;  // Record T0 overflow count
    unsigned char sec = 0;  // Record seconds

    ENLED = 0;      // Enable U3
    ADDR3 = 1;      // Select U3
    // Select LEDS0
    ADDR2 = 0;
    ADDR1 = 0;
    ADDR0 = 0;
    TMOD = 0x01;    // Timer0, Mode 1 (16-bit)
    // Timer0 initial value for ~20ms at 11.0592MHz
    TH0 = 0xB8;
    TL0 = 0x00;
    TR0 = 1;        // Start Timer0

    while (1) {
        if (TF0) { // Check Timer0 overflow flag
            cnt++;
            TF0 = 0; // Clear overflow flag
            // Reload Timer0 initial value
            TH0 = 0xB8;
            TL0 = 0x00;
            if (cnt >= 50) {        // ~1 second
                cnt = 0;
                P0 = LedChar[sec];  // Switch LEDS0 state
                sec++;
                if (sec >= 16) {
                    sec = 0;
                }
            }
        }
    }
}

// pio run -t upload
```

