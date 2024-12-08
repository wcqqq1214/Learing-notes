##### Makefile 的规则

```
目标文件: 依赖文件
	要执行的命令
	......
```

```
target: dependencies
	action
```

ps : 
1. 目标文件是要生成的文件
2. 依赖文件是生成这个文件所需要的文件
3. 命令是生成这个文件的具体步骤


**实例 :**

- ( 1 ) 在工程根目录新建一个文件，命名为 `makefile` 
	1. 不能有后缀名
	2. 首字母可以是大写，也可以是小写

- ( 2 ) 编写 `makefile` 文件

```
hello: main.c message.c
	gcc main.c message.c -o hello
```

- ( 3 ) 在终端执行 `make` 命令，系统会自动编译并生成可执行文件

- ( 4 ) `./hello` 执行 `.exe` 文件

ps :    只有当依赖文件有更新的时候，才会重新生成目标文件


---


- 在实际的项目中，应该把 **编译** 和 **链接** 分开来写

**实例 :**
	先把 `main.c` 和 `message.c` 分别编译成 `main.o` 和 `message.o` ，然后再把它们链接成 `hello` 可执行文件

```
hello: main.o message.o
	gcc main.o message.o -o hello

main.o: main.c
	gcc -c main.c    # gcc后加上-c参数, 表示只编译生成.o文件, 而不进行链接 

message.o: message.c
	gcc -c message.c
```


---
##### 伪目标 ( .phony )
	有时候可能会执行一些并不生成文件的操作，比如 清理临时文件，生成文档，打包等，这个时候可以用伪目标来定义这些规则

( 伪目标就是一个不生成文件的目标，只是一个标签，用来执行一些操作 )


 **实例1 :** 
	比如 `clean` ，用来清理编译过程中生成的临时文件或可执行文件

- ( 1 ) 删除所有的 `.o` 文件和 `hello` 文件

```
clean:
	rm -f *.o hello
```

- ( 2 ) 执行的时候，使用 `make clean`

ps :
1. 如果本地目录下有一个 `clean` 文件，`make clean` 会失效，因为 `make` 会把 `clean` 当成一个文件名来处理
2. 解决方法:    `.PHONY: clean` 显式地告诉 `Make` 这个 `clean` 是一个伪目标


**实例2 :** 
	假如想要生成的目标文件不止一个，可以使用伪目标 `all`

```
all: hello wolrd    # 想要生成的可执行文件
	echo "all" done    # echo打印提示信息

hello: main.o message.o
	gcc main.o message.o -o hello

world: main.o message.o
	gcc main.o message.o -o world
```

ps :    `all` 经常用来指定默认的目标
	例如 :    当把 `all` 伪目标放在第一行，执行 `make` 的时候默认生成所有的目标文件 ( 执行 `make` 等同于执行 `make all` )


**实例3 :**
	有时候只想编译某一个文件，而不是编译整个项目或者工程，可以在 `make` 后面加上要编译的文件名，如 :    `make hello`

ps :    只要是 `makefile`中定义的目标文件，都能单独编译 ( 包括中间文件 `.o` )


**实例4 :**
	`hello` 和 `world` 这两个文件 所依赖的文件 以及 生成规则 是一样的，所以可以写在一起，如 :
	
```
hello world: main.o message.o
	gcc main.o message.o -o $@    # $@ 表示自动变量
```

ps :    如果不想把执行的命令行输出打印到终端上的话，可以在命令行前面加上 `@`    
如 : 
```
all: hello wolrd
	@echo "all" done
```


---

##### 定义变量

- 在编译的时候，经常会用到一些编译选项，一般都会把这些编译选项定义成一个变量，这样在后面编译命令里面就可以直接使用这个变量

如 :  

```
.PHONY: clean all

# 定义 CFLAGS 变量
CFLAGS = -Wall -g -O2

# 把目标文件, 源文件, 中间文件定义成变量
targets = hello world
sources = main.c message.c
objects = main.o message.o

all: $(targets)    # $() 使用变量
    @echo "all done"

$(targets): $(objects)
    gcc $(CFLAGS) $(objects) -o $@    # $@为自动变量, 表示目标文件

%.o: %.c // 通配符简化, 表示所有的.o文件都是由.c文件生成的
    gcc $(CFLAGS) -c $< -o $@    # $<为自动变量, 表示第一个依赖文件

clean:  
    rm -f *.o *.exe
```

- 自动变量 : 
1. `$@`   表示目标文件
2. `$<`   表示第一个依赖文件
3. `$^`   表示所有的依赖文件

ps :    可以使用 **通配符** 来简化规则


---

##### make的参数

- `make -f`
	`make` 默认情况下会识别 `makefile` 文件，如果想要把 `makefile` 文件改名，执行命令时候要使用 `make -f <makefile-name>` 指定 `makefile` 的新文件名


- `make -n`
	可以用来打印出 `make` 要执行的命令，但是不会真正执行
	( 一般用在调试 `makefile` 的时候 )


- `make -C`
	用来指定 `makefile` 执行的目录
	例 :   
	 如果项目的 `makefile` 位于其他目录中，而又想在当前目录运行 `make` 命令时使用那个 `makefile`，可以使用 `-C` 选项






