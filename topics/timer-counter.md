---
title: Timer/Counter
layout: page
parent: Topics
---

# Timer/Counter
{:.no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

- Kingst51 晶振：11.0592M
- 时钟周期：$\frac{1}{11059200}$
- 机器周期：$\frac{12}{11059200}$

| 定时   | 机器周期  | 初始值     | 初始值(hex) |
| ---- | ----- | ------- | -------- |
| 1ms  | 921.6 | 64614.4 | 0xFC66   |
| 20ms | 18432 | 47104   | 0xB800   |

## TMOD: Timer/Counter Mode Control Register

<img src="topics/attachments/Pasted%20image%2020251015095212.png" alt="" srcset="{{ site.baseurl }}/topics/attachments/Pasted%20image%2020251015095212.png">


## IE: Interrupt Enable Register

<img src="topics/attachments/Pasted%20image%2020251015101736.png" alt="" srcset="{{ site.baseurl }}/topics/attachments/Pasted%20image%2020251015101736.png">

- `ET0`: Timer 0 interrupt enable bit

## TCON: Timer/Counter Control Register

<img src="topics/attachments/Pasted%20image%2020251015095355.png" alt="" srcset="{{ site.baseurl }}/topics/attachments/Pasted%20image%2020251015095355.png">
- `TF0`: Timer 0 overflow Flag. Set by hardware on Timer/Counter overflow. Cleared by hardware when processor vectors to interrupt routine.
- `TR0`: Timer 0 Run control bit. Set/cleared by software to turn Timer/Counter on/off.

## IP: Interrupt Priority Register

<img src="topics/attachments/Pasted%20image%2020251015102805.png" alt="" srcset="{{ site.baseurl }}/topics/attachments/Pasted%20image%2020251015102805.png">

