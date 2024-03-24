# 添加自定义系统调用

## 先导知识

???+ info "系统调用及其在操作系统中扮演的角色"

    系统调用（syscall）是计算机操作系统中一种特殊的程序调用机制，允许应用程序请求操作系统内核为其执行特权操作，如访问磁盘、创建子进程、等待信号等。系统调用隐藏了内核的功能实现细节，提供了标准的、易于使用的接口，让开发者能够以一种结构化的方式编写自己的应用程序。
    
    系统调用在操作系统中扮演着非常重要的角色，因为它可以让应用程序进程在用户态和内核态之间进行转换，以访问受保护的内核资源。当应用程序发出系统调用请求时，操作系统内核将检查请求的系统调用，验证权限，执行请求的操作和传递结果给应用程序。具体地，系统调用在操作系统中发挥以下重要作用：
    
    - 提供系统底层服务：系统调用提供了一组操作系统提供的底层服务，如创建进程、管道、文件操作等，允许应用程序访问受保护的内核资源。
    - 管理进程与系统：系统调用允许应用程序管理整个进程系统，从创建、暂停、恢复程序到查询系统状态的各方面，提供了充分的系统控制。
    - 访问外部设备：许多操作系统的系统调用提供了访问外部硬件设备的接口，例如访问磁盘、网络、打印机等，使应用程序能够与外部世界进行通信。
    
    系统调用作为操作系统和应用程序之间的桥梁和接口，发挥了极其重要的作用，为应用程序与内核之间的通信提供了统一的机制，方便、安全地访问系统资源。

## 添加一个自定义的 Linux syscall

本节我们将为 Linux 添加一个自定义的 syscall，来获得一个字符串 `"1815cec5210c289ac74b1aadbd167c7b\n"`。

首先回到 linux 源码文件夹 linux-6.8.1，添加一个文件 `custom/hello.c`：

```c
#include <linux/string.h>
#include <linux/syscalls.h>
#include <linux/uaccess.h>

SYSCALL_DEFINE2(hello, char *, buf, size_t, buf_len)
{
    static const char s[] = "1815cec5210c289ac74b1aadbd167c7b\n";
    if (strlen(s) <= buf_len) {
        return copy_to_user(buf, s, sizeof(s));
        // 此处可以直接使用 memcpy（内核态可以解引用用户态指针）
        // 不过 copy_to_user 可以协助校验目标地址是否可写
    } else {
        return -1;
    }
}
```

???+ info "`SYSCALL_DEFINE2`"

    在实现系统调用的函数中，需要使用 `SYSCALL_DEFINE2` 宏来声明函数原型和函数体。
    
    系统调用的返回值是由内核主动设置的，一般来说，它们是整数类型的错误码。

再在 `custom/` 下创建文件 `Makefile`，内容仅为：

```make
obj-y := hello.o
```

接下来我们将此文件夹添加到 Linux 的编译工具链中。修改 `linux-6.8.1/Makefile` 文件，找到

```Makefile
# Objects we will link into vmlinux / subdirs we need to visit
core-y		:=
```

将 `core-y` 初始化 `custom/`，变为：

```Makefile
<...>
core-y		:= custom/
<...>
```

之后编译时就会将我们自行添加的 `custom/hello.c` 文件纳入到编译和链接中了。

最后需要注册此系统调用，打开 `arch/x86/entry/syscalls/syscall_64.tbl`（syscall 表文件），在最后一行添加一个新 syscall：

```
<...>
548    common  hello   sys_hello
```

记住这个数字 548，之后我们会用此数字来进行调用。

然后在 `include/linux/syscalls.h` 中添加一行声明：

```c
// <...>
asmlinkage long sys_hello(char *buf, int buf_len);
// <...>
```

接下来重新编译内核，自定义的 syscall 就添加完成了。

请查询 [`syscall` Linux 文档](https://man7.org/linux/man-pages/man2/syscall.2.html)，编写 initrd 程序测试这个自定义 syscall，使得提供的 buffer 长度在充足和不足时均能输出希望的结果：

- 长度充足时，返回 0，buffer 中存放完整的字符串 `"1815cec5210c289ac74b1aadbd167c7b\n"`；
- 长度不充足时，返回 -1；
- 根据返回值的不同，initrd 程序需要有不同的可视行为，具体请自行设计。

## 本节评分标准

|                             要求                             | 满分 |
| :----------------------------------------------------------: | :--: |
| 提交编译好的内核 `bzImage`，保证能够在 buffer 长度充足时完成 syscall | 20%  |
| 编译好的内核 `bzImage` 能保证在 buffer 长度不充足时完成 syscall | 10%  |
|            提交测试 syscall 的 initrd 源代码文件             | 10%  |

在完成所有三节的任务后，请检查你的目录结构与首页的目录结构是否相符，并使用 Git 在截止时间前 push 至 GitHub。
