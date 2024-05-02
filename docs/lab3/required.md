# 必做部分

在 socket 简介一节，我们给出了一个简易的 HTTP Server 原型，它能够一个接一个的处理请求，当 `handle_clnt` 正在运行时，新的请求将被放置于请求缓冲区，在当前请求处理完成后，才会进行下一个请求的处理。

另外，这个版本的 HTTP 服务器存在以下问题：

- 没有实现并发处理，当某个客户端发送数据过慢时，新的请求等待时间过长；
- 没有很好的解析和检验 HTTP 头；
- 没有实现读取请求资源内容的功能；
- 没有实现返回不同的 HTTP 状态码；
- 没有实现错误和异常处理。

为了解决以上问题，本实验要求同学们在理解示例代码的基础上，实现一个高性能 HTTP Server，能尽可能准确、快速处理并发的请求。

??? question "实验误区"

    在做一个编程实验时，请避免下列误区：

    - **把精力花在与操作系统实验联系不大的部分**：比如为你的 URL 解析系统添加了过于繁杂的分析。

    - **搁置遇到不清楚的概念或技术**：文档中不少示例代码内容你可能会感到非常陌生，你需要做的是立即寻求教材/搜索引擎乃至 New Bing 以获得理解，或者通过一小段 demo 代码复现技术，这样方便之后定位程序问题。

    - **因为害怕「卷」而不在课程群中谈论任何有关实验思路的问题**：实验的目的是帮助大家通过上手来更好理解操作系统的设计与使用，实验的设计可以保证大部分同学在花费正常精力的情况下拿到接近满分。欢迎大家在课程群中讨论你遇到的问题或者你的 idea，这对你自己也是一种提升的机会。

    - **直接向助教求助或者要求助教帮忙看代码**：操作系统实验代码实现逻辑受个人思维影响较大，如果没有很好的松耦合编程意识，助教将无法从代码片段为你找出问题所在，出于公平原因更不可能直接帮你 debug，相反，实验文档中已经给出了（助教认为）足够的信息和提示，如果你仍旧有理解上的问题，欢迎在课程群中讨论。

## 并发请求处理

示例代码中，请求是**顺序阻塞**进行解析响应，这极大拖慢了 Server 的响应速度，我们可以将处理响应的部分移至新线程或新进程中处理，如：`fork()` 创建新进程来处理请求，也可以用 `pthread` 来创建新线程，这样能大大提高 Server 的并发性能，在多个请求到达时也能快速响应。

事实上，进程是用于资源分配和隔离的最小单位，它主要提供了地址空间这一抽象；而线程是用于调度的最小（而非唯一）单位，它是串行执行代码的单元。每个进程需要至少有一个线程才能实际执行；多个线程若同属于一个进程则共享地址空间。进程创建的开销要比线程大得多（显然新进程的创建至少伴随一个新线程的创建），因此，如果不需要资源隔离，应当使用线程来提高并发性能。需要注意的是，多线程对共享资源的访问需要加锁，如果你的实现用到了共享变量，你可能会需要使用互斥锁（mutex），这是操作系统提供的一种同步机制，用于保证多个线程对共享资源的访问是互斥的。

你需要在必做部分实现连接请求的多线程并发处理。

??? tip "pthread"

    如果你在类 Unix 系统下编程，可以选择使用 pthread 线程库，这里将对其基本用法做出介绍。

    ```c
    int pthread_create(pthread_t *thread, const pthread_attr_t *attr,     void *(*start_routine) (void *), void *arg);
    int pthread_join(pthread_t thread, void **retval);
    int pthread_detach(pthread_t thread);
    ```

    `pthread_create` 函数用于创建并启动一个新的线程。第一个参数为指向我们要创建的线程对象的指针；第二个参数为创建时的属性，目前可以忽略；第三个参数为线程启动后将要执行的函数指针；第四个参数为传入线程执行函数的数据。

    `pthread_join` 用于等待线程的结束，并接收返回值。第一个参数为线程对象，第二个参数为接收返回值的变量的指针，如果为 `NULL` 表示忽略返回值。

    `pthread_detach` 可以将线程与主线程分离，使该线程运行结束后得以终止自己并释放资源。分离后的线程将不能被 `pthread_join` 等待。

    另外 pthread 线程库还包含处理互斥锁的函数。

    ```c
    int pthread_mutex_init(pthread_mutex_t *restrict mutex, const pthread_mutexattr_t *restrict attr);
    int pthread_mutex_destroy(pthread_mutex_t *mutex);
    int pthread_mutex_lock(pthread_mutex_t *mutex);
    int pthread_mutex_unlock(pthread_mutex_t *mutex);
    ```

    正如函数名所示，这四个函数分别对应了互斥锁的初始化、销毁、加锁和解锁。你也可以直接使用 `PTHREAD_MUTEX_INITIALIZER` 宏来初始化互斥锁。

    pthread 也包含处理条件变量的函数，可用于阻塞和同步线程。

    ```c
    int pthread_cond_init(pthread_cond_t *restrict cond, const pthread_condattr_t *restrict attr);
    int pthread_cond_destroy(pthread_cond_t *cond);
    int pthread_cond_wait(pthread_cond_t *restrict cond, pthread_mutex_t *restrict mutex);
    ```

    下面的代码展示了条件变量和互斥锁简单的使用方法：

    ```c
    #include <stdio.h>
    #include <unistd.h>
    #include <pthread.h>

    pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
    pthread_cond_t cv = PTHREAD_COND_INITIALIZER;
    int ready = 0;
    int data;

    void *worker(void *p) {
        pthread_mutex_lock(&mutex);
        while (!ready) {
            pthread_cond_wait(&cv, &mutex);
        }
        printf("%d\n", data);
        pthread_mutex_unlock(&mutex);
        return NULL;
    }

    int main() {
        pthread_t thread;
        pthread_create(&thread, NULL, worker, NULL);
        pthread_mutex_lock(&mutex);
        sleep(1);
        data = 1234;
        ready = 1;
        pthread_cond_signal(&cv);
        pthread_mutex_unlock(&mutex);
        pthread_join(thread, NULL);
        return 0;
    }
    ```

    以上就是 pthread 线程库最基本的用法，如需进一步学习更高级的特性，请自行在互联网上搜索教程与文档。

??? tip "C++ 线程库"

    如果你比较熟悉 C++ ，也可以使用 C++ 标准库中的线程库。这里给出一个与上面的示例程序功能相同的例子：

    ```cpp
    #include <iostream>
    #include <thread>
    #include <mutex>
    #include <condition_variable>
    #include <chrono>

    int main() {
        int n = 0;
        int data;
        bool ready = false;
        std::mutex mutex;
        std::condition_variable cv;
        auto f = [&] {
            std::unique_lock<std::mutex> lock(mutex);
            cv.wait(lock, [&]{return ready;});
            std::cout << data << std::endl;
        };
        std::thread thread(f);
        std::this_thread::sleep_for(std::chrono::seconds(1));
        {
            std::lock_guard<std::mutex> lock(mutex);
            data = 1234;
            ready = true;
            cv.notify_one();
        }
        thread.join();
        return 0;
    }
    ```

    请自行参阅 C++ 手册学习线程库的用法。

??? tip "线程间通信"

    互斥锁用于对共享内存访问进行加锁，但正如 [Go Memory Model](https://golang.org/ref/mem) 中说的那样：

    > Don't communicate by sharing memory; share memory by communicating. (R. Pike)

    你也可以尝试一些直接的线程间通信方法，例如 [CSP(Communicating Sequential Process) 模型](https://swtch.com/~rsc/thread/)。CSP 模型中，线程之间通过通道进行通信，通道是单向的，只能从一端写入数据，从另一端读取数据。通道的写入和读取是原子操作，不会发生竞争。通道的写入和读取都是阻塞的，如果通道已满则写入线程会被阻塞，直到通道中的数据被读取；如果通道为空则读取线程会被阻塞，直到通道中有数据被写入。

    CSP 模型的实现也可能会用到互斥锁，但它相比我们手动编写控制内存共享的代码更加简单、安全乃至高效（因为一个高性能的实现可能会尽量多的用原子指令实现同步，性能开销更小）。对于编码而言，我们只需要知道一个通道有相应的读口和写口，调用读口和写口可以相应读出和写入数据即可，不需要关心内部的实现细节。这也是抽象带给我们的好处。

    对于 Rust 而言，你可以使用 [`std::sync::mpsc`](https://doc.rust-lang.org/std/sync/mpsc/)。C++ 用户可以自行探索实现方法。

## 解析和检验 HTTP 头

目前 `parse_request()` 只能处理正确的请求，对于畸形的请求，可能会造成严重问题，请根据 HTTP 协议简介一节完善这一部分的代码，使得对 HTTP 请求的检查更健壮。

对于本实验不要求实现的请求或畸形的请求，可以返回 `500 Internal Server Error`，例如 method 不是 GET，或者请求头不完整等。

## 实现读取请求资源内容

真正的 HTTP Server 会尝试返回用户请求的资源内容（文件内容），请参考 Python Server 的行为，实现这一部分内容。

例如：

- 请求的资源路径是 `/index.html` 则尝试返回当前运行目录下的 `index.html` 文件的内容；
- **请求的资源路径是一个目录，则返回 `500 Internal Server Error`**。

注意：

- 资源文件根目录为程序运行时的当前目录，**保证访问路径不会跳出当前路径，否则可以报错**；
- **返回头仅要求完成 `Content-Length`**；
- 当请求的资源（文件）不存在时，可以返回 `404 Not Found`。

???+ question "文件读取失败怎么办？"

    本实验不考虑这个情况。对于常见服务器，它们的做法是主动将 socket 异常关闭，以告知客户端文件没有按照预期被完整传送，客户端可能会显示文件下载失败。

## 实现错误和异常处理

示例代码中没有对各种错误和异常做良好的处理，请根据各个调用可能出错的情况进行分析，使得 Server 尽可能健壮，不会运行时崩溃，提高 Server 的可用性。
