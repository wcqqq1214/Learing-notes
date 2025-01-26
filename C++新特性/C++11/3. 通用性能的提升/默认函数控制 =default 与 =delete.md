### 1. 类与默认函数

在 C++ 中声明自定义的类，编译器会默认帮助程序员生成一些他们未自定义的成员函数。这样的函数版本被称为 "默认函数"。这样的函数一共有六个，我们一起来看一下 :
1. 无参构造函数 :    创建类对象
2. 拷贝构造函数 :    拷贝类对象
3. 移动构造函数 :    拷贝类对象
4. 拷贝赋值函数 :    类对象赋值
5. 移动赋值函数 :    类对象赋值
6. 析构函数 :           销毁类对象

在 C++ 语法规则中，一旦程序员实现了这些函数的自定义版本，则编译器不会再为该类自动生成默认版本

有时程序员会忘记上面提到的规则，最常见的是声明了带参数的构造，如果还需要无参构造函数，这时候必须定义出不带参数的版本。不过通过编译器的提示，这样的问题通常会得到更正。但更为严重的问题是，一旦声明了自定义版本的构造函数，则有可能导致我们定义的类型不再是 [[POD 类型]]，我们便不再能够享受POD类型为我们带来的便利

对于上面提到的这些，我们无需过度担心，因为C++11非常贴心地为我们提供了解决方案，就是使用 `=default` 


---
### 2. =default 和 =delete

在 C++11 标准中称 `= default` 修饰的函数为 **显式默认 ( 缺省 )** `explicit defaulted` **函数**，而称 `= delete` 修饰的函数为 **删除** ( `deleted` ) **函数或者显示删除函数**

 C++11 引入显式默认和显式删除是为了增强对类默认函数的控制，让程序员能够更加精细地控制默认版本的函数

#### 2.1 =default

我们可以在类内部修饰满足条件的类函数为显示默认函数，也可以在类定义之外修饰成员函数为默认函数。下面举例说明 :

- 在类内部 指定函数为默认函数

一般情况下，我们可以在定义类的时候直接在类内部指定默认函数，如下所示 :
```cpp
1. class Base {
2. public:
3.     Base() = default;
4.     Base(const Base& obj) = default;
5.     Base(Base&& obj) = default;
6.     Base& operator=(const Base& obj) = default;
7.     Base& operator=(Base&& obj) = default;
8.     ~Base() = default;
9. };
```
1. 第 `3` 行：指定 无参构造 为默认函数
2. 第 `4` 行：指定 拷贝构造函数 为默认函数
3. 第 `5` 行：指定 移动构造函数 为默认函数
4. 第 `6` 行：指定 复制赋值操作符重载函数 为默认函数
5. 第 `7` 行：指定 移动赋值操作符重载函数 为默认函数
6. 第 `8` 行：指定 析构函数 为默认函数

使用 `=defaut` 指定的默认函数和类提供的默认函数是等价的

- 在类外部 指定函数为默认函数

默认函数除了在类定义的内部指定，也可以在类的外部指定，如下所示 :
```cpp
// 类定义
class Base {
public:
    Base();
    Base(const Base& obj);
    Base(Base&& obj);
    Base& operator=(const Base& obj);
    Base& operator=(Base&& obj);
    ~Base();
};

// 在类定义之外指定成员函数为默认函数
Base::Base() = default;
Base::Base(const Base& obj) = default;
Base::Base(Base&& obj) = default;
Base& Base::operator=(const Base& obj) = default;
Base& Base::operator=(Base&& obj) = default;
Base::~Base() = default;
```

- 定义默认函数的注意事项 :
如果对 C++ 类提供的默认函数 ( 上面提到的六个函数 ) 进行了实现，那么可以通过 `=default` 将他们再次指定为默认函数，不能使用 `=default` 修饰这六个函数以外的函数
```cpp
1.  class Base {
2.  public:
3.      Base() = default;
4.      Base(const Base& obj) = default;
5.      Base(Base&& obj) = default;
6.      Base& operator= (const Base& obj) = default;
7.      Base& operator= (Base&& obj) = default;
8.      ~Base() = default;
9.  
10.     // 以下写法全部都是错误的
11.     Base(int a = 0) = default;
12.     Base(int a, int b) = default;
13.     void print() = default;
14.     bool operator==(const Base& obj) = default;
15.     bool operator>=(const Base& obj) = default;
16. };
```
1. 第 `11` 行 :    自定义带参构造，不允许使用 `=default` 修饰 ( 即使有默认参数也不行 )
2. 第 `12` 行 :    自定义带参构造，不允许使用 `=default` 修饰
3. 第 `13` 行 :    自定义函数，不允许使用 `=default` 修饰
4. 第 `14`、`15` 行 :    不是移动、复制赋值运算符重载，不允许使用 `=default` 修饰

#### 2.2 =delete

`=delete` 表示显示删除，**显式删除可以避免用户使用一些不应该使用的类的成员函数**，使用这种方式可以有效的防止某些类型之间自动进行隐式类型转换产生的错误。下面举例说明 :

- 禁止使用默认生成的函数
```cpp
1.  class Base {
2.  public:
3.      Base() = default;
4.      Base(const Base& obj) = delete;
5.      Base& operator= (const Base& obj) = delete;
6.  };
7.
8.  int main() {
9.      Base b;
10.     Base tmp1(b);    // error
11.     Base tmp = b;    // error
12.     return 0;
13. }
```
1. 第 `4` 行 :    禁用拷贝构造函数
2. 第 `5` 行 :    禁用 `=` 进行对象复制
3. 第 `10` 行 :  拷贝构造函数已被显示删除，无法拷贝对象
4. 第 `11` 行 :  复制对象的赋值操作符重载函数已被显示删除，无法复制对象

- 禁止使用自定义函数
```cpp
1.  class Base {
2.  public:
3.      Base(int num) : m_num(num) {}
4.      Base(char c) = delete;
5.      
6.      void print(char c) = delete;
7.    
8.      void print() {
9.          cout << "num: " << m_num << endl;
10.     }
11.    
12.     void print(int num) {
13.         cout << "num: " << num << endl;
14.     }
15. private:
16.     int m_num;
17. };
18.
19. int main() {
20.     Base b(97);       // 'a' 对应的 acscii 值为97
21.     Base b1('a');     // error
22.    
23.     b.print();
24.     b.print(97);
25.     b.print('a');     // error
26.    
27.     return 0;
28. }
```
1. 第 `4` 行 :    禁用带 `char` 类型参数的构造函数，防止隐式类型转换 ( `char` 转 `int` )
2. 第 `6` 行 :    禁止使用带 `char` 类型的自定义函数，防止隐式类型转换 ( `char`转 `int` )
3. 第 `21` 行 :  对应的构造函数被禁用，因此无法使用该构造函数构造对象
4. 第 `25` 行 :  对应的打印函数被禁用，因此无法给函数传递 `char` 类型参数
