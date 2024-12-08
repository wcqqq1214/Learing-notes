

1. 单例设计模式是一种常见的设计模式，用于确保某个类只能创建一个实例
2. 由于单例实例是全局唯一的，因此在多线程环境中使用单例模式时，需要考虑线程安全的问题


下面是一个简单的 **单例模式的实现** :
```cpp
class Singleton {
public:
    static Singleton& getInstance() {
         static Singleton instance; // 懒汉模式
         return instance;
    } 
       
    void setData(int data) {
         m_data = data;
    }
        
    int getData() const {
         return m_data;
    }
    
private:
    Singleton() {} // 私有构造函数，防止外部创建实例
    
    Singleton(const Singleton&) = delete; // 禁用拷贝构造函数
    Singleton& operator=(const Singleton&) = delete; // 禁用赋值操作符
        
    int m_data = 0;
};
```

1. 在这个单例类中，我们使用了一个静态成员函数 `getInstance()` 来获取单例实例，该函数使用了一个静态局部变量 `instance` 来存储单例实例
2. 由于静态局部变量只会被初始化一次，因此该实现可以确保单例实例只会被创建一次 

*ps :    C++11 及以后的标准中，局部静态变量的初始化是 **线程安全** 的*


---

#### call_once 使用场景 :

- **问题** : 
1. 如果多个线程同时调用 `getInstance()` 函数，可能会导致多个对象被创建，从而违反了单例模式的要求
2. 此外，如果多个线程同时调用 `setData()` 函数来修改单例对象的数据成员 `m_data`，可能会导致数据不一致或不正确的结果

- **解决方法** :
	可以使用 `std::call_once` 来实现一次性初始化，从而确保单例实例只会被创建一次


下面是一个使用 `std::call_once` 的单例实现 :
```cpp
class Singleton {
public:
    static Singleton& getInstance() {
		std::call_once(m_onceFlag, init);
		return *m_instance;
    }
    
    void setData(int data) {
        m_data = data;
    }
        
    int getData() const {        
	    return m_data;
    }

private:
    Singleton() {}
    
    Singleton(const Singleton&) = delete;
    Singleton& operator=(const Singleton&) = delete;
        
    static void init() {
	    // 用 new 创建一个对象并将其交给 m_instance 管理
        m_instance.reset(new Singleton);
    }
 
    static std::unique_ptr<Singleton> m_instance;    
    static std::once_flag m_onceFlag;    
    int m_data = 0;
};

// static 静态成员变量，类内声明，类外初始化
std::unique_ptr<Singleton> Singleton::m_instance;
std::once_flag Singleton::m_onceFlag;
```  

1. 在这个实现中，我们使用了一个静态成员变量 `m_instance` 来存储单例实例，使用了一个静态成员变量 `m_onceFlag` 来标记初始化是否已经完成
2. 在 `getInstance()` 函数中，我们使用 `std::call_once` 来调用 `init()` 函数，确保单例实例只会被创建一次
3. 在 `init()` 函数中，我们使用了 `std::unique_ptr` 来创建单例实例

*ps :    static 静态成员函数可以直接通过类名调用*


- 实现 :
	1. 使用 `std::call_once` 可以确保单例实例只会被创建一次，从而避免了多个对象被创建的问题
	2. 此外，使用 `std::unique_ptr` 可以确保单例实例被正确地释放，避免了内存泄漏的问题


---

#### std::call_once :

`std::call_once` 是 C++11 标准库中的一个函数，用于确保 **在多线程环境下，某个操作或函数只执行一次** 
( 只能在线程函数中使用 )


其函数原型如下 :
```cpp
template<class Callable, class... Args>
void call_once(std::once_flag& flag, Callable&& func, Args&&... args);
```
1. `flag` 是一个 `std::once_flag` 类型的对象，用于标记函数是否已经被调用
2. `func` 是需要被调用的函数或可调用对象
3. `args` 是函数或可调用对象的参数


- **使用细节** :

1. `flag` 参数必须是一个 `std::once_flag` 类型的对象，并且在多次调用 `call_once` 函数时需要使用同一个 `flag` 对象


2. `func` 参数是需要被调用的函数或可调用对象
	( 该函数只会被调用一次，因此应该确保该函数是幂等的 )
ps :    **幂等性** 表示 "执行一次" 和 "执行多次" 的效果是一样的。即使重复执行该函数多次，结果也不会改变


3. `args` 参数是 `func` 函数或可调用对象的参数
	( 如果 `func` 函数没有参数，则该参数可以省略 )


4. `std::call_once` 函数会抛出 `std::system_error` 异常，如果在调用 `func` 函数时发生了异常，则该异常会被传递给调用者


`std::call_once` 基本使用 :
```cpp
#include <iostream>
#include <thread>
#include <mutex>

std::once_flag flag;  // 用来标识是否执行过

void init() {
    std::cout << "Initialization\n";
}

void thread_func() {
    std::call_once(flag, init);  // 仅执行一次
}

int main() {
    std::thread t1(thread_func);
    std::thread t2(thread_func);
    std::thread t3(thread_func);

    t1.join();
    t2.join();
    t3.join();

    return 0;
}
```


- **总结** :
	1. 使用 `std::call_once` 可以在多线程环境中实现一次性初始化，避免了多个线程同时初始化的问题
	2. 例如，在单例模式中，可以使用 `std::call_once` 来保证单例实例只会被创建一次

