# CR寄存器

在x86架构中，CR 寄存器是一组控制寄存器（Control Registers），用于管理和控制处理器的某些关键方面。这些寄存器的作用包括对系统的保护、分段、分页以及控制特权级别的转换。

在32位和64位的x86架构中，有一些重要的控制寄存器，其中最常见的包括：

1. **CR0 寄存器**：
    - 控制处理器的工作模式和基本系统特性，包括启动分页机制、对齐检查、协处理器使用等。位于这个寄存器中的标志位控制着处理器的不同特性。

2. **CR2 寄存器**：
    - 用于存储造成最近一次页面错误（Page Fault）的线性地址。在发生页面错误时，CPU 会将引起错误的线性地址加载到 CR2 中，以便操作系统可以处理页面错误。

3. **CR3 寄存器**：
    - 存放分页机制下的页目录表（Page Directory Table）的物理地址。在启用分页机制后，CR3 中存放着用于地址转换的页表结构的物理地址。

4. **CR4 寄存器**：
    - 控制一些扩展特性，如物理地址扩展（PAE，Physical Address Extension）、页表扩展（PSE，Page Size Extension）、机器检查异常（MCE，Machine Check Exception）等。

这些寄存器在操作系统和系统级编程中扮演着重要的角色，允许对处理器的核心行为进行配置和控制。不同的操作系统和应用程序可能需要利用这些寄存器来实现不同的功能或优化。

在编程时，对这些寄存器的访问需要特殊的处理，通常通过汇编语言或者特权级别较高的代码（例如内核级别的代码）才能够直接访问这些寄存器。