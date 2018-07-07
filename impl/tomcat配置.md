### 将idea web项目部署到远程tomcat
1. 配置tomcat/bin/catalina.sh文件
`
export CATALINA_OPTS="-Dcom.sun.management.jmxremote 
-Dcom.sun.management.jmxremote.port=1099 
-Dcom.sun.management.jmxremote.ssl=false 
-Dcom.sun.management.jmxremote.authenticate=false 
-Djava.rmi.server.hostname=10.82.82.248"

export JAVA_OPTS="-Dcom.sun.management.jmxremote=
-Dcom.sun.management.jmxremote.port=1099
-Dcom.sun.management.jmxremote.ssl=false
-Dcom.sun.management.jmxremote.authenticate=false"
`
2. 启动tomcat
`
./catalina.sh run > /dev/null 2>&1 &
`
其中“ > /dev/null 2>&1 &”是Linux中的命令：把标准输出和出错处理都放到回收站，这样就免得一大堆输出占领你的屏幕。
3. 在用jps命令：

4. javahome: /usr/lib/jvm/java-8-oracle

5. 设置tomcat开机启动,将startup.sh修改
`
export JAVA_HOME=/usr/lib/jvm/java-8-oracle  
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar:.  
export PATH=$JAVA_HOME/bin:$PATH  
export CATALINA_HOME=/usr/local/tomcat  
/usr/local/tomcat/bin/catalina.sh start
`

6. 在/etc/rc.d/rc.local中加入`/usr/local/tomcat/bin/startup.sh`

