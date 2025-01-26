
- 我们都知道 C++11 中有 `std::make_shared` ，却没有 `std::make_unique` ，在 C++14 已经改善
```cpp
#include <memory>
struct A {};

std::unique_ptr<A> ptr = std::make_unique<A>();
```
