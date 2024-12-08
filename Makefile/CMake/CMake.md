
1. 在工程根目录下，新建 `CMakeLists.txt` 文件，内容如下 :

```
cmake_minimum_required(VERSION 3.10)    # CMake的最小版本要求
project(MyProject)    # 设置工程名字

# 设置 C++ 标准
set(CMAKE_CXX_STANDARD 17)

# 设置源文件列表
set(SOURCES main.cpp message.cpp)

# 生成可执行文件, ${SOURCES} 表示这个可执行文件是由这个变量中的源文件生成的
add_executable(MyProject ${SOURCES})
```

2. 在工程的根目录下新建一个 `build` 文件夹，`cd build`

3. 在 `build` 中执行 `cmake .. -G "MinGW Makefiles"` ，表示指向根目录的 `CMakeLists.txt` 文件进行构建 
	( 即指向上一级目录 )  ( 在 Linux 下直接运行 `cmake ..` )

4. 构建完毕后，生成 `makefile` 文件，按照 `make` 命令正常执行即可 

ps :
1. 别直接在根目录运行 `cmake .`，运行命令会产生很多文件 ! ! !
	( `cmake .` 表示运行当前目录下的 `CMakeLists.txt` 文件 )
2. 支持通配符
3. 构建方法二 :    `cmake -S . -B build`
	- `-S .` :    指定当前目录为源目录 ( 必须包含 `CMakeLists.txt` )
	- `-B build` :    指定一个新目录 `build` 用于存放构建文件

