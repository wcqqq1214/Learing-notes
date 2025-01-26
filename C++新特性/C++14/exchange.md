
- 示例 : 
```cpp
#include <utility>

int main() {
	int old_value = 42;
	int new_value = 99;

    exchange(old_value, new_value);
    
    cout << "old_value: " << old_value << '\n' << "new_value: " << new_value << endl;
    
    return 0;
}
```

- 输出结果 : 
```Shell
old_value: 99
new_value: 99
```

看样子貌似和 `std::swap` 作用相同，那它俩有什么区别呢？

- 可以看下 `exchange` 的实现 : 
```cpp
template<class T, class U = T>
constexpr T exchange(T& obj, U&& new_value) {
    T old_value = std::move(obj);      // 备份旧值
    obj = std::forward<U>(new_value);  // 替换新值
    return old_value;                  // 返回旧值
}
```

可以看见 `new_value` 的值给了 `obj` ，而没有对 `new_value` 赋值