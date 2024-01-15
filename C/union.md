# C语言中Union

union 联合体和struct结构体差不太多，区别就是，union中的成员共用内存地址，而struct中的成员单独拥有内存地址。Union是或的关系，struct是并的关系，

比如：

```c
union test{
  int a;
  char b[5];
}
```

这个结果体表示test结构体中存放`int型数据a` 或 `char数组b`

**一个联合变量，每次只能赋予一个成员值。** 换句话说，一个联合变量的值就是联合变员的某一个成员值

```c
#include "stdio.h"
union var
{
    long int l;
    int i;
};
 
int main(void)
{
 
    union var v;
    v.l = 5;
    printf("v.l is %d\n",v.i);
    v.i = 6;
    printf("now v.l is %ld! the address is %p\n",v.l,&v.l);
    printf("now v.i is %d! the address is %p\n",v.i,&v.i);
}
```

结果：

v.l is 5

now v.l is 6! the address is 0xbfad1e2c

now v.i is 6! the address is 0xbfad1e2c

所以说，管union的叫共用体还真是贴切——完全就是共用一个内存首地址，并且各种变量名都可以同时使用，操作也是共同生效。如此多的access内存手段，确实好用，不过这些“手段”之间却没法互相屏蔽——就好像数组+下标和指针+偏移一样。

上例中我改了v.i的值，结果v.l也能读取，那么也许我还以为v.l是我想要的值呢，因为上边提到了union的内存首地址肯定是相同的，那么还有一种情况和上边类似：

一个int数组变量a，一个long int(32位机中，long int占4字节，与int相同)变量b，我即使没给int变量b赋值，因为数据类型相同，我使用int变量b也完全会拿出int数组a中的a[0]来，一些时候一不小心用上，还以为用的就是变量b呢。这种逻辑上的错误是很难找出来的（只有当数据类型相去甚远的时候稍好，出个乱码什么的很容易发现错误）。

