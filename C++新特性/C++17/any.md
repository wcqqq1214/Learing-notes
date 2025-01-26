#### 1. std::any 简介

`std::any` 是 C++17 中引入的一个类型安全的通用类型容器。它可以存储任意类型的数据，并在运行时保持其类型信息。`std::any` 对于在运行时处理多种类型的数据非常有用，尤其是在类型信息不确定的情况下


---
#### 2. 使用 std::any 存储任意类型的示例

以下示例演示了如何使用 `std::any` 存储和访问任意类型的数据 : 
```cpp
#include <iostream>
#include <any>
#include <string>

int main() {
    std::any my_any;
    
    my_any = 42;
    std::cout << "my_any contains an int: " << std::any_cast<int>(my_any) << std::endl;
    
    my_any = 3.14;
    std::cout << "my_any contains a double: " << std::any_cast<double>(my_any) << std::endl;
    
    my_any = std::string("hello");
    std::cout << "my_any contains a string: " << std::any_cast<std::string>(my_any) << std::endl;
    
    return 0;
}
```
在这个示例中，我们定义了一个 `std::any` 类型的对象 `my_any` ，可以存储任意类型的数据。然后，我们为 `my_any` 分别赋值并使用 `std::any_cast` 来访问和输出结果

---
#### 3. std::any 与其他通用类型容器的比较

1. `void*` 指针 : `void*` 指针可以用于表示任何类型的数据，但它不提供类型信息。因此，在使用 `void*` 指针时，需要手动管理类型转换和内存管理。相比之下，`std::any` 可以自动处理类型转换和内存管理，并提供了类型安全的访问方式
2. `boost::any` : 在 C++17 之前，`boost::any` 是 C++ 程序员常用的类型安全通用类型容器。`std::any` 的设计借鉴了 `boost::any` ，它们的功能和用法非常相似。然而，`std::any` 作为 C++17 标准库的一部分，不再需要依赖 Boost 库
3. `std::variant` : `std::variant` 是 C++17 中的另一个类型安全的通用类型容器，但它仅限于存储预定义类型列表中的类型。相比之下，`std::any` 可以存储任意类型的数据。然而，`std::any` 的灵活性带来了额外的性能开销，因此在类型信息明确的情况下，使用 `std::variant` 可能更合适


