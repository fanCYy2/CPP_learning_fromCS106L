<p align="center">
  <img src="docs/marriage_pact.png" alt="Marriage Pact 标志" />
</p>

# 作业 2：Marriage Pact（婚约配对）

## 概述

作业 2 快乐！这是一个非常简短、轻松的小练习，帮助你开始使用 STL 的容器和指针。

你需要关心的文件有：

- `main.cpp`：你所有的代码都写在这里 😀！
- `short_answer.txt`：简答题的回答写在这里 📝！

要下载本次作业的初始代码，请参阅课程作业仓库中的 [**入门指南（Getting Started）**](../README.md#getting-started) 说明。

## 运行你的代码

要运行代码，首先需要编译它。打开一个终端（如果你使用 VSCode，按 <kbd>Ctrl+\`</kbd>，或在顶部选择 **Terminal > New Terminal**）。然后确认你位于 `assignment2/` 目录下，并运行：

```sh
g++ -std=c++20 main.cpp -o main
```

如果代码编译没有任何编译错误，你就可以运行：

```sh
./main
```

这会真正运行 `main.cpp` 中的 `main` 函数。

在按照下面的说明进行操作时，我们建议你时不时地编译并用自动评分程序测试一下，以确保你走在正确的方向上！

> [!NOTE]
>
> ### Windows 用户注意
>
> 在 Windows 上，你可能需要使用以下命令编译代码
>
> ```sh
> g++ -static-libstdc++ -std=c++20 main.cpp -o main
> ```
>
> 才能看到输出。另外，生成的可执行文件可能叫 `main.exe`，这种情况下你需要这样运行代码：
>
> ```sh
> ./main.exe
> ```

## 第 0 部分：准备工作

欢迎来到 Marriage Pact！开始之前，我们需要知道你的名字。请把 `main.cpp` 顶部的常量 `kYourName` 从 `"STUDENT TODO"` 改成你的全名（名和姓之间用一个空格隔开）。

## 第 1 部分：获取所有申请者

你已经等了好几天，就为了拿到今年 Marriage Pact 给你的配对对象的首字母缩写，现在它终于出现在你的收件箱里了！今年他们实施了一条新规则：你的配对对象必须和你的首字母缩写相同才有资格。然而，即使和朋友们讨论了好几个小时，你还是完全不知道你的配对对象会是谁！校园里有成千上万的学生，你不可能手动翻遍整个名册来列出你所有潜在的灵魂伴侣。幸运的是，你正在上 CS106L，而且你记得 C++ 有一种能快速处理一组同类信息的方法——容器！

我们提供了一个 `.txt` 文件（`students.txt`），里面是今年报名参加 Marriage Pact 的所有（虚构的）学生。每一行包含一个学生的名和姓。你首先要编写函数 `get_applicants`：

> [!IMPORTANT]
>
> ### `get_applicants`
>
> 从 `.txt` 文件中把所有名字解析到一个 set 中。名为 `filename` 的文件中每一行都是一位申请者的名字。在你的实现中，可以根据自己的意愿自由选择有序集合（`std::set`）或无序集合（`std::unordered_set`）！如果你选择使用无序集合，请相应地修改相关的函数定义！

此外，请在 `short_answer.txt` 中回答以下简答题：

> [!IMPORTANT]
>
> ### `short_answer.txt`
>
> **Q1：** 使用有序集合还是无序集合由你决定。请用几句话说明两者之间有哪些权衡取舍。此外，请给出一个（课堂上没有展示过的）有效哈希函数的例子，它可以用于在无序集合中对学生姓名进行哈希。

> [!NOTE]
> 本作业中出现的所有姓名均为虚构。如与真实人物（无论在世或已故）有任何雷同，纯属巧合。

## 第 2 部分：寻找配对

侦探工作干得漂亮！现在你已经缩小了潜在灵魂伴侣的范围，是时候检验一下了。在参加完一整天的无伴奏合唱团和咨询俱乐部活动之后，你回到宿舍，从室友那里得知当晚在 Main Quad 有一场 Marriage Pact 配对者的联谊会！你找到真爱的最佳机会就在眼前——只要你能逃掉极限飞盘训练。你迅速决定在联谊会上和每一个与你首字母缩写相同的人聊一聊，于是开始动手编写一个函数，自动帮你排好面谈顺序。

在这一部分，你需要编写函数 `find_matches` 和 `get_match`：

> [!IMPORTANT]
>
> ### `find_matches`
>
> 从（上一部分生成的）集合 `students` 中，找出所有与参数 `name` 首字母缩写相同的名字，并把指向它们的指针放入一个新的 `std::queue` 中。
>
> - 如果你不知道如何遍历一个 set，回顾一下[周四关于迭代器和指针的课程](https://office365stanford-my.sharepoint.com/:p:/g/personal/jtrb_stanford_edu/EbOKUV784rBHrO3JIhUSAUgBvuIGn5rSU8h3xbq-Q1JFfQ?e=BlZwa7)可能会有帮助。
> - 这一部分你需要熟悉 `std::queue` 的各种操作。可以看看 cppreference 的文档[这里](https://en.cppreference.com/w/cpp/container/queue)。
> - 提示：定义一个计算学生姓名首字母缩写的辅助函数可能会有帮助。然后你就可以用这个辅助函数，把 `name` 的首字母缩写与 `students` 中每个名字的首字母缩写进行比较。

接下来，请实现函数 `get_match` 来找到你的"唯一真爱"：

> [!IMPORTANT]
>
> ### `get_match`
>
> 从所有可能配对的队列中选出你的"唯一真爱"。具体怎么选由你决定；选择某种从队列中取出一个学生的方法，最好比单纯调用一次 `pop()` 多花点心思，但也不必特别复杂！可以考虑使用随机数或其他选择方式。
>
> 如果数据集中没有与你首字母缩写相同的人，请输出 `"NO MATCHES FOUND."`。明年好运 😢

之后，请在 `short_answer.txt` 中回答以下问题：

> [!IMPORTANT]
>
> ### `short_answer.txt`
>
> **Q2：** 注意我们在队列中保存的是指向名字的指针，而不是名字本身。为什么在这个问题中可能需要这样做？如果存放这些名字的原始集合超出了作用域，而这些指针又被解引用，会发生什么？

## 🚀 提交说明

提交作业的方法：
1. 请把你的 `main.cpp` 和 `short_answer.txt` 一起打包成一个 `.zip` 文件。
2. 用你的斯坦福邮箱把 `.zip` 文件发送到 `cs106l-aut2627-staff@lists.stanford.edu`，邮件主题为 `CS106L Assignment 2 Submission`。

你需要提交的内容：

- `main.cpp`
- `short_answer.txt`

在截止日期之前，你可以重复提交任意多次。
