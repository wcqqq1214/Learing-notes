
- C++14 引入 `std::quoted` 用于给字符串添加双引号，直接看代码 : 
```cpp
#include <iomanip>
#include <string>

int main() {
    string str = "hello world";
    cout << str << endl;
    cout << std::quoted(str) << endl;
    
    return 0;
}
```

- 编译 & 输出 : 
```Shell
~/test$ g++ test.cc -std=c++14
~/test$ ./a.out
hello world
"hello world"
```
