# eflags

## 简介

 `eflags` 包括

- 一组 `status flag` 
- **一个**`control flag`
- 一组`system flag`

![image-20230925141524538](https://www.liuxinpeng.cn/img/202401041526643.png)

在处理器初始化(`REST` or`INIT` pin)时，`eflags`寄存器的值是`00000002H`。

`elfags`中第1, 3, 5, 15, 22至31位是保留位，无法使用。

`elfags`中某些位可以通过指令直接改变，但是没有指令可以检查或者改变整个`eflags`寄存器

下面的指令可以把`flags` 读取或者写入 栈和`eax`寄存器：

- `LAHF`,`SAHF`,`PUSHF`,`PUSHFD`,`POPF`,`POPFD`

当`eflags`转移到栈或`eax`寄存器后，`falgs`可以被下面的指令检测或修改：

- `BT`,`BTS`,`BTR`,`BTC`

一个任务被挂起时，处理器会自动保存`eflags`寄存器到`task state segment(TSS)`，一个新任务被执行时，处理器也会自动从`TSS`加载`eflags`。

同样的，当调用一个`interrupt`或`exception`处理进程时，也会自动保存，处理完成也会自动加载

>  [`tss`](#task state segment): task segment stack

## status flags

状态标志位(bit 0, 2, 4, 6, 7, 11)标识算术指令

- **CF (bit 0) Carry flag** 

  进位标识

  — Set if an arithmetic operation generates a carry or a borrow out of the most significant bit of the result; cleared otherwise. This flag indicates an overflow condition for unsigned-integer arithmetic. It is also used in multiple-precision arithmetic.

- **PF (bit 2) Parity flag** 

  运算结果中"1"的个数的奇偶性，偶数个1，PF=1，反之为0。

  — Set if the least-significant byte of the result contains an even number of 1 bits; cleared otherwise.

- **AF (bit 4) Auxiliary Carry flag** 

  辅助进位标志

  — Set if an arithmetic operation generates a carry or a borrow out of bit 3 of the result; cleared otherwise. This flag is used in binary-coded decimal (BCD) arithmetic.

- **ZF (bit 6) Zero flag** 

  零标志，指令执行结果是否为0

  — Set if the result is zero; cleared otherwise.

- **SF (bit 7) Sign flag**

  正负标志位，它记录相关指令执行后，结果是否为负，如果结果为负，zf=1，如果结果非负，zf=0。（说明一下：符号位是程序员确定的，如果是无符号数，就算SF位，如果是无符号数，就不管SF位，想让SF=1，就：MOV EAX,0x1然后SUB EAX,0x2）

  — Set equal to the most-significant bit of the result, which is the sign bit of a signed integer. (0 indicates a positive value and 1 indicates a negative value.)

- **OF (bit 11) Overflow flag**

  溢出标志OF用于反映有符号数加减运算所得结果是否溢出。

  — Set if the integer result is too large a positive number or too small a negative number (excluding the sign-bit) to fit in the destination operand; cleared otherwise. This flag indicates an overflow condition for signed-integer (two’s complement) arithmetic.

## DF flag

Direction flag(bit 10)

控制字符串指令（mov、CMPS、SCAS、LODS以及STOS）DF=1，ESI，EDI自动递减(从高地址向低地址方向处理字符串），DF=0，ESI，EDI自动递增

## System Flags and IOPL Field

- **TF (bit 8) Trap flag** — Set to enable single-step mode for debugging; clear to disable single-step mode.

- **IF (bit 9) Interrupt enable flag** — Controls the response of the processor to maskable interrupt 

requests. Set to respond to maskable interrupts; cleared to inhibit maskable interrupts.

- **IOPL (bits 12 and 13)**
  - **I/O privilege level field** — Indicates the I/O privilege level of the currently running program or task. The current privilege level (CPL) of the currently running program or task must be less than or equal to the I/O privilege level to access the I/O address space. The POPF and IRET instructions can modify this field only when operating at a CPL of 0.

- **NT (bit 14) Nested task flag** — Controls the chaining of interrupted and called tasks. Set when the current task is linked to the previously executed task; cleared when the current task is not linked to another task.

- **RF (bit 16) Resume flag** — Controls the processor’s response to debug exceptions.

- **VM (bit 17) Virtual-8086 mode flag** — Set to enable virtual-8086 mode; clear to return to protected mode without virtual-8086 mode semantics.

- **AC (bit 18) Alignment check (or access control) flag** — If the AM bit is set in the CR0 register, alignment checking of user-mode data accesses is enabled if and only if this flag is 1.

  If the SMAP bit is set in the CR4 register, explicit supervisor-mode data accesses to user-mode pages are allowed if and only if this bit is 1. See Section 4.6, “Access Rights,” in the *Intel® 64* *and IA-32 Architectures Software Developer’s Manual, Volume 3A*.

- **VIF (bit 19) Virtual interrupt flag** — Virtual image of the IF flag. Used in conjunction with the VIP flag. (To use this flag and the VIP flag the virtual mode extensions are enabled by setting the VME flag in control register CR4.)

- **VIP (bit 20) Virtual interrupt pending flag** — Set to indicate that an interrupt is pending; clear when no interrupt is pending. (Software sets and clears this flag; the processor only reads it.) Used in conjunction with the VIF flag.

- **ID (bit 21) Identification flag** — The ability of a program to set or clear this flag indicates support for the CPUID instruction.

## task state segment

`"Task State Segment" (TSS)`，也称为任务状态段，是计算机操作系统中的一个重要数据结构，用于管理和切换任务或线程的上下文信息。它通常与x86架构的操作系统和处理器一起使用，用于实现多任务处理。下面是关于TSS的一些解释：

1. **上下文信息存储**：TSS存储了一个任务或线程的完整上下文信息，包括通用寄存器、段寄存器、栈指针、标志寄存器以及其他与任务执行相关的信息。当操作系统需要切换任务时，它会保存当前任务的上下文信息到当前任务的TSS中，然后加载下一个任务的TSS并恢复其上下文信息，从而实现任务切换。
2. **任务切换**：TSS使操作系统能够有效地进行任务切换，允许多个任务在同一处理器上轮流执行。这对于实现多任务操作系统至关重要，因为它允许每个任务独立地运行，而不会干扰其他任务。
3. **特权级别切换**：TSS也与特权级别（如用户态和内核态）切换相关。当从用户态切换到内核态时，操作系统会加载内核态任务的TSS，以便在内核模式下执行任务。这有助于实现特权级别的切换和安全性。
4. **任务描述符**：TSS是通过任务描述符来引用的。任务描述符是一种数据结构，其中包含了TSS的地址和其他相关信息。操作系统使用任务描述符来管理任务的TSS。

TSS 保存了所以x86寄存器的信息，在上下文任务切换时发挥很大的用处。不同的寄存器有不同的读写权限

![image-20230925152344372](https://www.liuxinpeng.cn/img/202401041526545.png)

![KioUB](https://www.liuxinpeng.cn/img/202401041527228.png)

![image-20230925152508575](https://www.liuxinpeng.cn/img/202401041526672.png)
