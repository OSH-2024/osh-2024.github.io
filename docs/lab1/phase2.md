# 创建初始内存盘

## 先导知识

???+ info "系统内核启动时的内存加载"

    上一节说到，Linux kernel 在自身初始化完成之后需要找到并运行 init 程序。用户程序存在于文件系统之中，因此，内核必须找到并挂载一个文件系统才可以成功完成系统的引导过程。

    GRUB 中提供了一个选项 `root=` 来指定第一个文件系统，但随着硬件的发展，很多情况下这个文件系统也许是存放在 USB 设备、SCSI 设备等等多种多样的设备之上，如果需要正确引导，USB 或 SCSI 驱动模块首先需要运行起来，但这些驱动程序也是存放在文件系统里，因此会形成一个悖论。

    为解决此问题，Linux kernel 提出了 RAM disk 的解决方案，把一些启动所必须的用户程序和驱动模块放在 RAM disk 中，这个 RAM disk 看上去和普通的 disk 一样，有文件系统，有 cache，内核启动时，首先把 RAM disk 挂载起来，等到 init 和一些必要模块运行起来之后，再切换到真正的文件系统之中。

    不过，这种 RAM disk 的方案（下称 initrd）虽然解决了问题，但并不完美。 比如，disk 有 cache 机制，对于 RAM disk 来说，这个 cache 机制就显得很多余且浪费空间；disk 需要文件系统，那文件系统必须被编译进 kernel 而不能作为模块来使用。Linux 2.6 kernel 提出了一种新的实现机制，即 initramfs。这是一种 RAM filesystem 而不是 disk。initramfs实际是一个 cpio 归档，启动所需的用户程序和驱动模块被归档成一个文件。因此，不需要 cache，也不需要文件系统。

## 创建过程

Linux 在启动时，会首先加载初始内存盘（initrd）进行初始化的操作，下面我们讲解如何创建一个最小化的 initrd。

创建一个新的目录（和 `linux-6.8.1` 文件夹平级，比如名为 `temp`）用于暂存文件，再在此临时目录下创建一个 C 程序 `init.c`，代码如下：

```c
#include <stdio.h>

int main() {
    printf("Hello! PB22XXXXXX\n"); // Your Student ID
    while (1) {}
}
```

???+ info "init"

    init 将会作为第一个用户态进程被启动，成为所有后续进程的父进程。

编译，**静态链接** 为可执行程序：

```bash
gcc -static init.c -o init
```

打包 initrd：

```bash
find . | cpio --quiet -H newc -o | gzip -9 -n > ../initrd.cpio.gz
```

这会在 `temp` 文件夹外创建压缩后的 `initrd.cpio.gz` 初始内存盘。

使用 QEMU 测试效果：

```bash
qemu-system-x86_64 -kernel linux-6.8.1/arch/x86_64/boot/bzImage -initrd initrd.cpio.gz
```

同第一部分，你可能需要在命令末尾增添 `-nographic -append console=ttyS0` 选项。

当你在屏幕上看到之前 `printf` 的信息的时候，就成功了。

## 本节评分标准

|                                                            要求                                                             | 满分 |
| :-------------------------------------------------------------------------------------------------------------------------: | :--: |
|                    提交 `initrd.cpio.gz`，保证其能够显示 `"Hello! PB22XXXXXX\n"` 信息（修改为真实学号）                     | 20%  |
| **「可选」**在`init.c`中将`while (1) {}`删去之后会使编译的内存盘 kernel panic，请你查阅资料并在 `lab1/README.md` 中解释原因 |  5%  |
