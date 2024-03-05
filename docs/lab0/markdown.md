# Markdown

我们 **强烈建议** 同学们使用 Markdown 撰写实验报告。因此，本节将推荐一些好用的 Markdown 编辑器，并介绍一些 Markdown 常用语法。如果你已有用着顺手的编辑器并掌握了基本语法，可以直接开始练习。

## Markdown 编辑器

- Typora：支持 Windows，OS X 和 Linux，支持即时渲染以及多种文档格式转换
- VSCode：推荐插件：Markdown All in One, Markdown Preview Enhanced, Markdown Preview Github Styling

## Markdown 常用语法

### 标题

在行首插入 1 到 6 个 # ，对应到标题 1 到 6 阶，例如：

```text
# this is H1
## this is H2
###### this is H6
```

### 字体

Markdown 使用星号（\*）和底线（\_）作为标记强调字词的符号，你可以随便用你喜欢的样式，唯一的限制是，你用什么符号开启标签，就要用什么符号结束。但个人感觉写中文时还是（\*）比较好用，因为它不区分全角半角，不用切换输入法。示例：

```text
**这是加粗**
__这也是加粗__
*这是倾斜*
_这也是倾斜_
***这是加粗倾斜***
~~这是加删除线~~
```

注意：强调也可以直接插在文字中间，但是如果你的 \* 和 \_ 两边都有空白的话，它们就只会被当成普通的符号。如果要在文字前后直接插入普通的星号或底线，你可以用反斜线 \ 。

### 引用

在引用的文字前加 > 即可。在 Markdown 文件中建立一个区块引用，那会看起来像是你自己先断好行，然后在每行的最前面加上 > ：

```text
> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
> consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
> Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
>
> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
> id sem consectetuer libero luctus adipiscing.
```

效果如下：

> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse id sem consectetuer libero luctus adipiscing.

引用的区块内也可以使用其他的 Markdown 语法，包括标题、列表、代码区块等。

### 列表

Markdown 支持有序列表和无序列表。无序列表使用星号、加号或是减号作为列表标记。示例：

```text
- 列表内容
+ 列表内容
* 列表内容

注意：- + * 跟内容之间都要有一个空格
```

有序列表则使用数字接着一个英文句点作为标记。示例：

```text
1. 列表内容
2. 列表内容
3. 列表内容

注意：序号跟内容之间要有空格
```

列表可以嵌套，上一级和下一级之间敲三个空格即可。

```text
* 一级无序列表内容

   * 二级无序列表内容
   * 二级无序列表内容
   * 二级无序列表内容
```

要让列表看起来更漂亮，你可以把内容用固定的缩进整理好：

```text
*   Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
    Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi,
    viverra nec, fringilla in, laoreet vitae, risus.
*   Donec sit amet nisl. Aliquam semper ipsum sit amet velit.
    Suspendisse id sem consectetuer libero luctus adipiscing
```

### 代码

在 Markdown 中加入代码块有两种方式：第一种，只要简单地缩进 4 个空格或是 1 个制表符就可以，

```text
这是一个普通段落：

    这是一个代码区块。

(当然，前面要有一个空行和前面的文字分隔开)
```

效果如下：

这是一个普通段落：

```text
这是一个代码区块。
```

第二种方法似乎是更为常用：

**单行代码**：代码之间分别用一个反引号包起来即可；

```text
这里有一句代码`int i;`。
```

效果如下：这里有一句代码`int i;`。

**代码块**：代码之间分别用三个反引号包起来，且两边的反引号单独占一行

````text
\```
  代码...
  代码...
  代码...
\```
\ 是为了防止转译，实际是没有的。
````

效果如下：

```text
代码...
  代码...
  代码...
```

还可以在上面的 ``` 后面注明你的代码类型，可以产生相应的代码高亮。

### 图片

很明显地，要在纯文字应用中设计一个「自然」的语法来插入图片是有一定难度的。Markdown 使用一种和链接很相似的语法来标记图片，同样也允许两种样式： **行内式**和**参考式**。

**行内式**的图片语法看起来像是：

```text
![Alt pic](/path/to/img.jpg)

![Alt pic](/path/to/img.jpg "Optional title")
```

详细叙述如下：一个感叹号！接着一个方括号，里面放上图片的替代文字 \* 接着一个普通括号，里面放上图片的网址或本地路径（可以是相对路径或绝对路径），最后还可以用引号包住并加上 选择性的 '标题' 文字。

**参考式**的图片语法则长得像这样：

```text
![Alt pic][id]
```

「id」是图片参考的名称，图片参考的定义方式则和连结参考一样：

```text
[id]: url/to/image  "Optional title attribute"
```

**但是在这里有两个很大的问题：**

1. 如果使用本地路径插入本地图片，不灵活不好分享，本地图片的路径更改或丢失都会造成 md 文档调不出图。
2. 插入网络图片，则非常依赖网络，如果是本地图片，还需要先上传到服务器上。

解决方法：

- 将本地图片上传到 Github：在当前文档同目录或仓库根目录下新建 src 文件夹，将需要引用的图片上传至其中，在文档中使用其路径引用即可。

- 把图片存入 markdown 文件：用 base64 转码工具把图片转成一段字符串，把 base64 字符串放在文档末尾，然后在文档中通过一个 id 来调用。

受篇幅所限，这里仅介绍 Markdown 一些常用语法，想要了解更多的同学可以自行 Search。

## 练习

- **「必做」** 在仓库根目录下建立并提交 `README.md`，这是对仓库内容的说明文件；
  - 文件应该以一个 1 阶标题开头，例如：`# OSH 2024 labs`；
  - 请在该 README.md 中标明你的学号姓名信息。
- **「必做」** 在 Linux 环境输出自己设备信息并截图附到 md 文件里，提交至 `lab0/linux.md`；

???+ info "对输出的设备信息有什么要求？"

    没有特别要求，并不需要很全面。只是让大家上网搜索一下自己陌生的指令。

- **「必做」** 在仓库的 lab0 目录下新建文档 markdown.md 以及目录 src；
- **「必做」** 在 src 目录中上传一张你喜欢的图片并在 markdown.md 中对其进行引用；
- **「必做」** 在 markdown.md 中加入一段 C/C++ 代码块并对其注明代码类型以高亮；
- 在 markdown.md 中写下你最喜欢的一个数学公式（方法请自行查阅）。
