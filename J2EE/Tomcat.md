## 改端口

修改server.xml，将8080替换为别的端口号。

## 端口被占用了怎么办？

### 查看80端口被哪些程序占用了

```powershell
netstat -ano|findstr "80"
```

### 根据pid（进程id） 查询对应的应用程序

```powershell
tasklist|findstr "1828"
```

### 根据名称 结束该程序

```powershell
taskkill /f /t /im java.exe
```

## Tomcat 问题排查

### JAVA_HOME

现象：点击startup.bat之后，屏幕一闪而过
检验：如图所示, 首先通过cmd命令进入控制台，然后切换到对应的目录执行startup命令，得到JRE_HOME environment .... 这么个提示，就表示JAVA_HOME环境变量没有设置。
分析：**Tomcat本身是JAVA程序**，必须要有JDK才可以执行，所以必须配置JAVA_HOME。

### CATALINA_HOME未设置

现象：点击startup.bat之后，屏幕一闪而过
检验：如图所示, 首先通过cmd命令进入控制台，然后切换到对应的目录执行startup命令，得到CATALINA_HOME environment .... 这么个提示，就表示CATALINA_HOME环境变量设置错误。
分析：Tomcat执行必须依赖CATALINA_HOME或者CATALINA_BASE这两个环境变量。 如果没有在环境变量里配置过，那么会自动采用bin目录的父目录作为CATALINA_HOME和CATALINA_BASE。 如果配置了，而所配置的地方又不是正确的TOMCAT目录，那么就会出现这个错误。
解决：

1. 在环境变量中删除CATALINA_HOME,CATALINA_BASE的配置，记得不仅要检查环境变量，还要检查用户变量。
2. 或者把CATALINA_HOME设置为正确的TOMCAT目录。

###localhost.yyyy-mm-dd.log

当server.xml， web.xml配置错误的时候，当前web应用就会部署失败，并且会将错误信息输出到localhost.yyyy-mm-dd.log文件中。根据实际情况分析。

### JDK版本

现象：404错误，明明有文件，但是就是不能访问。
检验：在命令行中运行java -version 检查一下当前java的版本
分析：当部署的web应用中的类是由高版本JDK编译生成，而当前tomcat运行所使用的JDK又是低版本的话，就会报出如图所示的错误：**UnsupportedClassVersionError**，进而导致web应用启动失败。

## 404问题

404表示File Not Found， 文件不存在错误。

### webapps下的ROOT目录

有的时候，在server.xml 中配置的<context 是以“/"为开始路径的，与此同时，在webapps目录下还存在一个ROOT目录，这个ROOT目录其实也是告诉tomcat，以“/” 为开始路径，那么这样就发生冲突了，Tomcat只能二选一，通常情况下都会选择ROOT目录，那么在server.xml中配置的就<context 就无法启动了，导致访问对应的资源提示404错误。
解决办法就是**删除掉ROOT目录**，或者**把ROOT目录重命名**，或者**给定server.xml中配置的<context 的path值不为“/”**



##Tomcat 组成

![img](https://dn-mhke0kuv.qbox.me/9ccc3ed9de0df39faa1e.jpeg?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

![img](https://pic002.cnblogs.com/images/2012/322938/2012090217005339.png)



##Container组成

![img](https://dn-mhke0kuv.qbox.me/1a2613edf5779c7bf184.jpeg?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

##Tomcat 的启动过程

![img](https://dn-mhke0kuv.qbox.me/94989563f76b0c2b6b19.jpeg?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)





