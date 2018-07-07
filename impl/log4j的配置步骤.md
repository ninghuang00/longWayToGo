#### log4j的配置步骤

* web.xml中的配置
```
  <!--配置log4j-->
  <context-param>
    <param-name>log4jConfigLocation</param-name>
    <param-value>/WEB-INF/log4j.properties</param-value>
  </context-param>
 ```

* 代码中的使用
```
    private static final String LOG_NAME = "hn_log";
    private static final String LOG_FORMATTER = "%s : %s.%s - %s";
    private static final String LOG_TAG = "hnexia";
    Logger logger = Logger.getLogger(LOG_NAME);
    ...

    String result = ret.toString();
    String location = "in logAspect =======================================================================================================";
    logger.info(String.format(LOG_FORMATTER, location + LOG_TAG, className, methodName, result));

```

* log4j.properties
```
### set log levels ###
log4j.rootLogger = debug,stdout,D,I,E

# 控制台输出的配置，所有日志输出，都会显示在控制台
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss,SSS} method:%l%n%m%n

# DEBUG级别及以上级别的日志，会写到F://logs/log.log文件中，文件不存在的时候会自动创建
#log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
#log4j.appender.D.File = /usr/local/tomcat/webapps/art-education-o2o/logs/debug.log
#log4j.appender.D.Append = true
#log4j.appender.D.Threshold = DEBUG
#log4j.appender.D.layout = org.apache.log4j.PatternLayout
#log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss} [%p] %m%n

# INFO，会写到F://logs/log.log文件中，文件不存在的时候会自动创建
log4j.appender.I = org.apache.log4j.DailyRollingFileAppender
log4j.appender.I.File = /usr/local/tomcat/webapps/art-education-o2o/logs/info.log
log4j.appender.I.Append = true
log4j.appender.I.Threshold = INFO
log4j.appender.I.layout = org.apache.log4j.PatternLayout
log4j.appender.I.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss} [%p] %m%n

# ERROR级别及以上级别的日志，会写到F://logs/error.log文件中，文件不存在的时候会自动创建
log4j.appender.E = org.apache.log4j.DailyRollingFileAppender
log4j.appender.E.File =/usr/local/tomcat/webapps/art-education-o2o/logs/error.log
log4j.appender.E.Append = true
log4j.appender.E.Threshold = ERROR
log4j.appender.E.layout = org.apache.log4j.PatternLayout
log4j.appender.E.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss} [%p] %m%n

# 指定com.neusoft包下的所有类的等级为DEBUG。可以把com.neusoft改为自己项目所用的包名。
log4j.logger.com.neusoft =DEBUG

```

