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

## LED显示及其驱动电路

<img src="labs/attachments/Pasted%20image%2020251014102352.png" alt="LED显示及其驱动电路" srcset="{{ site.baseurl }}/labs/attachments/Pasted%20image%2020251014102352.png">

- `LEDS0` - `LEDS5`: LED-7SEG
- `LEDS6`: LEDs

## 74HC138B

- `CR1J07.1`

<img src="labs/attachments/Pasted%20image%2020251014102805.png" alt="" srcset="{{ site.baseurl }}/labs/attachments/Pasted%20image%2020251014102805.png">

- `ENLED = 0`: enabling LED
- `ADDR3`
	- `= 1`: U3 (LEDS)
	- `= 0`: U4 (LEDC)

| `ADDR2` | `ADDR1` | `ADDR0` | #   |
| ------- | ------- | ------- | --- |
| `A2`    | `A1`    | `A0`    | `Y` |
| 0       | 0       | 0       | 0   |
| 0       | 0       | 1       | 1   |
| 0       | 1       | 0       | 2   |
| 0       | 1       | 1       | 3   |
| 1       | 0       | 0       | 4   |
| 1       | 0       | 1       | 5   |
| 1       | 1       | 0       | 6   |
| 1       | 1       | 1       | 7   |

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

