# JDBC

[TOC]



## 准备操作

###导入jar包

###初始化驱动

```java
//初始化驱动
try {
  //驱动类com.mysql.jdbc.Driver
  //就在 mysql-connector-java-5.0.8-bin.jar中
  //如果忘记了第一个步骤的导包，就会抛出ClassNotFoundException
  Class.forName("com.mysql.jdbc.Driver");
  System.out.println("数据库驱动加载成功 ！");
  
} catch (ClassNotFoundException e) {
  // TODO Auto-generated catch block
  e.printStackTrace();
}
```

### 建立与数据库的连接

```java
Connection c = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/how2java?characterEncoding=UTF-8&autoReconnect=true&useSSL=false&serverTimezone=UTC ",
                        "root", "password")
```

### 创建Statement

```java
// 注意：使用的是 java.sql.Statement
// 不要不小心使用到： com.mysql.jdbc.Statement;
Statement s = c.createStatement();
```

### 执行SQL语句

####增删改

```sql
-- 插入表
insert into hero values (null, '盖伦', 616, 100)

-- 删除数据
delete from hero where id = 1

-- 修改数据
update hero set hp = 818 where id = 1
```

####查

```sql
-- 查询数据
select * from hero
select count(*) from hero
-- 分页显示
select * from hero limit 0,5
```

### 关闭连接

```java
// 数据库的连接时有限资源，相关操作结束后，养成关闭数据库的好习惯
// 先关闭Statement
if (s != null)
  try {
    s.close();
  } catch (SQLException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
  }
// 后关闭Connection
if (c != null)
  try {
    c.close();
  } catch (SQLException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
  }
```

### 使用try-with-resource的方式自动关闭连接

```java
try {
  Class.forName("com.mysql.jdbc.Driver");
} catch (ClassNotFoundException e) {
  e.printStackTrace();
}

try (
  Connection c = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/how2java?characterEncoding=UTF-8","root", "admin");
  Statement s = c.createStatement();             
)
{
  // 糟糕的让人眼花缭乱的字符串拼接方式
  String sql = "insert into hero values(null," + "'提莫'" + "," + 313.0f + "," + 50 + ")";
  s.execute(sql);

} catch (SQLException e) {
  // TODO Auto-generated catch block
  e.printStackTrace();
}
```

## 预编译

和 Statement一样，PreparedStatement也是用来执行sql语句的；与创建Statement不同的是，需要根据sql语句创建PreparedStatement

Statement 需要进行字符串拼接，可读性和维护性比较差；PreparedStatement 使用参数设置，可读性好，不易犯错，避免SQL注入式攻击

PreparedStatement有预编译机制，性能比Statement更快

```java
String sql = "select * from limit ?, ?";
PreparedStatement ps = c.prepareStatement(sql);
...
ps.setInt(inx, y);
ps.setInt(inx, x);
...
ps.executeQuery(); // 此时不能是ps.executeQuery(sql);
```

显然，PreparedStatement比Statement要好用啊

## executeUpdate VS execute

### 相同点

execute与executeUpdate的相同点：都可以执行增加，删除，修改

### 不同点

1. execute可以执行查询语句，然后通过getResultSet，把结果集取出来（`boolean b = ps.execute(); ResultSet rs = ps.getResultSet();`， 这明显要比`ResultSet rs = ps.excuteQuery();` 麻烦一些，但至少可行）；executeUpdate不能执行查询语句
2. execute返回boolean类型，true表示执行的是查询语句，false表示执行的是insert,delete,update等等；executeUpdate返回的是int，表示有多少条数据受到了影响

###Tip

查询使用excuteQuery比较好，增删改使用executeUpdate比较好



## 特殊操作

### 获取自增长id

```java
// 确保会返回自增长ID
PreparedStatement ps = c.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);
...
// 执行完SQL的插入操作后，MySQL会为新插入的数据分配一个自增长id
// JDBC通过getGeneratedKeys获取该id
ResultSet rs = ps.getGeneratedKeys();
if (rs.next()) {
  int id = rs.getInt(1);
  System.out.println(id);
}
```

### 获取表的元数据

....

## JDBC事务

**MYSQL 表的类型必须是INNODB才支持事务**

数据库事务必须具备ACID特性，ACID是Atomic（原子性）、Consistency（一致性）、Isolation（隔离性）和Durability（持久性）的英文缩写。

数据库管理系统采**用日志来保证事务的原子性、一致性和持久性**。日志记录了事务对数据库所做的更新，如果某个事务在执行过程中发生错误，就可以根据日志，撤销事务对数据库已做的更新，使数据库退回到执行事务前的初始状态。

###使用事务

通过 `c.setAutoCommit(false);` 关闭自动提交
使用 `c.commit(); ` 进行手动提交

在事务中的多个操作，要么都成功，要么都失败；如果在setAutoCommit(false)和commit()之间执行了失败操作，则相当于多个操作都失败。

### 回滚

遇到失败操作应该回滚的

回滚应该在catch里进行

```java
// Can''t call rollback when autocommit=true
c.setAutoCommit(false);
// 有事务的前提下
// 在事务中的多个操作，要么都成功，要么都失败
try{
  // 加血的SQL
  String sql1 = "update hero set hp = hp +1 where id = 22";
  s.execute(sql1);

  // 减血的SQL
  // 不小心写错写成了 updata(而非update)

  String sql2 = "updata hero set hp = hp -1 where id = 22";
  s.execute(sql2);
} catch (SQLException ex) {
  c.rollback();
}
// 手动提交
c.commit();
```

**当前来看，使用回滚显得有些多余，但是java规范里要求要回滚**

### JDBC事务的优缺点

JDBC为使用Java进行数据库的事务操作提供了最基本的支持。通过JDBC事务，我们可以将多个SQL语句放到同一个事务中，保证其ACID特性。JDBC事务的主要优点就是**API比较简单**，可以实现**最基本的事务操作**，**性能**也相对较好。

但是，JDBC事务有一个局限：**一个 JDBC 事务不能跨越多个数据库！！！**所以，如果涉及到多数据库的操作或者分布式场景，JDBC事务就无能为力了。

## [JTA](http://www.hollischuang.com/archives/1658)



## ORM

ORM=Object Relationship Database Mapping 

对象和关系数据库的映射 

简单说，**一个对象，对应数据库里的一条记录**

**增删改查以函数的形式展现**

## DAO

DAO=DataAccess Object 

数据库访问对象 

实际上就是运用了[练习-ORM]中的思路，把数据库相关的操作都封装在这个类里面，其他地方看不到JDBC的代码

**对数据库表的操作封装进一个类中**



## 数据库连接池

###数据库连接池原理-传统方式

当有多个线程，每个线程都需要连接数据库执行SQL语句的话，那么每个线程都会创建一个连接，并且在使用完毕后，关闭连接。

创建连接和关闭连接的过程也是比较消耗时间的，当多线程并发的时候，系统就会变得很卡顿。

同时，一个数据库同时支持的连接总数也是有限的，如果多线程并发量很大，那么数据库连接的总数就会被消耗光，后续线程发起的数据库连接就会失败。

###数据库连接池原理-使用池

1. ConnectionPool() 构造方法约定了这个连接池一共有多少连接
2. 在init() 初始化方法中，创建了size条连接。 注意，这里不能使用try-with-resource这种自动关闭连接的方式，因为连接恰恰需要保持不关闭状态，供后续循环使用
3. getConnection， 判断是否为空，如果是空的就wait等待，否则就借用一条连接出去
4. returnConnection， 在使用完毕后，归还这个连接到连接池，并且在归还完毕后，调用notifyAll，通知那些等待的线程，有新的连接可以借用了。

注：连接池设计用到了多线程的wait和notifyAll



## 总结

1. PreparedStatement比Statement要好用
2. 查询使用excuteQuery比较好，增删改使用executeUpdate比较好
3. 使用事务和回滚避免出现SQL执行失败的情况
4. 使用ORM，DAO来封装对数据库的操作
5. 使用数据库连接池来节约启动和关闭连接的时间