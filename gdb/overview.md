# GDB 代码调试工具
GDB是UNIX/Linux系统下的，基于命令行的程序调试工具。主要功能包括：
1. 自定义启动程序
1. 指定断点（断点可以是条件表达式）
1. 在断点处检查程序状态
1. 动态改变程序的执行环境
1. 多线程的程序调试

项目主页：https://www.gnu.org/software/gdb/documentation/
使用手册：https://sourceware.org/gdb/current/onlinedocs/gdb/

**cgdb**
cgdb可以看作gdb的界面增强版,用来替代gdb的gdb -tui。cgdb主要功能是在调试时进行代码的同步显示，功能上等价于gdb中使用`layout src` 显示源代码窗口。
github：https://github.com/cgdb/cgdb
manual：https://cgdb.github.io/docs/cgdb.html


## 调试程序
1. 首先，需要在可执行文件中添加调试信息。在使用`cc/gcc/g++` 编译时添加`-g` 或`-ggdb`, `-ggdb3` 参数。注意，在用GDB调试被优化过的程序时，可能会发生某些变量不能访问，或是取值错误码的情况。这是因为优化程序会删改你的程序。所以调试时一般关闭优化选项，在GCC中还可以使用`-gstabs`。
```bash
$ g++ -g hello.cpp -o hello     # debug信息是OS native format，GDB可以使用
$ g++ -ggdb hello.cpp -o hello  # debug信息是给GDB使用的
$ g++ -ggdb3 hello.cpp -o hello # 更多给GDB使用的信息，包含宏定义
```
可以参考：https://gcc.gnu.org/onlinedocs/gcc-4.3.3/gcc/Debugging-Options.html
2. 使用`gdb` 命令启动`gdb` 程序，进入`gdb` 交互调试界面
```bash
$ gdb ./hello               # 可以在gdb启动后，使用`set args 7`命令设置参数
$ gdb --args ./hello 7      # 载入可执行程序和参数
...(若干GDB工具的提示信息)
(gdb)                       # 光标会停留在这行，等待用户输入交互指令
```
3. 查看源代码
```bash
(gdb) list                  # 使用list命令（或者用简写的 l）
...(紧接着上一次的内容，列出后面的10行代码及行号)
(gdb) l 12
...(列出12行处上下的10行代码及行号)
(gdb) l main
...(列出main函数的代码及行号)
```
4. 设置及查看断点
```bash
(gdb) break 23              # 在第23行设置断点（或者用简写的 b）
(gdb) b caffe.cpp:27        # 在caffe.cpp文件的第27行设置断点
(gdb) info breakpoints      # 查看所有设置的断点（或者用简写的 i b）
```
5. 执行程序
```bash
(gdb) run                   # 开始运行程序，在第一个断点处停止（或者用 r）
```
6. 输出程序状态
```bash
(gdb) print x               # 输出变量x的内容（或者用简写的p x）
```
7. 继续执行
```bash
(gdb) step                  # 单步执行，会进入函数（简写 s） 
(gdb) next                  # 单步执行，执行完整函数，不进入（简写 n）
(gdb) continue              # 继续执行，直到下一个断点或程序终止
```
## 调试core dump文件
当程序运行中发生异常导致程序退出时, 操作系统把程序当前的内存状况存储在一个文件中, 这个文件一般称为core dump又叫核心转储文件。core dump文件一般生成在调用目录下，使用core.[pid]的文件名保存。
系统一般不生成core dump文件。通过查询`ulimit -c`或`ulimit -a`，可以查看core file大小的配置，为0表示不生成。执行`ulimit -c unlimited`（只对当前环境有效）设置来打开core dump。

### 产生Core Dump文件的方法
1. 根据Linux的信号默认的处理行为，SIGQUIT，SIGABRT, SIGFPE和SIGSEGV都可以让该进程产生coredump文件。其中SIGSEGV就是Segmentation Fault段错误，一般由非法访问内存引起。
2. 如果程序正在执行，可以先用gdb载入程序源文件，在指定位置打上断点，再`attach`上这个进程时。当断点触发停下来后，使用gdb的命令`gcore`，可以立即产生一个coredump。 
3. 可以给进程发送信号。先获得进程ID，然后使用`kill -s QUIT 3207`发送信号，可以是QUIT，ABRT，TERM 或KILL 信号。 


### 调试过程
1. 启动gdb，并载入执行程序（已添加了debug信息）和core dump文件
```bash
$ gdb ./caffe core.12313
$ gdb -c core.12313 ./caffe
```
2. 查看堆栈信息
```bash
(gdb) bt 或 backtrace 或 where    # 显示当前堆栈信息
```
3. 切换堆栈
```bash
(gdb) up                    # 切换到上一层堆栈
(gdb) down                  # 切换到下一层堆栈
(gdb) frame 4               # 切换到帧 n
```
## 调试正在运行的程序
使用gdb调试一个正在运行的程序时，会自动暂停该程序，并将程序的执行交由gdb控制。
在将gdb attach到进程时会遇到`ptrace operation not permitted`的问题，网上的原因也不是很明确，可以修改gdb的权限`sudo chmod +s /usr/bin/gdb`，另一种方法需要修改系统文件按后重启系统。

1. 启动gdb并绑定进程
```bash
$ ps aux | grep [exec]          # 查看进程的pid
$ gdb [exec] [pid]              # 启动gdb，载入程序exec，并attach到pid号进程
或者
$ gdb [exec]                    # 启动gdb并载入程序
(gdb) attach [pid]              # attach到pid号进程
```
2. 查看进程状态
```bash
(gdb) info program              # 查看当前进程是否在运行，进程号等
(gdb) frame                     # 查看当前栈信息
```
3. 结束调试
```bash
(gdb) detach                    # 取消进程关联，相应的进程会恢复执行
(gdb) q                         # 如果退出gdb，会自动detach
```
## 调试多线程程序
`info thread`：查看当前进程的线程
`thread [ID]`：切换调试的线程为指定ID的线程
`break file.c:100 [thread n/all]`：在file.c文件第100行处为所有经过这里的线程设置断点
`set scheduler-locking off|on|step`：在使用step或者continue命令调试当前被调试线程的时候，其他线程也是同时执行的
  - off 不锁定任何线程，也就是所有线程都执行，这是默认值。
  - on 只有当前被调试程序会执行。
  - step 在单步的时候，除了next过一个函数的情况(熟悉情况的人可能知道，这其实是一个设置断点然后continue的行为)以外，只有当前线程会执行。

## 反向调试程序
反向调试的基本原理为 record/replay。因此，首先需要使用 record 命令进行录制。有 side effect 的语句虽然能够回退执行，但其所造成的 side effect 则无法撤销，比如IO操作。
参考资料：http://www.ibm.com/developerworks/cn/linux/l-cn-gdb7rd/index.html

`record`：开启反向调试记录
`rn`, `reverse-next`：语句单步向后跟踪程序
`rs`, `reverse-step`：向后执行一条语句，单步进入
`rc`, `reverse-continue`：继续程序运行到断点，但是是逆向运行程序
`reverse-finish`：逆向运行程序直到跳出本层函数
`rni`, `reverse-nexti`：指令单步向后一条指令
`reverse-stepi`：向后执行一条指令，单步进入
`set exec-direction [forward/reverse]`：设置程序执行方向，向前或向后


## 详细使用说明
在命令行中输入`gdb`就进入了GDB交互式模式，交互式模式中使用`(gdb) `作为提示符。在交互式模式下支持`TAB`键来补齐**命令**和待调试文件的**函数**。
输入命令时，可以只输入命令的前几个字符，只要能唯一区分该命令。有些常用命令有固定的缩写，比如`print x` 命令只需要输入`p x` 即可。

### 启动GDB
`gdb`：启动gdb
`gdb <program>`：启动时载入program可执行文件
`gdb --args <program> par1 par2 ...`：启动时载入program并指定program的参数par1 par2
`gdb <program> corefile`：启动时载入program和该程序的core dump文件，一般用于检查出错信息
`gdb <program> <PID>`：启动时载入program并attach到该程序的某个正在执行的进程上。在gdb中也可以用attach <pid>命令来挂接进程的PID，用detach来取消挂接的进程
`q`, `quit`：退出gdb环境

详细的启动参数参见`gdb -help`。

### 执行系统命令
`shell <command string>`：使用环境变量SHELL中定义的shell执行`<command string>`，如果SHELL没有定义就使用`/bin/sh`。（在Windows中使用Command.com或cmd.exe）
`make <make-args>`：执行make命令来重新build自己的程序。这个命令等价于`shell make <make-args>`

### 查看帮助
`help`：列出gdb的命令分类，包括`breakpoints`，`data`等
`help <class>或<command>`：查看命令分类（help命令的输出）下的命令或某个命令的帮助

### 环境参数
`file program`, `f program`：加载程序program，默认在当前路径和系统PATH路径下寻找
`set args par1 par2 ...`：设置程序运行参数，等价于`gdb --args program par1 par2`
`show args`：显示已设置运行参数
`set environment varname [=value]`：设置环境变量。如：`set env USER=hchen`
`show environment [varname]`：显示环境变量，不指定则显示所有环境变量
`path <dir>`：指定程序的查找路径
`show paths`：显示查找路径
`cd <dir>`：改变当前目录，相当于shell的cd命令
`pwd`：显示当前的所在目录
`重定向`：控制程序输入输出。如：`run < infile > outfile`
`tty`：指向输入输出的终端设备。如：`tty /dev/ttyb`

### 运行程序
`r`, `run`：运行程序，直到第一个断点处停止
`c`, `continue [num]`：继续执行，指定执行的步数num 或直至下一个断点或程序结束
`s`, `step [count]`：继续执行count（默认为1）行程序，遇到函数会进入函数中执行。进入函数的前提是，此函数被编译有debug信息。
`set step-mode on/off`：是否在没有debug信息时也执行单步调试。可用于查看没有debug信息程序的汇编代码
`n`, `next [count]`：继续执行count（默认为1）行程序，遇到函数时将函数语句当作一步来执行，不进入函数调用
`si`, `ni`：与`s`, `n`命令类似，但执行汇编命令
`finish`：运行程序，直到当前函数完成返回，并打印函数返回时的堆栈地址和返回值及参数值等信息
`u`, `until [linenum]`：运行程序直到退出循环体，或指定运行至某行
`return [expr]`：取消当前函数的执行，并立即返回
`jump [line/addr/+num]`：乱序执行，跳转到某条语句执行，一般在函数内跳转，否则会引发栈错误。等价于`set $pc=addr`
`call [expr]`:强制调用某函数，并显示函数的返回值（如果函数返回值不是void）。`print`也可以完成强制调用函数的功能
`signal [signalnum]`：产生一个信号量给被调试的程序，UNIX的系统信号量通常从1到15
`handle [signal] [actions]`：产生一个信号给gdb，可以是字符名或整数1-15。actions可以指定gdb对信号的处理方式。包括
* stop/nostop - 暂停/不暂停程序，并打出信号消息
* print/noprint - 显示/不显示信号信息
* pass/noignore - gdb不处理信号，而是交给被调试程序处理
* nopass/ignore - gdb处理信号，而被调试程序不知道信号


### 查看源代码
`l`, `list [linenum/func/+-offset/*addr/first,last]`：显示源代码，可以有多种指定方式，也支持file:line, file:func的方式
`l -`：显示当前行之前的源代码
`search <regexp>`：向前搜索源代码
`reverse-search <regexp>`：向后搜索源代码
`info line [linenum/func/...]`：查看源代码在内存中的地址，默认查看当前行代码

`set listsize [count]`：设置一次显示源代码的行数
`show listsize`：查看当前listsize的设置

`disassemble [func]`：反汇编，默认查看当前执行时的汇编，其实际上只是把目前内存中的指令dump出来

`info macro [mac]`：查看宏定义的位置和内容
`macro define/list/undef`：自定义若干宏
`macro expand [exp]`：将表达式中的宏展开

### 分割窗口查看
`layout [command]`：用于分割窗口，可以一边查看代码，一边测试
`layout src`：显示源代码窗口
`layout asm`：显示反汇编窗口
`layout regs`：显示源代码/反汇编和CPU寄存器窗口
`layout split`：显示源代码和反汇编窗口
`Ctrl + L`：刷新窗口

### 设置停止点（断点、观察点、捕捉点）
`b`, `break [linenum/func/*addr]`：设置断点，默认在下一条指令处新建断点。可用“行号”，“函数名称”，“执行地址”加星号。C++中可以使用class::function或function(type, type)格式来指定函数名。行号和函数名可以指定文件名，如`b main.cpp:18`， `b blob.cpp:get(int)`
`b +/-<offset>`：在当前行的前面/后面offset行设置断点
`b ... if <cond>`：在cond条件表示式为真时设置断点。“...”可以是[linenum]、+/-offset。比如在循环体中，可以设置`b 18 if i=100`，表示当i为100时在18行停住程序。
`break [linespec] thread [threadno] [if ...]`：可以单独设置某个线程的断点（默认所有线程都有这个断点），threadno是gdb指定的线程号，可以通过`info threads`获得
`condition [n] [expression]`：修改断点号为n的条件为expression
`condition [n]`：清除断点号为n的条件
`command [n]`：设置断点n处的批处理指令。输入多条指令，最后以`end`结束。每次执行到断点n处都会自动执行
`info break`：列出当前所设置了的所有停止点

`watch [expr]`：设置观察点。如果某个表达式（变量名）值有变化，则暂停程序
`rwatch [expr]`：当表达式（变量）expr被读时，停住程序
`awatch [expr]`：当表达式（变量）的值被读或被写时，停住程序
`info watchpoints`：列出当前所设置了的所有观察点

`catch [event]`：当发生某个事件时，暂停程序
`tcatch [event]`：只设置一次捕捉点，当程序停住以后，应点被自动删除
`catch throw`：一个C++抛出的异常。（throw为关键字）
`catch catch`：一个C++捕捉到的异常。（catch为关键字）
`catch exec`：调用系统调用exec时。（exec为关键字，目前此功能只在HP-UX下有用）
`catch fork`：调用系统调用fork时。（fork为关键字，目前此功能只在HP-UX下有用）
`catch vfork`：调用系统调用vfork时。（vfork为关键字，目前此功能只在HP-UX下有用）
`catch load [libname]`：载入共享库（动态链接库）时。（load为关键字，目前此功能只在HP-UX下有用）
`catch unload [libname]`：卸载共享库（动态链接库）时。（unload为关键字，目前此功能只在HP-UX下有用）

`clear`：清除所有的已定义的停止点
`clear [file]:[function]`：清除所有设置在函数上的停止点
`clear [file]:[linenum]`：清除所有设置在指定行上的停止点

`d`,`delete [n/n_range]`：删除指定的停止点，可以是范围（如：3-7）。如果不指定停止点号，则表示删除所有的停止点。
`dis`,`disable [n/n_range]`：disable所指定的停止点
`enable [once] [n/n_range]`：enable所指定的停止点，可以只enable一次

### 查看调用信息
`bt`,`backtrace [n/-n]`：显示向上/向下的n层堆栈，默认显示所有栈帧
`f`,`frame [n]`：切换到帧n，默认打印当前栈的信息
`info frame`：显示详细的当前栈信息
`up/down [n]`：向上或向下移动n层栈，默认为1
`info program`：来查看程序的是否在运行，进程号，被暂停的原因

### 查看内存
`p`, `print [expr]`：显示指定表达式的值，表达式可以包含变量（临时变量或全局变量）、常量、函数等内容，但不能包含宏定义，如 `p /x a.get(tee_count)+5`。在表达式中，还可以包含几种GDB所支持的操作符。
`p /[f] [expr]`：使用/f 指定的格式输出。输出格式可以为：
* /d 按十进制格式显示变量
* /x 按十六进制格式显示变量
* /a 按十六进制格式显示变量
* /u 按十六进制格式显示无符号整型
* /o 按八进制格式显示变量
* /t 按二进制格式显示变量
* /c 按字符格式显示变量
* /f 按浮点数格式显示变量

`p [file/func]::[var]`：指定在文件或函数中的变量。GDB能自动识别“::” 是否C++的操作符
`p *[addr]@[size]`：显示地址addr处的连续size个数据
`p array`：array是数组名，显示数组的全部内容
`p {[type]}[addr]`：显示指向内存地址addr的类型为type的一个对象
`p x=4`：修改变量x的值并显示

`x/[nfu] [addr/&var]`：查看内存地址中的内容。x是examine的意思，其中：
* [addr/&var] 表示一个内存地址。
* n 是一个正整数，表示显示内存的总长度
* f 是一个字符，表示显示的格式。s - 字符串，i - 指令，x - 十六进制，d - 十进制，c - 字符
* u 是一个字符，表示每次显示的字节数，默认是4字节。b - 单字节，h - 双字节，w - 四字节，g - 八字节。

`x/3uh 0x54320`：从内存地址0x54320开始以双字节为1个单位（h）、16进制方式（u）显示3个单位（3）的内存。
`x/10i $pc-20`：查看当前行附近的汇编指令（$pc是指令寄存器）

`display [/f] [expr/addr]`：设置自动显示的变量，当程序停住或是单步跟踪时，这些变量会自动显示，如`display /i $pc`表示以汇编指令格式自动显示指令寄存器中的指令（即下一步汇编指令）
`undisplay [n]`, `delete display [n]`：取消自动显示，可以使用逗号分隔的列表或范围
`disable/enable display [n]`：暂停或恢复自动显示
`info display`：查看display设置的自动显示的信息

`set [var/reg/addr=num]`：修改变量、寄存器、内存地址的值，如`set $v0 = 0x004000000`，`set $epc = 0xbfc00000`，`set {unsigned int}0x8048a51=0x0`
`set $i = n`：自定义变量i，并用程序中的n赋值。i可以用在gdb的语句中，比如`p a[$i++]`

### 查询运行信息
`info locals`：查看局部变量
`info registers`：查看除了浮点寄存器以外的寄存器
`info all-registers`：查看所有寄存器，包括浮点寄存器
`info registers [regname]`：查看所指定的寄存器
`info break/watchpoints`：列出当前所设置的所有观察点
`info signals/handle`：查看有哪些信号在被GDB检测中 
`info threads`：查看所有线程

# GCC 编译器
官网：https://gcc.gnu.org/