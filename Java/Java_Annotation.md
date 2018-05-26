##基本内置注解

###@Override 步骤

@Override 用在方法上，表示这个方法重写了父类的方法，如toString()。

###@Deprecated 步骤 

@Deprecated 表示这个方法已经过期，不建议开发者使用。(暗示在将来某个不确定的版本，就有可能会取消掉)

###@SuppressWarnings 

@SuppressWarnings Suppress英文的意思是抑制的意思，这个注解的用处是忽略警告信息。

###@SafeVarargs 

不懂

###@FunctionalInterface

@FunctionalInterface这是Java1.8 新增的注解，用于约定函数式接口，主要是配合Lambda 表达式 来使用。

如果一个接口有多于一个的函数，则不能被注解为函数式接口

## 自定义注解

怎么自定义注解以及如何解析这些自定义注解

### 自定义注解

```java
// 元注解
@Target({METHOD,TYPE}) // 表示这个注解可以用用在类/接口上，还可以用在方法上
@Retention(RetentionPolicy.RUNTIME) // 表示这是一个运行时注解，即运行起来之后，才获取注解中的相关信息，而不像基本注解如@Override 那种不用运行，在编译时eclipse就可以进行相关工作的编译时注解。
@Inherited // 表示这个注解可以被子类继承
@Documented // 表示当执行javadoc的时候，本注解会生成相关文档
// 创建注解类型的时候即不使用class也不使用interface,而是使用@interface
public @interface JDBCConfig {
  // 注解元素，这些注解元素就用于存放注解信息，在解析的时候获取出来   
  String ip();
  int port() default 3306;
  String database();
  String encoding();
  String loginName();
  String password();
}
```

### 如何解析

```java
@JDBCConfig(ip = "127.0.0.1", database = "test", encoding = "UTF-8", loginName = "root", password = "admin")
public class DBUtil {
  static {
    try {
      Class.forName("com.mysql.jdbc.Driver");
    } catch (ClassNotFoundException e) {
      e.printStackTrace();
    }
  }
 
  public static Connection getConnection() throws SQLException, NoSuchMethodException, SecurityException {
	// 通过反射，获取这个DBUtil这个类上的注解对象
    JDBCConfig config = DBUtil.class.getAnnotation(JDBCConfig.class);
	// 拿到注解对象之后，通过其方法，获取各个注解元素的值
    String ip = config.ip();
    int port = config.port();
    String database = config.database();
    String encoding = config.encoding();
    String loginName = config.loginName();
    String password = config.password();

    String url = String.format("jdbc:mysql://%s:%d/%s?characterEncoding=%s", ip, port, database, encoding);
    return DriverManager.getConnection(url, loginName, password);
  }
}
```

## 元注解

元注解 meta annotation：用于注解 自定义注解 的注解

@Target 表示这个注解能放在什么位置上，是只能放在类上？还是即可以放在方法上，又可以放在属性上。

@Retention 表示生命周期，自定义注解@JDBCConfig 上的值是 RetentionPolicy.RUNTIME, 表示可以在运行的时候依然可以使用。

1. RetentionPolicy.SOURCE： 注解只在源代码中存在，编译成class之后，就没了。@Override 就是这种注解。 

 	2. RetentionPolicy.CLASS： 注解在java文件编程成.class文件后，依然存在，但是运行起来后就没了
	3. @Retention的默认值，即当没有显式指定@Retention的时候，就会是这种类型。 RetentionPolicy.RUNTIME： 注解在运行起来之后依然存在，程序可以通过反射获取这些信息，自定义注解@JDBCConfig 就是这样。

@Inherited 表示该注解具有继承性。

@Documented 如图所示， 在用javadoc命令生成API文档后，DBUtil的文档里会出现该注解说明。

@Repeatable (java1.8 新增) 当没有@Repeatable修饰的时候，注解在同一个位置，只能出现一次。使用@Repeatable之后，再配合一些其他动作，就可以在同一个地方使用多次了。

## 仿HIBERNATE注解

1. 自定义注解
2. 应用在pojo对象上
3. 解析注解

## 注解分类

1. 按照作用域分
   1. RetentionPolicy.SOURCE： Java源文件上的注解
   2. RetentionPolicy.CLASS： Class类文件上的注解
   3. RetentionPolicy.RUNTIME： 运行时的注解
2. 按照来源分
   1. 内置注解
   2. 第三方注解
   3. 程序员自定义注解

