#### 1. std::optional 简介

C++17 引入了 `std::optional` 类模板，用于表示一个可能有值，也可能没有值的对象。`std::optional` 对于表示 可能失败的计算 或那些 可能没有合法值 的情况特别有用。`std::optional` 提供了一种类型安全的方式来表示这种情况，避免了使用指针或特殊值来表示缺失值的问题


---
#### 2. 使用 std::optional 表示可选值的示例

以下示例演示了如何使用 `std::optional` 表示一个可能有值，也可能没有值的计算结果 : 
```cpp
#include <iostream>
#include <optional>
#include <cmath>

// 计算平方根，当输入值为负数时返回 std::nullopt
std::optional<double> sqrt_optional(double x) {
    if (x >= 0) {
        return std::sqrt(x);
    } 
    else {
        return std::nullopt;
    }
}

int main() {

    auto result1 = sqrt_optional(4.0);
    auto result2 = sqrt_optional(-1.0);
    
    if (result1) {
        std::cout << "Square root of 4.0 is: " << *result1 << std::endl;
    } 
    else {
        std::cout << "Cannot compute square root of 4.0" << std::endl;
    }
    
    if (result2) {
        std::cout << "Square root of -1.0 is: " << *result2 << std::endl;
    } 
    else {
        std::cout << "Cannot compute square root of -1.0" << std::endl;
    }
    
    return 0;
}
```
在这个示例中，我们定义了一个函数 `sqrt_optional` ，它返回一个 `std::optional` 。当输入值为正数或零时，它返回平方根的值；当输入值为负数时，它返回 `std::nullopt` ，表示没有合法的结果


---
#### 3. std::optional 与指针、异常的比较

1. **指针** : 在 C++ 中，指针常被用于表示可选值，比如用空指针表示没有值。然而，使用指针可能会导致安全问题，如悬挂指针、空指针解引用等。而 `std::optional` 为表示可选值提供了一种类型安全的替代方案，避免了这些问题
2. **异常** : 在某些情况下，异常可以用于表示函数执行失败或无法产生合法值。但异常通常用于处理错误情况，而非表示可选值。此外，异常在某些场景下可能导致性能下降。与异常相比，`std::optional` 在表示可选值时具有更清晰的语义，且不会引入额外的性能开销

