<p align="center">
  <img src="docs/art.png" />
</p>

# 作业 7：Unique Pointer（独占指针）

截止时间：5 月 31 日（周日）晚上 11:59

## 概述

在本作业中，你将实现一个自定义版本的 `unique_ptr`，以便接触本周课上介绍的 RAII 和智能指针等概念。此外，你还将练习整个课程中学到的一些技能：模板、运算符重载和移动语义。

本次作业你会用到三个文件：

- `unique_ptr.h` - 包含你的 `unique_ptr` 实现的全部代码。
- `main.cpp` - 包含一些使用你的 `unique_ptr` 的代码。你将在这里编写一个函数！
- `short_answer.txt` - 包含几道简答题，你需要在完成作业的过程中回答它们。

## 运行你的代码

要运行代码，首先需要编译它。打开一个终端（如果你使用 VSCode，按 <kbd>Ctrl+\`</kbd>，或在顶部选择 **Terminal > New Terminal**）。然后确认你位于 `assign7/` 目录下，并运行：

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

## 第 1 部分：实现 `unique_ptr`

在作业的第一部分，你将实现我们在周四课上讨论过的一种智能指针：`unique_ptr`。你要实现的 `unique_ptr` 是标准库 [`std::unique_ptr`](https://en.cppreference.com/w/cpp/memory/unique_ptr) 的简化版本。回想一下，`unique_ptr` 表示一个指向动态分配内存的指针，这块内存由单个（*唯一的*）变量拥有。当该变量超出作用域时，它会通过调用 `delete` 自动清理它所拥有的已分配内存。这种行为被称为 RAII（资源获取即初始化，resource acquisition is initialization）。**就本作业而言，你可以假设 `unique_ptr` 指向单个类型为 T 的元素。你在任何时候都不需要调用 `delete[]`，也不需要处理指向动态分配数组的指针。**

> [!IMPORTANT]
> ##### `short_answer.txt`
> **Q1：** 列举一到两个使用 RAII 管理内存、而不是手动调用 `new` 和 `delete` 的好处。

> [!NOTE]
> 虽然我们的 `unique_ptr` 不支持指向数组的指针，但如果需要，我们也可以添加这一行为。例如，C++ 标准库的 `std::unique_ptr` 使用*模板特化*来为数组指针实现不同的行为。这样的模板特化可能如下所示：
>
> ```cpp
> template <typename T>
> class unique_ptr<T[]>;
> ```
>
> 实际上，我们会有两个版本的 `unique_ptr`：一个用于单个元素，一个用于元素数组。每个版本支持不同的操作；例如，数组版本提供下标运算符（`operator[]`）来访问数组中的元素，而单元素版本则没有。

### 实现 `unique_ptr` 的功能

花点时间浏览一下 `unique_ptr.h` 中提供的 `unique_ptr` 代码。我们已经提供了 `unique_ptr` 的基本接口：你需要实现这个接口。记住，`unique_ptr` 的外观和行为应该像一个普通指针，支持解引用（`operator*`）和成员访问（`operator->`）等操作。其中有几个方法同时有 `const` 和非 `const` 版本，以使我们的类完全满足 const 正确性。

你将通过实现以下各项来完成 `unique_ptr` 的基本指针接口。每一项任务都相对简单，只需在 `unique_ptr.h` 中添加/修改 1-2 行即可完成：

* `unique_ptr` 的 `private` 部分
* `unique_ptr(T* ptr)`（构造函数）
* `unique_ptr(std::nullptr_t)`（用于 `nullptr` 的构造函数）
* `T& operator*()`
* `const T& operator*() const`
* `T* operator->()`
* `const T* operator->() const`
* `operator bool() const`

### 实现 RAII

到这里，我们的 `unique_ptr` 表现得就像一个原始指针，但它并不会真正进行任何自动内存管理，例如在 `unique_ptr` 变量超出作用域时释放内存。另外，我们的指针也不是*独占的*：可以随意复制出多个副本（全都指向同一块内存）。例如，假设我们的 `unique_ptr` 在超出作用域时能够正确清理它的数据。考虑下面的代码块：

```cpp
int main()
{
  unique_ptr<int> ptr1 = make_unique<int>(5);

  // ptr1 指向 5（动态分配在堆上）

  {

    unique_ptr<int> ptr2 = ptr1; // 浅拷贝

  } // <-- ptr2 的数据在这里被释放

  std::cout << *ptr1 << std::endl;
  return 0;
}
```

由于 `ptr1` 和 `ptr2` 指向同一块内存，当 `ptr2` 超出作用域时，它把 `ptr1` 的数据也一并带走了！结果，`*ptr1` 就成了未定义行为。

另一方面，我们仍然应该能够**移动**一个 `unique_ptr`。回想一下，移动语义允许我们在不进行昂贵拷贝的情况下接管一个对象的资源。移动一个独占指针是合法的，因为它保持了指针的唯一性——在任何时刻，指向底层内存的指针仍然只有一个。我们只是改变了由谁（哪个变量）拥有这块内存。

为了实现这些目标——自动释放内存、禁止拷贝、支持移动语义——我们必须为 `unique_ptr` 类实现一些特殊成员函数。**具体来说，请实现以下 SMF：**

* `~unique_ptr()`：释放指针的内存
* `unique_ptr(const unique_ptr& other)`：拷贝一个独占指针。应当被删除（delete）。
* `unique_ptr& operator=(const unique_ptr& other)`：拷贝赋值一个独占指针。应当被删除（delete）。
* `unique_ptr(unique_ptr&& other)`：移动一个独占指针。
* `unique_ptr& operator=(unique_ptr&& other)`：移动赋值一个独占指针。

实现上述函数后，你应该能通过**第 1 部分**的所有自动评分测试。

> [!IMPORTANT]
> ##### `short_answer.txt`
> **Q2：** 在为 `unique_ptr` 实现移动语义时，例如在移动构造函数 `unique_ptr(unique_ptr&& other)` 中，必须在函数退出前把参数 `other` 的底层指针设为 `nullptr`。请用你自己的话解释，如果不这样做会出现什么问题。

## 第 2 部分：使用 `unique_ptr`

既然我们已经有了 `unique_ptr` 的实现，那就来用一用吧！看看 `main.cpp`。我们为你提供了一个完整的单向链表（`ListNode`）实现，它利用 `unique_ptr` 确保链表中的所有节点都能被正确释放。例如，下面的代码会产生如下输出：

```cpp
int main()
{

  auto head = cs106l::make_unique<ListNode<int>>(1);
  head->next = cs106l::make_unique<ListNode<int>>(2);
  head->next->next = cs106l::make_unique<ListNode<int>>(3);

  // head 的内存布局：
  //
  // head -> (1) -> (2) -> (3) -> nullptr
  //
  //

} // <- `head` 在这里被析构！

// 输出：
// Constructing node with value '1'
// Constructing node with value '2'
// Constructing node with value '3'
// Destructing node with value '1'
// Destructing node with value '2'
// Destructing node with value '3'
```

注意，我们完全不需要调用 `delete`！`unique_ptr` 的 RAII 行为保证了链表中的所有内存都会被递归地释放。当 `head` 超出作用域时，它会调用节点 `(1)` 的析构函数，后者调用 `(2)` 的析构函数，`(2)` 再调用 `(3)` 的析构函数。

> [!IMPORTANT]
> ##### `short_answer.txt`
> **Q3：** 这种通过 RAII 递归释放内存的方法对短链表很有效，但对于较长的链表可能会带来问题。为什么？提示：一个递归函数的调用栈最多能有多"深"？

**你的任务是实现函数 `create_list`，它把一个 `std::vector<T>` 转换成一个 `unique_ptr<ListNode<T>>`。** vector 中元素的顺序应在链表中保持不变，对于空 vector 应返回 `nullptr`。实现方法有很多；其中一种是反向构建链表（从尾部开始，逐步向头部推进）。**注意，你必须使用 `cs106l` 命名空间下的 `cs106l::unique_ptr`，而不是 `std::unique_ptr`！** 下面是你在实现中应遵循的算法：

1. 初始化一个 `cs106l::unique_ptr<ListNode<T>> head = nullptr`。
2. **从后往前**遍历 `std::vector`。对 vector 中的每个元素：
    - 2a. 创建一个新的 `cs106l::unique_ptr<ListNode<T>> node`，其值为 vector 中的该元素。
    - 2b. 把 `node->next` 设为 `head`。
    - 2c. 把 `head` 设为 `node`。
3. 最后，返回 `head`。

> [!IMPORTANT]
> ##### `short_answer.txt`
> **Q4：** 在实现第 2b 和 2c 步时，你可能很难让编译器允许你进行赋值，例如把 `head` 赋给 `node->next`，因为它会报错说没有拷贝赋值运算符。这完全正确，因为正如我们之前讨论的，`unique_ptr` 不能被拷贝！
>
> 为了得到我们想要的行为，我们必须强制编译器把 `head` **移动赋值**给 `node->next`，而不是拷贝赋值。回想移动语义那节课，我们可以写 `node->next = std::move(head)` 来做到这一点。
>
> 在这个上下文中 `std::move` 做了什么？为什么在这里使用 `std::move` 和移动语义是安全的？

> [!NOTE]
> 在从后往前遍历 vector 时，小心不要用 `size_t` 作为下标。`size_t` 只能是非负整数，在检查 for 循环边界时试图减到零以下可能会导致意料之外的行为。
> 要解决这个问题，试着改用 `int`。

实现了 `create_list` 之后，我们就可以创建一个链表并把它打印出来了。想加分的话，可以看看 `map_list()` 和 `linked_list_example()` 这两个函数，它们一起调用你的 `create_list` 函数，并把链表的每个元素各打印在单独一行。到这里，你应该能通过**第 2 部分**的所有测试。

## 🚀 提交说明

如果你通过了所有测试，就可以提交了！提交作业的方法：
1. 请填写[这个链接](https://forms.gle/uHr3J8Vm3gECkZpm9)中的反馈表。
2. 在 [Paperless](https://paperless.stanford.edu) 上提交你的作业！

你需要提交的内容：

- `unique_ptr.h`
- `main.cpp`
- `short_answer.txt`

在截止日期之前，你可以重复提交任意多次。
