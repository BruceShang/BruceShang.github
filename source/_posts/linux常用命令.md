---
title: Linux常用命令
tags: [linux, Linux命令]
date: 2018-01-29 14:16:32
categories: Linux
---

##### 1.命令的基本格式
命令 选项 参数

ls -a 全部文件/包括隐藏文件

ls -l 显示详细信息
ls -d 查看目录属性
ls -i 显示inode
ls -lh 显示文件的字节大小
<!--more-->
 > rw -r - - r - -

 > 文件类型 （ - 文件 d 目录 i软链接文件）
rw-              r - -               r - -
u所有者      g所属组         o其他人
r 读 w写 x执行


##### 2.目录处理命令
根目录和家目录是两个不同的概念

mkdir -p 目录
-p 递归创建

cd+enter 进入当前用户的家目录
cd ~ 进入当前用户的家目录
cd - 进入上次目录
cd .. 进入上一级目录
cd . 进入当前目录

相对路径：参照的是我当前的目录
绝对路径：具体的目标路径

按连续两次tab键：命全补充和目录补全
pwd：打印当前位置的工作路径


##### 3.删除文件或目录
rm -rf 文件/目录

-r 删除目录
-f 强制

##### 4.复制文件或目录
cp -r   原文件或目录 目标目录
     -p  连带文件属性复制
     -d  若源文件是链接文件，则复制链接属性
     -a  相当于pdr

##### 5.剪切或者改名命令
mv 源文件或者目录 目标目录

##### 6.目录介绍
根目录下的bin和sbin， usr目录下的bin和sbin，这四个目录都是用来保存系统命令的。
bin和sbin的区分：
bin： 任何用户都可以执行
sbin：只有root用户才能执行
boot: 启动目录
dev: 特殊文件保存目录
lib: 函数库目录
sys 和 proc: proc和sys目录不能直接操作，这两个目录保存的是内存的过载点。 
usr：系统软件资源目录
var：系统相关命令
media met misc 空目录

##### 7.文件搜索命令
- 文件搜索命令locate(执行没有命令执行时需要安装 yum install locate)
  locate 文件名   
  在后台数据库中按文件名搜索，搜索速度更快
  
  /var/lib/mlocate
  locate命令所搜索的后台数据库

  updated
  更新数据库
  /etc/updatedb.conf 配置文件

- 命令搜索命令whereis与which
 
  whereis 命令名
  搜索命令所在路径及帮助文档所在位置

  -b: 只查找可执行文件
  -m:只查找帮助文件

    which 命令名
    搜索命令所在路径及别名

- 文件搜索命令find

  find 搜索范围 搜索条件
  find / -name filelog.log
  > find 是在系统中搜索符合条件的文件名，如果需要匹配，s会用通配符匹配，通配符是完全匹配。

  find / -iname filelog.log
  >  加i忽略大小写

  find / -user root
  >  按照所有者搜索

  Linux中的通配符
  * 匹配任意内容
  ？匹配任意一个字符
  []  匹配任意一个中括号内的字符