# Ray

## Ray 背景介绍

Ray 是伯克利大学 RISELab 研发的分布式计算系统，我愿称之为“AI 时代的 Spark”。

按照官方文档的说法：
Ray provides a simple, universal API for building distributed applications.

为什么说 Ray 是一个简单高效的分布式计算引擎呢？它主要有以下的特点：

- 提供一种能够构建、运行分布式应用程序的 simple primitives；
- 从单机扩展到平行，几乎不需要改代码；
- 拥有良好的生态，能够在 core Ray 上构建复杂的应用程序。

## Ray 基础知识介绍

Ray 能够让开发者轻松的构建分布式变成，靠的是两样法宝（计算原语）：

- Task：一个无状态的计算任务（函数表示）。Ray 允许异步执行任意函数。这些“remote function”（Task）的开销非常低，可以在毫秒内执行，并且可以自动向集群添加节点并调度任务，非常适合扩展计算密集型应用程序和服务。

- Actor：一个有状态的计算任务（类表示）。Actor 模型是一个强大的异步编程范例（支持微服务），可以在本地和远程无缝工作。Actor 本质上是一个有状态的 Worker（或 service）。当一个新的 Actor 被实例化时，就创建一个新的 Worker，并将该 Actor 的方法调度到这个特定的 Worker，也可以对 Worker 的状态进行访问和修改。

通过以下 6 个 API 就可以完成 Ray 的绝大部分功能：

- `ray.init()`:在应用程序中初始化 Ray。

Example:

```python
ray.init() # 可以指定参数
```

- `@ray.remote`:修饰函数，则该函数变为一个远程 Task；修饰类，则该类变为一个远程 Actor。

Example:

```python
@ray.remote                     # 定义一个 Task
def train_model(source):
    ...

@ray.remote                     # 定义一个 Actor
class ActivityTracker():
    def record(event):
        ...
        return count
```

- `x.remote`:构造一个 Actor 实例，或是异步运行一个 Task 或 Actor 的方法。

Example:

```python
m_id = train_model.remote(...)       # 调用一个 Task

tracker = ActivityTracker.remote()   # 构造一个 Actor 实例
tr_id =  tracker.record.remote(...)  # 调用一个 Actor 方法
```

- `ray.put()`:将一个值放入分布式对象存储中

Example:

```python
put_id = ray.put(my_object)
```

- `ray.get()`:从分布式对象存储中获取一个对象，这个对象可以是由 ray.put 显式存入的，也可以是由 Task 或 Actor 方法存入的。这个方法会一直阻塞直到获取的对象可用。

Example:

```python
model = ray.get(m_id)           # 获取 train_model 任务的结果
count = ray.get(tr_id)          # 获取 tracker.record 方法的返回结果
thing = ray.get(put_id)         # 获取 my_object
```

- `ray.wait()`:返回已准备好的 ID 列表和尚未准备好的 ID 列表。第一个列表由对象引用组成，这些对象引用与对象存储中可用的对象相对应。第二个列表对应于其余的对象引用（可能已准备就绪，也可能尚未准备就绪）

Example:

```python
finished, running = ray.wait([m_id, tr_id])
```

Ray 的详细架构和设计可以参考原论文：
`Ray: A Distributed Framework for Emerging AI Applications.`

## Ray 性能测试及优化

Ray 的性能和 Ceph 不太一样，其不需要对各种参数进行调整。而是需要针对某一个计算任务，讲其代码拆解成不同模块，分别部署到 Ray worker 中，测试性能。不同的模块拆解方式会导致不同的性能。

官方文档中给出了参考的 Benchmark 程序：
https://docs.ray.io/en/latest/ray-air/benchmarks.html

同学们可以参考官方的 Benckmark，自行设计测试任务并进行模块拆分，测试其性能优化。

## 参考资料

https://zhuanlan.zhihu.com/p/399209343

https://docs.ray.io/en/latest/ray-air/benchmarks.html
