<p align="center">
  <img src="docs/logo.jpeg" alt="Treebook 的标志，一家虚构的斯坦福社交媒体创业公司" style="width: 300px; height: auto;" />
</p>

# 作业 5：Treebook

截止时间：5 月 15 日（周五）晚上 11:59

## 概述

斯坦福最新的社交媒体创业公司叫 Treebook，而你是团队的创始成员之一！为了让产品顺利起步，并与一个来自哈佛、未具名且在法律上毫无关联的应用竞争，你被分配了实现用户资料的任务。

在本作业中，你将实现一个类的部分内容以支持运算符重载，并修改特殊成员函数的某些方面。

本次作业你会用到两个文件：

* `user.h` - 包含 `User` 类的声明，你将为它扩展特殊成员函数和运算符。
* `user.cpp` - 包含 `User` 类的定义。

要下载本次作业的初始代码，请参阅课程作业仓库中的 [**入门指南（Getting Started）**](../README.md#getting-started) 说明。

## 运行你的代码

要运行代码，首先需要编译它。打开一个终端（如果你使用 VSCode，按 <kbd>Ctrl+\`</kbd>，或在顶部选择 **Terminal > New Terminal**）。然后确认你位于 `assign5/` 目录下，并运行：

```sh
g++ -std=c++20 main.cpp user.cpp -o main
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
> g++ -static-libstdc++ -std=c++20 main.cpp user.cpp -o main
> ```
>
> 才能看到输出。另外，生成的可执行文件可能叫 `main.exe`，这种情况下你需要这样运行代码：
>
> ```sh
> ./main.exe
> ```

## 第 1 部分：查看用户资料

看看 `user.h` 头文件。你的同事们已经开始编写一个 `User` 类，用来存储每个加入你们社交媒体平台的用户的姓名和好友列表！为了让这个类极致高效，他们选择用一个 `std::string` 的原始指针数组来表示好友列表（有点像 `std::vector` 在幕后存储元素的方式）。好在他们已经写好了创建新 `User` 以及向已有 `User` 的好友列表添加好友（`add_friend`）的逻辑，但在使用 `User` 对象时，他们开始遇到一些奇怪的问题。

首先，没有简便的方法把每个 `User` 对象的信息打印到控制台，这让 Treebook 的调试工作变得很困难。为了帮助同事们，请编写一个 `operator<<` 方法，把 `User` 打印到 `std::ostream`。**这个运算符应该在 `user.h` 中声明为友元函数，并在 `user.cpp` 中实现。** 例如，一个名为 `"Alice"`、好友为 `"Bob"` 和 `"Charlie"` 的用户，打印到控制台时应输出：

```
User(name=Alice, friends=[Bob, Charlie])
```

注意：`operator<<` 不应输出任何换行符。

> [!IMPORTANT]
> 在实现 `operator<<` 时，你需要访问并遍历 `User` 类的私有字段 `_friends`，才能打印出用户的好友。通常情况下，非成员函数无法访问类内部的私有字段——在这种情况下，我们可以通过**在 `User` 类内部把 `operator<<` 标记为友元函数**来绕过这个限制。更多信息请参阅周二课程的幻灯片！

## 第 2 部分：不友好的行为

借助你的 `operator<<`，同事们在这个社交媒体应用上取得了不错的进展。然而，当他们尝试在内存中复制 `User` 对象时，出现了一些看似离奇的问题，让他们百思不得其解。你最近刚上过 CS106L，怀疑这可能与 `User` 类的特殊成员函数（或者说缺少它们）有关。为了修复这个问题，我们将为 `User` 类实现自己版本的特殊成员函数（SMF），并删除另外一些编译器自动生成的版本不够用的特殊成员函数。

具体来说，你需要：

1. 为 `User` 类实现析构函数。为此，请实现 `~User()` 这个 SMF。
2. 让 `User` 类可以拷贝构造。为此，请实现 `User(const User& user)` 这个 SMF。
3. 让 `User` 类可以拷贝赋值。为此，请实现 `User& operator=(const User& user)` 这个 SMF。
4. 禁止 `User` 类被移动构造。为此，请删除 `User(User&& user)` 这个 SMF。
5. 禁止 `User` 类被移动赋值。为此，请删除 `User& operator=(User&& user)` 这个 SMF。

在完成这些任务时，你需要**同时**修改 `user.h` 和 `user.cpp` 文件。

> [!IMPORTANT]
> 在实现上面第 2 点和第 3 点时，你需要复制 `_friends` 数组的内容。回想一下周四关于特殊成员函数的课程：你可以先为新数组分配内存（可以在成员初始化列表中完成），然后用 for 循环把元素逐个复制过去，从而复制一个指针数组。
> 别忘了同时设置你正在修改的实例的 `_size`、`_capacity` 和 `_name`！

## 第 3 部分：永远在加好友

修改了特殊成员函数之后，你成功地把 Treebook 推广到了整个斯坦福，消息也开始在其他大学传开！然而，你和同事们发现，按照这个类目前的写法，`User` 类的一些常见用法要么不方便，要么根本无法实现。你认为可以通过实现一些自定义运算符来解决这个问题。

你将为 `User` 类重载两个运算符。**请把这两个运算符都实现为成员函数**（即在 `user.h` 的 `User` 类内部声明它们，并在 `user.cpp` 中提供实现）。

### `operator+=`

`+=` 运算符表示把一个用户添加到另一个用户的好友列表中。这应该是对称的，也就是说，例如把 Charlie 添加到 Alice 的好友列表中，应该同时让 Alice 也出现在 Charlie 的列表中。例如，考虑下面的代码：

```cpp
User alice("Alice");
User charlie("Charlie");

alice += charlie;
std::cout << alice << std::endl;
std::cout << charlie << std::endl;

// 预期输出：
// User(name=Alice, friends=[Charlie])
// User(name=Charlie, friends=[Alice])
```

这个运算符的函数签名应为 `User& operator+=(User& rhs)`。注意，和拷贝赋值运算符一样，它返回对自身的引用。

### `operator<`

回想一下，要把用户存入 `std::set`，就需要 `<` 运算符，因为 `std::set` 是基于比较运算符实现的。请实现 `operator<`，按姓名的字母顺序比较用户。例如：

```cpp
User alice("Alice");
User charlie("Charlie");

if (alice < charlie)
  std::cout << "Alice is less than Charlie";
else
  std::cout << "Charlie is less than Alice";

// 预期输出：
// Alice is less than Charlie
```

这个运算符的函数签名应为 `bool operator<(const User& rhs) const`。

## 🚀 提交说明

如果你通过了所有测试，就可以提交了！提交作业的方法：
1. 请填写[这个链接](https://forms.gle/tfLJSKnuUbUx9Xdi6)中的反馈表。
2. 在 [Paperless](https://paperless.stanford.edu) 上提交你的作业！

你需要提交的内容：

- `user.h`
- `user.cpp`

在截止日期之前，你可以重复提交任意多次。
