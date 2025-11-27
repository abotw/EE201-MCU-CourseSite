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

- TMOD 寄存器不可位寻址，需要整字节写入

---

- **Gate（门控位）：**
    - `Gate = 0`：仅由软件启动和停止。
    - `Gate = 1`：除软件控制外，还需外部引脚的高电平信号才能启动。
- **C/T（定时/计数选择位）：**
    - `C/T = 0`：作为**定时器**使用（基于机器周期计时）。
    - `C/T = 1`：作为**计数器**使用（对外部脉冲计数）

---

- **M1、M0** 组合出了定时器的四种工作方式：
    - **模式 0 (00)：** 13 位定时器，兼容旧型号，**不常用**。
    - **模式 1 (01)：** **16 位定时器**。最常用，由 THx 和 TLx 构成，可从 0 计数到 65535。
    - **模式 2 (10)：** **8 位自动重装模式**。TLx 溢出后，THx 的值自动回填，适合**固定周期定时**。
    - **模式 3 (11)：** 特殊模式，禁用定时器 1，并将定时器 0 拆分。**不常用**。
	    - 特殊双8位，把 T0 拆成2个8位定时器

## IE: Interrupt Enable Register

<img src="topics/attachments/Pasted%20image%2020251015101736.png" alt="" srcset="{{ site.baseurl }}/topics/attachments/Pasted%20image%2020251015101736.png">

- `ET0`: Timer 0 interrupt enable bit

## TCON: Timer/Counter Control Register

<img src="topics/attachments/Pasted%20image%2020251015095355.png" alt="" srcset="{{ site.baseurl }}/topics/attachments/Pasted%20image%2020251015095355.png">

- **作用：** 用于控制和监控定时器的状态，地址为 `0x88`，**可按位操作**。
- **主要位功能：**
    - **TRx (TR0/TR1)：** **运行控制位**，置 `1` 启动定时器，清 `0` 停止。
	    - 置 `1` 仅启动计数，不改变当前值
    - **TFx (TF0/TF1)：** **溢出标志位**，当定时器溢出时，由硬件自动置 `1`。
- **关系：** **TRx** 是我们用来**控制**定时器的，而 **TFx** 是定时器用来**报告**完成状态的。

---

- `TF0`: Timer 0 overflow Flag. Set by hardware on Timer/Counter overflow. Cleared by hardware when processor vectors to interrupt routine.
- `TR0`: Timer 0 Run control bit. Set/cleared by software to turn Timer/Counter on/off.

## IP: Interrupt Priority Register

<img src="topics/attachments/Pasted%20image%2020251015102805.png" alt="" srcset="{{ site.baseurl }}/topics/attachments/Pasted%20image%2020251015102805.png">

