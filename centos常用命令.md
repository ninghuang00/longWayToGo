#### centos常用命令

* 查看进程
`ps -ef|grep tomcat`

* 查看端口情况
`netstat -pan|grep 1099`
>常用参数:
-a 显示所有连接和监听的端口
-b　　　　　　　　　　　 显示包含于创建每个连接或监听端口的可执行组件。在某些情况下已知可执行组件
-e　　　　　　　　　　　 显示以太网统计信息。此选项可以与 -s选项组合使用。
-n　　　　　　　　　　　 以数字形式显示地址和端口号。
-o　　　　　　　　　　　 显示与每个连接相关的所属进程 ID。
-p proto　　　　　 显示 proto 指定的协议的连接；proto 可以是

* 修改密码
`passwd username`

* 添加用户
``

连接远程主机其他接口
`ssh root@ip -p port`

* 查看系统版本信息
`uname -a`

* filename.zip的解压
`unzip filename.zip`

* 解压文件
`tar -zxvf filename.tar.gz`
>其中zxvf含义分别如下
-c: 建立压缩档案
-x：解压
-t：查看内容
-r：向压缩归档文件末尾追加文件
-u：更新原压缩包中的文件
这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。
-z：有gzip属性的
-j：有bz2属性的
-Z：有compress属性的
-v：显示所有过程
-O：将文件解开到标准输出
下面的参数-f是必须的
-f: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。
事实上, 从1.15版本开始tar就可以自动识别压缩的格式,故不需人为区分压缩格式就能正确解压
tar -xvf filename.tar.gz
tar -xvf filename.tar.bz2
tar -xvf filename.tar.xz
tar -xvf filename.tar.Z

* 解压文件到指定文件夹
`tar -zxvf jdk-8u161-linux-i586.tar.gz -C java`


