
#### 1. async, future

1. 是 C++11 引入的一个函数模板，用于异步执行一个函数，并返回一个 `std::future` 对象，表示异步操作的结果
2. 使用 `std::async` 可以方便地进行异步编程，避免了手动创建线程和管理线程的麻烦

下面是一个使用 `std::async` 的案例 :  
```cpp
#include <iostream>
#include <thread>
#include <future>

int calculate() {
    // 模拟一个耗时的计算
    std::this_thread::sleep_for(std::chrono::seconds(1));
    return 42;
}

int main() {
    std::future<int> future_result 
    = std::async(std::launch::async, calculate);
    // 在这里可以做其他的事情
    int result = future_result.get(); // 获取异步操作的结果
    std::cout << result << std::endl; // 输出42
    
    return 0;
}
```
  
1. 这个例子中，我们使用 `std::async` 函数异步执行了一个耗时的计算，这个计算可以在另一个线程中执行，不会阻塞主线程
2. 同时，我们也避免了手动创建线程和管理线程的麻烦


---

#### 2. packaged_task

1. 在 C++ 中，`packaged_task` 是一个类模板，用于将一个可调用对象 ( 如函数、函数对象 或 Lambda 表达式 ) 封装成一个异步操作，并返回一个 `std::future` 对象，表示异步操作的结果
2. `packaged_task` 可以方便地将一个函数或可调用对象转换成一个异步操作，供其他线程使用

( *ps :    和 `async` 的区别是 :    `packaged_task` 需要手动开启线程* )


- **基本用法** :   
  
  1. 定义可调用对象  

```cpp
int calculate(int x, int y) {
    return x + y;
}
```

这里定义了一个函数 `calculate`，用于将两个整数相加  

  
  2. 创建 `packaged_task` 对象  
  
```cpp
std::packaged_task<int(int, int)> task(calculate);
std::future<int> future_result = task.get_future();
```

这里创建了一个 `packaged_task` 对象，将函数 `calculate` 封装成异步操作，并返回一个 `std::future` 对象，表示异步操作的结果  


3. 在其他线程中执行异步操作  
  
```cpp
std::thread t(std::move(task), 1, 2);
t.join();
```

这里创建了一个新的线程，并在这个线程中执行异步操作。由于 `packaged_task` 对象是可移动的，因此需要使用 `std::move()`函数将 `task` 对象转移至新线程中执行


4. 获取异步操作的结果  

```cpp
int result = future_result.get();
std::cout << result << std::endl; // 输出3
```

1. 在主线程中，我们可以使用 `future_result.get()` 方法获取异步操作的结果，并输出到控制台
2. 在这个例子中，我们成功地将一个函数 `calculate` 封装成了一个异步操作，并在其他线程中执行
3. 通过 `packaged_task` 和 `future` 对象，我们可以方便地实现异步编程，使得代码更加简洁和易于维护

  ---
  
#### 3. promise

1. 在 C++ 中，`promise` 是一个类模板，用于在一个线程中产生一个值，并在另一个线程中获取这个值
2. `promise` 通常与 `future` 和 `async` 一起使用，用于实现异步编程
  

- **基本用法** :  
  
  1. 创建 `promise` 对象  

```cpp
std::promise<int> p;
```
  
这里创建了一个 `promise` 对象，用于产生一个整数值 


2. 获取 `future` 对象  

```cpp
std::future<int> f = p.get_future();
```

通过 `promise` 对象的 `get_future()` 方法，可以获取与之关联的 `future` 对象，用于在另一个线程中获取 `promise` 对象产生的值  


3. 在其他线程中设置值  

```cpp
std::thread t([&p]() {
    p.set_value(42);
});
t.join();
```

这里创建了一个新的线程，并在这个线程中，使用 `promise` 对象的 `set_value()` 方法设置一个整数值 42  


4. 在主线程中获取值  

```cpp
int result = f.get();
std::cout << result << std::endl; // 输出42
```

1. 在主线程中，我们可以使用 `future` 对象的 `get()` 方法获取 `promise` 对象产生的值，并输出到控制台
2. 在这个例子中，我们成功地使用 `promise` 和 `future` 对象实现了 **跨线程的值传递**
3. 通过 `promise` 和 `future` 对象，我们可以方便地实现异步编程，避免了手动创建线程和管理线程的麻烦

*ps :    `f.get()` 会阻塞主线程，直到  `std::promise` 对象 `p` 设置值为止*

