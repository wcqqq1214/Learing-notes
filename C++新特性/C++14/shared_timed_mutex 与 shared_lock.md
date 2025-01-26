
C++14 通过 `std::shared_timed_mutex` 和 `std::shared_lock` 来实现读写锁，可以同时支持多个线程读取数据 ( 使用共享锁 )，并且在写操作时确保线程独占访问 ( 使用独占锁 )

- 实现方式如下 : 
```cpp
#include <shared_mutex>
#include <mutex>

struct ThreadSafe {
    mutable std::shared_timed_mutex mutex_;  // 允许在 const 成员函数中修改
    int value_;

    ThreadSafe() {
        value_ = 0;
    }

	// const 成员函数，允许多个线程同时读取共享数据
    int get() const {
        std::shared_lock<std::shared_timed_mutex> lock(mutex_);  // 获取共享锁
        return value_;
    }
    
	// 非 const 成员函数，确保只有一个线程可以修改共享数据
    void increase() {
        std::unique_lock<std::shared_timed_mutex> lock(mutex_);  // 获取独占锁
        value_ += 1;
    }
};
```

为什么是 `timed` 的锁呢，因为可以带超时时间，具体可以自行查询相关资料