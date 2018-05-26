```java
public class TestLog4j {
  // 基于类的名称获取日志对象
  static Logger logger = Logger.getLogger(TestLog4j.class);
  public static void main(String[] args) throws InterruptedException {
    // 与 Log4j入门 中的BasicConfigurator.configure();方式不同，采用指定配置文件
    PropertyConfigurator.configure("e:\\project\\log4j\\src\\log4j.properties");
    for (int i = 0; i < 5000; i++) {
      logger.trace("跟踪信息");
      logger.debug("调试信息");
      logger.info("输出信息");
      logger.warn("警告信息");
      logger.error("错误信息");
      logger.fatal("致命信息");
    }
  }
}
```



log4j.properties 内容

```properties
# 设置日志输出的等级为debug,低于debug就不会输出了
# 设置日志输出到两种地方，分别叫做 stdout 和 R
log4j.rootLogger=debug, stdout, R
# 第一个地方stdout, 输出到控制台
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
 
# Pattern to output the caller's file name and line number.
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] (%F:%L) - %m%n

# 第二个地方R, 以滚动的方式输出到文件，文件名是example.log,文件最大100k, 最多滚动5个文件
log4j.appender.R=org.apache.log4j.RollingFileAppender
log4j.appender.R.File=example.log
log4j.appender.R.MaxFileSize=100KB
log4j.appender.R.MaxBackupIndex=5
 
log4j.appender.R.layout=org.apache.log4j.PatternLayout
log4j.appender.R.layout.ConversionPattern=%p %t %c - %m%n
```

输出格式

log4j日志输出格式一览：
%c 输出日志信息所属的类的全名
%d 输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，比如：%d{yyy-MM-dd HH:mm:ss }，输出类似：2002-10-18- 22：10：28
%f 输出日志信息所属的类的类名
%l 输出日志事件的发生位置，即输出日志信息的语句处于它所在的类的第几行
%m 输出代码中指定的信息，如log(message)中的message
%n 输出一个回车换行符，Windows平台为“rn”，Unix平台为“n”
%p 输出优先级，即DEBUG，INFO，WARN，ERROR，FATAL。如果是调用debug()输出的，则为DEBUG，依此类推
%r 输出自应用启动到输出该日志信息所耗费的毫秒数
%t 输出产生该日志事件的线程名