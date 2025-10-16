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

## 矩阵键盘扫描：控制单个数码管

```c
#include <8052.h>

__sbit __at (0x90) ADDR0;   // P1^0
__sbit __at (0x91) ADDR1;   // P1^1
__sbit __at (0x92) ADDR2;   // P1^2
__sbit __at (0x93) ADDR3;   // P1^3
__sbit __at (0x94) ENLED;   // P1^4

__sbit __at (0xA0) KEY_OUT_4; // P2.0
__sbit __at (0xA1) KEY_OUT_3; // P2.1
__sbit __at (0xA2) KEY_OUT_2; // P2.2
__sbit __at (0xA3) KEY_OUT_1; // P2.3
__sbit __at (0xA4) KEY_IN_1;  // P2.4
__sbit __at (0xA5) KEY_IN_2;  // P2.5
__sbit __at (0xA6) KEY_IN_3;  // P2.6
__sbit __at (0xA7) KEY_IN_4;  // P2.7

__code unsigned char LEDChar[] = {
    0xC0, 0xF9, 0xA4, 0xB0,
    0x99, 0x92, 0x82, 0xF8,
    0x80, 0x90, 0x88, 0x83,
    0xC6, 0xA1, 0x86, 0x8E
};

unsigned char KeyState[4][4] = {
    {1, 1, 1, 1},
    {1, 1, 1, 1},
    {1, 1, 1, 1},
    {1, 1, 1, 1},
};

void main(void)
{
    unsigned char i, j;
    unsigned char PrevKeyState[4][4] = {
        {1, 1, 1, 1},
        {1, 1, 1, 1},
        {1, 1, 1, 1},
        {1, 1, 1, 1},
    };

    EA = 1;
    ENLED = 0;
    ADDR3 = 1;
    ADDR2 = 0;
    ADDR1 = 0;
    ADDR0 = 0;
    TMOD = 0x01; // Timer0 mode1
    TH0 = 0xFC;  // Load high byte for 1ms
    TL0 = 0x66;  // Load low byte for 1ms
    ET0 = 1;     // Enable Timer0 interrupt
    TR0 = 1;     // Start Timer0
    P0 = LEDChar[0];

    while (1) {
        for (i = 0; i < 4; i++) {
            for (j = 0; j < 4; j++) {
                if (KeyState[i][j] != PrevKeyState[i][j]) {
                    if (PrevKeyState[i][j]) {
                        // Key pressed
                        unsigned char keyVal = i * 4 + j;
                        P0 = LEDChar[keyVal];
                    } else {
                        // Key released
                    }
                    PrevKeyState[i][j] = KeyState[i][j];
                }
            }
        }
    }
}

void t0_isr(void) __interrupt(1)
{
    unsigned char i = 0;
    static unsigned char scan_row = 0;
    static unsigned char KeyBuf[4][4] = {
        {0xFF, 0xFF, 0xFF, 0xFF},
        {0xFF, 0xFF, 0xFF, 0xFF},
        {0xFF, 0xFF, 0xFF, 0xFF},
        {0xFF, 0xFF, 0xFF, 0xFF},
    };

    TH0 = 0xFC; // Reload high byte for 1ms
    TL0 = 0x66; // Reload low byte for 1ms

    switch(scan_row) {
        case 0:
            KEY_OUT_1 = 0;
            break;
        case 1:
            KEY_OUT_2 = 0;
            break;
        case 2:
            KEY_OUT_3 = 0;
            break;
        case 3:
            KEY_OUT_4 = 0;
            break;
        default:
            break;
    }

    KeyBuf[scan_row][0] = (KeyBuf[scan_row][0] << 1) | KEY_IN_1;
    KeyBuf[scan_row][1] = (KeyBuf[scan_row][1] << 1) | KEY_IN_2;
    KeyBuf[scan_row][2] = (KeyBuf[scan_row][2] << 1) | KEY_IN_3;
    KeyBuf[scan_row][3] = (KeyBuf[scan_row][3] << 1) | KEY_IN_4;

    for (i = 0; i < 4; i++) {
        if ((KeyBuf[scan_row][i] & 0x0F) == 0x00) {
            KeyState[scan_row][i] = 0; // Key pressed
        } else if ((KeyBuf[scan_row][i] & 0x0F) == 0x0F) {
            KeyState[scan_row][i] = 1; // Key released
        }
    }

    scan_row = (++scan_row & 0x03);

    KEY_OUT_1 = 1;
    KEY_OUT_2 = 1;
    KEY_OUT_3 = 1;
    KEY_OUT_4 = 1;

}
```

## 矩阵键盘扫描：控制LED

```c
#include <8052.h>

__sbit __at (0x90) ADDR0;   // P1^0
__sbit __at (0x91) ADDR1;   // P1^1
__sbit __at (0x92) ADDR2;   // P1^2
__sbit __at (0x93) ADDR3;   // P1^3
__sbit __at (0x94) ENLED;   // P1^4

__sbit __at (0xA0) KEY_OUT_4; // P2.0
__sbit __at (0xA1) KEY_OUT_3; // P2.1
__sbit __at (0xA2) KEY_OUT_2; // P2.2
__sbit __at (0xA3) KEY_OUT_1; // P2.3
__sbit __at (0xA4) KEY_IN_1;  // P2.4
__sbit __at (0xA5) KEY_IN_2;  // P2.5
__sbit __at (0xA6) KEY_IN_3;  // P2.6
__sbit __at (0xA7) KEY_IN_4;  // P2.7

unsigned char KeyState[4][4] = {
    {1, 1, 1, 1},
    {1, 1, 1, 1},
    {1, 1, 1, 1},
    {1, 1, 1, 1},
};

void main(void)
{
    unsigned char i, j;
    unsigned char PrevKeyState[4][4] = {
        {1, 1, 1, 1},
        {1, 1, 1, 1},
        {1, 1, 1, 1},
        {1, 1, 1, 1},
    };

    EA = 1;
    ENLED = 0;
    ADDR3 = 1;
    ADDR2 = 1;
    ADDR1 = 1;
    ADDR0 = 0;
    TMOD = 0x01; // Timer0 mode1
    TH0 = 0xFC;  // Load high byte for 1ms
    TL0 = 0x66;  // Load low byte for 1ms
    ET0 = 1;     // Enable Timer0 interrupt
    TR0 = 1;     // Start Timer0
    P0 = 0xFF; // Turn off all LEDs;

    while (1) {
        for (i = 0; i < 4; i++) {
            for (j = 0; j < 4; j++) {
                if (KeyState[i][j] != PrevKeyState[i][j]) {
                    if (PrevKeyState[i][j]) {
                        // Key pressed
                        unsigned char keyVal = i * 4 + j;
                        P0 = ~(0x01 << keyVal);
                    } else {
                        // Key released
                    }
                    PrevKeyState[i][j] = KeyState[i][j];
                }
            }
        }
    }
}

void t0_isr(void) __interrupt(1)
{
    unsigned char i = 0;
    static unsigned char scan_row = 0;
    static unsigned char KeyBuf[4][4] = {
        {0xFF, 0xFF, 0xFF, 0xFF},
        {0xFF, 0xFF, 0xFF, 0xFF},
        {0xFF, 0xFF, 0xFF, 0xFF},
        {0xFF, 0xFF, 0xFF, 0xFF},
    };

    TH0 = 0xFC; // Reload high byte for 1ms
    TL0 = 0x66; // Reload low byte for 1ms

    switch(scan_row) {
        case 0:
            KEY_OUT_1 = 0;
            break;
        case 1:
            KEY_OUT_2 = 0;
            break;
        case 2:
            KEY_OUT_3 = 0;
            break;
        case 3:
            KEY_OUT_4 = 0;
            break;
        default:
            break;
    }

    KeyBuf[scan_row][0] = (KeyBuf[scan_row][0] << 1) | KEY_IN_1;
    KeyBuf[scan_row][1] = (KeyBuf[scan_row][1] << 1) | KEY_IN_2;
    KeyBuf[scan_row][2] = (KeyBuf[scan_row][2] << 1) | KEY_IN_3;
    KeyBuf[scan_row][3] = (KeyBuf[scan_row][3] << 1) | KEY_IN_4;

    for (i = 0; i < 4; i++) {
        if ((KeyBuf[scan_row][i] & 0x0F) == 0x00) {
            KeyState[scan_row][i] = 0; // Key pressed
        } else if ((KeyBuf[scan_row][i] & 0x0F) == 0x0F) {
            KeyState[scan_row][i] = 1; // Key released
        }
    }

    scan_row = (++scan_row & 0x03);

    KEY_OUT_1 = 1;
    KEY_OUT_2 = 1;
    KEY_OUT_3 = 1;
    KEY_OUT_4 = 1;

}
```



