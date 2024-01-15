# 程序调用过程堆栈

本文讲述在程序调用和回调时，`rsp`和`rbp`寄存器的区别和发挥的作用

## x86通用寄存器

| 寄存器     | 16位 | 32位 | 64位 |
| ---------- | ---- | ---- | ---- |
| 累加寄存器 | AX   | EAX  | RAX  |
| 基址寄存器 | BX   | EBX  | RBX  |
| 计数寄存器 | CX   | ECX  | RCX  |
| 数据寄存器 | DX   | EDX  | RDX  |
| 堆栈基指针 | BP   | EBP  | RBP  |
| 变址寄存器 | SI   | ESI  | RSI  |
| 堆栈顶指针 | SP   | ESP  | RSP  |
| 指令寄存器 | IP   | EIP  | RIP  |

可以看到`rsp`是堆栈顶指针，`rbp`是堆栈基指针。

从名字就可大致看出，`rsp`一直指向程序栈的栈顶，而`rbp`一直指向程序栈的栈底

<img src="https://www.liuxinpeng.cn/img/202401041526446.png" alt="img" style="zoom:50%;" />

> 一个栈帧就是保存一个函数的状态，简单来说就是一个函数所需要的栈空间.

rsp(栈顶寄存器) /esp(栈指针寄存器)永远指向栈帧的栈顶，rbp(栈基寄存器)/ebp(扩展基址指针寄存器)则永远指向栈帧的栈底,rip(程序计数寄存器)/eip(指令指针寄存器)指向当前栈栈帧执行的命令。

> 栈从高地址向低地址开辟内存空间，所以低地址的是栈顶，而栈底的第一个栈帧在这里存放着我们的主函数的父函数，所以main函数并不是最栈顶的函数，main上面还会在编译过程中有一些库函数，但是他们并不会产生栈帧，因为栈先进后出的特性，所以当在main函数中需要调用其他函数时，就开辟一个新的函数栈帧，并存储上一个栈的栈底，当调用结束时，将现在的栈帧弹出，恢复到原来的main函数继续执行完main函数。

![img](https://www.liuxinpeng.cn/img/202401041526539.png)



## 代码实例

看下面的这一段代码：

```c
int sum(int a,int b){
        return a+b;
}
int add_a_and_b(int a, int b) {
        int temp = 100;
        int x = temp+sum(a,b);
        return x;
}

int main() {
        int x = add_a_and_b(8,5);
        return 0;
}
```

汇编代码如下：

```assembly
0000000000001129 <sum>:
int sum(int a,int b){
    1129:	f3 0f 1e fa          	endbr64
    112d:	55                   	push   %rbp
    112e:	48 89 e5             	mov    %rsp,%rbp
    1131:	89 7d fc             	mov    %edi,-0x4(%rbp)
    1134:	89 75 f8             	mov    %esi,-0x8(%rbp)
	return a+b;
    1137:	8b 55 fc             	mov    -0x4(%rbp),%edx
    113a:	8b 45 f8             	mov    -0x8(%rbp),%eax
    113d:	01 d0                	add    %edx,%eax
}
    113f:	5d                   	pop    %rbp
    1140:	c3                   	retq

0000000000001141 <add_a_and_b>:
int add_a_and_b(int a, int b) {
    1141:	f3 0f 1e fa          	endbr64
    1145:	55                   	push   %rbp
    1146:	48 89 e5             	mov    %rsp,%rbp
    1149:	48 83 ec 18          	sub    $0x18,%rsp
    114d:	89 7d ec             	mov    %edi,-0x14(%rbp)
    1150:	89 75 e8             	mov    %esi,-0x18(%rbp)
	int temp = 100;
    1153:	c7 45 f8 64 00 00 00 	movl   $0x64,-0x8(%rbp)
	int x = temp+sum(a,b);
    115a:	8b 55 e8             	mov    -0x18(%rbp),%edx
    115d:	8b 45 ec             	mov    -0x14(%rbp),%eax
    1160:	89 d6                	mov    %edx,%esi
    1162:	89 c7                	mov    %eax,%edi
    1164:	e8 c0 ff ff ff       	callq  1129 <sum>
    1169:	8b 55 f8             	mov    -0x8(%rbp),%edx
    116c:	01 d0                	add    %edx,%eax
    116e:	89 45 fc             	mov    %eax,-0x4(%rbp)
	return x;
    1171:	8b 45 fc             	mov    -0x4(%rbp),%eax
}
    1174:	c9                   	leaveq
    1175:	c3                   	retq

0000000000001176 <main>:

int main() {
    1176:	f3 0f 1e fa          	endbr64
    117a:	55                   	push   %rbp
    117b:	48 89 e5             	mov    %rsp,%rbp
    117e:	48 83 ec 10          	sub    $0x10,%rsp
   	int x = add_a_and_b(8,5);
    1182:	be 05 00 00 00       	mov    $0x5,%esi
    1187:	bf 08 00 00 00       	mov    $0x8,%edi
    118c:	e8 b0 ff ff ff       	callq  1141 <add_a_and_b>
    1191:	89 45 fc             	mov    %eax,-0x4(%rbp)
	return 0;
    1194:	b8 00 00 00 00       	mov    $0x0,%eax
}
    1199:	c9                   	leaveq
    119a:	c3                   	retq
    119b:	0f 1f 44 00 00       	nopl   0x0(%rax,%rax,1)
```



## 调用函数前

在`main`函数调用`add_a_and_b`之前，做了下面的事情：

```assembly
117a:	55                   	push   %rbp (1)
117b:	48 89 e5             	mov    %rsp,%rbp (2)
117e:	48 83 ec 10          	sub    $0x10,%rsp (3)
int x = add_a_and_b(8,5);
1182:	be 05 00 00 00       	mov    $0x5,%esi
1187:	bf 08 00 00 00       	mov    $0x8,%edi
118c:	e8 b0 ff ff ff       	callq  1141 <add_a_and_b>
```

- 保存上一个调用者的帧栈 (1) (2)

- 因为我们用到了局部变量x，所以(3)形成自己的函数帧栈进行保护

  > 这里之所以rsp递减，是因为在此处调用了其他函数，需要保护当前函数的栈帧

- 将调用函数的需要的参数，保存到`esi`和`edi`

  > `esi`和`edi`寄存器，通常是用来传递函数的调用参数，这里就是`add_a_and_b(8,5)`

## 调用函数中

```assembly
0000000000001141 <add_a_and_b>:
int add_a_and_b(int a, int b) {
    1141:	f3 0f 1e fa          	endbr64
    1145:	55                   	push   %rbp	(1)
    1146:	48 89 e5             	mov    %rsp,%rbp (2)
    1149:	48 83 ec 18          	sub    $0x18,%rsp (3)
    114d:	89 7d ec             	mov    %edi,-0x14(%rbp) (4)
    1150:	89 75 e8             	mov    %esi,-0x18(%rbp) (5)
	int temp = 100;
    1153:	c7 45 f8 64 00 00 00 	movl   $0x64,-0x8(%rbp) (6)
	int x = temp+sum(a,b);
    115a:	8b 55 e8             	mov    -0x18(%rbp),%edx
    115d:	8b 45 ec             	mov    -0x14(%rbp),%eax
    1160:	89 d6                	mov    %edx,%esi
    1162:	89 c7                	mov    %eax,%edi
    1164:	e8 c0 ff ff ff       	callq  1129 <sum>
    1169:	8b 55 f8             	mov    -0x8(%rbp),%edx
    116c:	01 d0                	add    %edx,%eax
    116e:	89 45 fc             	mov    %eax,-0x4(%rbp)
	return x;
    1171:	8b 45 fc             	mov    -0x4(%rbp),%eax
}
    1174:	c9                   	leaveq
    1175:	c3                   	retq
```

进入调用的函数`add_a_and_b`之后，首先保存上一个函数的帧栈的信息(`rbp`) (1)

`rbp`将维护当前函数帧栈的栈底位置，即当前的`rsp`位置 (2)

`add_a_and_b`函数也要调用其他的函数，所以先递减rsp形成栈帧 (3)

`ebp`在栈帧内使用内存，函数调用参数，位于栈顶 (4)(5)

局部变量`temp`从栈底开始使用内存(6)

