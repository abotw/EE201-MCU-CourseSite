---
title: Timer/Counter
layout: page
parent: Topics
---

# Timer/Counter

- Kingst51 晶振：11.0592M
- 时钟周期：$\frac{1}{11059200}$
- 机器周期：$\frac{12}{11059200}$
- 定时20ms（0.02s），则需要18432个时钟周期。16位定时器的溢出值为65536，所以初始值应该为47104 = 0xB800。
- 1ms = 0.001s，921.6, 64614 = 0xFC66

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

