### 1. final

C++ 中增加了 `final` 关键字来 **限制某个类不能被继承，或者某个虚函数不能被重写**，和 Java 的 `final` 关键字的功能是类似的。如果使用 `final` 修饰函数，只能修饰虚函数，并且 **要把** `final` **关键字放到类或者函数的后面**

#### 1.1 修饰函数

如果使用 `final` 修饰函数，**只能修饰虚函数，这样就能阻止子类重写父类的这个函数了** :
```cpp
class Base {
public:
    virtual void test() {
        cout << "Base class...";
    }
};

class Child : public Base {
public:
    void test() final {
        cout << "Child class...";
    }
};

class GrandChild : public Child {
public:
    // 语法错误, 不允许重写
    void test() {
        cout << "GrandChild class...";
    }
};
```
在上面的代码中一共有三个类 :
1. 基类 :        `Base`
2. 子类 :        `Child`
3. 孙子类 :    `GrandChild`
- `test()` 是基类中的一个虚函数，在子类中重写了这个方法，但是不希望孙子类中继续重写这个方法了，因此在子类中将 `test()` 方法标记为 `final` ，孙子类中对这个方法就只有使用的份了


#### 1.2 修饰类

使用 `final` 关键字 **修饰过的类是不允许被继承的，也就是说这个类不能有派生类**
```cpp
class Base {
public:
    virtual void test() {
        cout << "Base class...";
    }
};

class Child final : public Base {
public:
    void test() {
        cout << "Child class...";
    }
};

// error, 语法错误
class GrandChild : public Child {
public:
};
```
`Child` 类是被 `final` 修饰过的，因此 `Child` 类不允许有派生类 `GrandChild` 类的继承是非法的，`Child` 是个断子绝孙的类


---

### 2. override

`override` 关键字确保在派生类中声明的重写函数与基类的虚函数有相同的签名，同时也明确表明将会重写基类的虚函数，这样就可以保证重写的虚函数的正确性，也提高了代码的可读性，和 `final` 一样 **这个关键字要写到方法的后面**

- 使用方法如下 :
```cpp
class Base {
public:
    virtual void test() {
        cout << "Base class...";
    }
};

class Child : public Base {
public:
    void test() override {
        cout << "Child class...";
    }
};

class GrandChild : public Child {
public:
    void test() override {
        cout << "GrandChild class...";
    }
};
```
上述代码中显示指定了要重写父类的 `test()` 方法，使用了 `override` 关键字之后，假设在重写过程中因为误操作，写错了 函数名，函数参数 或者 返回值，编译器都会提示语法错误，提高了程序的正确性，降低了出错的概率
