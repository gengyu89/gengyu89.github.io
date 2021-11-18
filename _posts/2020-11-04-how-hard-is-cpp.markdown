---
layout:     post
title:      "C++ 有多难？ - 知乎"
subtitle:   "C++ 有多难？ - 纸条的回答 - 知乎"
date:       2020-11-04 12:00:00
author:     "heapify"
header-img: "img/home-bg.jpg"
catalog: true
tags:
    - 右值引用
    - 重载决议
    - 类型推导
    - 引用折叠
    - 构造函数自动推导
---

<span style="color:gray"> 1,319 人赞同了该回答 </span>

C++ 难就难在：<ins>在 C++ 中你找不到任何一件简单的事</ins>。

上面有人把 C++ 和物理作类比。我很同意。理论物理是一场无尽的旅程，总有最前沿的东西。我对神经科学很感兴趣，也有幸与一个神经科学相关专业的学生交流过，她还给我发过资料，我很感激。然而我在知乎上看到过一个相关的讨论。一个人说“我小时候就想知道大脑是如何工作的，于是我学了神经科学，如今我已经是神经科学博士，依然不知道大脑是如何工作的”。所以我的求知欲只能暂且到此为止。C++ 亦是如此。

扯远了，我们来说 C++ 有多难吧。

我们只谈构造函数。假如我们有一个类`Teacher`。
```cpp
class Teacher
{
private:
    std::string name;
    std::string position;
};
```
我们考虑给`Teacher`类加上构造函数。
```cpp
class Teacher
{
private:
    std::string name;
    std::string position;

public:
    Teacher(const std::string& n, const std::string& p)
        : name(n), position(p) {}
};
```
虽然语义正确，但是如果我们的实参只为了传递给`Teacher`，传递之后而没有其他作用的话，那么这个实现是效率低下的。字符串的拷贝花销可观（关于`std::string`的 COW，SSO，view 的讨论是另一个故事了）。我们在 C++11 里面有*右值引用*和*move语义*，所以呢，我们可以改成这样。
```cpp
class Teacher
{
private:
    std::string name;
    std::string position;

public:
    Teacher(const std::string& n, const std::string& p)
        : name(n), position(p) {}

    Teacher(std::string&& n, std::string&& p)
        : name(std::move(n)), position(std::move(p)) {};
};
```
你可能觉得这样也已经不错了。不过我们还有可能第一个参数右值，第二个参数左值。或者第一个参数左值，第二个参数右值。所以实际上我们需要四个函数的重载。
```cpp
class Teacher
{
private:
    std::string name;
    std::string position;

public:
    Teacher(const std::string& n, const std::string& p)
        : name(n), position(p) {}

    Teacher(std::string&& n, std::string&& p)
        : name(std::move(n)), position(std::move(p)) {};

    Teacher(const std::string&& n, const std::string& p)
        : name(std::move(n)), position(p) {}

    Teacher(const std::string& n, const std::string&& p)
        : name(n), position(std::move(p)) {}
};
```
代码有点多。我们有没有什么方法写一个通用的函数来实现这四个函数呢？有。我们在 C++11 中有*完美转发*。
```cpp
class Teacher
{
private:
    std::string name;
    std::string position;

public:
    template <typename S1, typename S2>
    Teacher(S1&& n, S2&& p)
        : name(std::forward<S1>(n)), position(std::forward<S2>(p)) {};
};
```
完成了。美滋滋。然而事情没有这么简单。如果我们的`position`有默认值，然后我们写如下代码的话。
```cpp
class Teacher
{
private:
    std::string name;
    std::string position;

public:
    template <typename S1, typename S2 = std::string>
    Teacher(S1&& n, S2&& p = "lecturer")
        : name(std::forward<S1>(n)), position(std::forward<S2>(p)) {};
};


int main()
{
    Teacher t1 = { "david", "assistant" };
    Teacher t2{ t1 };
}
```
我们出现了编译期错误。因为`Teacher t2{ t1 };`的*重载决议*的最佳匹配是我们的模板，而不是默认的拷贝构造函数，因为拷贝构造函数要求`t1`是`const`的。所以，我们可能需要`SFINAE`和`type traits`来修改我们的代码。注意，默认函数参数不能*类型推导*，所以我们才需要的`S2`的默认模板参数。
```cpp
class Teacher
{
private:
    std::string name;
    std::string position;

public:
    template <typename S1, typename S2 = std::string,
    typename = std::enable_if_t<!std::is_same_v<S1, Teacher>>>
    Teacher(S1&& n, S2&& p = "lecturer")
        : name(std::forward<S1>(n)), position(std::forward<S2>(p)) {};
};
```
仍然不对哦，因为我们的完美转发有*引用折叠*机制，我们应该判断的`S1`是`Teacher&`而不是`Teacher`。其次，如果有类继承我们的`Teacher`的话，拷贝的时候依然会出现这个问题，所以我们需要的不是`is_same`而是`is_convertible`。然而，如果我们直接写`std::is_convertible_v<S1, Teacher>`的话，我们实际上判定是不是可以转换的时候，还是会去看我们的构造函数。也就是说我们自己依赖了自己，无穷递归。所以我们需要的是`std::is_convertible_v<S1, std::string>`。所以，我们修改我们的代码。
```cpp
class Teacher
{
private:
    std::string name;
    std::string position;

public:
    template <typename S1, typename S2 = std::string,
    typename = std::enable_if_t<std::is_convertible_v<S1, std::string>>>
    Teacher(S1&& n, S2&& p = "lecturer")
        : name(std::forward<S1>(n)), position(std::forward<S2>(p)) {};
};
```
其次，因为我们的默认参数是字面量，字面量是`const char[]`类型的。我们调用构造函数的时候，也会用字面量。字面量不是`std::string`类型会造成很多问题。然而在 C++14 中，我们可以用*User-defined literals*来把字面量声明成`std::string`类型的。不过记得命名空间，这个名字空间不在 std 中。我们这里不再讨论了。

我们接下来讨论用构造函数初始化的问题。初始化有很多种写法，以下我列出有限的几种。
```cpp
Teacher t1("david"s);
Teacher t2 = Teacher("david"s);

Teacher t3{ "lily"s };
Teacher t4 = { "lily"s };
Teacher t5 = Teacher{ "lily"s };

auto t6 = Teacher("david"s);
auto t7 = Teacher{ "lily"s };
```
我们用了`auto`。然而`auto`是*decay*的。而`decltype(auto)`不。所以，以下代码如果用`auto`的话可能不是你需要的。
```cpp
const Teacher& t8 = t1;
auto t10 = t8;
```
我们需要写`const auto&`。

此外，我们可以看出，用小括号和大括号好像没什么区别。不过，在一些情况下会有很大的差别。我们列出一些。
```cpp
std::vector<int> vec1(30, 5);
std::vector<int> vec2{ 30, 5 };
```
甚至因为 C++17 的*构造函数自动推导*，我们可以写出更加疯狂的代码。
```cpp
std::vector vec3{ vec1.begin(), vec2.end() };
```
这个代码是用初始化列表初始化的，也就是说我们得到的`vec3`中有两个 iterator。

好了，我们回过头来说`auto`。我们可以看到好像我们所有的初始化都可以用`auto`。是这样吗？如果我们写`atomic`的代码呢？
```cpp
auto x = std::atomic<int>{ 10 };
```
是可以的。因为在 C++17 中我们有*Copy Elision*。所以这里没有拷贝函数的调用，和直接定义并初始化是一致的。但是`atomic`初始化是有问题的。
```cpp
std::atomic<int> x{};
```
这样是不能零初始化的。当然了，这显然是 API 的不一致，或者说错误。所以我们有*LWG issue 2334*。预计在 C++20 修复这个问题。嘻嘻。

以上内容基于《C++ templates》的作者 Nicolai Josuttis 的几场 talk。

<span style="color:gray"> 编辑于 2018-12-31 </span>
