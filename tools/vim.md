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
