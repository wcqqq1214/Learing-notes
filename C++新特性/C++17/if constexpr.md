#### 1. 编译时 if 语句简介

`if constexpr` 是 C++17 引入的编译时 `if` 语句，它在编译时执行条件检查，根据条件的真假决定是否保留相应的分支代码。这种特性使得在编写模板函数和模板类时可以根据模板参数类型选择性地保留代码，从而简化模板元编程，并提高生成的代码的效率


---
#### 2. 使用 if constexpr 简化模板元编程的示例

以下示例展示了如何使用 `if constexpr` 简化模板元编程 : 
```cpp
#include <iostream>
#include <type_traits>

template <typename T>
auto print_type_info(const T& t) {
    if constexpr (std::is_integral_v<T>) {
        std::cout << t << " is an integral number." << std::endl;
    }
    else if constexpr (std::is_floating_point_v<T>) {
        std::cout << t << " is a floating-point number." << std::endl;
    }
    else {
        std::cout << "Unknown type." << std::endl;
    }
}

int main() {
    int i = 42;
    double d = 3.14;
    std::string s = "hello";
    print_type_info(i);
    print_type_info(d);
    print_type_info(s);
    
    return 0;
}
```
在这个示例中，`print_type_info` 函数根据参数类型选择性地执行不同的输出操作。`if constexpr` 根据类型特征值决定保留哪个分支代码


---
#### 3. if constexpr 与 SFINAE 的关系

SFINAE ( Substitution Failure Is Not An Error，替换失败不是错误 ) 是 C++ 模板元编程中的一种技巧，用于在编译时为模板函数和模板类生成合适的实例。SFINAE 基于编译器在遇到无法完成替换的模板参数时不产生错误，而是退化到其他可用的模板

`if constexpr` 与 SFINAE 在某种程度上有类似的作用，都可以实现根据模板参数类型选择性地执行代码。然而，`if constexpr` 的语法更加简洁和直观，使得在某些场景下，你不再需要复杂的 SFINAE 技巧

尽管如此，`if constexpr` 并不能完全替代 SFINAE。在某些情况下，例如需要根据模板参数的某种特性选择不同的函数重载时，SFINAE 仍然是必要的。在 C++20 中，引入了 `concept` 特性，使得 SFINAE 技巧变得更加简单和直观