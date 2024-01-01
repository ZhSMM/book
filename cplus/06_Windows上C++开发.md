# Windows上C++开发

#### lib与dll的区别

静态链接库与动态链接库：

+ 动态链接库：dynamic link library， 后缀.dll，是一种比较小的可执行文件，该dll包含了函数所在的DLL文件和文件中函数位置的信息（入口），代码由运行时加载在进程空间中的DLL提供。
+ 静态链接库：static link library，后缀.lib，该lib中包含函数代码本身，在编译时直接将代码加入程序当中。在程序编译的链接阶段链接各部分目标文件（通常为.obj）到可执行文件（通常为.exe）过程中，需要向编译器指定连接的lib位置。使用lib文件前提：
  + 包含一个对应的头文件告知编译器lib文件中的具体内容
  + 设置lib文件允许编译器去查找已经编译好的二进制代码

当需要从代码中分离出一个dll来代替静态链接库时，仍然需要一个lib文件。这个lib文件将被连接到程序告诉操作系统在运行的时候你想用到什么dll文件，一般情况下，lib文件里有相应的dll文件的名字和一个指明dll输出函数入口的顺序表。如果不想用lib文件或者是没有lib文件，可以用WIN32 API函数LoadLibrary、GetProcAddress。事实上，我们可以在Visual C++ IDE中以二进制形式打开lib文件，大多情况下会看到ASCII码格式的C++函数或一些重载操作的函数名字。

一般我们最主要的关于lib文件的麻烦就是出现unresolved symble这类错误，这就是lib文件连接错误或者没有包含.c、.cpp文件到工程里，关键是如果在C++工程里用了C语言写的lib文件，就必需要这样包含：

```c++
extern “C”

{
    #include “myheader.h”
}
```



#### X64驱动进程保护/软件安全/反调试/防结束

实现效果：任务管理器无法结束进程，火绒剑无法查看模块信息跟内存信息，应用层访问拒绝，X64DBG无法附加，CE无法附加，且不存在蓝屏，不需要考虑PG

相关概念：

+ PG：patch guard，对内核方面作的保护机制
+ PP：protected process，受保护的进程
+ PPL：protected process light，轻量级受保护的进程

Process Exploerer：查看进程信息，下载链接 [https://learn.microsoft.com/en-us/sysinternals/downloads/process-explorer](https://learn.microsoft.com/en-us/sysinternals/downloads/process-explorer)

实现过程：

1. 下载pplib以及其依赖libcapcom源码：https://github.com/notscimmy/pplib

   ```shell
   git clone git@github.com:notscimmy/pplib.git
   git clone git@github.com:notscimmy/libcapcom.git
   ```

2. 将 libcapcom 目录下的代码和工程文件复制到 pplib 的 libcapcom 文件夹下，然后点击pplib.sln文件夹打开pplib项目，并”重定解决方案目标“更改SDK版本

3. 找到 pplib 的 kerneloffsets.h，定义了系统特征码
