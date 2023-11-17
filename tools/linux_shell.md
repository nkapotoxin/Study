# linux shell

## command

### uniq

消除重复行

    sort unsort.txt | uniq

统计各行在文件中出现的次数

    sort unsort.txt | uniq -c

找出重复行

    sort unsort.txt | uniq -d

指定每行中需要比较的重复内容: -s 开始位置  -w 比较字符数    


### printf

进制转换

    %x 十六进制
    %d 十进制
    %o 八进制
    printf "%x\n" 27

### bc

进制转换

    echo "obase=16;ibase=2;101100" | bc 
    #obase 输出格式
    #ibase 输入格式
    #加上输入 bc输出

    备注: obase与ibase混合使用时, bc会有bug, 建议bc紧配合ibase使用, 默认输出10进制
          即x进制转10进制使用

    echo "ibase=2;{}"|bc

    10进制转x进制

    printf "%x\n" {}

    配合xargs使用时, bc不会出错

    
### xargs

命令行参数转换

将多行转换成单行

    cat file.txt|xargs

将单行转换成多行输出, -n指定每行显示的字段数

    cat file.txt|xargs -n 3

参数说明

    -d: 定界符, 默认位空格 多行的定界符为\n
    -n: 指定输出为多行
    -I {}: 指定替换字符串, 用于待执行的命令需要多个入参时,如
    cat file.txt| xargs -I {} echo "ibase=2;{}" | wc
    -0: 指定\0为输入定界符, 比如统计程序行数
    find source_dir/ -type f -name "*.cpp" -print0 |xargs -0 wc -l
    find ./ -type f -name "*.md" -print |  xargs wc -l

    推荐做法: find和xargs一起用时, 建议使用-print0 和 -0分别确定两个命令的定界符为字符null来分隔输出,
    避免find命令输出结果的定界符究竟是'\n'还是' ', 因为有些文件名中可能包含空格符, xargs可能会误认为他们是定界符

### subshell hack

xargs只能以几种有限的方式来提供参数, 除此之外, 更灵活的方式是通过子shell的妙招.
比如一个包含while循环的子shell可以用来读取参数, 并通过一种巧妙地方式执行命令:

    cat files.txt | ( while read arg; do cat $arg; done )
    # eq. cat files.txt | xargs -I {} cat {}


### fg bg jobs ctrl+z
linux提供的fg和bg命令，可以让我们轻松调度正在运行的任务. 假如你发现前天运行的一个程序需要很长的时间，但是需要干前天的事情，你就可以用ctrl-z挂起这个程序，然后可以看到系统的提示:

[1]+ Stopped /root/bin/rsync.sh

然后我们可以吧程序调度到后台执行：（bg 作业号）

bg 1
[1]+ /root/bin/rsync.sh &

用jobs命令查看任务

jobs

[1]+ Running /root/bin/rsync.sh &

把它调回到控制台运行

fg 1

/root/bin/rsync.sh

这样，你这控制台上就只有等待这个任务完成了。

fg、bg、jobs、&、 ctrl+z都是跟系统任务有关的，学会了相当的实用

一、&最经常被用到

这个用在一个命令的最后，可以把这个命令放到后台执行

二、ctrl + z

可以将一个正在前台执行的命令放到后台，并且暂停

三、jobs

查看当前有多少在后台运行的命令

四、fg

将后台中的命令调至前台继续运行

如果后台有多个命令，可以用fg %jobnumber将选中的命令调出，%jobnumber是通过jobs命令查到的后台正在执行的命令的序号（不是pid）

五、bg

将一个在后台暂停的命令，变成继续执行

如果后台有多个命令，可以用bg %jobnumber将选中的命令调出，%jobnumber是通过jobs命令查到的后台正在执行的命令的序号（不是pid）


