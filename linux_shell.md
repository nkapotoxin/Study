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


