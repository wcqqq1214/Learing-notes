#### 1. std::variant 简介

C++17 引入了 `std::variant` 类模板，它是一个类型安全的联合体。`std::variant` 可以存储其类型参数中的任何一个类型，并在运行时保持其当前类型的信息。`std::variant` 对于在运行时处理多种类型的数据非常有用，它提供了一种类型安全且灵活的方式来表示和处理不同类型的数据


---
#### 2. 使用 std::variant 的示例

以下示例演示了如何使用 `std::variant` 存储多种类型的数据 : 
```cpp
#include <iostream>
#include <variant>
#include <string>

int main() {

    std::variant<int, double, std::string> my_variant;
    my_variant = 42;
    std::cout << "my_variant contains an int: " << std::get<int>(my_variant) << std::endl;
    
    my_variant = 3.14;
    std::cout << "my_variant contains a double: " << std::get<double>(my_variant) << std::endl;
    
    my_variant = "hello";
    std::cout << "my_variant contains a string: " << std::get<std::string>(my_variant) << std::endl;
    
    return 0;
}
```

在这个示例中，我们定义了一个 `std::variant` 类型的对象 `my_variant` ，可以存储 `int` 、`double` 和 `std::string` 类型的数据。然后，我们为 `my_variant` 分别赋值并输出结果


---
#### 3. std::variant 与其他联合类型的比较

1. C 联合体 : C 语言中的联合体 ( `union` ) 是一种灵活的数据结构，允许在同一内存区域中存储不同类型的数据。然而，C 联合体在使用时存在类型安全问题，因为它无法保留当前存储类型的信息。相比之下，`std::variant` 提供了类型安全的保证，并能自动处理类型间的转换和访问
2. `void*` 指针 : `void*` 指针可以用于表示任何类型的数据，但它不提供类型信息，因此在使用 `void*` 指针时，需要手动管理类型转换和内存管理。相比之下，`std::variant` 可以自动处理类型转换和内存管理，并提供了类型安全的访问方式
3. `boost::variant` : 在 C++17 之前，`boost::variant` 是 C++ 程序员常用的类型安全联合体实现。`std::variant` 的设计借鉴了 `boost::variant` ，它们的功能和用法非常相似。然而，`std::variant` 作为 C++17 标准库的一部分，不再需要依赖 Boost 库

