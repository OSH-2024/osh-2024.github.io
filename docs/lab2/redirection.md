# 重定向

形如 `ls > out.txt` 会将 `ls` 命令的标准输出（stdout）重定向到 `out.txt` 文件中，具体地说，会将 `out.txt` 关联到程序的标准输出，然后再运行相应的命令。类似的，`ls >> out.txt` 会将输出追加（而不是覆盖）到 `out.txt` 文件，`cat < in.txt` 会将程序的标准输入（stdin）重定向到文件 `in.txt`。

请为你的 shell 程序实现 `>`、`>>` 和 `<` 的功能。你可能要用到的函数：`open`、`close`、`dup` 等。

样例：

```shell
$ ls | cat -n | grep 1 > out
$ cat out
     1  Cargo.lock
$ cat < out
     1  Cargo.lock
$ ls | cat -n | grep 5 >> out
$ cat out
     1  Cargo.lock
     5  target
```

边界情况说明：

- 你不需要考虑含有内建命令的重定向；
- 你不需要考虑 `stderr` 的重定向；
- 你可以认为重定向符号的两边总是各有至少一个空格字符。

## 支持基于文件描述符的文件重定向、文件重定向组合（选做）

一般情况下，每个 Unix/Linux 命令运行时都会打开三个文件：

- 标准输入文件（stdin）：文件描述符为 0，Unix/Linux 程序默认从 stdin 读取数据；
- 标准输出文件（stdout）：文件描述符为 1，Unix/Linux 程序默认向 stdout 输出数据；
- 标准错误文件（stderr）：文件描述符为 2，Unix/Linux 程序会向 stderr 流中写入错误信息。

我们可以手动更改输入输出的文件描述符。请自行查找资料，实现以下这些文件重定向：

1. 形如 `cmd 10> out.txt` 和 `cmd 20< in.txt` 这样的命令会将打开文件描述符 10 和 20 并重定向到相应的文件；
2. `cmd <<< text` 会将 `"text\n"` 作为标准输入重定向给 `cmd`；
3. 以下命令会将字符串 `"this\noutput\n"` 作为标准输入重定向给 `cmd`：

```shell
cmd << EOF
this
output
EOF
```

|              要求               | 满分 |
| :-----------------------------: | :--: |
|         支持 `>` 重定向         |  5%  |
|         支持 `<` 重定向         |  5%  |
|        支持 `>>` 重定向         |  5%  |
| **「可选」** 支持数字文件描述符 |  5%  |
|   **「可选」** 支持文本重定向   |  5%  |
|  **「可选」** 支持 EOF 重定向   |  5%  |
