---
title: "Lab2: 7SegDT"
layout: page
parent: Labs
---

# Lab 2: 数码管
{:.no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

![](labs/attachments/Pasted%20image%2020251014113332.png)

## 数码管真值表

| 字符  | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    |
| --- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 数值  | 0xC0 | 0xF9 | 0xA4 | 0xB0 | 0x99 | 0x92 | 0x82 | 0xF8 |

| 字符  | 8    | 9    | A    | B    | C    | D    | E    | F    |
| --- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 数值  | 0x80 | 0x90 | 0x88 | 0x83 | 0xC6 | 0xA1 | 0x86 | 0x8E |

```c
// 7-segment display character map for hexadecimal digits 0-F
__code unsigned char LedChar[] = {
    0xC0, 0xF9, 0xA4, 0xB0,
    0x99, 0x92, 0x82, 0xF8,
    0x80, 0x90, 0x88, 0x83,
    0xC6, 0xA1, 0x86, 0x8E
};
```

## 数码管静态显示

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

## 数码管动态显示

### macOS

```c
/*
 * File:   7segment_display_dynamic.c
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

// Buffer for 6 digits of the 7-segment display
unsigned char LedBuff[6] = {
    0xFF, 0xFF, 0xFF,
    0xFF, 0xFF, 0xFF
};

// Function to scan and update the 7-segment display
void DisplayScan(void) {
    static unsigned char i = 0;
    switch (i) {
        case 0: ADDR2=0;ADDR1=0;ADDR0=0;P0=LedBuff[0]; break;
        case 1: ADDR2=0;ADDR1=0;ADDR0=1;P0=LedBuff[1]; break;
        case 2: ADDR2=0;ADDR1=1;ADDR0=0;P0=LedBuff[2]; break;
        case 3: ADDR2=0;ADDR1=1;ADDR0=1;P0=LedBuff[3]; break;
        case 4: ADDR2=1;ADDR1=0;ADDR0=0;P0=LedBuff[4]; break;
        case 5: ADDR2=1;ADDR1=0;ADDR0=1;P0=LedBuff[5]; break;
    }
    i++;
    if (i >= 6) i = 0;
}

void main()
{
    unsigned char i = 0;    // Loop variable
    unsigned int cnt = 0;  // Record T0 overflow count
    unsigned long sec = 0;  // Record seconds

    ENLED = 0;      // Enable U3
    ADDR3 = 1;      // Select U3
    TMOD = 0x01;    // Timer0, Mode 1 (16-bit)
    // Timer0 initial value for ~1ms at 11.0592MHz
    TH0 = 0xFC;
    TL0 = 0x67;
    TR0 = 1;        // Start Timer0

    while (1) {
        if (TF0) { // Check Timer0 overflow flag
            cnt++;
            TF0 = 0; // Clear overflow flag
            // Reload Timer0 initial value
            TH0 = 0xFC;
            TL0 = 0x67;
            if (cnt >= 1000) {        // ~1 second
                cnt = 0;
                sec++;
                LedBuff[0] = LedChar[sec % 10];          // Units
                LedBuff[1] = LedChar[(sec / 10) % 10];   // Tens
                LedBuff[2] = LedChar[(sec / 100) % 10];  // Hundreds
                LedBuff[3] = LedChar[(sec / 1000) % 10]; // Thousands
                LedBuff[4] = LedChar[(sec / 10000) % 10]; // Ten-thousands
                LedBuff[5] = LedChar[(sec / 100000) % 10]; // Hundred-thousands
                if (sec >= 999999) {
                    sec = 0; // Reset after 999999
                }
            }
            DisplayScan();
        }
    }
}

// pio run -t upload
```

### 中断 (macOS)

```c
/*
 * File:   7segment_display.c
 * Author: Matt Lee
 * Description: Display seconds on 6-digit 7-segment display using Timer0 interrupt
 * Target: 8052 (e.g., Kingst51 board)
 */

#include <8052.h>

// === Pin definitions ===
__sbit __at (0x90) ADDR0;   // P1^0
__sbit __at (0x91) ADDR1;   // P1^1
__sbit __at (0x92) ADDR2;   // P1^2
__sbit __at (0x93) ADDR3;   // P1^3
__sbit __at (0x94) ENLED;   // P1^4

// === 7-segment character table (0–F) ===
__code unsigned char LedChar[] = {
    0xC0, 0xF9, 0xA4, 0xB0, 0x99, 0x92, 0x82, 0xF8,
    0x80, 0x90, 0x88, 0x83, 0xC6, 0xA1, 0x86, 0x8E
};

// === Display buffer ===
unsigned char LedBuff[6] = { 0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF };

// === Global variables ===
unsigned char i = 0;              // scan index
unsigned int cnt = 0;             // Timer0 interrupt count
volatile unsigned char flag1s = 0; // 1-second flag

// === Timer0 Interrupt Service Routine ===
void Timer0_ISR(void) __interrupt (1) __using (1)
{
    TH0 = 0xFC;   // reload for 1ms
    TL0 = 0x67;
    cnt++;

    // 1-second flag
    if (cnt >= 1000) {
        cnt = 0;
        flag1s = 1;
    }

    // === Dynamic display ===
    P0 = 0xFF; // blank display
    switch (i) {
        case 0: ADDR2=0; ADDR1=0; ADDR0=0; P0=LedBuff[0]; break;
        case 1: ADDR2=0; ADDR1=0; ADDR0=1; P0=LedBuff[1]; break;
        case 2: ADDR2=0; ADDR1=1; ADDR0=0; P0=LedBuff[2]; break;
        case 3: ADDR2=0; ADDR1=1; ADDR0=1; P0=LedBuff[3]; break;
        case 4: ADDR2=1; ADDR1=0; ADDR0=0; P0=LedBuff[4]; break;
        case 5: ADDR2=1; ADDR1=0; ADDR0=1; P0=LedBuff[5]; break;
    }
    i++;
    if (i >= 6) i = 0;
}

// === Main function ===
void main(void)
{
    unsigned long sec = 0;

    // === Port initialization ===
    P0 = 0xFF;
    P1 = 0xFF;

    ENLED = 0;   // enable LED display (U3)
    ADDR3 = 1;   // select U3 (fixed)

    // === Timer0 configuration ===
    TMOD = 0x01; // Timer0, mode 1 (16-bit)
    TH0  = 0xFC;
    TL0  = 0x67;
    ET0  = 1;    // enable Timer0 interrupt
    EA   = 1;    // enable global interrupt
    TR0  = 1;    // start Timer0

    // === Main loop ===
    while (1) {
        if (flag1s) {
            flag1s = 0;
            sec++;
            if (sec >= 1000000) sec = 0;

            LedBuff[0] = LedChar[sec % 10];
            LedBuff[1] = LedChar[(sec / 10) % 10];
            LedBuff[2] = LedChar[(sec / 100) % 10];
            LedBuff[3] = LedChar[(sec / 1000) % 10];
            LedBuff[4] = LedChar[(sec / 10000) % 10];
            LedBuff[5] = LedChar[(sec / 100000) % 10];
        }
    }
}
```

