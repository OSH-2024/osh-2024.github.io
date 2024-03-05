# Linux

OSH 几乎所有实验都需要在 Linux 平台上完成、实现，所以本节期望你有一个可用的 Linux 工作环境和基本的 Linux 使用能力。如果你是一个熟练的 Linux 用户，你可以跳过本节。

## 获得 Linux 环境

- 在 [USTC Vlab](https://vlab.ustc.edu.cn/vm/) 上得到一个 Linux 的容器环境（并非虚拟机，*可能*有部分实验无法在其上面完成），可以通过图形界面或 VSCode 远程 SSH 等多种方式连接；
- 使用虚拟机，Windows 用户推荐 WSL2 或 VMware，macOS 用户可以参考 [在 macOS 中使用虚拟机（By Taoky）](https://blog.taoky.moe/2019-02-23/installing-os-on-vm.html)；
- 本地系统换成 Linux 或双系统。**实机安装可能会存在一定的风险**，如愿意尝试请根据自己想要安装的发行版搜索相应教程。

我们会保证 Ubuntu 虚拟机/实体机不出现兼容性问题，并尽量保证使用 Vlab 可以完成实验。

## Linux 入门

???+ tip "终端/shell/命令行/软件源/换源是什么意思？"

    如果你还不知道这些词语的意思，请自行上网搜索。这会让你对 Linux 建立基本的了解。

    安装好 Linux 后推荐先更换软件源。如对于 Ubuntu 系统，参考 [USTC Mirror Ubuntu 源使用帮助](https://mirrors.ustc.edu.cn/help/ubuntu.html)。

推荐以下资源：

- [LUG Linux 101 教程](https://101.lug.ustc.edu.cn/)（**关注第三章的文件操作、第六章的重定向和第七章 Linux 上的编程**）；
- [Debian 教程](https://www.debian.org/doc/manuals/debian-reference/ch01.zh-cn.html)；
- [Arch Linux 文章索引](<https://wiki.archlinux.org/index.php/General_recommendations_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)>)。

你至少需要理解命令行的目录导航、文件操作、重定向以及 Makefile。

??? question "为什么要使用 Linux"

    参考 [南京大学 ICS PA](https://nju-projectn.github.io/ics-pa-gitbook/ics2022/0.5.html)（License: CC BY-NC-SA 3.0 CN）。

    我们先来看两个例子。

    **如何比较两个文件是否完全相同？**这个例子看上去非常简单，在 Linux 下使用 `diff` 命令就可以实现。如果文件很大，那不妨用 `md5sum` 来计算并比较它们的 MD5。对一个 Linux 用户来说，键入这些命令只需要花费大约 3 秒的时间。但在 Windows 下，这件事要做起来就不那么容易了。也许你下载了一个 MD5 计算工具，但你需要点击多少次鼠标才能完成比较呢？也许你觉得一次好像也省不了多少时间，<span style="color: red">然而真相是，你的开发效率就是这样一点点降低的。</span>

    **如何列出一个 C 语言项目中所有被包含过的头文件？**这个例子比刚才的稍微复杂一些，但在 Windows 下你几乎无法高效地做到它。在 Linux 中，我们只需要通过一行命令就可以做到了：

    ```shell
    find . -name "*.[ch]" | xargs grep "#include" | sort | uniq
    ```

    通过查阅 `man`，你应该不难理解上述命令是如何实现所需功能的。这个例子再次体现了 Unix 哲学：

    - 每个工具只做一件事情，但做到极致；
    - 工具采用文件方式进行输入输出，从而易于使用；
    - 通过工具之间的组合来解决复杂问题。

    Unix 哲学的最后一点最能体现 Linux 和 Windows 的区别：<span style="color:blue">编程创造</span>。如果把工具比作代码中的函数，工具之间的组合就是一种编程。而 Windows 的工具之间几乎无法组合，因为面向普通用户的 Windows 需要强调易用性。

    所以，你应该使用 Linux 的原因非常简单：<span style="color:red">作为一个码农，Windows 一直在阻碍你思想，能力和效率的提升。</span>

    上面这段文字是从一个程序员的角度来说的。对于操作系统课程而言，也有必须熟悉与使用 Linux 的理由：作为目前最流行的开源操作系统，Linux 在足够易用，实用的同时也能帮助你将操作系统课程的理论与实践相结合。你不仅可以使用系统调用或系统调用封装而来的函数库，也可以通过源代码了解到每一个系统调用和数据结构的具体实现。

???+ info "The Missing Semester of Your CS Education"

    [The Missing Semester of Your CS Education](https://missing.csail.mit.edu/) 是 MIT 的一门 Linux 工具系列教程，官网上有简体中文的翻译版。如果你想 **进阶** 自己的 Linux 使用技巧，不妨看看这套课程。
