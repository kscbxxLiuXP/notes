# __thread

## __thread

在C语言中，我们有时候能看到在变量定义的时候，前面会加上一个__thread关键字

`__thread` 是C语言中用于声明线程局部存储（Thread-Local Storage，TLS）变量的关键字。在多线程编程中，每个线程都有自己独立的内存空间，`__thread` 关键字用于声明的变量在每个线程中都有独立的实例。

使用 `__thread` 声明的变量具有以下特点：
- 每个线程都有其自己的变量实例，互不干扰。
- 变量的生命周期与所在线程的生命周期相同，即当线程结束时，线程局部变量也会被销毁。

示例：

```c
#include <stdio.h>
#include <pthread.h>

__thread int tls_var; // 使用__thread声明线程局部变量

void* thread_function(void* arg) {
    tls_var = 20;
    printf("Thread-local variable value in thread: %d\n", tls_var);
    return NULL;
}

int main() {
    pthread_t thread;
    tls_var = 10; // 主线程中的线程局部变量

    printf("Thread-local variable value in main thread: %d\n", tls_var);

    pthread_create(&thread, NULL, thread_function, NULL);
    pthread_join(thread, NULL);

    printf("Thread-local variable value in main thread after thread execution: %d\n", tls_var);

    return 0;
}
```

输出示例：
```
Thread-local variable value in main thread: 10
Thread-local variable value in thread: 20
Thread-local variable value in main thread after thread execution: 10
```

在上述示例中，`tls_var` 是一个使用 `__thread` 声明的线程局部变量。主线程和子线程都有各自独立的 `tls_var` 实例，它们互不干扰。