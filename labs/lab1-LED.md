---
title: "Lab1: LED"
layout: page
parent: Labs
---

# Lab 1: LED
{:.no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## LED & Display Driver

<img src="labs/attachments/Pasted%20image%2020251014102352.png" alt="LED显示及其驱动电路" srcset="{{ site.baseurl }}/labs/attachments/Pasted%20image%2020251014102352.png">


### 🔌 Common Electrical Characteristics

- **Driver Type:** **Common Anode** (CA).
- **Active Logic:** **Low-level active** (LEDs turn ON when the signal is Low/0).

---

### 🔢 Driver Signals Group (LEDSx / LEDCx)

These signals generally function as **Select** lines (Digit Select, Row Select).

|**Signal**|**Count**|**Function Description (Digit/Row Select)**|**Notes**|
|---|---|---|---|
|`LEDS0` - `LEDS5`|6|**7-Segment Display** **Digit Select** (Enable).|Chooses **which** 7-segment display is driven.|
|`LEDS6`|1|**Individual LEDs** Select/Switch.|Controls the ON/OFF state of the **individual** LEDs.|
|`LEDC0` - `LEDC7`|8|**8x8 LED Dot Matrix** **Row Select**.|Chooses **which row** of the dot matrix is driven.|

---

### ⚙️ Data Bus (DBx)

These signals generally function as **Data** lines (Segment Select, Column Select).

|**Signal**|**Count**|**Corresponding Display Function Description (Segment/Column Select)**|
|---|---|---|
|`DB0` - `DB7`|8|**7-Segment Display:** **Segment Select**. Chooses **which segment** (e.g., a, b, c, DP) to light up.|
|||**Individual LEDs:** Data input. Determines **which individual LED** lights up.|
|||**8x8 LED Dot Matrix:** **Column Select**. Chooses **which column** of the dot matrix to light up.|

---

### 📝 Summary / Key Takeaways

- **Digit/Row Select** uses `LEDSx` or `LEDCx` (determines **which** physical component/row is active).
- **Segment/Column Select** uses `DBx` (determines **the pattern/content** to be displayed).
- All components follow the **Common Anode / Low-level active** driving rule.

## 74HC138

- `CR1J07.1`

<img src="labs/attachments/Pasted%20image%2020251014102805.png" alt="" srcset="{{ site.baseurl }}/labs/attachments/Pasted%20image%2020251014102805.png">

This system uses a 4-bit address space to select one display component (Digit, Row, or Individual LED) at a time, facilitating display multiplexing.

### 1. Address Decoding Logic (74HC138)

The system uses a **74HC138 3-to-8 Line Decoder** to select the specific component driver.

- **Decoder Inputs:** `ADDR2`, `ADDR1`, `ADDR0` determine the specific output line ($Y_0$ to $Y_7$).
- **Active Logic:** The outputs ($Y_i$) are **active-low**. When addressed, the selected output line is driven **LOW**.
- **Global Enable:** `ENLED = 0` is required to **enable** the 74HC138 and the entire display system.

### 2. Device Selection (4-Bit Address Mapping)

The 4-bit address (`ADDR3` down to `ADDR0`) selects either the 7-Segment/Individual LED drivers (U3) or the Dot Matrix drivers (U4).

|**Address Bit**|**Value**|**Selected Driver Chip**|**Controlled Outputs**|
|---|---|---|---|
|**`ADDR3`**|**1**|**U3** (7-Segment & Individual LEDs)|`LEDS0` - `LEDS6` (Digit/Unit Select)|
|**`ADDR3`**|**0**|**U4** (8x8 Dot Matrix)|`LEDC0` - `LEDC7` (Row Select)|

#### A. Selecting Dot Matrix Rows (U4)

When $\text{ADDR3} = 0$, the $Y_i$ outputs connect to the $\mathbf{LEDC}$ signals, selecting a specific row for the 8x8 Dot Matrix.

|**ADDR**|**A2​**|**A1​**|**A0​**|**Selected Signal (Driven LOW)**|**Function**|
|---|---|---|---|---|---|
|0|0|0|0|$\mathbf{LEDC_0}$|Selects Row 0 (U4)|
|...|...|...|...|...|...|
|0|1|1|1|$\mathbf{LEDC_7}$|Selects Row 7 (U4)|

#### B. Selecting 7-Segment Digits & Individual LED (U3)

When $\text{ADDR3} = 1$, the $Y_i$ outputs connect to the $\mathbf{LEDS}$ signals, selecting a specific digit or the individual LED.

|**ADDR**|**A2​**|**A1​**|**A0​**|**Selected Signal (Driven LOW)**|**Function**|
|---|---|---|---|---|---|
|1|0|0|0|$\mathbf{LEDS_0}$|Selects Digit 0 of the 7-Segment Display (U3)|
|...|...|...|...|...|...|
|1|1|0|1|$\mathbf{LEDS_5}$|Selects Digit 5 of the 7-Segment Display (U3)|
|1|1|1|0|$\mathbf{LEDS_6}$|Selects the Individual LED (U3)|

### 3. Data Control (DB0 - DB7)

While the `LEDS` and `LEDC` lines select **where** the display data goes, the **DB** lines control **what** is displayed.

- $\text{DB}_0$ to $\text{DB}_7$ are the 8-bit **Data Bus** lines.
- The data written to this bus specifies the pattern:
    - **7-Segment Display:** Controls the **Segment Select** (which segments, a through g, light up).
    - **Dot Matrix:** Controls the **Column Select** (which dots in the currently selected row light up).

## P0

<img src="labs/attachments/Pasted%20image%2020251014102509.png" alt="" srcset="{{ site.baseurl }}/labs/attachments/Pasted%20image%2020251014102509.png">

```c
/* P0 */
__sbit __at (0x80) P0_0 ;
__sbit __at (0x81) P0_1 ;
__sbit __at (0x82) P0_2 ;
__sbit __at (0x83) P0_3 ;
__sbit __at (0x84) P0_4 ;
__sbit __at (0x85) P0_5 ;
__sbit __at (0x86) P0_6 ;
__sbit __at (0x87) P0_7 ;
```

---

```c
// Keil C51 Standard Bit Definitions for Port 0 (Address 0x80)

// The 'sbit' keyword is used to declare a single bit at a specific address.
// Format: sbit Name = Port^Bit_Number; or sbit Name = Address^Bit_Number;

// Option 1: Using the Port Address (0x80 for P0)
sbit P0_0 = 0x80^0;
sbit P0_1 = 0x80^1;
sbit P0_2 = 0x80^2;
sbit P0_3 = 0x80^3;
sbit P0_4 = 0x80^4;
sbit P0_5 = 0x80^5;
sbit P0_6 = 0x80^6;
sbit P0_7 = 0x80^7;

// OR

// Option 2: Using the SFR Name (P0 is defined in <reg51.h> or similar header)
// This is the most common and preferred method if P0 is already defined as an SFR.

#include <reg51.h> // Include your microcontroller-specific header file

sbit P0_0_Alias = P0^0;
sbit P0_1_Alias = P0^1;
sbit P0_2_Alias = P0^2;
sbit P0_3_Alias = P0^3;
sbit P0_4_Alias = P0^4;
sbit P0_5_Alias = P0^5;
sbit P0_6_Alias = P0^6;
sbit P0_7_Alias = P0^7;
```

## P1

<img src="labs/attachments/Pasted%20image%2020251014102538.png" alt="" srcset="{{ site.baseurl }}/labs/attachments/Pasted%20image%2020251014102538.png">

```c
/* P1 */
__sbit __at (0x90) P1_0 ; // ADDR0
__sbit __at (0x91) P1_1 ; // ADDR1
__sbit __at (0x92) P1_2 ; // ADDR2
__sbit __at (0x93) P1_3 ; // ADDR3
__sbit __at (0x94) P1_4 ; // ENLED
__sbit __at (0x95) P1_5 ;
__sbit __at (0x96) P1_6 ;
__sbit __at (0x97) P1_7 ;
```

---

```c
#include <reg51.h> // Include your microcontroller-specific header file

// P1 Bit Definitions and Function Mapping
sbit ADDR0 = P1^0; // 74HC138 Address Input A0
sbit ADDR1 = P1^1; // 74HC138 Address Input A1
sbit ADDR2 = P1^2; // 74HC138 Address Input A2
sbit ADDR3 = P1^3; // Display Device Select (U3/U4)
sbit ENLED = P1^4; // Display System Global Enable (Active Low)
sbit P1_5  = P1^5; // General Purpose (P1.5)
sbit P1_6  = P1^6; // General Purpose (P1.6)
sbit P1_7  = P1^7; // General Purpose (P1.7)
```

## LEDS6

<img src="labs/attachments/Pasted%20image%2020251014103803.png" alt="" srcset="{{ site.baseurl }}/labs/attachments/Pasted%20image%2020251014103803.png">

| DB  | LED |
| --- | --- |
| 0   | 2   |
| 1   | 3   |
| 2   | 4   |
| 3   | 5   |
| 4   | 6   |
| 5   | 7   |
| 6   | 8   |
| 7   | 9   |

