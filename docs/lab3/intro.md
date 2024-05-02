# 实验介绍

本实验通过 socket 编程，利用多线程技术，实现一个简易的支持并发的 HTTP/1.0 服务器，并可以利用线程复用、I/O 复用、缓存、预测等机制，提高 HTTP 服务器的性能。

??? question "先导知识"

    本实验涉及网络通信功能，这是操作系统主要提供的 I/O 功能之一。文档中可能会出现一些有关计算机网络的术语，为了方便同学们流畅地阅读实验文档，助教在这里给出一些术语的基本解释。如果你想详细了解，可以查阅各类百科。

    - **协议**：网络通信常常是以客户端和服务器之间的「会话」为载体的，这些会话消息在底层其实就是字符流。「协议」规定了客户端和服务器解释这些字符流的方式，为这些字符流赋予语义，以便双方计算机进行处理。

    - **HTTP**：一种网络协议，用于在客户端和服务器间传送 hypermedia（即 Web 内容），它定义了请求和响应消息的形式。

    - **TCP**：一种网络协议。这里不需要关心 TCP 协议的具体内容，只需要知道 TCP 协议能在两台主机间建立一条可靠的、双向的、基于字节流的通信。

    - **socket**：操作系统提供的网络通信接口，其原意为「插座」，在计算机领域一般被译为「套接字」。本实验中将大量用到操作系统关于 socket 的接口，请同学们先了解 socket 的工作原理再上手实验。

## 启动一个简易 HTTP Server

Python 自带了一个简单的 HTTP Server，我们可以通过以下命令来体验一个 HTTP Server 的工作：

```bash
# /tmp 可用于存放临时文件
# 会在系统重启后被清空，因此不要在这里存放代码文件
mkdir -p /tmp/webroot && cd /tmp/webroot
echo "Hello from Python Server" > hello.html

# Linux 发行版一般都至少预装了一个 Python 版本
# 根据实际情况选择下面指令之一执行

# 若使用 python2
python2 -m SimpleHTTPServer

# 若使用 python3
python3 -m http.server
```

当屏幕中出现：

```bash
Serving HTTP on 0.0.0.0 port 8000 ...
```

即说明该 Server 已经成功运行，地址 `0.0.0.0` 不是一个具体的地址，它代表 Server 监听在本地所有的 IPv4 地址上， `port 8000` 代表 Server 监听在端口 `8000` 上。可以在浏览器打开本地回环地址 `127.0.0.1` （这也是本地 IPv4 地址之一），端口 `8000` 访问这个 Server。

在浏览器中打开 <http://127.0.0.1:8000/hello.html>，就可以看到来自 Python Server 的返回内容。

## TCP 协议简介

实际的网络通信是分层的，每一层都有自己的协议，每一层都可以面向上一层提供服务。我们这里不需要关心 TCP 协议的具体内容，只需要知道 TCP 协议能在两台主机间建立一条可靠的、双向的、基于字节流的通信即可。操作系统已经帮我们封装好了 socket 接口用以提供 TCP 协议的服务，我们只需要调用 socket 接口就可以实现 TCP 协议的通信（具体接口说明见后文），进而实现实验要求的部分 HTTP/1.0 协议。

## HTTP 协议简介

HTTP/1.0 是一种基于文本的 TCP 协议，我们可以使用命令行工具 curl 以文本形式查看 HTTP 1.0 协议交互的全过程：

```bash
# curl --http1.0 -v http://127.0.0.1:8000/hello.html
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> GET /hello.html HTTP/1.0
> Host: 127.0.0.1:8000
> User-Agent: curl/7.52.1
> Accept: */*
>
* HTTP 1.0, assume close after body
< HTTP/1.0 200 OK
< Server: SimpleHTTP/0.6 Python/2.7.13
< Date: Tue, 14 May 2019 00:00:00 GMT
< Content-type: text/html
< Content-Length: 25
< Last-Modified: Mon, 13 May 2019 00:00:00 GMT
<
Hello from Python Server
* Curl_http_done: called premature == 0
* Closing connection
```

其中 `>` 开头的为请求（request）内容，本实验中，我们可以忽略第三行、第四行，得到必要的请求内容如下：

```bash
> GET /hello.html HTTP/1.0
> Host: 127.0.0.1:8000
>
```

第一行是 HTTP 请求行，`GET` 代表 HTTP 方法，向指定的资源发出“显示”请求；`/hello.html` 为请求的资源路径；`HTTP/1.0` 为请求的协议版本。

接下来的行是请求行，`Host: 127.0.0.1:8000` 指明了请求的 Host，服务器的域名，以及服务器所监听的传输控制协议端口号。

注意，最后还有一个空行，这是不可省略的。

`<` 开头的为响应（response）内容，本实验中，我们只关注这些内容：

```
< HTTP/1.0 200 OK
< Content-Length: 25
<
Hello from Python Server
```

第一行为响应行，分别是协议版本和 HTTP 状态码，本实验中我们只考虑以下状态码：

```
200 OK 请求已成功，请求所希望的响应头或数据体将随此响应返回。

404 Not Found 请求失败，请求所希望得到的资源未被在服务器上发现。

500 Internal Server Error 本实验中未定义的各种错误。
```

接下来的行为响应头，其中 `Content-Length` 为回应消息体的长度，以字节为单位。

接着是一个空行，这是不可省略的。

空行之后，是返回的内容：`Hello from Python Server`，这就是我们请求的 `/hello.html` 的内容。

注意：

- 除了 `curl`，我们还可以使用 `nc 127.0.0.1 8000` 来手工构造请求测试。

- 现实生活中，HTTP 1.1 和 HTTP 2.0 协议使用更为广泛（以及不再基于 TCP 的 HTTP 3.0 协议），本次实验不做要求。关于 HTTP 1.1 协议的更多内容可以阅读参考资料中的 RFC 2616，也可以网上查找资料了解：`User-Agent`，`Accept` ，`Content-type` 等字段的含义和必要性。

## socket 简介

类似于本地进程间通过管道、消息队列、共享内存等通信，网络间的进程广泛使用 socket 进行通信，我们想要实现 HTTP server 就要实现一个 socket 服务端，接收来自客户端的请求，并根据客户端的的请求内容进行响应。

下面是本实验中可能用到的一些 socket 接口：

```
int socket(int af, int type, int protocol);
int bind(int sock, struct sockaddr *addr, socklen_t addrlen);
int listen(int sock, int backlog);
int accept(int sock, struct sockaddr *addr, socklen_t *addrlen);
```

请结合我们给出的一个有详细注释的 C 语言的例子掌握相应概念：

```c
// server.c
#include <math.h>
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>
#include <netinet/in.h>

#define BIND_IP_ADDR "127.0.0.1"
#define BIND_PORT 8000
#define MAX_RECV_LEN 1048576
#define MAX_SEND_LEN 1048576
#define MAX_PATH_LEN 1024
#define MAX_HOST_LEN 1024
#define MAX_CONN 20

#define HTTP_STATUS_200 "200 OK"

void parse_request(char* request, ssize_t req_len, char* path, ssize_t* path_len)
{
    char* req = request;

    // 一个粗糙的解析方法，可能有 BUG！
    // 获取第一个空格 (s1) 和第二个空格 (s2) 之间的内容，为 PATH
    ssize_t s1 = 0;
    while(s1 < req_len && req[s1] != ' ') s1++;
    ssize_t s2 = s1 + 1;
    while(s2 < req_len && req[s2] != ' ') s2++;

    memcpy(path, req + s1 + 1, (s2 - s1 - 1) * sizeof(char));
    path[s2 - s1 - 1] = '\0';
    *path_len = (s2 - s1 - 1);
}

void handle_clnt(int clnt_sock)
{
    // 一个粗糙的读取方法，可能有 BUG！
    // 读取客户端发送来的数据，并解析
    char* req_buf = (char*) malloc(MAX_RECV_LEN * sizeof(char));
    // 将 clnt_sock 作为一个文件描述符，读取最多 MAX_RECV_LEN 个字符
    // 但一次读取并不保证已经将整个请求读取完整
    ssize_t req_len = read(clnt_sock, req_buf, MAX_RECV_LEN);

    // 根据 HTTP 请求的内容，解析资源路径和 Host 头
    char* path = (char*) malloc(MAX_PATH_LEN * sizeof(char));
    ssize_t path_len;
    parse_request(req_buf, req_len, path, &path_len);

    // 构造要返回的数据
    // 这里没有去读取文件内容，而是以返回请求资源路径作为示例，并且永远返回 200
    // 注意，响应头部后需要有一个多余换行（\r\n\r\n），然后才是响应内容
    char* response = (char*) malloc(MAX_SEND_LEN * sizeof(char)) ;
    sprintf(response,
        "HTTP/1.0 %s\r\nContent-Length: %zd\r\n\r\n%s",
        HTTP_STATUS_200, path_len, path);
    size_t response_len = strlen(response);

    // 通过 clnt_sock 向客户端发送信息
    // 将 clnt_sock 作为文件描述符写内容
    write(clnt_sock, response, response_len);

    // 关闭客户端套接字
    close(clnt_sock);

    // 释放内存
    free(req_buf);
    free(path);
    free(response);
}

int main(){
    // 创建套接字，参数说明：
    //   AF_INET: 使用 IPv4
    //   SOCK_STREAM: 面向连接的数据传输方式
    //   IPPROTO_TCP: 使用 TCP 协议
    int serv_sock = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);

    // 将套接字和指定的 IP、端口绑定
    //   用 0 填充 serv_addr（它是一个 sockaddr_in 结构体）
    struct sockaddr_in serv_addr;
    memset(&serv_addr, 0, sizeof(serv_addr));
    //   设置 IPv4
    //   设置 IP 地址
    //   设置端口
    serv_addr.sin_family = AF_INET;
    serv_addr.sin_addr.s_addr = inet_addr(BIND_IP_ADDR);
    serv_addr.sin_port = htons(BIND_PORT);
    //   绑定
    bind(serv_sock, (struct sockaddr*)&serv_addr, sizeof(serv_addr));

    // 使得 serv_sock 套接字进入监听状态，开始等待客户端发起请求
    listen(serv_sock, MAX_CONN);

    // 接收客户端请求，获得一个可以与客户端通信的新的生成的套接字 clnt_sock
    struct sockaddr_in clnt_addr;
    socklen_t clnt_addr_size = sizeof(clnt_addr);

    while (1) // 一直循环
    {
        // 当没有客户端连接时，accept() 会阻塞程序执行，直到有客户端连接进来
        int clnt_sock = accept(serv_sock, (struct sockaddr*)&clnt_addr, &clnt_addr_size);
        // 处理客户端的请求
        handle_clnt(clnt_sock);
    }

    // 实际上这里的代码不可到达，可以在 while 循环中收到 SIGINT 信号时主动 break
    // 关闭套接字
    close(serv_sock);
    return 0;
}
```

编译运行方法：

```
gcc server.c -o server
./server
```

测试方法（新开一个终端）：

```bash
curl --http1.0 http://127.0.0.1:8000/hello
```

如果成功，将会收到内容为 `/hello` 的 HTTP 响应。

打开 `-v` (verbose) 选项，可以看到服务器返回的全部内容：

```bash
curl --http1.0 http://127.0.0.1:8000/hello -v
```

???+ question "重启 server 之后会连接失败？"

    这个问题和 socket 没有正确关闭有关。

    在出现这个问题时，可通过 `sudo netstat -naop4 | grep 8000` 看到处于 timewait 状态的 IPv4 的 TCP 连接以及回收需要等待的时间，（默认是 60s，可以 `cat /proc/sys/net/ipv4/tcp_tw_recycle` 查看）。在此期间，因为示例程序没有设置任何 reuse 选项，新启动的程序未能成功绑定端口，连接失败。

    在调试开发时，为了避免这个问题，可以暂时将 timewait 的回收时间 `tcp_tw_recycle` 改为一个较小的值，如：`sudo sh -c 'echo 1 > /proc/sys/net/ipv4/tcp_tw_recycle'`，这一改动在重启后会失效。

示例程序中使用到的各调用的具体用法请同学们使用 `man` 命令查阅手册，如：`man 2 listen`。

下面是一些本实验中需要重点理解的概念：

### 请求队列

当套接字正在处理客户端请求时，如果有新的请求进来，套接字是没法处理的，只能把它放进缓冲区，待当前请求处理完毕后，再从缓冲区中读取出来处理。如果不断有新的请求进来，它们就按照先后顺序在缓冲区中排队，直到缓冲区满。这个缓冲区，就称为请求队列（Request Queue）。

缓冲区的长度（能存放多少个客户端请求）可以通过 `listen()` 函数的 `backlog` 参数指定，但究竟为多少并没有什么标准，可以根据你的需求来定。如果将 `backlog` 的值设置为 `SOMAXCONN`，就由系统来决定请求队列长度，这个值一般比较大，可能是几百，或者更多。

当请求队列满时，就不再接收新的请求，对于 Linux，客户端会收到 `ECONNREFUSED` 错误，为了使得我们的服务器可用性尽可能高，这是我们要避免的。

???+ note "注意"

    `listen()` 只是让套接字处于监听状态，并没有接收请求。接收请求需要使用 `accept()` 函数。

### 数据的接收和发送

Linux 不区分文件描述符和套接字，也就是说，你可以使用 Lab2 中对于文件描述符中的各种操作完成对套接字的操作。

可能会用到的接口：

```
ssize_t write(int fd, const void *buf, size_t nbytes);
ssize_t read(int fd, void *buf, size_t nbytes);
```

???+ warning "网络 I/O 带来的问题"

    注意这两个接口使用时很容易出错，如 `read` 时，不能假定能一次读取完 HTTP 请求的全部内容，也不能假定读到 EOF 为止，而应该根据 HTTP 协议，不断尝试读取，直到读取到两个换行符（`\r\n\r\n`）时才算读取完成。

    同理，你也不能假定 `write` 函数可以一次写入所有内容。

具体用法请同学们自行查阅手册，掌握各参数及返回值的意义。
