#### centos7配置环境

* 配置java环境
	1. 先将jdk的压缩包上传到服务器`scp jdk.tar.gz root@xx.xxx.xxx.xxx:/usr/local/java/`
	2. 然后使用`tar -zxvf jdk.tar.gz`解压
	3. 然后配置`vim /etc/profile`,添加以下内容到底部

			JAVA_HOME=/usr/local/java/jdk
	        PATH=$JAVA_HOME/bin:$PATH
	        CLASSPATH=$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar
	        export PATH JAVA_HOME CLASSPATH

	4. 然后`source /etc/profile` 

* 查看所有端口
	`netstat -ntlp`
* 查看端口的开启情况
	`netstat`
* 查看指定端口的占用情况
	`netstat -lnp|grep 8080`
	
* 检测远程主机端口是否打开
	`telnet 120.79.202.146 8080`

* centos7启动firewalld配置防火墙
	1. 确定打开firewalld`systemctl restart firewalld`
	2. 查看firewalld状态,版本等`firewall-cmd --state`
	3. 查看已经打开的port`firewall-cmd --zone=dmz --list-ports`
	4. 开启端口`firewall-cmd --zone=public --add-port=1099/tcp --permanent`

			命令含义：
			–zone #作用域
			–add-port=80/tcp #添加端口，格式为：端口/通讯协议
			–permanent #永久生效，没有此参数重启后失效

	5. 重启防火墙

			firewall-cmd --reload 				#重启firewall
			systemctl stop firewalld.service 	#停止firewall
			systemctl disable firewalld.service #禁止firewall开机启动
	6. 注意阿里云服务器还要在控制中心添加端口规则

* 将idea web项目部署到远程tomcat,以及开机启动tomcat

	1. 配置`tomcat/bin/catalina.sh`文件,首行加入

			export CATALINA_OPTS="-Dcom.sun.management.jmxremote 
			-Dcom.sun.management.jmxremote.port=1099 
			-Dcom.sun.management.jmxremote.ssl=false 
			-Dcom.sun.management.jmxremote.authenticate=false 
			-Djava.rmi.server.hostname=10.82.82.248"
			export JAVA_OPTS="-Dcom.sun.management.jmxremote=
			-Dcom.sun.management.jmxremote.port=1099
			-Dcom.sun.management.jmxremote.ssl=false
			-Dcom.sun.management.jmxremote.authenticate=false"

	2. 启动tomcat
	`
	./catalina.sh run > /dev/null 2>&1 &
	`
	其中“ > /dev/null 2>&1 &”是Linux中的命令：把标准输出和出错处理都放到回收站，这样就免得一大堆输出占领你的屏幕。
	3. 在用jps命令：

	4. javahome: /usr/lib/jvm/java-8-oracle

	5. 设置tomcat开机启动,将startup.sh修改
	`
	export JAVA_HOME=/usr/local/java/jdk1.8
	export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar:.  
	export PATH=$JAVA_HOME/bin:$PATH  
	export CATALINA_HOME=/usr/local/tomcat  
	/usr/local/tomcat/bin/catalina.sh start
	`

	6. 在/etc/rc.d/rc.local中加入`/usr/local/tomcat/bin/startup.sh`

* docker开机自动启动
`systemctl  enable docker.service`

* 使用iptables管理防火墙
	`vi /etc/sysconfig/iptables`配置端口
	`service iptables restart`重启防火墙
	`systemctl restart iptables.service`重启防火墙
	`/etc/init.d/iptables restart`重启防火墙
	`systemctl enable iptables.service`设置防火墙卡机启动


