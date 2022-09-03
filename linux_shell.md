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

    echo "ibase=16;obase=2;FFEE" | bc 
    #ibase 输入格式
    #obase 输出格式
    #加上输入 bc输出
    
### xargs

    
