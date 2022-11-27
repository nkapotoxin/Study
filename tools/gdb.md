## 1 gdb是什么

	gdb是一个在UNIX环境下的命令行调试工具, 如果需要使用gdb调试程序，需要在gcc编译时加上-g选项.

## 2 gdb常用命令

### gdb
	进入gdb, 其中test是要调试的程序，由gcc test.c -g -o test生成。进入后提示符变为(gdb). 
	gdb test

### list l
    查看当前代码行
    list

    查看之前代码行
    list -

### info i
    查看代码位置
    info source

    查看local变量
    info locals

    查看stack frame 参数
    info args

    查看当前stack信息
    info stack

	查看设置的断点
	info break
	info b

### break b
	在当前文件指定代码行设置断点
	b 6

### print p
	显示变量值(GDB在显示变量值时都会在对应值之前加上"$N"标记，它是当前变量值的引用标记，以后若想再次引用此变量，就可以直接写作"$N"，而无需写冗长的变量名)
	p test

### watch
    观察某个变量, gdb在"test"设置了观察点
	watch test

### r
	运行代码
	r

### n
	单步运行
	n

### c
	程序继续运行, 直到下一个断点或者程序结束
	c

### q
	退出程序
	q

### shell
	运行shell命令
	shell ls

## 3 调试core文件

	CoreDump:
	Core的意思是内存，Dump的意思是扔出来，堆出来。开发和使用Unix程序时，有时程序莫名其妙的down了，却没有任何的提示(有时候会提示core
	dumped)，这时候可以查看一下有没有形如core.进程号的文件生成，这个文件便是操作系统把程序down掉时的内存内容扔出来生成的,
	它可以做为调试程序的参考.

### 生成Core文件

	一般默认情况下，core file的大小被设置为了0，这样系统就不dump出core
	file了。修改后才能生成core文件。一般情况下Core文件会生成在可执行文件同目录下, 一般来说文件名为Core或者Core.<PID>

	ulimit -c unlimited

### 查看Core文件

	gdb [exec file] [core file]

### 实时观察进程crash信息

	gdb -p PID c


	
