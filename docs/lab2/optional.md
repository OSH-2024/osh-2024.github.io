# 拓展功能参考

## 支持 History

在使用 shell（这里特指 bash）的时候输入 `history n` 可以显示最近执行的 n 条指令（对于 zsh 用户，`history` 命令会被自动替换为内建的 `fc -l`，显示从 n 条命令开始的记录）。同时我们可以使用 `!n` 重复执行历史记录中的第 n 条命令，用 `!!` 命令重复执行上一条命令。

例如：

```shell
$ history 3
  843  echo osh-2024
  844  echo osh
  845  history 3
$ !843
echo osh-2024
osh-2024
$ !!
echo osh-2024
osh-2024
```

请为你的 shell 实现对历史命令的处理，支持 `!n`、`!!` 和 `history n` 命令，且 `history n` 的输出请尽可能与 bash 相同。

|                      要求                      | 满分 |
| :--------------------------------------------: | :--: |
|            **「可选」**支持 history            |  5%  |
| **「可选」**通过上下方向键切换到不同的历史命令 |  5%  |

## 处理 `CTRL-D` (EOF) 按键

在使用 shell 的时候输入 `exit` 即可退出 shell，`CTRL-D` (EOF) 在这种场景下等同于 `exit`。

???+info "提示"

    CTRL-D 并不会发送信号，而是输入一个特殊的 `EOF` 字符。

|           要求            | 满分 |
| :-----------------------: | :--: |
| **「可选」**支持 `CTRL-D` |  5%  |

## Bash 风格的 TCP 重定向

在精简的 Linux 环境中（如 Docker 容器里），常常是没有 `nc` 命令用来进行原始的 TCP 网络通信的。Bash 和一些其他 shell 支持一种特殊的重定向语法：`/dev/tcp/<host>/<port>`。

通过查看 [Bash 的 man 文档][bash.1]，`REDIRECTION` 一节，当重定向目标是下面几种路径，且操作系统没有提供这个路径时，Bash 会自行处理它们：

```text
/dev/fd/<fd>
/dev/stdin
/dev/stdout
/dev/stderr
/dev/tcp/<host>/<port>
/dev/udp/<host>/<port>
```

阅读相关文档，模拟 Bash 的行为实现 `cmd > /dev/tcp/<host>/<port>` 和 `cmd < /dev/tcp/<host>/<port>` 的重定向。你可能要用到的函数：`socket`, `connect`

方便起见，你只需要处理 `<host>` 是典型的 IPv4 地址（即 `a.b.c.d` 的形式，其中 abcd 均为 0 ~ 255 之间的整数）且 `<port>` 为 1 ~ 65535 之间的整数时的情况。

|            要求             | 满分 |
| :-------------------------: | :--: |
| **「可选」**支持 TCP 重定向 | 10%  |

[bash.1]: https://linux.die.net/man/1/bash

## 其他功能

我们一般使用的 shell 非常强大，你还可以自行了解下面这些语法的含义：

```shell
echo ~root               # 5% hint: /etc/passwd
echo $SHELL              # 5%
A=1 env                  # 5%
alias ll='ls -l'         # 5%
(sleep 10; echo aha) &   # 5%
```

可以自行选择实现。
