# OpenSSL库

## OpenSSL

`SHA-1`（英语：`Secure Hash Algorithm 1`，中文名：安全散列算法1）是一种密码散列函数，美国国家安全局设计，并由美国国家标准技术研究所（NIST）发布为联邦数据处理标准（FIPS）。`SHA-1`可以生成一个被称为消息摘要的160位（20字节）散列值，散列值通常的呈现形式为40个十六进制数。

本文假设你已经安装好了`OpenSSL`，并且持有一份1.1.1的源码。
SHA1相关的头文件在`sha.h`中、源文件在`crypto/sha`目录中。

### 主要结构：

```cpp
# define SHA_LONG unsigned int

# define SHA_LBLOCK      16
# define SHA_CBLOCK      (SHA_LBLOCK*4)/* SHA treats input data as a
                                        * contiguous array of 32 bit wide
                                        * big-endian values. */
# define SHA_LAST_BLOCK  (SHA_CBLOCK-8)
# define SHA_DIGEST_LENGTH 20

typedef struct SHAstate_st {
    SHA_LONG h0, h1, h2, h3, h4; 
    SHA_LONG Nl, Nh; 
    SHA_LONG data[SHA_LBLOCK];
    unsigned int num;
} SHA_CTX;
```

这个结构定义了`SHA`运算的海棉算法上下文。主要字段含义：

- h0, h1, h2, h3, h4 —— 存放海棉运算的中间结果。
- data —— 数据分组缓冲区，用于暂存不够分组大小的数据。
- num —— 缓冲区数据大小。

在1.1.1中，大多数的数据结构已经不再向使用者开放，从封装的角度来看，这是更合理的。如果你在头文件中找不到结构定义，不妨去源码中搜一搜。

### 主要函数：

`int SHA1_Init(SHA_CTX *c);`

初使化SHA1上下文结构。

总是返回1。

`int SHA1_Update(SHA_CTX *c, const void *data, size_t len);`

向SHA1上下文输入字节流。
成功返回1，失败返回0。

`int SHA1_Final(unsigned char *md, SHA_CTX *c);`
生成最终的SHA1摘要串。
成功返回1，失败返回0。

`unsigned char *SHA1(const unsigned char *d, size_t n, unsigned char *md);`
对于短的字符串，可以使用该函数一次性生成SHA1摘要，简化调用。

### 使用举例：

下面这个例子演示了使用两种方式生成SHA1摘要，并对比结果。

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <stdint.h>
#include <string.h>

#include <openssl/sha.h>

namespace dakuang {}

void printHex(const unsigned char* pBuf, int nLen)
{
    for (int i = 0; i < nLen; ++i)
    {
        printf("%02x", pBuf[i]);
    }
    printf("\n");
}

int main(int argc, char* argv[])
{
    char sText[] = "abdefg1234567890";

    {
        SHA_CTX ctx;
        SHA1_Init(&ctx);
        for (int i = 0; i < strlen(sText); ++i)
        {
            SHA1_Update(&ctx, (void*)&sText[i], 1);
        }
        unsigned char sSHA[20] = {0};
        int ret = SHA1_Final(sSHA, &ctx);
        printf("ret:%d \n", ret);
        printHex(sSHA, 20);
    }

    {
        unsigned char sSHA[20] = {0};
        unsigned char* ret = SHA1((const unsigned char*)sText, strlen(sText), sSHA);
        printf("ret %p \n", ret);
        printf("sSHA %p \n", sSHA);
        printHex(sSHA, 20);
    }

    return 0;
}
```

输出：

```
ret:1
1a7ef0b4975534ab2d8781b1136997e9a73b960a
ret 0x7fff1aba9a80
sSHA 0x7fff1aba9a80
1a7ef0b4975534ab2d8781b1136997e9a73b960a
```



