
`std::integer_sequence` 常用于模板元编程中生成索引序列 ( 如 `std::index_sequence` )

```cpp
#include <utility>

template<typename T, T... ints>
void print_sequence(std::integer_sequence<T, ints...> int_seq) {

    std::cout << "The size of sequence: " << int_seq.size() << ": ";
    ((std::cout << ints << ' '), ...);  // 折叠表达式, 遍历并打印 ints 中的每个值 (C++17 引入的特性)
    std::cout << '\n';
}

int main() {
    print_sequence(std::integer_sequence<int, 9, 2, 5, 1, 9, 1, 6>{});
    
    return 0;
}
```

- 输出结果 :
```Shell
The size of sequence: 7
9 2 5 1 9 1 6
```


- `std::integer_sequence` 和 `std::tuple` 的配合使用 : 
```cpp
template <std::size_t... Is, typename F, typename T>
auto map_filter_tuple(F f, T& t) {
    return std::make_tuple(f(std::get<Is>(t))...);
}

template <std::size_t... Is, typename F, typename T>
auto map_filter_tuple(std::index_sequence<Is...>, F f, T& t) {
    return std::make_tuple(f(std::get<Is>(t))...);
}

template <typename S, typename F, typename T>
auto map_filter_tuple(F&& f, T& t) {
    return map_filter_tuple(S{}, std::forward<F>(f), t);
}
```
