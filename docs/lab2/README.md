# 简介

在实验一中，我们使用了许多命令行操作来完成一系列工作，如编译 Linux 内核、打包 initrd 等。所有的命令行都由一个叫做 shell 的程序解释并执行。本实验我们将自己编写一个简单的 shell 并理解 Linux shell 程序的工作原理。你可以在本文末尾获得一段示例代码。

???+ warning "重要"

    本实验作为代码编程实验，**会进行查重**，一切抄袭行为后果自负！

shell 程序包含一些必须实现的功能点和**「可选」**功能。

必做部分得分上限 100%，可选部分得分上限 20%。你最多可以累计获得 120% 的分数。

???+ question "如何提问？"

    [提问的智慧](https://lug.ustc.edu.cn/wiki/doc/smart-questions/)

    以下是一些要点：

    - 提问前，请先 [STFW 和 RTFM](https://lug.ustc.edu.cn/wiki/doc/smart-questions/#rtfm-%E5%92%8C-stfw%E5%A6%82%E4%BD%95%E7%9F%A5%E9%81%93%E4%BD%A0%E5%B7%B2%E5%AE%8C%E5%85%A8%E6%90%9E%E7%A0%B8%E4%BA%86)，ChatGPT 也是一个可考虑的选择；
    - 尽量使你的问题是可复现的、明确的，并在此基础上裁剪定位到会导致出问题的核心代码；
    - 详细描述问题，并提供相关指令及相关问题的报错截图，如果涉及系统问题请提供系统版本。

    **优先**在实验文档评论区、[Issue 区](https://github.com/OSH-2024/osh-2024.github.io/issues) 及课程群内讨论实验的相关问题以便解决共性问题。

## 实验要求

请按照以下目录结构组织你的 GitHub 仓库：

```
.                     // Git 仓库目录
├── lab2              // 实验二根目录
│   ├── shell.cpp     // 你的 shell 的源代码
│   ├── other.cpp
│   ├── Makefile      // 你提供的 Makefile
│   └── README.md     // 运行说明及简要的实验报告
├── .gitignore
└── README.md
```

如果选用 Rust 语言，lab2 目录下按照 Rust 工程组织（即包含 `src` 文件夹和 `Cargo.lock`, `Cargo.toml` 文件）即可。

本实验可以使用 libc, libstdc++, libm 以及 iostream, STL 等 C/C++ 语言标准和常用库。如果你愿意，你也可以使用 readline 和 ncurses 等 Linux 程序常用库。对 Rust 允许使用库的说明请参见实验零。使用此处没有列出的库前请询问助教。

Git 的使用应符合规范。当出现以下情况时，我们会酌情扣除一定分数：

- 很大一部分的 commit 是由 GitHub 网页上传的文件；
- commit 寥寥无几。最好的习惯是每实现一个新的功能及每一次代码重构都对应 commit；
- 上传了大量与实验要求无关的文件。

## 实验报告

你在 `README.md` 中应该至少包括：

- 你的 shell 实现可能与系统中的 bash（或助教期望的表现）有所不同，简要介绍这些潜在的区别，以免产生误会，导致不必要的扣分；
- **介绍你完成的选做项目**，方便助教进行更准确的评估。

本实验的主要内容为 shell 程序的编写，因此不必花费太多工夫在实验报告上。

## 关于自由选做

在必做部分以外，你可以参考标记为**「可选」**的几个功能来实现其他 shell 功能以得到选做分数（即不限本文档列出的功能）。每一项额外功能都会由助教参照其他可选功能讨论评估。

我们鼓励进行与操作系统相关的实验探究，因此过度脱离主题的项目可能不会获得加分，例如：

- 过于简单的内置命令，如 `:` (colon), `true`, `false`, `help` 等
- 严重偏离 shell 的基本功能的项目，例如你 [模仿 Zsh](https://github.com/johan/zsh/blob/master/Functions/Misc/tetris) 为你的 shell 内置了一个俄罗斯方块游戏

作为一个参考基准，GNU Bash 具有的功能大部分都会被认可。

## 关于编译

在本实验中，你需要提供 `Makefile` 或者 `Cargo.toml` 文件，我们将使用它来编译你提交的程序；在这些文件不能正常运行时，我们将尽可能去尝试编译 `lab2/` 目录下所有的 `*.c`、`*.cpp` 或者 `*.rs` 文件。

你可以在 `README.md` 中说明编译与运行相关的注意事项。如果在参考了你的说明后，我们依然无法正常编译你的代码，我们会尝试修复并酌情扣除一定分数。

## 示例程序

可以将文末程序命名为 `shell.cpp`，然后在相同目录下创建 `Makefile`：

```Makefile
CC=g++
CFLAGS=-c -Wall
SOURCES=shell.cpp
OBJECTS=$(SOURCES:.cpp=.o)
EXECUTABLE=shell

all: $(SOURCES) $(EXECUTABLE)

$(EXECUTABLE): $(OBJECTS)
	$(CC) $(OBJECTS) -o $@

.cpp.o:
	$(CC) $(CFLAGS) $< -o $@

clean:
	rm -f *o $(EXECUTABLE)
```

继续输入 `make` 即可编译出一个可执行文件 shell。

你可以输入 `./shell` 来运行它。这是一个非常简陋的 shell，它会提示你输入命令，你可以调用系统中有的其他命令来运行，例如 `ls`、`cat` 等，或输入 `exit` 退出。

输入 `make clean` 删除编译结果和中间产物。

???+ info "gitignore"

    你可以通过将 `.o` 文件和可执行文件添加到 `.gitignore` 文件的方式，来确保不会误 commit 中间文件。具体方式请 STFW。

你可以改进框架（示例代码嵌套层数较多，且错误处理不算很完备）并在框架的基础上继续完成实验，也可以从头编写自己的 shell 或者采用其它语言编写。

???+ question "思考"

    了解 `exec` 系统调用家族。`execvp` 是如何根据 `ls` 获取到 `ls` 的可执行文件位置并执行的？

```cpp
// IO
#include <iostream>
// std::string
#include <string>
// std::vector
#include <vector>
// std::string 转 int
#include <sstream>
// PATH_MAX 等常量
#include <climits>
// POSIX API
#include <unistd.h>
// wait
#include <sys/wait.h>

std::vector<std::string> split(std::string s, const std::string &delimiter);

int main() {
  // 不同步 iostream 和 cstdio 的 buffer
  std::ios::sync_with_stdio(false);

  // 用来存储读入的一行命令
  std::string cmd;
  while (true) {
    // 打印提示符
    std::cout << "$ ";

    // 读入一行。std::getline 结果不包含换行符。
    std::getline(std::cin, cmd);

    // 按空格分割命令为单词
    std::vector<std::string> args = split(cmd, " ");

    // 没有可处理的命令
    if (args.empty()) {
      continue;
    }

    // 退出
    if (args[0] == "exit") {
      if (args.size() <= 1) {
        return 0;
      }

      // std::string 转 int
      std::stringstream code_stream(args[1]);
      int code = 0;
      code_stream >> code;

      // 转换失败
      if (!code_stream.eof() || code_stream.fail()) {
        std::cout << "Invalid exit code\n";
        continue;
      }

      return code;
    }

    if (args[0] == "pwd") {
      std::cout << "To be done!\n";
      continue;
    }

    if (args[0] == "cd") {
      std::cout << "To be done!\n";
      continue;
    }

    // 处理外部命令
    pid_t pid = fork();

    // std::vector<std::string> 转 char **
    char *arg_ptrs[args.size() + 1];
    for (auto i = 0; i < args.size(); i++) {
      arg_ptrs[i] = &args[i][0];
    }
    // exec p 系列的 argv 需要以 nullptr 结尾
    arg_ptrs[args.size()] = nullptr;

    if (pid == 0) {
      // 这里只有子进程才会进入
      // execvp 会完全更换子进程接下来的代码，所以正常情况下 execvp 之后这里的代码就没意义了
      // 如果 execvp 之后的代码被运行了，那就是 execvp 出问题了
      execvp(args[0].c_str(), arg_ptrs);

      // 所以这里直接报错
      exit(255);
    }

    // 这里只有父进程（原进程）才会进入
    int ret = wait(nullptr);
    if (ret < 0) {
      std::cout << "wait failed";
    }
  }
}

// 经典的 cpp string split 实现
// https://stackoverflow.com/a/14266139/11691878
std::vector<std::string> split(std::string s, const std::string &delimiter) {
  std::vector<std::string> res;
  size_t pos = 0;
  std::string token;
  while ((pos = s.find(delimiter)) != std::string::npos) {
    token = s.substr(0, pos);
    res.push_back(token);
    s = s.substr(pos + delimiter.length());
  }
  res.push_back(s);
  return res;
}
```
