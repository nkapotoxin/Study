## command

### 显示line number

    set number

### 关闭设置

    set no{option}

    set nonumber # for close number display
    
### 设置缩进
    
    表示每一级缩进的长度
    set shiftwidth=4

    表示在编辑模式的时候按退格键的时候退回缩进的长度
    set softtabstop=4

    自动缩进
    set autoindent

    set tabstop=4

### 设置分屏

	水平分屏
	ctrl+w v

	上下分屏
	ctrl+w s

	切换屏幕
	ctrl+w w

	打开一个文件并上下分屏
	:sp <newfile>

	打开一个文件并水平分屏
	:vsp <newfile>

### 执行shell命令

	不退出vim执行shell命令
	:!<command>
	:!bash

	插入shell命令结果到当前行
	:r !<command>

	将m~n行的内容给shell命令处理
	:m,n!<command>
	
	将当前行的小写转换为大写
	:.!tr [a-z] [A-Z]

	看一下将m~n行的内容给shell命令处理后的效果
	:m,n w!<command>
	
