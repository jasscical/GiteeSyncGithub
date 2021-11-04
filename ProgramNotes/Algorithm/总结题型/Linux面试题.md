# Linux

### 常见问题

### 1、[grep、sed、awk](https://www.cnblogs.com/along21/p/10366886.html)

[参考链接](https://blog.csdn.net/weixin_46833747/article/details/108228180)

awk、grep、sed是linux操作文本的三大利器，合称文本三剑客，也是必须掌握的linux命令之一。

三者的功能都是处理文本，但侧重点各不相同，其中属awk功能最强大，但也最复杂。

grep更适合单纯的查找或匹配文本，sed更适合编辑匹配到的文本，awk更适合格式化文本，对文本进行较复杂格式处理。

###### grep

用于过滤/搜索的特定字符。可使用正则表达式能多种命令配合使用，使用上十分灵活

```shell
grep [option] pattern file
-c  只输出匹配行的计数。
-i  不区分大小写（只适用于单字符）。
 
-h  查询多文件时不显示文件名。
-l   查询多文件时只输出包含匹配字符的文件名。
 
-n  显示匹配行及行号。 
-s  不显示不存在或无匹配文本的错误信息。
-v  显示不包含匹配文本的所有行。(反向过滤)
```

###### 常见面试题

1、显示`/proc/meminfo`文件中以不区分大小的`s`开头的行；

```shell
grep -i ^s /proc/meminfo
cat /proc/meminfo|grep –i ^s
```

2、显示`/etc/passwd`中以`nologin`结尾的行;

```shell
grep nologin$ /etc/passwd
sed -n '/nologin$/p' passwd 
```

3、显示`/etc/inittab`中以`#`开头，且后面跟一个或多个空白字符，而后又跟了任意字符的行；

```shell
grep ^#.* /etc/inittab
```

4、显示`/etc/inittab`中包含了`:一个数字:`(即两个冒号中间一个数字)的行；

```shell
grep :[0-9]: /etc/inittab 
grep :[[:digit:]]: /etc/inittab
```

5、显示`/boot/grub/grub.conf`文件中以`一个或多个空白字符开头`的行；

```shell
grep ^[[:space:]] /boot/grub2/grub.cfg
```

6、显示`/etc/inittab`文件中以`一个数字开头，与开头数字相同的数字结尾`的行；

```shell
grep "^[09].*\1$" /etc/inittab
```

7、ifconfig命令可以显示当前主机的IP地址相关的信息等，要求不包括127.0.0.1；

```shell
ifconfig eth0| grep "inetaddr" | cut -d： -f2 | cut -d " " f1(注意“”中的空格)
ifconfig eth0| grep "inetaddr" | awk '{print $2}'| awk -F ':''{print $2}'
```

8、显示`/etc/sysconfig/network-scripts/ifcfg-eth0`文件中的包含了类似IP地址点分十进制数字格式的行；

```shell
grep -E "([0-9]{1,3}\.){3}\.[0-9]{1,3}"/etc/sysconfig/network-scripts/ifcfg-eth0
grep"[0-9]\{1,3\}\."\{3\}\.[0-9]\{1,3\}/etc/sysconfig/network-scripts/ifcfg-eth0
```

###### sed

sed是非交互式的编辑器。它不会修改文件，除非使用shell重定向来保存结果。默认情况下，所有的输出行都被打印到屏幕上。sed编辑器逐行处理文件（或输入），并将结果发送到屏幕。具体过程如下：

首先sed把当前正在处理的行保存在一个临时缓存区中（也称为模式空间），然后处理临时缓冲区中的行，完成后把该行发送到屏幕上。

sed每处理完一行就将其从临时缓冲区删除，然后将下一行读入，进行处理和显示。

处理完输入文件的最后一行后，sed便结束运行。

sed把每一行都存在临时缓冲区中，对这个副本进行编辑，所以不会修改原文件。

 

常用选项：

```
p	        打印（需加 -n）
d	        删除
a	        追加
i	        插入
c	        更改/替换
w	        写入
r	        整合文件
's/xx/xx/g'	字符替换
```

###### 常见面试题

1、在text.txt文件中匹配regex的行之后插入一空行

```shell
sed ‘/regex/G’ text.txt
```

2、sed将文件test中第50行中的haiwao改为haiwai

```shell
sed ‘50s/haiwao/haiwai/’ test
```

3、替换一个文件/etc/passwd里的这`root:x:0:0:root:/root:/bin/bash`一行第二个root为test

```shell
sed ‘/root/s/:root:/:test:/’ /etc/passwd
```

4、打印/etc/passwd的奇数行？

```shell
sed -n ‘1~2p’ /etc/passwd
```

5、⽇志⽂件a.log，内容是时间顺序递增，从0点到23点的所有⽇志记录，每条时间的⽇志为⼀⾏：

```
2016/06/12 00:00:00 - - 200 190 http://www.a.com/o1html xxxxxxx
2016/06/12 00:00:01 - - 200 390 http://www.b.com/o1html xxxxxxx
2016/06/12 00:00:02 - - 200 490 http://www.v.com/o.html xxxxxxx
2016/06/12 00:00:03 - - 200 890 http://www.a.com/o.html xxxxxxx
…
2016/06/12 23:59:56 - - 200 320 http://www.3.com/o.jpg xxxxxxx
2016/06/12 23:59:57 - - 200 131 http://www.9.com/o.html xxxxxxx
2016/06/12 23:59:58 - - 200 489 http://www.r.com/o.net xxxxxxx
2016/06/12 23:59:59 - - 200 772 http://www.w.com/o.php xxxxxxx
```

打印出05:30:35到22:45:55之间的所有日志

```shell
sed -nr ‘/05:30:35/,/22:45:55/p’ file.log
```

6.123abc456	要求输出：    456ABC123
   456def123						 123DEF456
   567abc789					    789ABC567
   789def567						 567DEF789
答案：

```shell
sed -r -i 's#(...)(.)(.)(.)(...)#\5\u\2\u\3\u\4\1#g' 22.txt
返回
sed -r -i 's#(...)(.)(.)(.)(...)#\5\l\2\l\3\l\4\1#g' 22.txt
```

###### awk

awk是一种编程语言，用于在linux/unix下对文本和数据进行处理。数据可以来自标准输入(stdin)、一个或多个文件，或其它命令的输出。它**支持用户自定义函数**和动态正则表达式等先进功能，是linux/unix下的一个强大编程工具。它在命令行中使用，但更多是作为脚本来使用。**awk有很多内建的功能**，比如数组、函数等，这是它和C语言的相同之处，灵活性是awk最大的优势。

（1）格式

- -  **FS** ：**输入字段分隔符**，**默认为空白字符**
  -  **OFS** ：**输出字段分隔符**，默认为空白字符
  -  RS ：**输入记录分隔符**，指定输入时的换行符，原换行符仍有效
  -  ORS ：**输出记录分隔符**，输出时用指定符号代替换行符
  -  **NF** ：字段数量，**共有**多少字段， **$NF引用最后一列，$(NF-1)引用倒数第2列**
  -  **NR** ：**行号**，后可跟多个文件，第二个文件行号继续从第一个文件最后行号开始
  -  FNR ：各文件分别计数, 行号，后跟一个文件和NR一样，跟多个文件，第二个文件**行号从1开始**
  -  FILENAME ：**当前文件名**
  -  ARGC ：**命令行参数**的个数
  -  ARGV ：数组，保存的是命令行所给定的各参数，**查看参数**

  

```shell
[root@along ~]# cat awkdemo
hello:world
linux:redhat:lalala:hahaha
along:love:youou
[root@along ~]# awk -v FS=':' '{print $1,$2}' awkdemo  #FS指定输入分隔符
hello world
linux redhat
along love
[root@along ~]# awk -v FS=':' -v OFS='---' '{print $1,$2}' awkdemo  #OFS指定输出分隔符
hello---world
linux---redhat
along---love
[root@along ~]# awk -v RS=':' '{print $1,$2}' awkdemo
hello
world linux
redhat
lalala
hahaha along
love
you
[root@along ~]# awk -v FS=':' -v ORS='---' '{print $1,$2}' awkdemo
hello world---linux redhat---along love---
[root@along ~]# awk -F: '{print NF}' awkdemo
2
4
3
[root@along ~]# awk -F: '{print $(NF-1)}' awkdemo  #显示倒数第2列
hello
lalala
love
[root@along ~]# awk '{print NR}' awkdemo awkdemo1
1
2
3
4
5
[root@along ~]# awk END'{print NR}' awkdemo awkdemo1
5
[root@along ~]# awk '{print FNR}' awkdemo awkdemo1
1
2
3
1
2
[root@along ~]# awk '{print FILENAME}' awkdemo
awkdemo
awkdemo
awkdemo
[root@along ~]# awk 'BEGIN {print ARGC}' awkdemo awkdemo1
3
[root@along ~]# awk 'BEGIN {print ARGV[0]}' awkdemo awkdemo1
awk
[root@along ~]# awk 'BEGIN {print ARGV[1]}' awkdemo awkdemo1
awkdemo
[root@along ~]# awk 'BEGIN {print ARGV[2]}' awkdemo awkdemo1
awkdemo1
```

