
C++14 相较于 C++11 对 `constexpr` 减少了一些限制 : 

1. C++11 中 `constexpr` 函数可以使用递归，在 C++14 中可以使用局部变量和循环
```cpp
constexpr int factorial(int n) { // C++14 和 C++11 均可
    return n <= 1 ? 1 : (n * factorial(n - 1));
}
```

在 C++14 中可以这样做 : 
```cpp
constexpr int factorial(int n) { // C++11 中不可，C++14 中可以
    int ret = 0;
    for (int i = 0; i < n; ++i) {
        ret += i;
    }
    return ret;
}
```

---

2. C++11 中 `constexpr` 函数必须必须把所有东西都放在一个单独的 `return` 语句中，而 `constexpr` 则无此限制 : 
```cpp
constexpr int func(bool flag) { // C++14 和 C++11 均可
    return 0;
}
```

在 C++14 中可以这样 : 
```cpp
constexpr int func(bool flag) { // C++11 中不可，C++14 中可以
    if (flag) return 1;
    else return 0;
}
```
