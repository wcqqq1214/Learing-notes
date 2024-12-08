
#### std::condition_variable 
	C++11 条件变量, 用于实现线程间的同步与通信

- **使用步骤** :

1. 创建一个 `std::condition_variable` 对象

2. 创建一个互斥锁 `std::mutex` 对象，用来保护共享资源的访问

3. 在需要等待条件变量的地方 :
    1. 使用 `std::unique_lock<std::mutex>` 对象锁定互斥锁
	
	2. 并调用 `wait()`、`wait_for()` 或 `wait_until()` 函数等待条件变量

4. 在其他线程中需要通知等待的线程时，调用 `notify_one()` 或 `notify_all()` 函数通知等待的线程


- **notify_one( ) 和 wait( )** :
	1. `wait()` :
		当调用 `wait()` 时，线程会释放与该条件变量关联的互斥锁并进入等待状态，直到其他线程通知它可以继续执行
		( `wait()` 会让当前线程等待直到条件满足 ) 
		( 第二个参数为 `false` 会阻塞 )
	
	2. `notify_one()` :
		使得一个线程从 `wait()` 中恢复执行，重新尝试获取锁并继续执行
		( `notify_one()` 会通知一个线程条件已满足，可以继续执行 )


- 作用 :
	1. **避免忙等** :
		1. 如果消费者线程不断地检查队列是否为空，可能会浪费 CPU 时间
		2. `wait()` 能让消费者线程有效地休眠，直到被唤醒
	2. **同步和协调** :
		`notify_one()` 是在生产者生产了任务后，通知消费者线程继续消费，避免消费者线程一直等待或过度消费

ps :    即使调用了 `notify_one()` 唤醒了消费者线程，消费者线程仍然需要检查 `wait()` 的条件 


---

#### 生产者与消费者模型
	线程池使用的模型


- 下面是一个简单的 **生产者-消费者模型** 的案例，其中使用了 `std::condition_variable` 来实现线程的等待和通知机制 :

```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <queue>
std::mutex g_mutex;
std::condition_variable g_cv;
std::queue<int> g_queue; // 任务队列
bool done = false;  // 用于通知消费者线程生产已结束

void Producer() {
    for (int i = 0; i < 10; i++) {
    // {} 创建一个局部作用域, 一旦退出这个作用域, lock 会被销毁并释放互斥锁
        {             
            std::unique_lock<std::mutex> lock(g_mutex);
            g_queue.push(i);            
            std::cout << "Producer: produced " << i << std::endl;
        }
        // 通知消费者取任务
        g_cv.notify_one();
        // 令生产者线程休眠 100ms 
        // (模拟实际的生产过程, 给消费者线程足够的时间来消费队列中的任务)
        std::this_thread::sleep_for(std::chrono::milliseconds(100));
    }

	// 通知消费者生产已结束
    {
        std::unique_lock<std::mutex> lock(g_mutex);
        done = true;
    }
    g_cv.notify_one();  // 唤醒消费者以检查结束条件
}

void Consumer() {    
    while (true) {        
        std::unique_lock<std::mutex> lock(g_mutex);

		// 等待条件变量，直到队列非空或者生产者已结束
        g_cv.wait(lock, []() { return !g_queue.empty() || done; });
        if (!g_queue.empty()) {
            int value = g_queue.front();
            g_queue.pop();
            std::cout << "Consumer: consumed " << value << std::endl;
        }
        else if (done) {
            break;  // 如果生产者已经完成并且队列为空，退出消费线程
        }
    }
}

int main() {    
    std::thread producer_thread(Producer);    
    std::thread consumer_thread(Consumer);
    
    producer_thread.join();
    consumer_thread.join();
        
    return 0;
}
```

1. 使用 `std::condition_variable` 可以实现线程的等待和通知机制，从而在多线程环境中实现同步操作
2. 在生产者-消费者模型中，使用 `std::condition_variable` 可以让消费者线程等待生产者线程生产数据后再进行消费，避免了数据丢失或者数据不一致的问题


