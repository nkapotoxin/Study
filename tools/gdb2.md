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

### 
