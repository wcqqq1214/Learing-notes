### 1. TCP状态转换

在 TCP 进行三次握手，或者四次挥手的过程中，通信的服务器和客户端内部会发送状态上的变化，发生的状态变化在程序中是看不到的，这个状态的变化也不需要程序员去维护，但是在某些情况下进行程序的调试会去查看相关的状态信息，先来看三次握手过程中的状态转换

![[TCP状态转换1.png]]

---
#### 1.1 三次握手

```Text
在第一次握手之前，服务器端必须先启动，并且已经开始了监听
  - 服务器端先调用了 listen() 函数, 开始监听
  - 服务器启动监听前后的状态变化: 没有状态 ---> LISTEN
```

当服务器监听启动之后，由客户端发起的三次握手过程中状态转换如下 : 

第一次握手 :

- 客户端 : 调用了 `connect()` 函数，状态变化 : 没有状态 -> SYN_SENT
- 服务器 : 收到连接请求 SYN，状态变化 : LISTEN -> SYN_RCVD

第二次握手 :

- 服务器 : 给客户端回复 ACK，并且请求和客户端建立连接，状态无变化，依然是 SYN_RCVD
- 客户端 : 接收数据，收到了 ACK，状态变化 : SYN_SENT -> ESTABLISHED

第三次握手 :

- 客户端 : 给服务器回复 ACK，同意建立连接，状态没有变化，还是 ESTABLISHED
- 服务器 : 收到了 ACK，状态变化 : SYN_RCVD -> ESTABLISHED

**三次握手完成之后，客户端和服务器都变成了同一种状态，这种状态叫 : ESTABLISHED，表示双向连接已经建立，可以通信了。在通过过程中，正常的通信状态就是 ESTABLISHED**

---
#### 1.2 四次挥手

关于四次挥手对于客户端和服务器哪段先断开连接没有要求，根据实际情况处理即可。下面根据上图中的实例描述一下四次挥手过程中 TCP 的状态转换 ( 上图中主动断开连接的一方是客户端 ) :

第一次挥手 :

- 客户端 : 调用 `close()` 函数，将 TCP 协议中的 FIN 设置为 1，请求和服务器断开连接
	状态变化 : **ESTABLISHED -> FIN_WAIT_1**

- 服务器 : 收到断开连接请求，状态变化 : **ESTABLISHED -> CLOSE_WAIT**

第二次挥手 :

- 服务器 : 回复 ACK，同意断开连接的请求，状态没有变化，还是 **CLOSE_WAIT**

- 客户端 : 收到 ACK，状态变化 : **FIN_WAIT_1 -> FIN_WAIT_2**

第三次挥手 :

- 服务器端 : 调用 `close()` 函数，发送 FIN 给客户端，请求断开连接，状态变化 : **CLOSE_WAIT -> LAST_ACK**

- 客户端 : 收到 FIN，状态变化 : **FIN_WAIT_2 -> TIME_WAIT**

第四次挥手 :

- 客户端 : 回复 ACK 给服务器，状态是没有变化的，状态变化 : **TIME_WAIT -> 没有状态**

- 服务器端 : 收到 ACK，双向连接断开，状态变化 : **LAST_ACK -> 无状态 ( 没有了 )**

---
#### 1.3 状态转换

在下图中同样是描述 TCP 通信过程中的客户端和服务器端的状态转换，看起来比较乱，其实只需要看两条主线 : 红色实线和绿色虚线。关于黑色的实线对应的是一些特殊情况下的状态切换，在此不做任何分析

因为三次握手是由客户端发起的，据此分析红色的实线表示的客户端的状态，绿色虚线表示的是服务器端的状态

![[TCP状态转换2.png]]
- 客户端 : 
	- 第一次握手 : 发送 SYN，**没有状态 -> SYN_SENT**
	- 第二次握手 : 收到回复的 ACK，**SYN_SENT -> ESTABLISHED**
	- 主动断开连接，第一次挥手发送 FIN，状态 **ESTABLISHED -> FIN_WAIT_1**
	- 第二次挥手，收到 ACK，状态 **FIN_WAIT_1 -> FIN_WAIT_2**
	- 第三次挥手，收到 FIN，状态 **FIN_WAIT_2 -> TIME_WAIT**
	- 第四次挥手，回复 ACK，等待 2 倍报文时长之后，状态 **TIME_WAIT -> 没有状态**
- 服务器端 : 
	- 启动监听， **没有状态 -> LISTEN**
	- 第一次握手，收到 SYN，状态 **LISTEN -> SYN_RCVD**
	- 第三次握手，收到 ACK，状态 **SYN_RCVD -> ESTABLISHED**
	- 收到断开连接请求，第一次挥手状态 **ESTABLISHED -> CLOSE_WAIT**
	- 第三次挥手，发送 FIN 请求和客户端断开连接，状态 **CLOSE_WAIT -> LAST_ACK**
	- 第四次挥手，收到 ACK，状态 **LAST_ACK -> 无状态 ( 没有了 )**
	- 在 TCP 通信的时候，当主动断开连接的一方接收到被动断开连接的一方发送的 FIN 和最终的 ACK 后 ( 第三次挥手完成 ) ，连接的主动关闭方必须处于**TIME_WAIT** 状态并持续 **2MSL ( Maximum Segment Lifetime )** 时间，这样就能够让 TCP 连接的主动关闭方在它发送的 ACK 丢失的情况下重新发送最终的 ACK

一倍报文寿命 ( MSL ) 大概时长为30s，因此两倍报文寿命一般在 1 分钟作用

主动关闭方重新发送的最终 ACK，是因为被动关闭方重传了它的 FIN。事实上，被动关闭方总是重传 FIN 直到它收到一个最终的 ACK

---
#### 1.4 相关命令

```Shell
$ netstat 参数
$ netstat -apn | grep 关键字
```
- 参数 :
	- `-a` : ( `all` ) 显示所有选项
	- `-p` : 显示建立相关链接的程序名
	- `-n` : 拒绝显示别名，能显示数字的全部转化成数字
	- `-l` : 仅列出有在 `listen` ( 监听 ) 的服务状态
	- `-t` : ( `TCP` ) 仅显示 TCP 相关选项
				- `-u` : ( `UDP` ) 仅显示 UDP 相关选- 


---
---
### 2. 半关闭

TCP 连接只有一方发送了 FIN，另一方没有发出 FIN 包，仍然可以在一个方向上正常发送数据，这中状态可以称之为半关闭或者半连接。当四次挥手完成两次的时候，就相当于实现了半关闭，在程序中只需要在某一端直接调用 `close()` 函数即可。套接字通信默认是双工的，也就是双向通信，如果进行了半关闭就变成了单工，数据只能单向流动了。比如下面的这个例子 : 

- 服务器端 :
	- 调用了 `close()` 函数，因此不能发数据，只能接收数据
	- 关闭了服务器端的写操作，现在只能进行读操作 –> 变成了读端

- 客户端 :
	- 没有调用 `close()`，客户端和服务器的连接还保持着
	- 客户端可以给服务器发送数据，也可以接收服务器发送的数据  ( 但是，服务器已经丧失了发送数据的能力 )，因此客户端也只能发送数据，接收不到数据 –> 变成了写端

按照上述流程做了半关闭之后，从双工变成了单工，数据单向流动的方向 : 客户端 —–> 服务器端

```c
// 专门处理半关闭的函数
#include <sys/socket.h>

// 可以有选择的关闭 读/写, close()函数只能关闭写操作
int shutdown(int sockfd, int how);
```
- 参数 :
	- `sockfd` : 要操作的文件描述符
	- `how` :
		- `SHUT_RD` : 关闭文件描述符对应的 读 操作
		- `SHUT_WR` : 关闭文件描述符对应的 写 操作
		- `SHUT_RDWR` : 关闭文件描述符对应的 读写 操作
- 返回值 : 函数调用成功返回 0，失败返回 -1


---
---
### 3. 端口复用

在网络通信中，一个端口只能被一个进程使用，不能多个进程共用同一个端口。我们在进行套接字通信的时候，如果按顺序执行如下操作 : 先启动服务器程序，再启动客户端程序，然后关闭服务器进程，再退出客户端进程，最后再启动服务器进程，就会出如下的错误提示信息 : `bind error: Address already in use`

```Shell
# 第二次启动服务器进程
$ ./server 
bind error: Address already in use

$ netstat -apn|grep 9999
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp        0      0 127.0.0.1:9999          127.0.0.1:50178         TIME_WAIT   -   
```

通过 `netstat` 查看 TCP 状态，发现上一个服务器进程其实还没有真正退出。因为服务器进程是主动断开连接的进程, 最后状态变成了 **TIME_WAIT** 状态，这个进程会等待 **2msl ( 大约1分钟 )** 才会退出，如果该进程不退出，其绑定的端口就不会释放，再次启动新的进程还是使用这个未释放的端口，端口被重复使用，就是提示 **bind error: Address already in use** 这个错误信息

如果想要解决上述问题，就必须要设置端口复用，使用的函数原型如下 : 

```c
// 这个函数是一个多功能函数, 可以设置套接字选项
int setsockopt(int sockfd, int level, int optname, const void *optval, socklen_t optlen);
```
- 参数 :
	- `sockfd` : 用于监听的文件描述符
	- `level` : 设置端口复用需要使用 `SOL_SOCKET` 宏
	- `optname` : 要设置什么属性 ( 下边的两个宏都可以设置端口复用 )
		- `SO_REUSEADDR`
		- `SO_REUSEPORT`
	- `optval` : 设置是 去除端口复用属性 还是 设置端口复用属性，实际应该使用 `int` 型变量
		- 0 : 不设置
		- 1 : 设置
	- `optlen` : `optval` 指针指向的内存大小 `sizeof(int)`

这个函数应该添加到服务器端代码中，具体应该放到什么位置呢？答 : 在绑定之前设置端口复用

1. 创建监听的套接字
2. 设置端口复用
3. 绑定
4. 设置监听
5. 等待并接受客户端连接
6. 通信
7. 断开连接

参考代码如下 :
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <arpa/inet.h>
#include <ctype.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <sys/socket.h>
#include <sys/select.h>

// server
int main(int argc, const char* argv[]) { // 允许程序从命令行获取参数
    
    // 创建监听的套接字
    int lfd = socket(AF_INET, SOCK_STREAM, 0);
    if (lfd == -1) {
        perror("socket error");
        exit(1);
    }

    // 绑定
    struct sockaddr_in serv_addr;
    memset(&serv_addr, 0, sizeof(serv_addr));
    serv_addr.sin_family = AF_INET;
    serv_addr.sin_port = htons(9999);
    serv_addr.sin_addr.s_addr = htonl(INADDR_ANY);  // 本地多有的 IP
    // 127.0.0.1
    // inet_pton(AF_INET, "127.0.0.1", &serv_addr.sin_addr.s_addr);
    
    // 设置端口复用
    int opt = 1;
    setsockopt(lfd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(opt));

    // 绑定端口
    int ret = bind(lfd, (struct sockaddr*)&serv_addr, sizeof(serv_addr));
    if (ret == -1) {
        perror("bind error");
        exit(1);
    }

    // 监听
    ret = listen(lfd, 64);
    if (ret == -1) {
        perror("listen error");
        exit(1);
    }

    fd_set reads, tmp;
    FD_ZERO(&reads);
    FD_SET(lfd, &reads);

    int maxfd = lfd;

    while (1) {
        tmp = reads;
        int ret = select(maxfd + 1, &tmp, NULL, NULL, NULL);
        if (ret == -1) {
            perror("select");
            exit(0);
        }

        if (FD_ISSET(lfd, &tmp)) {
            int cfd = accept(lfd, NULL, NULL);
            FD_SET(cfd, &reads);
            maxfd = cfd > maxfd ? cfd : maxfd;
        }
        
        for (int i = lfd + 1; i <= maxfd; ++i) {
            if (FD_ISSET(i, &tmp)) {
                char buf[1024];
                int len = read(i, buf, sizeof(buf));
                if (len > 0) {
                    printf("client say: %s\n", buf);
                    write(i, buf, len);
                }
                else if (len == 0) {
                    printf("客户端断开了连接\n");
                    FD_CLR(i, &reads);
                    close(i);
                }
                else {
                    perror("read");
                    exit(0);
                }
            }
        }
    }

    return 0;
}
```
