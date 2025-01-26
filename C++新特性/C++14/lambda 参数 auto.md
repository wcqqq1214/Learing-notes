
- 在 C++11 中，lambda 表达式参数需要使用具体的类型声明 : 
```cpp
auto f = [](int a) { return a; }
```

- 在 C++14 中，对此进行优化，lambda 表达式参数可以直接是 `auto` : 
```cpp
auto f = [](auto a) { return a; };

cout << f(1) << endl;
cout << f(2.3f) << endl;
```