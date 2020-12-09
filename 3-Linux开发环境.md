# 3 Linux开发环境

## 3.1 Linux简介

### Unix

- Linux源自UNIX，是**类Unix操作系统**
- UNIX操作系统是美国贝尔实验室的Ken.Thompson和Dennis Ritchie于1969年夏在PDP-7上开发的一个操作系统
- Thompson在1969年夏天利用一个月的时间开发了UNIX操作系统的原型。后经Ritchie于1972年用C语言进行了改写，使得UNIX系统在大专院校得到了推广

### GNU

- GNU计划和自由软件基金会FSF(the Free Software Foundation)是由Richard M. Stallman于1984年一手创办的，旨在创造一套完全自由免费，兼容于Unix的操作系统GNU(`GNU's Not Unix!`)
- Stallman于1989年与一群律师起草了广为使用的`GNU通用公共协议证书(GNU GPL)`
- GNU项目有许多高质量的免费软件，例如：
    - emacs编辑系统
    - bash shell程序
    - gcc系列编译程序
    - gdb调试程序等等

### linux简介

- Linux前身是`MINIX系统`，是由Andrew S. Tanenbaum于1987年开发的，主要用于学生学习操作系统原理

- `MINIX操作系统`是**UNIX的一种克隆系统**，由于MINIX系统提供源代码(免费用于大学内)，在全世界的大学中都开始学习UNIX系统

- 1991年芬兰大学生李纳斯在`GPL条例`下发布他自己创作的Linux操作系统内核，至此GNU计划正式完成，操作系统被命名为`GNU/Linux(或简称Linux)`

- `Linux`只是**一个内核**，GNU软件为Linux操作系统的开发创造了一个合适的环境，因此Linux操作系统被称为`GNU/Linux`

- `Linux`同样是**UNIX的一种克隆系统**，是一种**准(类)UNIX**

- 其他相关简介

    <img src="./Images/3.1 Linux其他简介.png" style="zoom:60%;" alt="Linux其他简介" />

## 3.2 Linux命令

### 常用命令

1. 关机命令`shutdown`
2. 广播命令`wall`，发送通知信息
3. 退出服务器或当前用户命令`exit`
4. 修改用户口令命令`passwd`
5. 查看当前用户命令`who`
6. 在终端显示字符串命令`echo`
7. 显示或设置日期或时间命令`date`
8. 清除当前屏幕显示内容命令`clear`

### 文件与目录命令

1. 查看当前目录下的文件命令`ls`
2. 查看文件内容命令`cat`
3. 分页查看文件内容命令`more`
4. 改变工作目录命令`cd`
5. 复制文件命令`cp`
6. 移动或更改文件、目录名称命令`mv`
7. 建立新目录命令`mkdir`
8. 删除目录命令`rmdir`
9. 删除文件命令`rm`
10. 文件权限设定命令`chmod`，`chmod [-cfvR] mode name`，[点此前往菜鸟教程查看详细介绍](https://www.runoob.com/linux/linux-comm-chmod.html)
    1. mode：3个8进制数字或rwx的组合（文件拥有者`u`，组`g`和其他用户`o`）
        1. `r: read`，可读取
        2. `w: write`，可写入
        3. `x: execute`，可执行
        4. `X`，只有当文件为目录文件，或者其他类型的用户有可执行权限时，才将文件权限设置可执行
        5. `u`：文件拥有者
        6. `g`：与该文件的拥有者属于同一个群体(group)者
        7. `o`：其他用户
        8. `a`：`u, g, o`三者
    2. 其他参数说明
        1. `-c`：若该文件权限确实已经更改，才显示其更改动作
        2. `-f`：若该文件权限无法被更改也不要显示错误讯息
        3. `-v`：显示权限变更的详细资料
        4. `-R`：对目前目录下的所有文件与子目录进行相同的权限变更(即以递归的方式逐个变更)
        5. `--help`：显示辅助说明
        6. `--version`：显示版本
    3. 例子：
        1. `chmod 755 dir1`：755对应的二进制数是：`111，101，101`，按顺序对应`u, g, o`，每一组内又按顺序对应`rwx`
        2. `chmod u+x file2`：给`file2`的文件拥有者增添`x`权限，即执行权限
        3. `chmod o-r file3`：给`file3`的其他用户减去`r`权限，即读权限
        4. `chmod a+x file4`：为所有用户增添对`file4`的`x`权限，即执行权限
11. 列出当前所在的目录位置命令`pwd`
12. 查看目录所占磁盘容量命令`du`
13. 改变文件或目录的所有权命令`chown`
14. 检查所属的工作组名称命令`groups`
15. 改变文件或目录工作组所有权命令`chgrp`
16. 文件中字符串的查询命令`grep`
17. 查询文件或命令的路径命令`whereis`
18. 比较文件或目录的内容命令`diff`
19. 查看文件属性命令`file`

### 帮助命令

1. 帮助`help`
2. 帮助命令`man`
3. 给出命令功能信息的命令`whatis`

### 其他命令

1. 压缩/解压命令`gzip`，适用于`.gz`文件，`gzip -d filename`
2. 压缩/解压命令`tar`，适用于`.tar, .tgz, .tar.gz`文件，`x`为解压，`c`为压缩
    1. `tar xzf linux-2.4.x.tgz`
    2. `tar czf linux-2.4.x.tgz linux-2.4.x`
3. 挂载及卸载命令`mount, umount`
4. `ip`设置命令，`ifconfig eth0 192.168.0.230 netmask 255.255.255.0`

## 3.3 编译工具链

### 3.3.1 编译过程

#### 一个程序的编译过程

1. 预处理：Pre-process，`gcc -E hello.i hello.c`
2. 编译：Compile，`gcc -S -o hello.s hello.i`
3. 汇编：Assemble，`gcc -c -o hello.o hello.s`
4. 链接：Link，`gcc -o hello hello.o`

#### hello world的编译过程

1. `gcc -o hello hello.c`，一步完成，输入文件是`text`，输出文件是`binary`
    1. `gcc -c -o hello.o hello.c`，一步完成到汇编，输入文件是`text`，输出文件是`binary`
        1. `gcc -S -o hello.s hello.c`，一步完成到编译，输入文件是`text`，输出文件是`text`
            1. `gcc -E hello.i hello.c`，预处理，输入文件是`text`，输出文件是`text`
            2. `gcc -S -o hello.s hello.i`，编译，输入文件是`text`，输出文件是`text`
        2. `gcc -c -o hello.o hello.s`，汇编，输入文件是`text`，输出文件是`binary`
    2. `gcc -o hello hello.o`，链接，输入文件是`binary`，输出文件是`binary`

#### 中间文件扩展名

<img src="./Images/3.3 编译工具链 - 文件扩展名.png" style="zoom:80%;" alt="编译工具链 - 文件扩展名" />

- 需要预处理源码：.c、.cc
- 不需预处理源码：.i、.ii
- 需要预处理的汇编代码：.S
- 不需预处理的汇编代码：.s
- 目标文件：.o
- 静态库：.a
- 动态库：.so
- linux编译和链接生成的默认文件名是a.out

9月30日

***

### 3.3.2 GNU工具链

#### GCC编译器

- GNU的c和c++编译器，如gcc-2.95.3.tar.gz
- gcc是编译的前端程序，编译时可依次调用cpp、cc1、as等工具完成编译
- gcc具有丰富的命令选项，如
    - `-o`，指定输出文件名
    - `-c`，完成汇编
    - `-L`，指定库所在目录
    - `-l`，指定库文件
    - `-I`，指定头文件目录
    - `-O`，指定编译时的优化级别

#### binutils二进制工具

例如binutils-2.11.2.tar.gz。主要包括

- 连接器`ld`
- 汇编代码编译器`as`
- 库管理器`ar`
- 等等

#### glibc库函数

- C函数库
- Linux线程库
- 如glibc-2.2.3.tar.gz和glibc-linuxthreads-2.2.3.tar.gz

#### 库管理器`ar`

- Linux下的函数库
    - 静态函数库：用`.a`作为文件的后缀，生成目标代码时会被加入到其中，**生成代码体积大，速度快**
    - 共享函数库：存放可执行程序在运行的时候才会被加载的函数，后缀是`.so`及版本号信息，**生成代码可以共享**，便于升级
    - 函数库名字必须`lib`作为前缀，如：`libgui.so.1`

- 库管理器`ar`

    - `ar`用于生成**静态函数库**，可以将**多个可重定位**的目标模块归档位**一个函数库文件**
- 基本格式：`ar [-dmpqrtx][cfosSuvV][a<成员文件>][b<成员文件>][i<成员文件] [库文件] [成员文件]`
    - `ar`命令能够建立函数库，能够针对库中成员完成添加、删除或取出、调整次序及显示等操作

#### gdb调试器

##### gdb功能

- 使你能监视程序中变量的值
- 使你能设置断点以使程序在指定的代码行上停止执行
- 使你能一行行的执行你的代码

##### gdb命令

- `file`装入想要调试的可执行文件
- `kill`终止正在调试的程序
- `list`列出产生执行文件的源代码的一部分
- `next`执行一行源代码但不进入函数内部
- `step`执行一行源代码而且进入函数内部
- `run`执行当前被调试的程序
- `quit`终止
- `gdbwatch`使你能监视一个变量的值而不管它何时被改变
- `break`在代码里设置断点, 这将使程序执行到这里时被挂起
- `make`使你能不退出 gdb 就可以重新产生可执行文件
- `shell`使你能不离开 gdb 就执行 Linux shell 命令

## 3.4 使用实例

详见课件3-1，3-2，3-3，3-4 P16-P30

### 一些命令参数

#### gcc编译

1. `-w`，关闭所有警告信息
2. `-Wall`，打开所有警告信息
3. `-fpic`，生成的目标码使用相对地址
4. `-shared`，生成可被链接的共享可执行程序代码
5. `-s`，生成的可执行代码不含gdb调试信息
6. `-l`，指定要链接的库
7. `ldd mystar`，列出程序`mystar`的动态库依赖关系
8. `ar -rc libhello.a hello.o`
    1. `r`，插入库文件
    2. `c`，建立库文件

#### gdb调试

1. `-g`，生成调试信息
2. `run`，运行被调试的程序
3. `list`，列出被调试程序的源码
4. `print`，输出变量的值，后跟变量名
5. `break`，设置断点，后跟代码行号
6. `info locals`，查看当前程序的局部变量
7. `c`，继续执行

## 3.5 make与makefile（考试5分）

### 简介

- 在Linux操作系统下编程时，通常利用`make命令`来自动完成编译工作
- `make`命令根据一个称为`makefile`的文件来完成并自动维护编译工作
- `Makefile文件`需要按照某种语法进行编写，其中**说明了如何编译各个源文件并链接生成可执行文件**，并**定义了源文件之间的相互依赖关系**
- 当修改了其中某个源文件时，如果其他源文件依赖于该文件，则也要重新编译所有依赖该文件的源文件
- 默认情况下，make命令在当前工作目录**按如下顺序搜索makefile**
    1. `GNUmakefile`
    2. `makefile`
    3. `Makefile`
- 如果要使用其他文件作为makefile，则可利用`-f`参数指定， `make -f Makefile.debug`

### Makefile规则介绍

- Makefile文件中包含一些规则来告诉make处理哪些文件以及如何处理这些文件

- 规则主要是描述**哪些文件(target目标文件)是从哪些别的文件(dependency依赖文件)中产生的**，以及**用什么命令(command)来执行这个过程**

- 依靠这些信息，make会对文件进行检查，**如果目标文件的生成或被改动时的时间(时间戳)比它的某个依赖文件还旧的话，make就执行命令更新目标文件**

- 目标文件可以是可执行文件或中间文件

- Makefile规则的一般形式如下，制表符是必须有的

    ```makefile
    target：dependency dependency
    (tab)<command>
    ```

- **通常target即make最终需要创建的文件，但也可以是要执行的动作**，如“clean”

- `dependency列表`，通常是编译目标文件所需要的其他文件

- `command`是创建target文件所需要执行的步骤，比如编译命令，每个命令占一行，**起始字符必须为TAB字符**

### Makefile实例

假设我们已完成`prog.c, prog.h, code.c, code.h`代码的编写，我们就可以使用如下的`Makefile`文件实现生成`test`可执行程序的目的，我们可以使用如下命令中的一个：

1. `make`，该命令暗含了`Makefile`文件中的第一个目标
2. `make test`

```makefile
test: prog.o code.o
	gcc -o test prog.o code.o
prog.o: prog.c prog.h code.h
	gcc -c prog.c -o prog.o
code.o: code.c code.h
	gcc -c code.c -o code.o
# clean就是一个动作
clean:
	rm -f *.o test
```

#### 对上述Makefile的分析

- 上述`Makefile`文件中共定义了四个目标：`test, prog.o, code.o, clean`

- 目标文件与依赖文件使用`:`分隔
- 多个依赖文件使用`' '(空格)`分隔
- 另起一行以`tab(制表符)`开头并给出实现目标的命令，命令过长时，可以使用`\`作为续行符
- `make clean`用于清除编译过程中的中间文件，这是约定俗成的做法

### `VPATH`与`vpath`

#### `VPATH`

- 在`makefile`中，可以通过给`VPATH`赋值来**设置目标文件和依赖文件的搜索目录**。`make`首先搜索当前目录，再按照环境变量`VPATH`中给的目录依次搜索
- 例如：`demo.o: demo.c demo.h`，`demo.c`在目录`src/demo/`中，`demo.h`在目录`src/demo/head`中，则可以给`VPATH`变量赋值
    - `VPATH := src/demo src/demo/head`
    - `VPATH := src/demo : src/demo/head`

#### `vpath`

- 也可以使用`vpath`给不同类型文件指定不同的搜索目录
- 问题同`VPATH`中问题
    - `vpath %.c src/demo`
    - `vpath %.h src/demo/head`
    - `vpath %.c`，清除所有`vpath`对`%.c`设置的搜索目录
    - `vpath`，清除所有以前用`vpath`设置的搜索目录

### 变量

#### 定义变量

- 在一行的开始给出变量名字，后面跟一个`:=`或`=`及要设定的值

    - `varname := string`，使用`:=`定义的变量或者给它赋值时就确定了它的值
    - `varname = string`，使用`=`定义的变量仅在被用到时才取它的值

- 使用变量的格式：`${varname}`或`$(varname)`

- 注意区分`:=`与`=`

    如下方式可以获得输出`hello second`

    ```makefile
    var1 = hello first
    var2 = ${var1}
    var1 = hello second
    test_echo:
    	echo ${var2}
    ```

    如下方式可以获得输出`hello first`

    ```makefile
    var1 = hello first
    var2 := ${var1}
    var1 = hello second
    test_echo:
    	echo ${var2}
    ```

    如下方式，将会使程序陷入死循环，因为**“递归”定义了var1的值**

    ```makefile
    var1 = hello first
    var1 = ${var1} and second
    echo_test:
    	echo ${var1}
    ```

    如下方式可以获得输出`hello first and second`

    ```makefile
    var1 := hello first
    var1 := ${var1} and second
    echo_test:
    	echo ${var1}
    ```

#### 使用环境变量

- `Makefile`文件中还可以使用环境变量，以及预定义变量，如

    - `$@`，目标名
    - `$^`，所有的依赖文件
    - `$<`，第一个依赖文件

- 实例

    ```makefile
    demo.o: demo.c demo.h
    	gcc -c $< -o $@
    ```

- Linux具有跨平台特性，常常装有多个编译器，所以makefile中常通过变量指定编译器及参数，如`LD, LDFLAGS, AS, ASFLAGS, CC, CFLAGS`

### 函数

#### 使用方式

1. `$(function arguments)`
2. `${function arguments}`

#### 常用函数

1. `$(subst from, to, text)`，将字符串`text`中的`from`子串替换为`to`子串，例如：

    ```makefile
    STR := $(subst I am, He is, I am an engineer)
    ```

    `STR`的值为: `He is an engineer`

2. `$(patsubst pattern, replacement, text)`，按模式`pattern`替换`text`中的子串，例如：

    ```makefile
    OBJS = init.o main.o string.o
    STR := $(patsubst %.o, %.c, ${OBJS})
    ```

    `STR`的值为：`init.c main.c string.c`

    `%.o`表示所有以`.o`结尾的子串

3. `$(wildcard pattern...)`，表示与`pattern`相匹配的所有文件，例如：

    ```makefile
    SRCS := $(wildcard *.c)
    ```

    若在当前目录中有`init.c`、`main.c`、`string.c`，则`SRCS`的值为：`init.c main.c string.c`

### 隐含规则

- `make`进行编译时遵循一些称作**隐含规则**的内置规则，这些规则在`makefile`中并没有被完整地给出，但`make`知道没有给出的部分是什么

- 典型的隐含规则给出了由`*.c`文件如何生成`*.o`文件，被隐含的规则是：

    ```makefile
    %.o: %.c
    	${CC} ${CFLAGS} $< -o $@
    ```

    这样一来，如下两种方式编写的`makefile`文件就是等价的：

    ```makefile
    prog.o: prog.c prog.h code.h
    	${ CC } –c prog.c –o prog.o
    code.o: code.c code.h
    	${ CC } –c code.c –o code.o
    ```

    ```makefile
    prog.o: prog.c prog.h code.h
    code.o: code.c code.h
    ```

- 使用隐含规则的一个实例

    ```makefile
    OBJS := prog.o code.o
    CC := gcc
    CFLAGS := -c
    test: ${OBJS}
    	${CC} –o $@ $^
    prog.o: prog.c prog.h code.h
    code.o: code.c code.h
    clean: 
    	rm –f ${OBJS} test
    ```

### `Make`常用的命令行选项

1. `-c DIR`，在读取`makefile`之前改变到指定的目录`DIR`
2. `-f FILE`，以指定的`FILE`文件作为`makefile`
3. `-h`，显示所有的`make`选项
4. `-i`，忽略所有的命令执行错误
5. `-I DIR`，当包含其他头文件时，可用该选项指定搜索目录
6. `-n`，只打印要执行的命令，但不执行这些命令
7. `-p`，显示`make`变量数据和隐含规则
8. `-s`，在执行命令时不显示命令
9. `-w`，在处理`makefile`之前和之后，显示工作目录
10. `-W FILE`，假定文件`FILE`已经被修改

10月5日，3-5，3-6PPT第10页

***

## 3.6 shell编程（考试5分）

### 基本介绍

- `shell`是`linux`的用户接口，**提供了用户与内核进行交互操作的命令解释**，它接收用户输入的命令并把它送入内核去执行
- `shell`有自己的编程语言，**允许用户编写由shell命令组成的程序**，强大的shell是linux的特点
- 实际上，和Unix一样，**linux期望用户能书写小的模块化的程序，并把它们作为构件去构建复杂的程序**
- 在`shell`程序中，**程序相当于能被执行的命令**，它们之间**通过I/O重定向和管道之类的方式传递参数**

### 多种不同的版本

- `sh`，GNU Bourne Shell
- `bash`，GNU Bourne Again Shell，PC默认
- `Korn Shell`，大部分与`Bourne Shell`兼容
- `C Shell`，是`SUN`公司`Shell`的`BSD`版本
- `Z Shell`，集成了`bash, ksh`的重要特性
- 通常在`shell`程序开始声明类型，如`#!/bin/sh`和`#!/bin/bash`

### 4种变量

#### 用户变量

- `shell`允许把值存在变量中，`shell`变量名**以字母或下划线字符开始，由字母、数字或下划线组成**

- 要把值存入变量，只要写出变量名，或紧跟一个`=`，再加变量值即可，例如

    ```bash
    # 格式：variable = value
    count=1
    ```

- 在程序中使用变量的值时，要在变量名前面加上一个符号`$`，例如

    ```shell
    echo $variable
    ```

#### 环境变量

- 环境变量是一种特殊的变量，**可以由其他程序传递给脚本，在脚本中被调用的任何程序都将继承环境变量**

- **可以像定义一个变量一样来设置环境变量**，在标记它为环境变量时需要使用`export`命令

    ```bash
    export MYENV=1
    echo $MYENV
    ```

- 使用`set`命令可以获取**当前上下文中全部的环境变量**

- 使用`env`命令可以查看**系统中全部环境变量**

#### 位置变量

##### 位置变量

- 由出现**命令行上的位置**确定的参数称做位置参数

- 总共有是个位置参数，其对应的名称依次是`$0, $1, $2, ..., $9`，其中`$0`表示命令名或`shell`脚本名，不可或缺，其他位置参数可有可无

- 编辑`ison`脚本文件内容如下

    ```bash
    #!/bin/bash
    who | grep $1
    ```
    
    执行
    
    ```bash
    # 给文件ison添加执行权限
    chmod +x ison
    # 执行ison
    ./ison bc
    ```

##### shift命令

- `shift`命令的作用是把位置参数左移，原来`$1`中的值丢失，参数变量的个数自动减1

- 编辑`shiftdemo`程序如下

    ```bash
    #!/bin/bash
    echo $# $*
    shift
    echo $# $*
    shift
    echo $# $*
    shift
    echo $# $*
    ```
    
    执行
    
    ```bash
    # 给shiftdemo文件添加执行权限
    chmod +x shiftdemo
    # 执行shiftdemo
    ./shiftdemo a b c
    3 a b c
    2 b c
    1 c
    0
    ```

#### 预定义变量

##### $#变量

- 用于存放命令行中所键入的参数个数

- shell程序测试这个变量确定用户输入的参数个数是否正确

- 编辑`args`脚本文件内容如下

    ```bash
    #!/bin/bash
    echo $# arguments passed
    echo arg1=:$1 arg2=:$2 arg3=:$3
    ```
    
    执行
    
    ```bash
    # 第一次执行
    ./args a b c
    3 arguments passed
    arg1=:a arg2=:b arg3=:c
    # 第二次执行
    ./args a b
    2 arguments passed
    arg1=:a arg2=:b arg3=：
    # 第三次执行
    ./args
    0 arguments passed
    arg1=: arg2=: arg3=:
    ```

- 从上述例子可以看到`shell`程序将命令行的参数个数传递给了`$#`变量

##### $*变量

- 可以引用传递给程序的所有参数

- 经常应用在参数不确定或者参数数目可变的程序中

- 编辑`args2`脚本文件内容如下

    ```bash
    #!/bin/bash
    echo $# arguments passed
    echo they are $*
    ```
    
    执行
    
    ```bash
    # 给args2添加执行权限
    chmod +x args2
    # 第一次执行
    ./args a b c
    3 arguments passed
    they are a b c
    # 第二次执行
    ./args a b
    2 arguments passed
    they are a b
    # 第三次执行
    ./args
    0 arguments passed
    they are
    ```

##### $@变量

- `$@`变量与`$*`变量功能基本相同

- 改写的`args3`脚本程序如下：

    ```bash
    #!/bin/bash
    echo $# arguments passed
    echo they are $@
    ```

##### $?变量

- 每当**程序执行完成后**都会**给系统返回**一个**退出状态**。该状态是个数值，**通常指示该命令运行是否成功**。**退出状态为0表示运行成功，非零表示运行失败**

- `shell`自动将**最后所执行命令的退出状态**设置到`shell`变量`$?`中，可以用`echo`命令在终端上显示它的值，例如

    ```bash
    #!/bin/bash
    who | grep lwg
    echo $?
    who | grep root
    echo $?
    ```

### 特殊字符

#### 3种通配符

- `*`，星号，它匹配**任意字符的0次或多次出现**。但注意，**文件名前面的圆点**`.`（例如`.gitignore`文件）和**路径名中的斜线**`/`**必须显式匹配**
- `?`，问号，匹配任意一个字符
- `[]`，一对方括号，其中有一个字符组，其作用是匹配该字符组所限定的任意一个字符
    - `*`和`?`，星号和问号在方括号内，**就不是通配符**了
    - `!`，叹号，若它**紧跟在一对方括号的左方括号**`[`之后，则表示**不在**一对方括号中所列出的字符

#### 引号与反斜线

- `""`，双引号括起来的字符， 除`$`、倒(反)引号（即Markdown语法中用来表示代码的符号）和反斜线`\`仍保留其功能外，其余字符通常作为普通字符对待
- `''`，单引号括起来的字符都作为普通字符出现
- `\``，反引号，即Markdown语法中用来表示代码的符号， 用于设置系统命令的输出到变量，反引号中的内容作为一个系统命令并执行
- `\`，反斜线为转义字符，若想在字符串中使用反斜线本身，则必须采用`\\`的形式

    > 变量的使用方式
    > 
    > <img src="./Images/3.6 变量使用方式.png" style="zoom:100%;" alt="变量的使用方式" />

### 表达式

#### 逻辑表达式

1. `&&`，与
2. `||`，或
3. `!`，非，需要有空格

#### Expr求值表达式

- `expr`命令一般用于整数值，但也可用于字符串。一般格式为：`expr argument operator argument`

- 其中`operator`为`+, -, *, /, %`，但对`*`的使用要用转义符`\`

- 例如：

    ```bash
    v1=3
    v2=2
    v3=`expr $v1 \* $v2`
    echo $v3
    # output: 6
    ```
#### 条件判断

##### if表达式

###### 格式

```shell
if command1
then
	...
elif command2
then
	...
else
	...
# 反转的fi表示if语句的结束
fi
```

`command1 `和`command2`需要执行并检测其**退出状态**，**如果退出状态为0，则执行其后的语句**

###### 一个实例

编写`ifdemo`如下所示

```bash
#!/bin/bash
if [ "$1" -gt 100 ]
then
	echo "the number is greater than 100."
elif [ "$1" -lt 10 ]
then
	echo "the number is smaller than 10."
else
	echo "the number is between 10 and 100."
fi
```

执行：

```bash
# 给ifdemo添加执行权限
chmod +x ifdemo
# 执行ifdemo
./ifdemo 100
```

###### 表示command的test命令

- 一般格式：`test expression`

- 常用格式：`[ expression ]`，两个中括号附近一定要有**空格**

- 命令计算`expression`

    - 若结果为**真**，其返回的**退出状态为0**
    - 若结果为**假**，返回的**退出状态就不为0**

- 使用范例

    ```bash
    name=bc
    # 使用形式1，引用name变量时，可以不加双引号
    test "$name" = bc
    # 使用形式2，注意添加空格
    [ "$name" = bc ]
    echo $?
    ```

- `test`把所有操作数`($name和bc)`和**操作符**作为单独的参数分别对待，**它们之间至少要有一个空白字符分隔**

###### 整数比较操作符：知道即可，即用即查

| 操作符        | 返回真的条件       |
| ------------- | ------------------ |
| int1 -eq int2 | int1等于int2       |
| int1 -ge int2 | int1大于或等于int2 |
| int1 -gt int2 | int1大于int2       |
| int1 -le int2 | int1小于或等于int2 |
| int1 -lt int2 | int1小于int2       |
| int1 -ne int2 | int不等于int2      |

###### 字串测试操作符：知道即可，即用即查

| 操作符 | 返回真的条件          |
| ------ | --------------------- |
| =      | 等于则为真            |
| !=     | 不相等则为真          |
| -z     | 字符串长度为0则为真   |
| -n     | 字符串长度不为0则为真 |

###### 一元文件操作符：知道即可，即用即查

| 操作符  | 返回真的条件               |
| ------- | -------------------------- |
| -d file | file为目录                 |
| -e file | file存在                   |
| -f file | file为普通文件             |
| -r file | file为进程可读文件         |
| -s file | file长度不为0              |
| -w file | file为进程可写文件         |
| -x file | file可执行                 |
| -L file | file为符号链接（文件系统） |

##### case表达式

###### 格式

```shell
case string in 
	string1 )
		...;;
	string2 )
		...;;
	* )
		...;;
# 反转的case表示case块结束
esac
```

字符串`string`首先与`string1`和`string2`比较

- 如果**匹配就执行它们下面的语句直到双分号**
- 如果**都不匹配**，则执行`* )` 下面的语句

###### 一个中英文数字转换程序实例

```shell
if [ "$#" -ne 1 ]
then
	echo "usage: ./casedemo number"
	exit 1
fi
case "$1" in
	0) echo zero;;
	1) echo one;;
	2) echo two;;
	# 省略了一部分转换
	...
	8) echo eight;;
	9) echo nine;;
esac
```

##### for循环

###### 基本形式

- 用来将一组命令循环执行预先确定的次数，`fordemo1`基本格式如下：

    ```shell
    for var in word1 word2 ... wordn
    	do
    		command
    	done
    ```

- 一个实例

    ```shell
    #!/bin/bash
    for i in 1 2 3
    	do
    		echo $i
    	done
    ```

###### 不带列表的形式

- shell会自动将命令行键入的所有参数依次组织成列表，`fordemo2`如下：

    ```shell
    for var 
    	do
    		command
    	done
    ```

- 一个实例

    ```shell
    #!/bin/bash
    echo Number of arguments passed is $#
    for arg
    	do
    		echo $arg
    	done
    ```

##### while循环

###### 格式

```shell
while command1
	do
		command2
	done
```

- 先执行`command1`，并检测其退出状态，如果为0则执行do与done之间的命令，直到`command1`的退出状态不为0

- `break`命令与`continue`命令与`C`中相应命令功能相同

###### `while`循环与`shift`命令的结合

`while`循环与`shift`命令结合可以处理命令行中键入的参数个数可变的情况，参见如下示例程序：

```shell
#!/bin/bash
while [ $# -ne 0 ]
	do
		echo $1
		shift
	done
```

`shift`命令使位置变量向下移，并且`$#`递减，这种方式不会受限于10个位置变量的限制

##### 函数定义和调用

- 和“真正的”编程语言一样，`shell`也有函数，虽然在某些方面稍有些限制

- **格式**
  
    ```shell
    funcname()
    {
        command
        ...
        command
    }
    ```
    
- 函数被调用或被触发，只需要简单地使用函数名即可

- 一个实例

    ```shell
    #!/bin/bash
    fun ()
    {
    	# 第一个参数是否长度为零
    	if [ -z "$1" ]
    	then
    		# 没有参数传递进来
    		echo "Parameter #1 is zero length."
    	else
    		echo "Param #1 is \"$1\"."
    	fi
    }
    fun
    fun "hello"
    ```

## 我也忘记为什么会有这部分内容了😂

> 2>/dev/null
>
> 2是linux的标准错误号，null是一个不存在的设备

