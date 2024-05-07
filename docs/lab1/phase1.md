# Linux 编译

## 先导知识

这里对本节可能涉及的概念做简单解释，你也可以通过必应等搜索引擎了解实验中不熟悉的概念。

??? info "Linux 系统的启动过程"

    Linux 系统的启动过程经常被称为 “引导过程”，主要包括以下几个阶段：

    - **BIOS 自检**（Power-On Self-Test，POST）：当计算机通电时，BIOS（Basic Input/Output System）首先自检并初始化硬件，检查计算机的硬件是否正常工作。
    - **Boot Loader** 阶段：BIOS 的自检完成后，接下来启动引导程序，例如 GRUB（GNU GRand Unified Bootloader）或 LILO（Linux Loader），用于加载内核和操作系统所需的其他文件，该过程会展示启动菜单，让用户选择要启动的操作系统或内核。
    - **内核加载**：操作系统的核心是内核，之前选择的内核文件现在被加载到计算机的内存中。内核的加载过程开始时，被启动的内核将确认计算机硬件的情况，例如处理器、内存和硬盘等。内核接下来会初始化计算机硬件和设备驱动程序，并建立一个虚拟的根文件系统。
    - **init 进程**：完成内核加载后，将建立一个 PID 为 1 的 init 进程。在初始化过程中，init 进程是最先启动的进程，负责启动并配置系统中的其他服务和进程。
    - **启动级别（runlevel）**：Linux 系统有多个引导级别，例如单用户模式、命令行模式和图形化模式等。在此阶段中，操作系统会进入指定的运行级别，以准备好用户的登录。
    - **用户登录**：最后，当操作系统启动并进入运行级别后，用户可以通过登录窗口登录系统，进入 Linux 系统的工作环境。

???+R info "QEMU"

    QEMU 是一个开源虚拟机。可以在里面运行 Linux 甚至 Windows 等操作系统。

    本次实验需要在虚拟机中安装 QEMU，并使用该 QEMU 来运行编译好的 Linux 内核。首先，在 Windows 下编译 Linux 源码十分麻烦，且 QEMU 在 Windows 下速度很慢；其次，之后的实验会涉及修改 Linux 源码。如果直接修改 Ubuntu 的内核，改完代码重新编译之后需要重启才能完成更改，但带 GUI 的 Ubuntu 系统启动速度较慢。另外，操作失误可能导致 Ubuntu 系统损坏无法运行。

## Linux 编译

在编译源码前，我们需要先安装编译 Linux 所需的依赖：

```bash
sudo apt update
sudo apt install build-essential libncurses-dev bison flex libssl-dev libelf-dev qemu-system-x86
```

???+ question "思考"

    思考这两条指令分别的作用。

在 [kernel.org](https://www.kernel.org/) 上可以下载到 Linux 内核的源代码。此次实验，我们选择最新的 **稳定版**，Linux 6.8.1 的内核进行编译。

**请在 AMD64 架构下的 Linux 环境中进行编译**。如果你使用的设备是 Apple M 系列处理器、架构为 ARM，请使用 Vlab 完成实验。

下载好 linux-6.8.1.tar.xz 文件后，解压缩为文件夹 linux-6.8.1，进入后创建默认配置：

```bash
make defconfig
```

然后使用下列命令之一，对 Linux 内核进行修改：

```bash
make xconfig
make menuconfig
```

初次编译可以不进行修改，体验编译的过程。

最后进行编译，执行：

```bash
make -j $((`nproc`-1))
```

???+ info "提示"

    - 直接使用 `make` 指令是完全串行编译，速度较慢。`-j` 选项可以指定使用 CPU 核心数；
    - `nproc` 是一个打印进程可用 CPU 核心数的指令。VMWare 虚拟机用户可以先调整分配的 CPU 数；
    - `$(( ))` 语法是 POSIX 标准指定的 shell 算术运算的语法；
    - 如果执行 `make -j` 命令时未指定处理器核数，编译器将无限制地使用多线程进行编译，可能导致系统资源瞬间被大量占用。对于 Linux 内核等复杂软件的编译，这一操作可能导致系统不稳定或崩溃；
    - 编译过程可能需要数分钟至数十分钟，这与你在 `make menuconfig` 时的配置有关，请耐心等待；
    - 使用 `make clean` 可以恢复原状，重新开始；
    - 此次实验不是调参比赛，**满分可以在只修改前两层选项的情况下轻松达成**，请注意哪些选项是能够真正地、切实地影响编译后的内核大小的；
    - **「可选」** 交叉编译生成其他目标平台的 Linux 内核的方法请自行搜索。

如果编译成功，我们可以在 `linux-6.8.1/arch/x86_64/boot/` 下看到一个 `bzImage` 文件，这个文件就是内核镜像文件。可以使用 QEMU 进行测试：

```bash
qemu-system-x86_64 -kernel arch/x86_64/boot/bzImage
```

???+ info "提示"

    使用`Ctrl-a x`可以退出qemu type.

???+ question "无 GUI 环境/无法看到输出"

    在上述命令后面加上选项 `-nographic -append console=ttyS0`

    不过这会导致 Ctrl+C 无法终止 qemu，需要关闭 Terminal 或者 kill 对应进程。

重复上述的修改和编译过程，即可进行对于 Linux 内核的删减。

## 本节评分标准

|                                                   要求                                                   | 满分 |
| :------------------------------------------------------------------------------------------------------: | :--: |
|                          提交编译好的内核文件 `bzImage`，保证其能够完成后续实验                          | 10%  |
|                                       `bzImage` 文件大小小于 7 MiB                                       | 10%  |
|                                       `bzImage` 文件大小小于 6 MiB                                       | 10%  |
|                                       `bzImage` 文件大小小于 4 MiB                                       | 10%  |
|         **「可选」**在 `lab1/README.md` 中分析哪些选项能够切实地影响编译后的内核大小或编译的时间         |  5%  |
| **「可选」**对 linux 内核进行交叉编译 (平台自选) 并用对应版本 qemu 运行，将过程记录在`lab1/README.md` 中 |  5%  |
