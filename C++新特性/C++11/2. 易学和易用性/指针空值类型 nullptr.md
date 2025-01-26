
在 C++ 程序开发中，为了提高程序的健壮性，一般会在定义指针的同时完成初始化操作，或者在指针的指向尚未明确的情况下，都会给指针初始化为 `NULL` ，避免产生 **野指针 ( 即没有明确指向的指针，操作也这种指针极可能导致程序发生异常 )**

- C++98/03 标准中，将一个指针初始化为空指针的方式有 2 种 :
```cpp
char *ptr = 0;
char *ptr = NULL;
```

- 在底层源码中 `NULL` 这个宏是这样定义的 :
```cpp
#ifndef NULL
    #ifdef __cplusplus
        #define NULL 0
    #else
        #define NULL ((void *)0)
    #endif
#endif
```

1. 如果源码是 C++ 程序 `NULL` 就是 `0` ，如果是 C 程序 `NULL` 表示 `(void*)0`
2. 由于 C++ 中，`void *` 类型无法隐式转换为其他类型的指针，此时使用 `0` 代替 `((void *)0)`，用于解决空指针的问题。这个 `0 (0x0000 0000)` 表示的就是虚拟地址空间中的 `0` 地址，这块地址是只读的

- C++ 中将 `NULL` 定义为字面常量 `0`，并不能保证在所有场景下都能很好的工作，比如，函数重载时，`NULL` 和 `0` 无法区分 :
```cpp
#include <iostream>
using namespace std;

void func(char *p) {
    cout << "void func(char *p)" << endl;
}

void func(int p) {
    cout << "void func(int p)" << endl;
}

int main() {
    func(NULL);   // 想要调用重载函数 void func(char *p)
    func(250);    // 想要调用重载函数 void func(int p)

    return 0;
}
```

- 测试代码打印的结果为 :
```
void func(int p)
void func(int p)
```

通过打印的结果可以看到，虽然调用 `func(NULL)` ，最终链接到的还是 `void func(int p)` 和预期是不一样的 ( 在C++中 `NULL` 和 `0` 是等价的 )

---

出于兼容性的考虑，C++11 标准并没有对 `NULL` 的宏定义做任何修改，而是另其炉灶，引入了一个新的关键字 `nullptr`


- `nullptr` 专用于初始化空类型指针，不同类型的指针变量都可以使用 `nullptr` 来初始化 :   
```cpp
int*    ptr1 = nullptr;
char*   ptr2 = nullptr;
double* ptr3 = nullptr;
```

对应上面的代码编译器会分别将 `nullptr` 隐式转换成 `int*`、`char*` 以及 `double*` 指针类型

- 使用 `nullptr` 可以很完美的解决上边提到的函数重载问题 :
```cpp
#include <iostream>
using namespace std;

void func(char *p) {
    cout << "void func(char *p)" << endl;
}

void func(int p) {
    cout << "void func(int p)" << endl;
}

int main() {
    func(nullptr);
    func(250);
    
    return 0;
}
```

- 测试代码输出的结果:
```
void func(char *p)
void func(int p)
```

1. 通过输出的结果可以看出，`nullptr` **无法隐式转换为整形，但是可以隐式匹配指针类型**
2. 在 C++11 标准下，相比 `NULL` 和 `0`，使用 `nullptr` 初始化空指针可以令我们编写的程序更加 **健壮**     ( `nullptr` 是一个类型安全的空指针常量 )

