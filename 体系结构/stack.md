# stack frames

堆栈

如下图中所示，假设内存空间从下往上，地址空间增长。堆栈是由高地址指向低地址。

![Stack-based memory allocation - Wikipedia](https://www.liuxinpeng.cn/img/202401041528085.png)

每向栈中存入数据，栈指针需要向低地址递减，移出数据时，栈指针需要向高地址递增。

在汇编中通常用 `esp` 来表示栈顶指针( `stack pointer` )

![enter image description here](https://www.liuxinpeng.cn/img/202401041528036.png)

`ebp`通常被称为`frame pointer`，`esp`被称为`stack pointer`

`ebp`可以理解为函数调用栈中用于偷懒的办法

当一个函数调用另一个函数，可以通过以下的操作来完成:

- 保存原来`ebp`的值，存入栈中
- 将`esp`存入`ebp`中，这样函数调用前的栈状态就保存在`ebp`中
- `esp`可以任意活动

函数调用完成后只需要将`ebp`中的值替换`esp`，栈的状态就可回到调用之前