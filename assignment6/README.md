# 作业 6：Explore Courses（课程浏览）

截止时间：5 月 22 日（周五）晚上 11:59

## 概述

在本作业中，你将练习对 `std::optional` 的理解。我们会使用作业 1 中的同一个 `courses.csv`。本次作业你需要编写一个函数：尝试在 `CourseDatabase` 对象中查找某个 `Course` 并返回它。
你还将探索 `std::optional` 类自带的单子操作（monadic operations）。请阅读代码并查看 `CourseDatabase` 类，了解它的接口。

## 运行你的代码

要运行代码，首先需要编译它。打开一个终端（如果你使用 VSCode，按 <kbd>Ctrl+\`</kbd>，或在顶部选择 **Terminal > New Terminal**）。然后确认你位于 `assignment6/` 目录下，并运行：

```sh
g++ -std=c++23 main.cpp -o main
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
> g++ -static-libstdc++ -std=c++23 main.cpp -o main
> ```
>
> 才能看到输出。另外，生成的可执行文件可能叫 `main.exe`，这种情况下你需要这样运行代码：
>
> ```sh
> ./main.exe
> ```

## 第 0 部分：包含 `<optional>`

在 `main.cpp` 顶部 include `<optional>`，本次作业我们要用到 `std::optional`！

## 第 1 部分：编写 `find_course` 函数

这个函数接受一个字符串 `course_title`，它应该尝试在 `CourseDatabase` 对象的私有成员 `courses` 中查找对应的 `course`。返回类型应该是什么？（提示：传入的 `course_title` 可能有、也可能没有对应的 `Course`）

> [!NOTE]
> 你需要修改 `find_course` 的返回类型，它目前是 `FillMeIn`。

## 第 2 部分：修改 `main` 函数

注意我们在 `main` 函数中这样调用了 `find_course`：

```cpp
auto course = db.find_course(argv[1]);
```

现在，你需要使用[单子操作](https://en.cppreference.com/w/cpp/utility/optional)来正确地填充 `output` 字符串。我们一步步来看怎么做。

下面是你需要重现的行为，**但不能使用任何条件语句**（例如 `if` 语句）：
```cpp
if (course.has_value()) {
    std::cout << "Found course: " << course->title << ","
            << course->number_of_units << "," << course->quarter << "\n";
} else {
    std::cout << "Course not found.\n";
}
```

简单来说，如果找到了课程，那么 `main` 末尾的这一行

```cpp
std::cout << output << std::end;
```

应该输出：
```bash
Found course: <title>,<number_of_units>,<quarter>
```

如果没有找到课程，那么

```cpp
std::cout << output << std::end;
```

应该输出：
```bash
Course not found.
```

### 单子操作

一共有三个单子操作：[`and_then`](https://en.cppreference.com/w/cpp/utility/optional/and_then)、[`transform`](https://en.cppreference.com/w/cpp/utility/optional/transform) 和 [`or_else`](https://en.cppreference.com/w/cpp/utility/optional/or_else)。请阅读课程幻灯片中对它们各自的描述，并查看[标准库文档](https://en.cppreference.com/w/cpp/utility/optional)。你只需要用到其中 2 个单子操作。

你的代码最终应该大致是这样的：

```cpp
std::string output = course
    ./* monadic function one */ (/* ... */)
    ./* monadic function two */ (/* ... */)
    .value();                                  // 或者 `.value_or(...)`，见下文
```

**思考 `output` 的类型是什么，然后从那里倒推**，会很有帮助。请留意每个单子函数的作用，如下面的提示所述。

> [!NOTE]
> 回顾一下每个单子函数的作用。C++ 官方文档在这方面解释得不太好，所以我们在这里附上一份简短的参考。假设 `T` 和 `U` 是任意类型。
>
> ```cpp
> /**
>  * 太长不看版：
>  * 如果有值，就调用一个函数来产生一个新的 optional；否则什么都不返回。
>  *
>  * 传给 `and_then` 的函数接受一个类型为 `T` 的非 optional 实例，并返回一个 `std::optional<U>`。
>  * 如果 optional 有值，`and_then` 会把函数应用到这个值上并返回结果。
>  * 如果 optional 没有值（即它是 `std::nullopt`），则返回 `std::nullopt`。
>  */
> template <typename U>
> std::optional<U> std::optional<T>::and_then(std::function<std::optional<U>(T)> func);
>
> /**
>  * 太长不看版：
>  * 如果存有值，就对其应用一个函数并把结果包装成 optional；否则什么都不返回。
>  *
>  * 传给 `transform` 的函数接受一个类型为 `T` 的非 optional 实例，并返回一个类型为 `U` 的非 optional 实例。
>  * 如果 optional 有值，`transform` 会把函数应用到这个值上，并返回包装在 `std::optional<U>` 中的结果。
>  * 如果 optional 没有值（即它是 `std::nullopt`），则返回 `std::nullopt`。
>  */
> template <typename U>
> std::optional<U> std::optional<T>::transform(std::function<U(T)> func);
>
> /**
>  * 太长不看版：
>  * 如果 optional 有值就返回它自身；否则调用一个函数来产生一个新的 optional。
>  *
>  * 与 `and_then` 相反。
>  * 传给 `or_else` 的函数不接受任何参数，并返回一个 `std::optional<U>`。
>  * 如果 optional 有值，`or_else` 就返回它。
>  * 如果 optional 没有值（即它是 `std::nullopt`），`or_else` 会调用该函数并返回其结果。
>  */
> template <typename U>
> std::optional<U> std::optional<T>::or_else(std::function<std::optional<U>(T)> func);
> ```
>
> 例如，给定一个 `std::optional<T> opt` 对象，可以像下面这样调用单子操作：
>
> ```cpp
> opt
>   .and_then([](T value) -> std::optional<U> { return /* ... */; })
>   .transform([](T value) -> U { return /* ... */; });
>   .or_else([]() -> std::optional<U> { return /* ... */; })
> ```
>
> <sup>注意，lambda 函数中的 `->` 写法是一种显式写出函数返回类型的方式！</sup>
>
> 注意，由于每个方法都返回一个 `std::optional`，你可以把它们链式调用。如果你确定在链的末尾 optional 一定有值，可以调用 [`.value()`](https://en.cppreference.com/w/cpp/utility/optional/value) 来获取该值。否则，你可以调用 [`.value_or(fallback)`](https://en.cppreference.com/w/cpp/utility/optional/value_or)，在 optional 有值时获取结果，没有值时获取另外的 `fallback` 值。



## 🚀 提交说明

如果你通过了所有测试，就可以提交了！提交作业的方法：
1. 请填写[这个链接](https://forms.gle/aGuFqLyhB18mNoPKA)中的反馈表。
2. 在 [Paperless](https://paperless.stanford.edu) 上提交你的作业！

你需要提交的内容：

- `main.cpp`

在截止日期之前，你可以重复提交任意多次。
