# Function pointer

## 定义

```c
void fun(int a){
  ...
}

void (*fun_ptr)(int) = &fun;
```

函数指针的定义可以看下面的演变过程：

1. 定义一个函数 `void fun(int);`
2. 参考定义整型指针，加一个`*`，`void *fun(int)`
3. 但是C操作符`()`比`*`优先级更高，上述可能定义了一个`fun`函数，函数的返回值类型是`void *`
4. 所以需要用`()`将函数指针部分包裹起来`void (*fun)(int)`

## 调用

How to invoke fun()?

使用下面的方式调用

```c
(*fun_ptr)(10);
```

## 技巧

1. 函数指针赋值时不一定要加`&`符号
2. 调用函数指针时不一定要`()`

```c
int main()
{
    // fun_ptr is a pointer to function fun() 
    void (*fun_ptr)(int) = &fun;
    (*fun_ptr)(10);
    return 0;
}
```

简写：

```c
int main()
{ 
    void (*fun_ptr)(int) = fun;  // & removed
    fun_ptr(10);  // * removed
    return 0;
}
```

## 函数指针数组

函数指针如果写成数组的形式，则可以代替`switch case`

```c

#include <stdio.h>
void add(int a, int b)
{
    printf("Addition is %d\n", a+b);
}
void subtract(int a, int b)
{
    printf("Subtraction is %d\n", a-b);
}
void multiply(int a, int b)
{
    printf("Multiplication is %d\n", a*b);
}
  
int main()
{
    // fun_ptr_arr is an array of function pointers
    void (*fun_ptr_arr[])(int, int) = {add, subtract, multiply};
    unsigned int ch, a = 15, b = 10;
  
    printf("Enter Choice: 0 for add, 1 for subtract and 2 "
            "for multiply\n");
    scanf("%d", &ch);
  
    if (ch > 2) return 0;
  
    (*fun_ptr_arr[ch])(a, b);
  
    return 0;
}
```

定义一个函数指针数组：

```c
void (*fun_ptr_arr[])(int, int) = {add, subtract, multiply};
```

调用：

```c
(*fun_ptr_arr[ch])(a, b);
```

## 作为参数

```
void wrapper(void (*fun)())
{
    fun();
}
```

作为参数时，就和定义函数指针一样

函数指针作为参数有一个很典型的例子，就是`c++ STL`中的`qsort()`函数，这个函数需要接收一个函数用来比较参数的大小

```c
// An example for qsort and comparator
#include <stdio.h>
#include <stdlib.h>
  
// A sample comparator function that is used
// for sorting an integer array in ascending order.
// To sort any array for any other data type and/or
// criteria, all we need to do is write more compare
// functions.  And we can use the same qsort()
int compare (const void * a, const void * b)
{
  return ( *(int*)a - *(int*)b );
}
  
int main ()
{
  int arr[] = {10, 5, 15, 12, 90, 80};
  int n = sizeof(arr)/sizeof(arr[0]), i;
  
  qsort (arr, n, sizeof(int), compare);
  
  for (i=0; i<n; i++)
     printf ("%d ", arr[i]);
  return 0;
}
```

