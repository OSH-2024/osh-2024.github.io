# Makefile

`Makefile` 是一个用来描述源文件之间的依赖关系以及如何编译和链接这些源文件的脚本文件。它可以大大简化编译过程，使得代码的维护和管理更加方便。我们建议同学们在 lab2 以及 lab3 中使用 `Makefile` 编写编译脚本，以便助教检查实验。
`Makefile` 的功能非常强大，我们在此给出一些基础的介绍以及一些参考资料，如有感兴趣的同学可以参考或自行搜索相关资料。

`Makefile` 有一套专用的语法系统，大致语法规则如下：

```Makefile
target ... : prerequisites ...
    recipe
    ...
    ...
```

**target**
可以是一个 object file（目标文件），也可以是一个可执行文件，还可以是一个标签（label）。

**prerequisites**
生成该target所依赖的文件和/或target。

**recipe**
该 target 要执行的命令（任意的 shell 命令）。

这是一个文件的依赖关系，也就是说，`target` 这一个或多个的目标文件依赖于 `prerequisites` 中的文件，其生成规则定义在 `recipe` 中。

`prerequisites` 中如果有一个以上的文件比 `target` 文件要新的话，`recipe` 所定义的命令就会被执行。

以下是一个简单的 Makefile 示例：

```Makefile
# 定义编译器和编译选项
CC = g++
CFLAGS = -O2
VERSION = -std=c++11

# 定义目标文件和源文件
TARGET = program
SRCS = main.cpp utils.cpp

# 生成可执行文件
$(TARGET): $(SRCS)
    $(CC) $(CFLAGS) $(VERSION) -o $@ $^

# 清理文件
clean:
    rm -f $(TARGET)
```

在这个示例中：

`CC` 定义了使用的编译器（这里是 g++）。
`CFLAGS` 定义了编译选项（这里开启了编译优化选项-O2）。
`VERSION` 指定了编译器使用的 C++版本。
`TARGET` 定义了最终生成的可执行文件的名称。
`SRCS` 定义了源文件的列表。
`$(TARGET): $(SRCS)` 表示 `TARGET` 依赖于 `SRCS` 中的所有源文件。
`$(CC) $(CFLAGS) -o $@ $^` 是编译和链接的命令，`$@` 表示目标文件，`$^` 表示所有依赖文件。
`clean` 是一个伪目标，用于清理生成的可执行文件。
`$`后面紧跟某个变量即为在此处用这个变量的值替换其内容，例如，`$(CC)`将会被替换成变量 CC 的值，即 `g++`。

如果在当前目录只使用 `make` 命令，`Makefile` 会查找到文件中的第一个目标文件，在本示例中为 `$(TARGET)`，即 `program`，并使用 `$(CC) $(CFLAGS) -o $@ $^`，即 `g++ -O2 -o program main.cpp utils.cpp` 编译目标文件。

注：从网页上复制的 Makefile 文件会将 tab 缩进替换为四个空格，复制后请替换回去，以免 Makefile 无法识别命令。

## 练习

**「必做」**

以下是一些示例代码文件：

**main.cpp**

```cpp
#include <iostream>
#include "bubblesort.hpp"

int main() {
    std::vector<int> arr = {64, 34, 25, 12, 22, 11, 90};
    bubbleSort(arr);
    for (int num : arr) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
    return 0;
}
```

**bubblesort.cpp**

```cpp
#include <iostream>
#include <vector>

void bubbleSort(std::vector<int>& arr) {
    int n = arr.size();
    for (int i = 0; i < n - 1; ++i) {
        for (int j = 0; j < n - i - 1; ++j) {
            if (arr[j] > arr[j + 1]) {
                std::swap(arr[j], arr[j + 1]);
            }
        }
    }
}
```

**bubblesort.hpp**

```cpp
#include <iostream>
#include <vector>

void bubbleSort(std::vector<int>& arr);
```

请你将上述三段代码复制到lab0/目录下，并在该目录下：

参照上述示例 Makefile 格式，编写 Makefile 文件来编译这段代码，输出可执行文件命名为 bubble_sort.

**要求：**

- 使用 `make` 命令时，编译生成可执行文件
- 使用 `make clean` 命令时，删除生成的可执行文件

**「选做」**

请你参考 C++的宏机制以及 g++的编译选项，在 Makefile 中加入一段代码，以及对 cpp 源代码进行适当修改，来打印出冒泡排序中每次交换元素之后的 vector 的值。

**要求：**

- 使用 `make debug` 命令时，编译生成的可执行文件在每次交换元素之后会打印出 vector 的值 (debug 信息)，与此同时，使用 `make` 命令生成的可执行文件不会打印 debug 信息

**参考资料**：

1. https://seisman.github.io/how-to-write-makefile/introduction.html
2. https://www.gnu.org/software/make/manual/make.html
3. https://makefiletutorial.com/
