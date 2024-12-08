	C++ 标准库中提供的 互斥量封装类

#### std::lock_guard

用于保护共享数据，防止多个线程同时访问同一资源而导致的数据竞争问题
( 通过自动加锁和解锁来确保线程安全，防止出现死锁或忘记解锁的情况 )

- 特点 :
	1. 当构造函数被调用时，该互斥量会被自动锁定
	
	2. 当析构函数被调用时，该互斥量会被自动解锁
	
	3. `std::lock_guard` 对象不能复制或移动，因此它只能在局部作用域中使用

```cpp
#include <iostream>
#include <thread>
#include <mutex>

int shared_data = 0;

std::mutex mtx;
void func() {
    for (int i = 0; i < 10000; i++) {
        std::lock_guard<std::mutex> lg(mtx);
        shared_data++;
    }
} // 当作用域结束时，lock_guard 会自动解锁

int main() {
    std::thread t1(func);
    std::thread t2(func);
    t1.join();
    t2.join();

    std::cout << shared_data << std::endl;
    return 0;
}
```

  
---

#### C++11 std::unique_lock

用于在多线程程序中对互斥量进行加锁和解锁操作。它的主要特点是可以对互斥量进行更加灵活的管理，包括延迟加锁、条件变量、超时等

##### 构造函数 :

1. `unique_lock() noexcept = default` :
	默认构造函数，创建一个未关联任何互斥量的 `std::unique_lock` 对象


2. `explicit unique_lock(mutex_type& m)` :
	构造函数，使用给定的互斥量 `m` 进行初始化，并对该互斥量进行加锁操作


3. `unique_lock(mutex_type& m, defer_lock_t) noexcept` :
	构造函数，使用给定的互斥量 `m` 进行初始化，但不对该互斥量进行加锁操作
```cpp
std::mutex mtx;

std::unique_lock<std::mutex> ul(mtx, std::defer_lock);
```


4. `unique_lock(mutex_type& m, try_to_lock_t) noexcept` :
	构造函数，使用给定的互斥量 `m` 进行初始化，并尝试对该互斥量进行加锁操作。如果加锁失败，则创建的 `std::unique_lock` 对象不与任何互斥量关联
```cpp
std::mutex mtx;

std::unique_lock<std::mutex> ul(mtx, std::try_to_lock);
```


5. `unique_lock(mutex_type& m, adopt_lock_t) noexcept` :
	构造函数，使用给定的互斥量 `m` 进行初始化，并假设该互斥量已经被当前线程成功加锁
```cpp
std::mutex mtx;

std::unique_lock<std::mutex> ul(mtx, std::adopt_lock);
```


##### 成员函数 :

1. `lock()` : 
	尝试对互斥量进行加锁操作，如果当前互斥量已经被其他线程持有，则当前线程会被阻塞，直到互斥量被成功加锁
```cpp
std::mutex mtx;

std::unique_lock<std::mutex> ul(mtx, std::defer_lock);
ul.lock();
```


2. `try_lock()` :
	尝试对互斥量进行加锁操作，如果当前互斥量已经被其他线程持有，则函数立即返回 `false`，否则返回 `true`


3. `try_lock_for(const std::chrono::duration<Rep, Period>& rel_time)` :
	尝试对互斥量进行加锁操作，如果当前互斥量已经被其他线程持有，则当前线程会被阻塞，直到互斥量被成功加锁，或者超过了指定的时间
	( 等待一段时间 )  ( 返回 `bool` 类型 )
```cpp
std::timed_mutex mtx;
int shared_data = 0;

void func() {
	for (int i = 0; i < 2; i++) {
	    // 创建一个 unique_lock，且不立即加锁，稍后通过 try_lock_for 尝试加锁
	    std::unique_lock<std::timed_mutex> ul(mtx, std::defer_lock);
    
	    // 尝试获取锁，最多等待 2 秒
	    if (ul.try_lock_for(std::chrono::seconds(2))) {
	        // 获取到锁后，模拟一些工作 (线程休眠)
	        std::this_thread::sleep_for(std::chrono::seconds(1));
        
	        // 共享数据更新，确保在临界区内访问共享资源
	        shared_data++;
	    }
	}
}
```
*ps :     `std::mutex` 不支持延迟加锁，应该使用时间锁 `std::timed_mutex`*


4. `try_lock_until(const std::chrono::time_point<Clock, Duration>& abs_time)` :
	尝试对互斥量进行加锁操作，如果当前互斥量已经被其他线程持有，则当前线程会被阻塞，直到互斥量被成功加锁，或者超过了指定的时间点
	( 等待，直到超过某个指定时间点 )


5. `unlock()` :
	对互斥量进行解锁操作

