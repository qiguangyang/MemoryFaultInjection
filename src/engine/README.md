#工具介绍
-------


##**关于本模块**
-------

本模块作为故障注入工具的驱动模块, 负责如下功能

*   获取进程的task_struct的该信息

*   负责将给定的虚拟地址空间(pid + 虚拟地址)转换为物理地址


##**关于本工具**
-------

本工具最早完成于2010年针对安腾架构架构实现,

已基本完成了功能, 但是当时所有的工作都在用户空间通过读写/proc/pid来完成

因为在2016年的更新中, 由gatieme对工具进行了重写, 主要增加了如下功能

1.  对工具进行解耦才操作, 划分为应用层, 工具层, 驱动层.

2.  实现了一个通用的内核驱动proc模块, 替代原来/proc/pid用户空间读写方式 

3.  对应用层的改动比较小, 主要是解耦和增加功能(故障注入次数和支持内核栈故障注入)



##关于**文件戳**
-------

由于此工具的初版完成时间较早, 期间也经过几代人的努力

鄙人认为, 所遇人的努力都应该得到重视, 

因此文件的开头保留了文件戳, 谨以此来感谢所有为本项目做过贡献的师兄和网友

```cpp
/*
*  Author: gatieme @ HIT CS HDMC team.
*  Create: 2016-3-12 8:50
*  Last modified: 2010-6-13 14:06:20
*  Description:
*
*   Our project was completed in 2010 @ v1.0.0
*   This module is create by gatieme @2015-11-29
*
*  	Memory fault injection engine running as a kernel module.
*	This module will create "/proc/memoryEngine/" directory and 9 proc nodes.
*   Write paramenters and request to these proc nodes and read the output from related proc node.
*/
```

#获取进程的pcb信息
-------

##说明
-------

|  可执行程序 | 源代码 | 脚本 | 描述 |
| ------------- |:-------------:| -----:| -----:|
| getpinfo | getTaskInfo.cpp |  getpinfo.sh | 获取进程的task结构信息 |

其实其本质是执行了如下信息

##执行操作
-------

```c
# 写入将要读取的进程pid
echo pid > /proc/memoryEngine/pid

#  写入控制标识REQUEST_GETTASKINFO = 1
echo 1 > /proc/memoryEngine/ctl

# 读取信号量, 信号量为1时说明读入完成, 否则则等待

cat /proc/memoryEngine/signal
# 读取进程的task信息

cat /proc/memoryEngine/taskInfo
```

##执行
-------

**二进制文件执行**

-p执行进程号, 获取进程号为pid的进程信息
`
``c
./getpinfo -p pid
```

-e指定可执行程序, 我们将通过execve加载可执行程序, 并读取其信息
```c
./getpinfo -e ./hello
```

**脚本执行**
```c
sh getpinfo.sh pid
```

##执行结果

![获取进程的pcb信息](./images/get_task_info.jpg)

