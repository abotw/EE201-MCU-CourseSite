---
title: Timer/Counter
layout: page
parent: Topics
---

# Timer/Counter

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

