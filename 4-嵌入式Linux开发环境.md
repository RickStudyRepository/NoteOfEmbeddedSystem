# 4-嵌入式Linux开发环境

## 4.1 主机Linux开发环境

就是虚拟机的配置过程，这里不再赘述，详情参阅[开发环境搭建文档](./OtherDocuments/物联网教学科研平台(IMX6核心)开发环境搭建文档.pdf)

实际上，还是有一些内容不在这个文件里，但是来不及整理了/捂脸，不过那些内容无关痛痒就是了

10月7日

***

## 4.2 Bootloader

### 简介

<img src="./Images/4.2 Bootloader简介.png" style="zoom:70%;" alt="Bootloader简介" />

- BootLoader是**操作系统的启动加载程序**，是在**操作系统内核运行之前**运行的一段小程序，相对于操作系统内核来说，是一个硬件抽象层

- BootLoader的主要功能是**初始化硬件设备和内存空间，调整系统的软硬件环境**，以便操作系统内核启动

- BootLoader**不通用**，**依赖于硬件和具体的板级配置**，通常不同CPU的BootLoader也不同，但象U-Boot这样的BootLoader可通过简单移植支持多种CPU，如ARM和MIPS

- 嵌入式系统的NandFlash中的存储内容分布

    <img src="./Images/4.2 嵌入式系统的NandFlash中的存储内容分布.png" style="zoom:60%;" alt="嵌入式系统的NandFlash中的存储内容分布" />

- 嵌入式系统的Bootloader类型

    - `u-boot`
    - `vivi`
    - `blob`
    - `redboot`

#### BootLoader的存储

- 不同处理器组成的计算机系统都规定了启动后执行的第一条指令的地址
- 嵌入式系统通常把固态存储设备(如ROM或FLASH等)映射到这个地址上，把Bootloader放在这里，这样启动时，首先执行的就是Bootloader
- 例如：
    - 51系列单片：100h
    - 8086处理器：FFFF:0000
    - ARM处理器：0x00000000

#### BootLoader的输出

- 主机和目标机之间**一般通过串口建立物理连接**，没有串口的主机需要通过一条**USB转串口**的电缆完成连接
- Bootloader执行时**通常会将串口作为其标准输入输出设备**，输出打印提示信息到串口，从串口读取用户控制字符等
- 串口的参数要事先约定，一般多为115200（波特率），n，8，1

### 工作流程

嵌入式系统的Bootloader一般分为两个阶段执行：

1. stage1为汇编语言编写，**初始化硬件**
2. stage2为C语言编写，**为操作系统运行做准备**

#### BootLoader的生命周期

```mermaid
graph TD
A[初始化硬件] --> B[设置启动参数] --> C[跳转到操作系统首地址] --> D[消亡]

```

##### stage1

<img src="./Images/4.2 Bootloader stage1.png" style="zoom:60%;" alt="Bootloader stage1" />

- `trampoline`(弹簧床)编程方式

    - 汇编小程序`trampoline`作为**stage2的执行入口点**

    - 在`trampoline`中用CPU跳转指令跳入`main()`函数中去执行

    - 当`main()`函数返回时，CPU执行路径显然再次回到`trampoline`程序

    - 用`trampoline`小程序来作为`main()`函数的外部包裹(`external wrapper`)

    - `blob`的`trampoline`程序示例

        ```assembly
        .text
        .globl _trampoline
        _trampoline:
        bl main
        /* if main ever returns we just call it again */
        b _trampoline
        ```

    - `vivi`的`trampoline `程序示例

        ```assembly
        @ get read to call C functions
        ldr sp,DW_STACK_START @ setup stack pointer
        mov fp,#0 @ no previous frame,so fp=0
        mov a2,#0 @ set argv to NULL
        bl main @ call main;
        mov pc,#FLASH_BASE@ otherwise,reboot
        ```

##### stage2

<img src="./Images/4.2 Bootloader stage2.png" style="zoom:60%;" alt="Bootloader stage2" />

##### BootLoader的2种操作模式

Bootloader通常同时支持这两种工作模式，允许用户在这两种工作模式之间进行切换，一般在**启动时会延时等待按键输入进行切换到命令模式**，否则继续启动操作系统

1. 启动加载（自主Autonomous）模式

    直接启动操作系统的模式，是`Bootloader`的**正常工作模式**，没有外界输入时的缺省模式

2. 命令模式

    使用Bootloader命令的模式，部分嵌入式系统提供从主机向目标机下载映像文件（如内核和根文件系统）的命令，**本试验箱（我们的试验箱）则不支持相关命令**

### U-Boot（Universal BootLoader）

#### 基本介绍

- 德国DENX软件工程中心的Wolfgang Denk开发，遵循 GPL条款的开放源码项目，全称`Universal Bootloader`
- **其源码目录、编译形式与Linux内核很相似，是Linux内核源程序(尤其是设备驱动程序)的简化**
- 支持嵌入式Linux/VxWorks/QNX等**操作系统**，支持PowerPC、MIPS、x86、ARM、XScale等**处理器**
- 对PowerPC系列处理器/对Linux的支持最完善

#### 主要命令

- 打印环境变量：`printenv`
- 设置环境变量：`setenv`
- 保存环境变量：`saveenv`

## 4.3 嵌入式Linux内核

### 基本介绍

- linux内核主要由5个子系统组成：
    - 进程调度
    - 内存管理
    - 虚拟文件系统
    - 网络接口
    - 进程间通信
- 一般在Linux系统中的`/usr/src/linux-*.*.*`目录下就是内核源代码
- Linux内核代码可以分为平台相关部分和平台无关部分，绝大部分代码是平台无关的
- 依赖于硬件的代码在Linux中采用条件编译的方式区分(makefile中)
    - `ARCH=x86` 台式计算机x86平台
    - `ARCH=arm` 嵌入式系统arm平台
- 嵌入式Linux开发有时要编写或移植内核中的代码，这种编程有一些限制，主要有：
    1. 内核中**没有标准C库支持**
    2. 内核中**没有**像用户程序那样的**内存保护**
    3. 内核中**不便使用浮点操作**
    4. 内核的**堆栈是固定大小的，并且比较有限**
    5. 在内核中**需要编程者考虑并发带来的竞争与冒险，以及同步问题**

### 4.3.1 源码目录

TODO：待完善

10月12日，4-3PPT8页

***

### 4.3.2 内核移植

#### 内核移植的几种情形

1. 体系结构(CPU)的移植
    1. 内核**已有**CPU的相关支持
    2. 内核**没有**CPU的相关支持
2. 启动相关外存(NandFlash)的移植
3. 标准I/O设备(串口)的移植

TODO：尚待完善

### 4.3.3 配置编译

#### 简介

- Linux配置内核常用2个不同命令中的1个
    - `make menuconfig`：**不带图形界面**，字符型的界面，需要`libncurses(大概是这个名字)`的支持
    - `make xconfig(gconfig)`：**带图形界面**，需要`qt4/5`的支持
- `嵌入式Linux`内核配置通常使用第一个命令，`PC Linux`内核则通常使用第二个命令
- 命令执行的结果是产生一个`.config`文件，**并在每个C语言文件中**加入`<linux/config.h>`，以便使所有`CONFIG_XXX`起全局作用，根据这个生成不同的内核

#### 配置编译流程

1. **安装内核**，释放内核源码
2. **编译清理**，使用`make clean`命令清除原来编译内核时的`.o`文件和不必要关联
3. **配置内核**，运行`make menuconfig`配置内核
4. **编译内核**，运行`make`或`make zImage`编译内核

#### 配置内核

内核的配置选项有很多，可以使用预配置文件，也可以自行配置，选择时，最多有三种选择(按空格键进行选择)

- `[*]`，将该功能编译进内核
- `[ ]`，不将该功能编译进内核
- `[M]`，将该功能编译成**可以在需要时动态插入到内核**中的模块

#### 编译内核

1. 进入源码目录
2. 用`source`命令指定交叉编译器
3. 编译
    1. `make clean`
    2. `make`
4. 编译成功后会生成内核镜像文件`arch/arm/boot/zImage`和设备树文件`arch/arm/boot/dts/imx6dl-sabresd.dtb`，针对我们试验箱的内核
5. `system.map`是**内核符号映射文件**，应用程序所有可以使用的符号应该在其中

### 4.3.4 配置选项

#### 我们的实验平台的内核配置选项

TODO：待完善

#### Kconfig和Makefile

在内核的源码树目录下一般都会有两个重要文件

- `Kconfig`，2.4内核为`Config.in`
- `Makefile`

##### Kconfig

- 分布在各目录下的`Kconfig`构成了**一个分布式的内核配置数据库**，**每个Kconfig分别描述了所属目录下源文件相关的内核配置菜单**
- 在内核配置`make menuconfig`时，从`Kconfig`中读出配置菜单，用户配置完后保存到`.config`(在内核源码顶层目录下生成)中
- 内核编译时，主`Makefile`调用`.config`（隐藏文件），就知道了用户对内核的配置情况
- `Kconfig`的**作用就是对应着内核的配置菜单**。**假如要想添加新的驱动到内核的源码中，可以通过修改`Kconfig`来增加对我们驱动的配置菜单，这样就有途径选择我们的驱动**
- `Kconfig`语法可参考`Documentation/kbuild/kconfig-language.txt`文档

##### Makefile

- **如果想使某个驱动被编译进内核或被内核支持，还要修改该驱动所在目录下的Makefile文件**
- 这个Makefile文件定义了所在目录下**驱动源码在内核目录树中的编译规则**
- 在make编译内核的时候，**内核源码目录顶层Makefile**文件会递归的连接相应**子目录下的Makefile**文件，进而对驱动程序进行编译
- 内核的`Makefile`分为5个部分
    - `Makefile`，最顶层的`Makefile`
    - `.config`，内核的当前配置文档，编译时成为顶层`Makefile`的一部分
    - `arch/$(ARCH)/Makefile`，和**体系结构相关的** `Makefile`，提供编译体系结构相关代码的信息
    - `Makefile.*`，一些`Makefile`通用规则
    - `kbuild`和`Makefile`，各级目录有几百个文档，根据上层Makefile的宏定义和其他编译规则，将源码编译成模块或编入内核；**顶层的**`Makefile`读取`.config`内容，总体负责编译内核和模块

#### 如何增加选项

例如，将驱动程序`imx6-leds.c`加入内核

1. 将`imx6-leds.c`复制到内核的`drivers/char`目录，字符型设备

2. 更改内核的`drivers/char`目录下的`Kconfig`，添加：

    TODO：对格式有一定要求，待完善

    ```Kconfig
    config IMX6_LED_TEST
    	bool "imx6 leds test"
    	default y
    	help
    	Test Leds on imx6 board
    ```

3. 更改内核的`drivers/char`目录下的`Makefile`，添加：

    ```makefile
    obj-$(CONFIG_IMX6_LED_TEST) += imx6-leds.o
    ```

4. 通过`make menuconfig`选择`imx6 leds test`，编译内核并烧写

10月14日

***

## 4.4 嵌入式Linux根文件系统

### 4.4.1 Linux文件系统

#### 简介

#### mount命令

#### 文件类型

- 普通文件
- 目录文件
- 设备文件
- 链接文件

##### 目录文件

- 目录文件包含的数据是文件名、子目录名以及指向它们的指针
- 目录文件是存储文件名的唯一地方，可以把文件和目录对应起来
- 每个文件都对应一个唯一的数值，叫做索引节点号，它指向的索引节点包含文件的所有信息
- Linux文件系统把**索引节点号1**赋于根目录
- Linux通过操作目录文件来实现对整个文件系统的操作

##### 设备文件

- Linux把设备当作文件来进行操作，大大方便了用户的使用
- 与设备相关的文件一般放在`/dev`目录下，主要包括两种，一种是块设备文件，另一种是字符设备文件
    - 块设备文件对应数据的读写**以块(如由柱面和扇区编址的块)为单位**的设备，最简单的如硬盘(`/dev/hda1`)等
    - 字符设备主要是指**串行端口**等接口设备

##### 硬链接与软链接文件

- 软链接又叫符号链接，软链接文件的**主要内容是另一个文件的路径名**。可以可以指向不同文件系统的任意文件或目录
- **一个文件名**和**一个索引节点**形成了对应关系，称为这个**索引节点的一个硬链接**，即硬链接只是指向一个文件索引节点的**指针**，等同于原文件
- 硬链接**节省空间**，是Linux系统整合文件系统的传统方式，**不可以**在**不同文件系统的文件间建立链接**，而且**只有超级用户**才可以为目录创建硬链接

##### 硬链接与软链接的区别

- 硬链接原文件和链接文件公用一个索引节点号，是同一个文件，而软链接原文件和链接文件拥有不同的索引节点号，是两个不同的文件
- 在文件属性上软链接明确写出了是链接文件，而硬链接等同于原文件
- 硬链接数目可以增加，软链接不会
- 硬链接文件显示的大小跟原文件一样，软链接显示的大小与原文件不同
- 相关命令：
    - 建立软链接：`ln [-s] source_path target_path`
    - 建立硬链接：`ln -d existfile newfile`

#### 文件系统类型（台式计算机）

- `EXT2`：GNU/Linux系统中标准的文件系统，**存取文件的性能极好**，**适于处理中小型的文件**，**支持16TB文件系统和2TB文件**。
- `EXT3`：Ext3是Ext2的下一代，加上了日志功能，是一个**日志文件系统(Journal File System)**，即会将整个磁盘的写入动作完整记录在磁盘的某个区域上，以便有需要时可以回溯追踪。
- `EXT4`：Ext3的改进版，可以提供更佳的性能和可靠性，还有更为丰富的功能，**支持 1EB的文件系统**，**以及 16TB 的文件**。
- `SWAP`：**专门用于交换分区的文件系统**，Linux使用整个分区来作为交换空间，而不像**Windows使用交换文件**，一般将这个交换分区的容量大小设为主存容量的2倍。

#### 虚拟文件系统VFS

- VFS(虚拟文件系统)**相当于文件系统中的“抽象基类”**，而一个的具体文件系统相当于这个抽象基类的“派生类”

- 对文件的open，write，read等操作在VFS中实现了一个类似于“**纯虚函数**”的接口，每种具体的文件系统，会使用其派生类中被“改写”的“虚函数”

- 文件操作时，VFS把操作映射到相关的物理文件系统

- VFS不是一个实际文件系统，**只是物理文件系统和服务之间的接口层**，它对文件系统的细节进行抽象，使不同文件系统在linux系统中看来是相同的

- **VFS只存在于内存中，在系统启动时建立，在系统关闭时消亡**

- VFS的主要功能是

    - 记录可用的文件系统类型
    - 把设备同对应的文件系统联系起来
    - 处理面向文件的通用操作

- 通过VFS，文件调用就可以面向用户界面**提供一个统一的编程接口**，在不同的文件系统上创建文件，而使用的函数或命令都是相同的

- Linux的文件系统主要分3块：

    <img src="./Images/4.4.1 linux的文件系统.png" style="zoom:60%;" alt="linux的文件系统" />

    - 上层文件系统的系统调用
    - VFS
    - 挂载到VFS中的各种实际的文件系统

### 4.4.2 Linux启动流程

TODO

### 4.4.3 嵌入式Linux文件系统

#### 发展

- 2000年，阿克斯(Axis)公司针对嵌入式系统中的Flash存储器发布了jffs日志文件系统
- 2001年初，Red Hat公司jffs基础上推出了jffs2文件系统，在节点头部中增加了CRC校验码和节点类型等新信息
- 目前这两种嵌入式文件系统已基本不再使用

#### 类型

- jffs/jffs2文件系统：针对早期小容量的nor flash和nand flash
- yaffs文件系统：针对 512 字节小页的小容量的nand flash
- yaffs2文件系统：针对大页的大容量的nand flash
- cramfs文件系统：存储嵌入式操作系统，只读，安全可靠
- ext3/4文件系统：针对大页的超大容量的eMMc存储器

#### YAFFS文件系统

- JFFS/JFFS2是针对Flash建立的文件系统，它可支持NOR和NAND Flash，但是把它应用于NAND Flash还存在如下问题：
    - JFFS需要通过建立在内存中的jffs_node结构体维护Flash中的日志节点，**每个节点需要占用48个字节的内存空间**
    - JFFS/JFFS2在挂载时需要扫描整个Flash的内容，以找出所有的日志节点，建立文件结构
- YAFFS（Yet Another Flash FileSystem）是专门针对NAND Flash特点编写的日志文件系统。它克服了JFFS/JFFS2的缺点，具有如下特性：
    - 很小的内存空间占用
    - 很短的挂载时间
    - 跨平台的文件系统

#### YAFFS2文件系统



#### CRAMFS文件系统



### 4.4.4 嵌入式Linux根文件系统

#### 简介

<img src="./Images/4.4.4 嵌入式Linux根文件目录.png" style="zoom:60%" alt="嵌入式Linux根文件目录" />

- **Linux启动时挂载的挂载点为根目录的文件系统，被称为根文件系统**，是存放运行、维护系统所必需的各种工具软件、库文件、脚本、配置文件和其它各种文件的地方，也可以安装各种软件包
- **根文件系统是所有Linuix系统中不可缺少的组件**，没有根文件系统，Linux系统就不能正确的运行
- 根文件系统的具体形式就是就是我们平时看到的linux目录
- 嵌入式系统中的根文件系统，nandflash常用cramfs，eMMC常用ext3/4

#### 主要目录

- `/bin`：包含了**引导启动所需的命令或普通用户可能用的命令**，这些命令都是二进制文件的可执行程序，都是重要的系统文件

- `/sbin`：类似`/bin`，也用于存储二进制文件。因为**其中的大部分文件都是系统管理员使用的基本的系统程序**，所以虽然普通用户必要且允许时可以使用，但一般不给普通用户使用

- `/dev`：**存放了设备文件，即设备驱动程序**，用户通过这些文件访问外部设备。比如，用户可以通过访问`/dev/mouse`来访问鼠标的输入，就像访问其他文件一样

- `/boot`：**存放引导加载器(bootloader)使用的文件**，如LILO、grub，内核映像也经常放在这里，而不是放在根目录中

- `/tmp`：存放程序在运行时产生的临时信息和数据

- `/mnt`： 是系统管理员临时挂载文件系统的挂载点

    - 系统并不自动支持挂载到/mnt ，要使用`mount`命令挂载
    - 可分为许多子目录，如
        - /mnt/dosa
        - /mnt/exta
        - /mnt/cdrom等

- 嵌入式Linux的根文件系统中，必须的目录有/bin,/dev,/etc,/lib,/proc,/sbin和/usr，其他目录都是可选的，目录中必须添加的内容包括：

    - 链接库

    - 配置文件

    - 内核模块

    - 设备文件

        TODO：此处应当有表格

    - 主要的系统应用程序

    - init程序

10月19日，PPT4-4 25页

***

## 4.5 基于imx6的嵌入式linux烧写

### 如何烧写

1. 复制烧写工具目录`mfgtools`……
2. 编译u-boot已经生成了文件：`u-boot.imx`，复制时要更名为u-boot-im……
3. 编译内核生成了文件：
    1. `zImage`
    2. `imx6dl-sabresd.dtb`，复制时要更名为zIma……
4. 生成根文件系统压缩包：`rootfs.tar.bz2`
5. 将拨码开关调整到`USB OTG`模式：`00001100`
6. 占位