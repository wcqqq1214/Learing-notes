
在 C++98/03 中，不同的容器和数组遍历的方式不尽相同，写法不统一，也不够简洁，而 C++11 基于范围的 `for` 循环可以以简洁、统一的方式来遍历容器和数组，用起来也更方便了

### 1. for 循环新语法

- 在介绍新语法之前，先来看一个使用迭代器遍历容器的例子 :
```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> t{1, 2, 3, 4, 5, 6};
    for (auto it = t.begin(); it != t.end(); ++it) {
        cout << *it << " ";
    }
    cout << endl;
    
    return 0;
}
```

1. 我们在遍历的过程中需要给出容器的两端 : 开头 ( `begin` ) 和结尾 ( `end` )，因为这种遍历方式不是基于范围来设计的
2. 在基于范围的 `for` 循环中，不需要再传递容器的两端，循环会自动以容器为范围展开，并且循环中也屏蔽掉了迭代器的遍历细节，直接抽取容器中的元素进行运算，使用这种方式进行循环遍历会让编码和维护变得更加简便

- C++98/03中普通的 `for` 循环，语法格式 :
```cpp
for(表达式 1; 表达式 2; 表达式 3) {
    // 循环体
}
```

- C++11 基于范围的 `for` 循环，语法格式 :
```cpp
for (declaration : expression) {
    // 循环体
}
```

1. 在上面的语法格式中 `declaration` 表示遍历声明，在遍历过程中，当前被遍历到的元素会被存储到声明的变量中
2. `expression` 是要遍历的对象，它可以是表达式、容器、数组、初始化列表等

- 使用基于范围的 `for` 循环遍历容器，示例代码如下 :
```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> t{1, 2, 3, 4, 5, 6};
    for (auto value : t) {
        cout << value << " ";
    }
    cout << endl;

    return 0;
}
```

在上面的例子中，是将容器中遍历的当前元素拷贝到了声明的变量 `value` 中，因此无法对容器中的元素进行写操作，如果需要在遍历过程中修改元素的值，需要使用**引用**
```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> t{1, 2, 3, 4, 5, 6};
    cout << "遍历修改之前的容器: ";
    for (auto &value : t) {
        cout << value++ << " ";
    }
    cout << endl << "遍历修改之后的容器: ";

    for (auto &value : t) {
        cout << value << " ";
    }
    cout << endl;

    return 0;
}
```

- 代码输出的结果 :
```
遍历修改之前的容器: 1 2 3 4 5 6
遍历修改之后的容器: 2 3 4 5 6 7
```
对容器的遍历过程中，如果只是读数据，不允许修改元素的值，可以使用 `const` 定义保存元素数据的变量，在定义的时候建议使用 `const auto &` ，这样相对于 `const auto` 效率要更高一些

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> t{1, 2, 3, 4, 5, 6};
    for (const auto &value : t) {
        cout << value << " ";
    }

    return 0;
}
```


---

### 2. 使用细节

#### 2.1 关系型容器

使用基于范围的 `for` 循环有一些需要注意的细节，先来看一下对关系型容器 `map` 的遍历 :
```cpp
#include <iostream>
#include <string>
#include <map>
using namespace std;

int main() {
    map<int, string> m{
        {1, "lucy"}, {2, "lily"}, {3, "tom"}
    };

    // 基于范围的for循环方式
    for (auto &it : m) {
        cout << "id: " << it.first << ", name: " << it.second << endl;
    }

    // 普通的for循环方式
    for (auto it = m.begin(); it != m.end(); ++it) {
        cout << "id: " << it->first << ", name: " << it->second << endl;
    }

    return 0;
}
```

上面的例子中使用两种方式对 `map` 进行了遍历，通过对比有两点需要注意的事项 :

1. 使用普通的 `for` 循环方式 ( 基于迭代器 ) 遍历关联性容器， `auto` 自动推导出的是一个迭代器类型，需要使用迭代器的方式取出元素中的键值对 ( 和指针的操作方法相同 ) :
```cpp
it->first
it->second
```
2. 使用基于范围的 `for` 循环遍历关联性容器，`auto` 自动推导出的类型是容器中的 `value_type` ，相当于一个对组 ( `std::pair` ) 对象，提取键值对的方式如下 :
```cpp
it.first
it.second
```

#### 2.2 元素只读

通过对基于范围的 `for` 循环语法的介绍可以得知，在 `for` 循环内部声明一个变量的引用就可以修改遍历的表达式中的元素的值，但是这并不适用于所有的情况，对应 `set` 容器来说，内部元素都是只读的，这是由容器的特性决定的，因此在 `for` 循环中 `auto &` 会被视为 `const auto &` 
```cpp
#include <iostream>
#include <set>
using namespace std;

int main() {
    set<int> st{1, 2, 3, 4, 5, 6};
    for (auto &item : st) {
        cout << item++ << endl;		// error, 不能给常量赋值
    }
    return 0;
}
```
除此之外，在遍历关联型容器时也会出现同样的问题，基于范围的 `for` 循环中，虽然可以得到一个 `std::pair` 引用，但是我们是不能修改里边的 `first` 值的，也就是`key` 值

```cpp
#include <iostream>
#include <string>
#include <map>
using namespace std;

int main() {
    map<int, string> m{
        {1, "lucy"}, {2, "lily"}, {3, "tom"}
    };

    for (auto& item : m) {
        // item.first 是一个常量
        cout << "id: " << item.first++ << ", name: " << item.second << endl;  // error
    }

    return 0;
}
```

#### 2.3 访问次数

基于范围的 `for` 循环遍历的对象可以是一个表达式或者容器/数组等。假设我们对一个容器进行遍历，在遍历过程中 `for` 循环对这个容器的访问频率是一次还是多次呢？我们通过下面的例子验证一下 :
```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<int> v{1, 2, 3, 4, 5, 6};
vector<int>& getRange() {
    cout << "get vector range..." << endl;
    return v;
}

int main() {
    for (auto val: getRange()) {
        cout << val << " ";
    }
    cout << endl;

    return 0;
}
```

输出的结果如下 :
```
get vector range...
1 2 3 4 5 6
```
从上面的结果中可以看到，不论基于范围的 `for` 循环迭代了多少次，函数`getRange()` 在第一次迭代之前被调用，得到这个容器对象之后就不会再去重新获取这个对象了

- **结论** :
1. 对应基于范围的 `for` 循环来说，冒号后边的表达式只会被执行一次
2. 在得到遍历对象之后会先确定好迭代的范围，基于这个范围直接进行遍历
3. 如果是普通的 `for` 循环，在每次迭代的时候都需要判断是否已经到了结束边界
