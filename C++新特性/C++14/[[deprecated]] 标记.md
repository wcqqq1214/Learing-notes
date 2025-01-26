
C++14 中增加了 `deprecated` 标记，修饰类、变量、函数等，当程序中使用到了被其修饰的代码时，编译时被产生警告，用户提示开发者该标记修饰的内容将来可能会被丢弃，尽量不要使用
```cpp
struct [[deprecated]] A { };

int main() {
    A a;
    
    return 0;
}
```

- 当编译时，会出现如下警告 : 
```Shell
~/test$ g++ test.cc -std=c++14
test.cc: In function ‘int main()’:
test.cc:11:7: warning: ‘A’ is deprecated [-Wdeprecated-declarations]
     A a;
       ^
test.cc:6:23: note: declared here
 struct [[deprecated]] A {
```
