# os-链接

![](/images/20190517152529386_1090812524.png)

* 编译程序往往需要编译器驱动程序，常用的如GCC，它将调用程序预处理器，编译器，汇编器和链接器
![](/images/20190517152740565_218196429.png)

## 编译过程
1. 程序预处理器将代码文件处理为ascii码的文件main.i
2. 编译器将main.i文件翻译为汇编文件main.s
3. 汇编器将main.s翻译为一个可重定位目标文件 main.o
4. 链接器将main.o和sum.o组合起来，创建一个可执行目标文件
5. shell调用加载器，加载可执行目标文件，将代码和数据加载到内存中执行

## 链接器的工作
- 符号解析
每个符号对应于一个函数，变量等 符号解析做的就是将每个符号引用和符号定义关联起来
- 重定位
编译器和汇编器生成的是地址从0开始的代码和数据节。链接器通过把每个符号定义和一个内存位置关联起来（会合并不同可重定位目标文件的.data节），然后更新所有这个符号的引用，使其指向这个引用位置
由于ELF加载的方式已经定了，各个节的大小也已经定了，所以链接之后，所有的符号的位置已经定了（虚拟内存地址）

## 目标文件
![](/images/20190517152807697_1472796774.png)

## Linux中目标文件的格式ELF文件
* ELF文件的格式
![](/images/20190517152824102_1398865296.png)
> ELF的头是一个16字节的固定序列，包含一些基本信息，包括目标文件的类型，机器类型，节头部表偏移量  
在ELF头和节头部表之间的一些节    
.text 已经编译程序的机器代码    
.rodata 只读数据 类似于代码中的硬编码 sprintf(‘%s’)    
.data 已初始化的全局&静态变量 (局部变量在运行时保存在栈中)    
.bss 未初始化的全局变量&静态变量，以及被声明为0的全局&静态变量    


## 静态链接库

![](/images/20190517153310801_1836563784.png)

> 可执行目标文件
![](/images/20190517153340571_1345059357.png)
可执行文件包含只读内存段（代码段）和读写内存段（数据段）    
ELF头中还包含程序的入口点，即第一条指令的地址    

### 加载可执行目标文件
* Shell指令加载ELF文件到内存中，1.将代码段和数据段加载到内存中，并跳转到程序入口点

> 运行时的内存映像
![](/images/20190517153400274_1999744777.png)
代码段总是从0x400000地址开始的，之后是数据段    
数据段之后是堆，堆后面的内存是为共享模块保留的    
用户栈总是从最大的合法地址开始向下延伸，之后的是内核内存    

## 动态链接共享库

* 动态链接库在链接器阶段，会引入来更新重定位和符号表信息
* 在加载器之后，会调用动态链接库来引入代码和数据

￼
![](/images/20190517153419409_1591391489.png)

* 动态链接库是在调用动态库代码时，程序被载入时才计算代码的逻辑地址
* 这就要求动态库代码是由位置无关代码构成

## 位置无关代码
* 链接一个函数时，多个进程都引用同一个函数，每个进程都占用内存，如果可以引用共享内存中的库代码是极好的
![](/images/20190517153506679_1755415885.png)

![](/images/20190517153529330_1144436709.png)

![](/images/20190517153544637_1684225631.png)

![](/images/20190517153603489_798229110.png)

## 动静态链接共享库区别
* 动态链接库占用空间比静态小，但是会影响程序性能
* 动态库并不是和可执行文件一起存储在磁盘上，共享代码端一次性加载到共享段中，供所有的进程使用
* 静态库在链接阶段已经确定了内存位置，动态是在程序加载时才会计算出代码地址
* 静态库一旦有修改，所有使用的可执行文件都需要重新编译，动态链接则不用
