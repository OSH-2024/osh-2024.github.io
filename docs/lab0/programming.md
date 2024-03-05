# 编程说明

本学期实验如无特殊声明均允许使用 C、C++、Rust 其中任意一种语言完成。本章将简单介绍需要掌握或了解的 C、C++、Rust 语言知识。这些知识并不一定会在实验中用到，但是理解它们或许能够指导你的实际开发过程并减少在各个语言文档中探索的时间。

## C 的要求

- 了解 C99 标准下几乎所有的 C 用法，这是考虑到 C 的标准语言特性并不太多，更进阶的用法也更倾向于依赖编译器特性或是标准用法的巧用；
- 了解基本的宏知识，例如宏函数中参数使用时需要括号包裹，这是因为调用 Linux API 时需要用到宏；
- 掌握查询 Linux API 文档的方法，无论是使用 `man`、`tldr` 或是直接查看网页版 man 文档。

## C++ 的要求

- 掌握基本的 C 用法；
- 宏，如 C 要求所述；
- Linux API 文档，如 C 要求所述;
- 了解一些 Morden C++ 的用法，可以为你减少编程工作量。

??? tips "关于 Modern C++"

    如果你选择使用 C++ 完成实验，那么<span style="color:red">请不要将 C++ 代码写得宛如 C 一样</span>。现代的、C++11 标准后的 C++ 增添了众多强有力的工具，能够帮助你以更高效、更简洁的手段达成目标。

    如需全方位的了解 Modern C++，可以阅读《A Tour of C++》以及进阶的例如《Modern Effective C++》等书籍，其中详细讲解了 Modern C++ 中的一些设计理念和最佳实践。也可以参考《[现代 C++ 教程](https://changkun.de/modern-cpp/zh-cn/00-preface/)》。

    下面我们也给出一些 tips：

    - （STL）栈上的固定大小数组 `int a[12]` 使用 `std::array` 代替；
    - （STL）堆上的不定长数组 `new int[n]` 使用 `std::vector` 代替；
    - （STL）使用 `std::string` 代替 `chat buf[N]`。用 `s.c_str()` 或是 `&s[0]` 获取 C 字符串的不可变或可变指针。

    以下是一些进阶内容，可以选择性了解：

    - （STL）`<memory>` 中 `unique_ptr`、`shared_ptr` 和 `weak_ptr` 智能指针可以实现自动在合适的时刻释放资源，可以配合 `std::thread` 等使用。<span style="color:blue">可以尝试使用智能指针替换所有裸指针</span>；
    - （性能）了解左值，右值和将亡值的概念。了解移动语义、`std::move` 的使用和完美转发；
    - （设计）了解 lambda 表达式及其捕获方式；
    - （设计）了解虚函数及如何通过继承实现多态。

## Rust 的要求

??? tips "关于 Rust"

    安装教程可以在 [官网安装界面](https://www.rust-lang.org/tools/install) 查看。
    详细语言教程也可以在 [官网教程汇总界面](https://www.rust-lang.org/learn) 按需查阅。
    其中重点是 [Rust Book](https://doc.rust-lang.org/book/) 这份教程，其在高低层次上都对 Rust 进行了详细的解析。
    而如果你之前只了解过 C 开发，可以着重关注 Rust Book 中的以下概念：

    - Ownership，lifetime，borrow，这些是 Rust 最核心的概念之一；
      - 对应章节是 [Understanding Ownership](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html) 和 [Validating References with Lifetimes](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html)。
    - 模式匹配（pattern matching），如 `match` 和 `if let` 的用法；
      - <span style="color:blue">用好模式匹配可以极大简化你的代码</span>
    - Cargo 的使用，从而完成 Rust 的依赖管理及编译等工作；
      - 安利：`cargo clippy` 可以对代码进行 <span style="color:blue">静态检查</span>，帮助你 <span style="color:blue">重构代码</span>。C++ 也有类似的工具 clangd（是 CLion 的默认分析器，也和 VSCode 配合的很好），可以参照官网安装使用。
    - 并发编程，包括多线程（互斥锁，channel）、协程（async）等；
    - 函数式编程（functional programming），如 `map`、`filter` 的用法；
    - 基本的 unsafe Rust 用法，可能会在调用外部库时用到。

    在本课程实验之外，也可以拓展了解以下内容：

    - 智能指针（smart pointer），C++ `<memory>` 库中也有相似实现；
    - 错误处理（error handling），Rust 错误处理与 Go 有类似之处，如有经验可以对照学习，但本次实验应该不会在错误处理上有较高要求，把握概念即可；
    - Traits（有译作特质的），类似于接口，可用于实现多态。

    其他的例如 [Rust by Example](https://doc.rust-lang.org/rust-by-example/) 也是很优质的教程，也能让你迅速找到自己所需要的写法。

实验自愿使用 Rust 语言，不会做进一步的要求。
如果你选择在实验中使用 Rust 语言，你依然可以从助教处获取一些语言使用上的基础帮助，并与其他同学在同一标准下进行评测。

### 实验中的优缺点

选择 Rust 可能会在本轮实验中遇见以下优缺点：

优点：

- Rust 的基础库和包管理比 C++ 更全面而更易用；
- Rust 的内存安全保证可以尽量让你避免遇到难以 debug 的 segmentation fault 问题；
- Rust 对于一些比较现代的技术，例如协程，有相对更好的支持，在实验中需要遇到类似情况时会更方便。

缺点：

- Rust 的学习曲线稍显陡峭，尤其是从原汁原味的 C 出发的话；
- Rust 不能直接使用 Linux API（由众多 C header 文件提供），下文我们将详细讨论这个问题。

### 外部库的要求

如优缺点中所言，尽管 Rust 标准库相对较丰富，但需要调用特定 Linux API 时，Linux 提供的是 C header 文件。从头文件到 Rust 的绑定较为麻烦，所以如无特地声明，我们默认允许使用 Rust 语言时调用以下这些外部库（后续可能会增充此列表，但不会删减）：

- `nix`
- `libc`

如你不满足于这些外部库、希望使用其他外部库时，请尽量提前询问助教。在最后验收实验时，助教会参考以下条件对除上述列表以外的外部库进行评定：

- 此外部库是否仅是 Linux C API FFI？如是，则允许；
- 此外部库使用方法是否与原本 C 中调用的方法相类似？如是，参考相似程度允许；
- 此外部库是否破坏了原本的实验设计目标？如是，则禁止；如否，参考和实验目标相关程度允许。

如果一个外部库被禁止，实验中对应项目将可能被酬情扣分。

## 调试

???+ tips "预期目标"

    了解 `-g` 编译选项的作用，至少掌握 GDB/VSCode 调试中的一种。

在本学期的实验中，你可能会写出各式各样的 bug，用好 debugger 往往可以大幅提升你的调试效率。本部分主要介绍 C++ 程序的调试。

### GDB 调试

本节会对 C/C++ 常用的 [GDB (GNU Project debugger)](https://www.sourceware.org/gdb/) 进行介绍。

例如，在以下的程序中，运行到 `throw 20` 时会抛出一个异常

```c++
#include <iostream>
void foo()
{
    throw 20;
}
int main()
{
    std::cout << "testing";
    foo();
    return 0;
}
```

如果你直接编译（`g++ example.cpp`）并运行，会得到以下输出：

```bash
./a.out
terminate called after throwing an instance of 'int'
[1]    12079 IOT instruction (core dumped)  ./a.out
```

此时只知道程序发生了错误，但并不知道是哪里出了问题。这时候就可以使用 gdb 来进行调试（取代简单的 print）：

在编译时使用 `g++ example.cpp -g`，完成编译后，运行 `gdb a.out`，会进入以下界面：

```
<some intro here>
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from a.out...
(gdb)
```

此时输入 `run` 然后回车，gdb 就会运行你的程序，得到：

```
Starting program: /home/catoverflow/Projects/tmp/a.out
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/usr/lib/libthread_db.so.1".
terminate called after throwing an instance of 'int'
Program received signal SIGABRT, Aborted.
0x00007ffff7ae534c in __pthread_kill_implementation () from /usr/lib/libc.so.6
(gdb)
```

说明程序发生了错误，并回到终端。输入 `bt` (back trace) 并回车就可以看到程序的调用栈：

```
(gdb) bt
#0  0x00007ffff7ae534c in __pthread_kill_implementation () from /usr/lib/libc.so.6
#1  0x00007ffff7a984b8 in raise () from /usr/lib/libc.so.6
#2  0x00007ffff7a82534 in abort () from /usr/lib/libc.so.6
#3  0x00007ffff7dfc7ee in __gnu_cxx::__verbose_terminate_handler () at /usr/src/debug/gcc/libstdc++-v3/libsupc++/vterminate.cc:95
#4  0x00007ffff7e08c4c in __cxxabiv1::__terminate (handler=<optimized out>) at /usr/src/debug/gcc/libstdc++-v3/libsupc++/eh_terminate.cc:48
#5  0x00007ffff7e08cb9 in std::terminate () at /usr/src/debug/gcc/libstdc++-v3/libsupc++/eh_terminate.cc:58
#6  0x00007ffff7e08f5e in __cxxabiv1::__cxa_throw (obj=<optimized out>, tinfo=0x555555557db0 <typeinfo for int@CXXABI_1.3>, dest=0x0)
    at /usr/src/debug/gcc/libstdc++-v3/libsupc++/eh_throw.cc:95
#7  0x00005555555551a4 in foo () at example.cpp:5
#8  0x00005555555551c6 in main () at example.cpp:10
```

调用栈将函数调用自底向上显示，最上面的就是最后被调用的函数，在这里上面的都是链接的系统库，下面的两个则是我们自己的函数。这时就很容易发现错误发生在 `foo` 中，代码的第五行。

最后，输入 `q` 并回车就可以退出了。除了上面的 bt，常用的指令还有 `break`（添加断点），用 `attach` 命令连接到线程（在多线程调试中非常有用）等等，具体的用法可以查阅相关文档。

??? tips "编译时参数 -g 的作用？"

    在终端输入 `man gdb` 并找到 `-g` 的帮助文档，可以看到：

    ````
    -g  Produce debugging information in the operating system's native format (stabs, COFF, XCOFF, or DWARF).  GDB can work with this
           debugging information.
    ````

    简单来说，这个参数会在编译的时候加入额外的调试信息，比如代码所在的行号（在 gdb 报错的时候非常有用，还可以拿来插入断点）等等。否则 gdb 调试的就是一个简单的二进制文件，能输出的信息会少很多。

    你也可以不加 `-g`，然后对比一下 gdb 的输出。

### VSCode 调试

在命令行里用 gdb debug 虽然简单，但是很多时候不够直观方便。你也可以尝试用 IDE 自带的 debug 功能（有些是基于 gdb 的，但是提供了非常方便的操作界面）。这里以 VSCode 为例：

VSCode 调试的核心是工作区的 `launch.json` 文件。在安装了 C/C++ 插件后，点击调试，应该可以自动在工作区的 `.vscode` 文件夹下生成一份与下面类似的 `launch.json` 文件。正确配置后，点击调试即可启动断点调试功能。

```json
{
  "version": "2.0.0",
  "configurations": [
    {
      "name": "(gdb) Launch",
      "type": "cppdbg",
      "request": "launch",
      "program": "${workspaceFolder}/可执行文件名",
      "args": [],
      "cwd": "${workspaceFolder}",
      "environment": [],
      ...... // 其他配置选项，这里略去
    }
  ]
}
```

以下配置选项需要关注：

- `cwd`：当前的工作路径，调试若需要用到命令行参数，其中的文件路径就是相对工作路径的；
- `program`：程序位置。注意这里的程序需要是带 `-g` 选项编译出来的；
- `args`：命令行参数数组。例如，如果我想调试 `./main -o output.txt input.txt`：
  ```json
  ["-o", "output.txt", "input.txt"]
  ```
- `environment`：参考 [官方文档](https://code.visualstudio.com/docs/cpp/launch-json-reference#_environment)。

???+ question "每次修改代码后需要再次手动编译？"

    是的，对于单个文件或 CMake 项目等具体情况，VSCode 都有很方便的一键编译调试功能，但这里介绍的是一般情况，需要先编译出一个带有调试信息的可执行文件，再用 VSCode 对该可执行文件调试。所以每次修改源文件后都需要再次手动编译。

在完成了 lab2 后，你会对 `args` 和 `environment` 选项有更深刻的理解。

IDE 的断点调试往往都比较直观，这里就不再介绍了，同学们可以自行探索。

## 练习

- 使用 C++ 编写一个 `split` 字符串函数，函数签名如下：

  ```C++
  std::vector<std::string> split(std::string s, const std::string& d);
  ```

  如调用 `split("1,2,3", ",")` 后，会返回一个包含 string 对象 `"1", "2", "3"` 的 vector。
