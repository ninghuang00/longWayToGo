#### tomcat配置笔记

* 开启远程调试
	1. centos环境,在startup.sh首行输入
	```
	declare -x CATALINA_OPTS="-server -Xdebug -Xnoagent -Djava.compiler=NONE -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=5005"
	```
	2. 在Ubuntu环境中,startup.sh首行输入
	```
	CATALINA_OPTS="-server -Xdebug -Xnoagent -Djava.compiler=NONE -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=5005"
	```

