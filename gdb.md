# gdb调试命令

　　gdb是一个在UNIX环境下的命令行调试工具。

　　如果需要使用gdb调试程序，请在gcc时加上-g选项。

　　下面的命令部分是简化版，比如使用l代替list等等。

 

## 1.基本命令
### 1）进入GDB　　#gdb test

　　test是要调试的程序，由gcc test.c -g -o test生成。进入后提示符变为(gdb) 。

### 2）查看源码　　(gdb) l

　　源码会进行行号提示。

　　如果需要查看在其他文件中定义的函数，在l后加上函数名即可定位到这个函数的定义及查看附近的其他源码。或者：使用断点或单步运行，到某个函数处使用s进入这个函数。

### 3）设置断点　　(gdb) b 6

　　这样会在运行到源码第6行时停止，可以查看变量的值、堆栈情况等；这个行号是gdb的行号。

### 4）查看断点处情况　　(gdb) info b

　　可以键入"info b"来查看断点处情况，可以设置多个断点；

### 5）运行代码　　(gdb) r

### 6）显示变量值　　(gdb) p n

　　在程序暂停时，键入"p 变量名"(print)即可；

　　GDB在显示变量值时都会在对应值之前加上"$N"标记，它是当前变量值的引用标记，以后若想再次引用此变量，就可以直接写作"$N"，而无需写冗长的变量名；

### 7）观察变量　　(gdb) watch n

 在某一循环处，往往希望能够观察一个变量的变化情况，这时就可以键入命令"watch"来观察变量的变化情况，GDB在"n"设置了观察点；

### 8）单步运行　　(gdb) n

### 9）程序继续运行　　(gdb) c

　　使程序继续往下运行，直到再次遇到断点或程序结束；

### 10）退出GDB　　(gdb) q

 

## 2.断点调试
命令格式　　                      例子             　　　　　　作用

break + 设置断点的行号　　break n　　　　　　在n行处设置断点

tbreak + 行号或函数名　　tbreak n/func　　　　设置临时断点，到达后被自动删除

break + filename + 行号　　break main.c:10　　用于在指定文件对应行设置断点

break + <0x...>　　break 0x3400a　　　　　　用于在内存某一位置处暂停 

break + 行号 + if + 条件　　break 10 if i==3　　　用于设置条件断点，在循环中使用非常方便 

info breakpoints/watchpoints [n]　　info break　　n表示断点号，查看断点/观察点的情况 

clear + 要清除的断点行号　　clear 10　　　　用于清除对应行的断点，要给出断点的行号，清除时GDB会给出提示

delete + 要清除的断点编号　　delete 3　　　　用于清除断点和自动显示的表达式的命令，要给出断点的编号，清除时GDB不会给出任何提示

disable/enable + 断点编号　　disable 3　　　　让所设断点暂时失效/使能，如果要让多个编号处的断点失效/使能，可将编号之间用空格隔开

awatch/watch + 变量　　awatch/watch i　　　　设置一个观察点，当变量被读出或写入时程序被暂停 

rwatch + 变量　　　　　　rwatch i　　　　　　　　设置一个观察点，当变量被读出时，程序被暂停 

catch　　　　　　　　　　　　　　　　　　设置捕捉点来补捉程序运行时的一些事件。如：载入共享库（动态链接库）或是C++的异常 

tcatch　　　　　　　　　　　　　　　　　　只设置一次捕捉点，当程序停住以后，应点被自动删除

 

## 3.数据命令
display +表达式　　display a　　用于显示表达式的值，每当程序运行到断点处都会显示表达式的值 

info display　　　　　　用于显示当前所有要显示值的表达式的情况 

delete + display 编号　　delete 3　　用于删除一个要显示值的表达式，被删除的表达式将不被显示

disable/enable + display 编号　　disable/enable 3　　使一个要显示值的表达式暂时失效/使能 

undisplay + display 编号　　undisplay 3　　用于结束某个表达式值的显示

whatis + 变量　　whatis i　　显示某个表达式的数据类型

print(p) + 变量/表达式　　p n　　用于打印变量或表达式的值

set + 变量 = 变量值　　set i = 3　　改变程序中某个变量的值

　　在使用print命令时，可以对变量按指定格式进行输出，其命令格式为print /变量名 + 格式

　　其中常用的变量格式：x：十六进制；d：十进制；u：无符号数；o：八进制；c：字符格式；f：浮点数。

 

 

## 4.调试运行环境相关命令
set args　　set args arg1 arg2　　设置运行参数

show args　　show args　　参看运行参数

set width + 数目　　set width 70　　设置GDB的行宽

cd + 工作目录　　cd ../　　切换工作目录

run　　r/run　　程序开始执行

step(s)　　s　　进入式（会进入到所调用的子函数中）单步执行，进入函数的前提是，此函数被编译有debug信息

next(n)　　n　　非进入式（不会进入到所调用的子函数中）单步执行

finish　　finish　　一直运行到函数返回并打印函数返回时的堆栈地址和返回值及参数值等信息

until + 行数　　u 3　　运行到函数某一行 

continue(c)　　c　　执行到下一个断点或程序结束 

return <返回值>　　return 5　　改变程序流程，直接结束当前函数，并将指定值返回

call + 函数　　call func　　在当前位置执行所要运行的函数

 

## 5.堆栈相关命令
backtrace/bt　　bt　　用来打印栈帧指针，也可以在该命令后加上要打印的栈帧指针的个数，查看程序执行到此时，是经过哪些函数呼叫的程序，程序“调用堆栈”是当前函数之前的所有已调用函数的列表（包括当前函数）。每个函数及其变量都被分配了一个“帧”，最近调用的函数在 0 号帧中（“底部”帧）

frame　　frame 1　　用于打印指定栈帧

info reg　　info reg　　查看寄存器使用情况

info stack　　info stack　　查看堆栈使用情况

up/down　　up/down　　跳到上一层/下一层函数

 

## 6.跳转执行
jump  指定下一条语句的运行点。可以是文件的行号，可以是file:line格式，可以是+num这种偏移量格式。表式着下一条运行语句从哪里开始。相当于改变了PC寄存器内容，堆栈内容并没有改变，跨函数跳转容易发生错误。

 

##7.信号命令
signal 　　signal SIGXXX 　　产生XXX信号，如SIGINT。一种速查Linux查询信号的方法：# kill -l

handle 　　在GDB中定义一个信号处理。信号可以以SIG开头或不以SIG开头，可以用定义一个要处理信号的范围（如：SIGIO-SIGKILL，表示处理从SIGIO信号到SIGKILL的信号，其中包括SIGIO，SIGIOT，SIGKILL三个信号），也可以使用关键字all来标明要处理所有的信号。一旦被调试的程序接收到信号，运行程序马上会被GDB停住，以供调试。其可以是以下几种关键字的一个或多个：
　　nostop/stop
　　　　当被调试的程序收到信号时，GDB不会停住程序的运行，但会打出消息告诉你收到这种信号/GDB会停住你的程序  
　　print/noprint
　　　　当被调试的程序收到信号时，GDB会显示出一条信息/GDB不会告诉你收到信号的信息 
　　pass 
　　noignore 
　　　　当被调试的程序收到信号时，GDB不处理信号。这表示，GDB会把这个信号交给被调试程序会处理。 
　　nopass 
　　ignore 
　　　　当被调试的程序收到信号时，GDB不会让被调试程序来处理这个信号。 
　　info signals 
　　info handle 
　　　　可以查看哪些信号被GDB处理，并且可以看到缺省的处理方式

　　single命令和shell的kill命令不同，系统的kill命令发信号给被调试程序时，是由GDB截获的，而single命令所发出一信号则是直接发给被调试程序的。

 

## 8.运行Shell命令
　　如(gdb)shell ls来运行ls。　　

 

## 9.更多程序运行选项和调试
### 1、程序运行参数。 
　　set args 可指定运行时参数。（如：set args 10 20 30 40 50） 
　　show args 命令可以查看设置好的运行参数。 
### 2、运行环境。 
　　path 可设定程序的运行路径。 
　　show paths 查看程序的运行路径。

　　set environment varname [=value] 设置环境变量。如：set env USER=hchen 

　　show environment [varname] 查看环境变量。 

### 3、工作目录。

　　cd　　  相当于shell的cd命令。 

　　pwd　　显示当前的所在目录。 
### 4、程序的输入输出。 
　　info terminal 显示你程序用到的终端的模式。 
　　使用重定向控制程序输出。如：run > outfile 
　　tty命令可以指写输入输出的终端设备。如：tty /dev/ttyb

### 5、调试已运行的程序

两种方法： 
　　(1)在UNIX下用ps查看正在运行的程序的PID（进程ID），然后用gdb PID格式挂接正在运行的程序。 
　　(2)先用gdb 关联上源代码，并进行gdb，在gdb中用attach命令来挂接进程的PID。并用detach来取消挂接的进程。

### 6、暂停 / 恢复程序运行　　当进程被gdb停住时，你可以使用info program 来查看程序的是否在运行，进程号，被暂停的原因。 在gdb中，我们可以有以下几种暂停方式：断点（BreakPoint）、观察点（WatchPoint）、捕捉点（CatchPoint）、信号（Signals）、线程停止（Thread Stops），如果要恢复程序运行，可以使用c或是continue命令。

### 7、线程（Thread Stops）

如果程序是多线程，可以定义断点是否在所有的线程上，或是在某个特定的线程。 
　　break thread
　　break thread if ... 
　　linespec指定了断点设置在的源程序的行号。threadno指定了线程的ID，注意，这个ID是GDB分配的，可以通过“info threads”命令来查看正在运行程序中的线程信息。如果不指定thread 则表示断点设在所有线程上面。还可以为某线程指定断点条件。如： 
　　(gdb) break frik.c:13 thread 28 if bartab > lim 
当你的程序被GDB停住时，所有的运行线程都会被停住。这方便查看运行程序的总体情况。而在你恢复程序运行时，所有的线程也会被恢复运行。

 

## 10.调试core文件
Core Dump：Core的意思是内存，Dump的意思是扔出来，堆出来。开发和使用Unix程序时，有时程序莫名其妙的down了，却没有任何的提示(有时候会提示core dumped)，这时候可以查看一下有没有形如core.进程号的文件生成，这个文件便是操作系统把程序down掉时的内存内容扔出来生成的, 它可以做为调试程序的参考

### (1)生成Core文件

　　一般默认情况下，core file的大小被设置为了0，这样系统就不dump出core file了。修改后才能生成core文件。
　　#设置core大小为无限
　　ulimit -c unlimited
　　#设置文件大小为无限
　　ulimit unlimited

　　这些需要有root权限, 在ubuntu下每次重新打开中断都需要重新输入上面的第一条命令, 来设置core大小为无限

core文件生成路径:输入可执行文件运行命令的同一路径下。若系统生成的core文件不带其他任何扩展名称，则全部命名为core。新的core文件生成将覆盖原来的core文件。

#### 1）/proc/sys/kernel/core_uses_pid可以控制core文件的文件名中是否添加pid作为扩展。文件内容为1，表示添加pid作为扩展名，生成的core文件格式为core.xxxx；为0则表示生成的core文件同一命名为core。
可通过以下命令修改此文件：
echo "1" > /proc/sys/kernel/core_uses_pid

#### 2）proc/sys/kernel/core_pattern可以控制core文件保存位置和文件名格式。
可通过以下命令修改此文件：
echo "/corefile/core-%e-%p-%t" > core_pattern，可以将core文件统一生成到/corefile目录下，产生的文件名为core-命令名-pid-时间戳
以下是参数列表:
    %p - insert pid into filename 添加pid
    %u - insert current uid into filename 添加当前uid
    %g - insert current gid into filename 添加当前gid
    %s - insert signal that caused the coredump into the filename 添加导致产生core的信号
    %t - insert UNIX time that the coredump occurred into filename 添加core文件生成时的unix时间
    %h - insert hostname where the coredump happened into filename 添加主机名
    %e - insert coredumping executable name into filename 添加命令名

### (2)用gdb查看core文件

　　发生core dump之后, 用gdb进行查看core文件的内容, 以定位文件中引发core dump的行.
　　gdb [exec file] [core file]
　　如:
　　gdb ./test core

　　或gdb ./a.out
 　　core-file core.xxxx
　　gdb后, 用bt命令backtrace或where查看程序运行到哪里, 来定位core dump的文件->行.

　　待调试的可执行文件，在编译的时候需要加-g，core文件才能正常显示出错信息

　　1）gdb -core=core.xxxx
　　file ./a.out
　　bt
　　2）gdb -c core.xxxx
　　file ./a.out
　　bt

### (3)用gdb实时观察某进程crash信息

　　启动进程
　　gdb -p PID
　　c
　　运行进程至crash
　　gdb会显示crash信息
　　bt


