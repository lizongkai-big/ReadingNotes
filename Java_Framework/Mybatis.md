## mybatis-config.xml

```xml
<!--别名，自动扫描com.how2java.pojo下的类型，使得在后续配置文件Category.xml中使用resultType的时候，可以不用输入类的全路径，可以直接使用Category, 而不必写全com.how2java.pojo.Category-->
<typeAliases>
  <package name="com.how2java.pojo"/>
</typeAliases>

<!--提供连接数据库用的驱动，数据库名称，编码方式，账号密码-->
<environments default="development">
  <environment id="development">
    <transactionManager type="JDBC"/>
    <dataSource type="POOLED">
      <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
      <property name="url" value="jdbc:mysql://localhost:3306/how2java?characterEncoding=UTF-8&amp;autoReconnect=true&amp;useSSL=false&amp;serverTimezone=UTC"/>
      <property name="username" value="root"/>
      <property name="password" value="password"/>
    </dataSource>
  </environment>
</environments>
<!--映射sql-mapper.xml 帮助找到SQL语句-->
<mappers>
  <mapper resource="com/how2java/pojo/sql-mapper.xml"/>
</mappers>
```



## Mapper XML 文件

SQL 映射文件有很少的几个**顶级元素**（按照它们应该被定义的顺序）：

- `cache` – 给定命名空间的缓存配置。
- `cache-ref` – 其他命名空间缓存配置的引用。
- `resultMap` – 是最复杂也是最强大的元素，用来描述如何从数据库结果集中来加载对象。
- `parameterMap` – 已废弃！老式风格的参数映射。内联参数是首选,这个元素可能在将来被移除，这里不会记录。
- `sql` – 可被其他语句引用的可重用语句块。
- `insert` – 映射插入语句
- `update` – 映射更新语句
- `delete` – 映射删除语句
- `select` – 映射查询语句

###ResultMap

`resultMap` – 是最复杂也是最强大的元素，用来描述如何从数据库结果集中来加载对象。

ResultMap 的设计思想是，简单的语句不需要明确的结果映射，而复杂一点的语句只需要描述它们的关系就行了。

它可以让你从 90% 的 JDBC `ResultSets` 数据提取代码中解放出来, 并在一些情形下允许你做一些 JDBC 不支持的事情。 实际上，在对复杂语句进行联合映射的时候，它很可能可以代替数千行的同等功能的代码。 

使用 resultMap 或 resultType，但不能同时使用。

#### 子元素和结构

- `constructor`  - 用于在实例化类时，注入结果到构造方法中
  - `idArg` - ID 参数;标记出作为 ID 的结果可以帮助提高整体性能
  - `arg` - 将被注入到构造方法的一个普通结果
- `id` – 一个 ID 结果;标记出作为 ID 的结果可以帮助提高整体性能
- `result` – 注入到字段或 JavaBean 属性的普通结果
- `association` – 一个复杂类型的关联;许多结果将包装成这种类型
  - 嵌套结果映射 – 关联可以指定为一个 `resultMap` 元素，或者引用一个
- `collection`  – 一个复杂类型的集合
  - 嵌套结果映射 – 集合可以指定为一个 `resultMap` 元素，或者引用一个
- `discriminator` – 使用结果值来决定使用哪个 `resultMap`
  - `case` – 基于某些值的结果映射
    - 嵌套结果映射 – 一个 `case` 也是一个映射它本身的结果,因此可以包含很多相 同的元素，或者它可以参照一个外部的 `resultMap`。

为什么要关注回撤 / 止损？

1. 你买一支股票，肯定是判断它**涨**的啊，可是现在它跌了，是不是之前判断的不对？有没有足够的理由说明它只是暂时性的下跌？
2. 不卖掉的原因。
   1. 它可能是暂时性的下跌。之后会上涨的，你判断是什么时候会涨？需要多久？这段时间里，是不是应该去寻找别的好机会？
   2. 现在卖出，损失的钱能从别的股票里获得吗？不管能不能获得，先把损失减小再说？

为什么要止盈？

最怕你自己不懂 + 大行情好，使你不知道自己无知；

也怕你自己不懂 + 大行情不好，因为亏钱啊，可是也长记性 + 涨知识；

看准再去投资！不要急于下手，以免骑虎难下。多观察。

查下其他几个银行/证券下跌的情况，与中行、方正相比



#### id & result

id 和 result 都将一个列的值映射到一个简单数据类型(字符串,整型,双精度浮点数,日期等)的属性或字段。

这两者之间的唯一不同是， id 表示的结果将是对象的标识属性，这会在比较对象实例时用到。 这样可以提高整体的性能，尤其是缓存和嵌套结果映射(也就是联合映射)的时候。

#### 关联

关联元素处理“有一个 / 多对一”类型的关系。

关联中不同的是你需要告诉 MyBatis 如何加载关联。MyBatis 在这方面会有两种不同的 方式:

- 嵌套查询:通过执行另外一个 SQL 映射语句来返回预期的复杂类型。
- 嵌套结果:使用嵌套结果映射来处理重复的联合结果的子集。首先,然让我们来查看这个元素的属性。所有的你都会看到,它和普通的只由 select 和resultMap 属性的结果映射不同。**常用** 下面的 “多对一” 一节就是用的嵌套结果

#### 集合

处理“一对多”的关系

#### 鉴别器

有时一个单独的数据库查询也许返回很多不同 (但是希望有些关联) 数据类型的结果集。 鉴别器元素就是被设计来处理这个情况的, 还有包括类的继承层次结构。 鉴别器非常容易理 解,因为它的表现很**像 Java 语言中的 switch 语句。**

定义鉴别器指定了 column 和 javaType 属性。 列是 MyBatis 查找比较值的地方。 JavaType 是需要被用来保证等价测试的合适类型(尽管字符串在很多情形下都会有用)。

#### 自动映射

当自动映射查询结果时，MyBatis会获取sql返回的列名并在java类中查找相同名字的属性（忽略大小写）。 这意味着如果Mybatis发现了*ID*列和*id*属性，Mybatis会将*ID*的值赋给*id*。

通常数据库列使用大写单词命名，单词间用下划线分隔；而java属性一般遵循驼峰命名法。 为了在这两种命名方式之间启用自动映射，需要将 `mapUnderscoreToCamelCase`设置为true。



#### 解决列名不匹配

```xml
<select id="selectUsers" resultType="User">
  select
    user_id             as "id",
    user_name           as "userName",
    hashed_password     as "hashedPassword"
  from some_table
  where id = #{id}
</select>

<resultMap id="userResultMap" type="User">
  <id property="id" column="user_id" />
  <result property="username" column="user_name"/>
  <result property="password" column="hashed_password"/>
</resultMap>
```





## 

## CRUD 语句

配置文件 sql-mapper.xml

```xml
<select id="listCategory" resultType="Category">
  select * from   category_
</select>

<insert id="addCategory" parameterType="Category" >
  insert into category_ ( name ) values (#{name})
</insert>

<delete id="deleteCategory" parameterType="Category" >
  delete from category_ where id= #{id}
</delete>

<select id="getCategory" parameterType="_int" resultType="Category">
  select * from   category_  where id= #{id}
</select>

<update id="updateCategory" parameterType="Category" >
  update category_ set name=#{name} where id=#{id}
</update>
```

java

```java
// 增
Category c = new Category();
c.setName("category2");
session.insert("addCategory",c);

// 删
session.delete("deleteCategory", 2);

// 改
session.update("updateCategory", new Category(4, "category22"));

// 查（单一结果）
Category c = session.selectOne("getCategory", 1);
System.out.println(c.getName());

// 查（多个结果）
List<Category> cs = session.selectList("listCategory");
for (Category t : cs) {
  System.out.println(t.getName());
}
```

## 更多查询

配置文件 sql-mapper.xml

```xml
<select id="listCategoryByName" resultType="Category">
  select * from category_ where name like concat('%', #{0}, '%')
</select>

<select id="listCategoryByIdAndName"  parameterType="map" resultType="Category">
  select * from   category_  where id> #{id}  and name like concat('%',#{name},'%')
</select>
```

java

当有多个参数时，又不能放入一个类中，可以使用map来封装。

```java
Map<String, Object> params = new HashMap<>();
params.put("id", 3);
params.put("name", "c");

List<Category> cs = session.selectList("listCategoryByIdAndName", params);

for (Category t : cs) {
  System.out.println(t.getName());
}
```

## 一对多

```xml
<resultMap type="Category" id="categoryBean">
  <id column="cid" property="id" />
  <result column="cname" property="name" />

  <!-- 一对多的关系 -->
  <!-- property: 指的是集合属性的值, ofType：指的是集合中元素的类型 -->
  <collection property="products" ofType="Product">
    <id column="pid" property="id" />
    <result column="pname" property="name" />
    <result column="price" property="price" />
  </collection>
</resultMap>

<!-- 关联查询分类和产品表 -->
<select id="listCategory" resultMap="categoryBean">
  select c.*, p.*, c.id 'cid', p.id 'pid', c.name 'cname', p.name 'pname' from category_ c left join product_ p on c.id = p.cid
</select> 
```

## 多对一

```xml
<resultMap type="Product" id="productBean">
  <id column="pid" property="id" />
  <result column="pname" property="name" />
  <result column="price" property="price" />

  <!-- 多对一的关系 -->
  <!-- property: 指的是属性名称, javaType：指的是属性的类型 -->
  <association property="category" javaType="Category">
    <id column="cid" property="id"/>
    <result column="cname" property="name"/>
  </association>
</resultMap>

<!-- 根据id查询Product, 关联将Orders查询出来 -->
<select id="listProduct" resultMap="productBean">
  select c.*, p.*, c.id 'cid', p.id 'pid', c.name 'cname', p.name 'pname' from category_ c left join product_ p on c.id = p.cid
</select>  
```

# 动态SQL

## if

```xml
<select id="listProduct" resultType="Product">
  select * from product_
  <if test="name!=null">
    where name like concat('%',#{name},'%')
  </if>
</select>
```

如果没有传参数name,那么就查询所有，如果有name参数，那么就进行模糊查询。

这样只需要定义一条sql语句即可应付多种情况了，在测试的时候，也只需要调用这么一条sql语句listProduct 即可。

## where & set & trim

###where

```xml
<select id="listProduct" resultType="Product">
  select * from product_
  <where>
    <if test="name!=null">
      and name like concat('%',#{name},'%')
    </if>		 	
    <if test="price!=null and price!=0">
      and price > #{price}
    </if>	
  </where>	 	
</select>
```

<where>标签会进行自动判断

如果任何条件都不成立，那么就在sql语句里就不会出现where关键字

如果有任何条件成立，<u>会自动去掉多出来的 and 或者 or</u>。所以 if 里得写上and 或者 or

假设没有<where></where> ，仅凭if标签，在多条件查询时会很麻烦。

### set

与[where标签](http://how2j.cn/k/mybatis/mybatis-where/1114.html#step4271)类似的，在update语句里也会碰到多个字段相关的问题。 在这种情况下，就可以使用set标签：

```xml
<set>
  <if test="name != null">name=#{name},</if>
  <if test="price != null">price=#{price}</if>
</set>
```

其效果与where标签类似，有数据的时候才进行设置，<u>会自动去掉多出来的逗号</u>。同样，if 中得写上', '

### trim

trim 用来定制想要的功能，比如where标签等价于

```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ... 
</trim>
```

set标签等价于

```xml
<trim prefix="set" suffixOverrides=",">
</trim>
```



## choose

Mybatis里面没有else标签，但是可以使用when otherwise标签来达到这样的效果。（写Mybatis的人之前肯定是写jsp哒！或者吸取了jsp的经验~）

```xml
<select id="listProduct" resultType="Product">
  SELECT * FROM product_ 
  <where>
    <choose>
      <when test="name != null">
        and name like concat('%',#{name},'%')
      </when>			  
      <when test="price !=null and price != 0">
        and price > #{price}
      </when>			  		
      <otherwise>
        and id >1
      </otherwise>
    </choose>
  </where>
</select>
```

其作用是： 按照顺序，提供了任何条件，就进行条件查询，否则就使用id>1这个条件。

## foreach

```xml
SELECT * FROM product_
where id IN
<!--属性都得有。其中open, close, sepatator是用于拼接#{item}的； index是下标；collection属性的值有三个分别是list、array、map三种；item是collection中的每一项-->
<foreach item="item" index="index" collection="collection" open="(" separator="," close=")">
  #{item}
</foreach>
```

正常得出的SQL是 `select * from product_ where id in (1, 2, 3);`

我们通常可以将之用到批量删除、添加等操作中

## bind

bind标签就像是再做一次字符串拼接，方便后续使用

```xml
<select id="listProduct" resultType="Product">
  <bind name="likename" value="'%' + name + '%'" />
  select * from   product_  where name like #{likename}
</select>
```

# [Java API](http://www.mybatis.org/mybatis-3/zh/java-api.html)

## SqlSessions

SqlSessionFactoryBuilder --> SqlSessionFactory --> SqlSession

使用 MyBatis 的主要 Java 接口就是 SqlSession。你可以通过这个接口来执行命令，获取映射器和管理事务。我们会概括讨论一下 SqlSession 本身，但是首先我们还是要了解如何获取一个 SqlSession 实例。SqlSessions 是由 SqlSessionFactory 实例创建的。SqlSessionFactory 对象包含创建 SqlSession 实例的所有方法。而 SqlSessionFactory 本身是由 SqlSessionFactoryBuilder 创建的，它可以从 XML、注解或手动配置 Java 代码来创建 SqlSessionFactory。

`Note`  当 Mybatis 与一些依赖注入框架（如 Spring 或者 Guice）同时使用时，SqlSessions 将被依赖注入框架所创建，所以你不需要使用 SqlSessionFactoryBuilder 或者 SqlSessionFactory，可以直接看 SqlSession 这一节。请参考 Mybatis-Spring 或者 Mybatis-Guice 手册了解更多信息。

#### SqlSessionFactoryBuilder

SqlSessionFactoryBuilder 有五个 build() 方法，每一种都允许你从不同的资源中创建一个 SqlSession 实例。

```java
SqlSessionFactory build(InputStream inputStream) // 最常用的
SqlSessionFactory build(InputStream inputStream, String environment)
SqlSessionFactory build(InputStream inputStream, Properties properties)
SqlSessionFactory build(InputStream inputStream, String env, Properties props)
SqlSessionFactory build(Configuration config)
```

属性可以从 mybatis-config.xml 中被引用，或者直接指定它。因此理解优先级是很重要的。

如果一个属性存在于这些位置，那么 MyBatis 将会按照下面的顺序来加载它们：

- 首先读取在 properties 元素体中指定的属性；
- 其次，读取从 properties 元素的类路径 resource 或 url 指定的属性，且会覆盖已经指定了的重复属性；
- 最后，读取作为方法参数传递的属性，且会覆盖已经从 properties 元素体和 resource 或 url 属性中加载了的重复属性。

因此，通过方法参数传递的属性的优先级最高，resource 或 url 指定的属性优先级中等，在 properties 元素体中指定的属性优先级最低。

以下给出一个从 mybatis-config.xml 文件创建 SqlSessionFactory 的示例：

```java
String resource = "./mybatis-config.xml";
// 从类路径下、文件系统或一个 web URL 中加载资源文件
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
SqlSession session = sqlSessionFactory.openSession();

```

#### SqlSessionFactory

SqlSessionFactory 有六个方法创建 SqlSession 实例。通常来说，当你选择这些方法时你需要考虑以下几点：

- **事务处理**：我需要在 session 使用事务或者使用自动提交功能（auto-commit）吗？（通常意味着很多数据库和/或 JDBC 驱动没有事务）
- **连接**：我需要依赖 MyBatis 获得来自数据源的配置吗？还是使用自己提供的配置？
- **执行语句**：我需要 MyBatis 复用预处理语句和/或批量更新语句（包括插入和删除）吗？

基于以上需求，有下列已重载的多个 openSession() 方法供使用。

```java
SqlSession openSession()
SqlSession openSession(boolean autoCommit); // true 值即可开启自动提交功能
SqlSession openSession(Connection connection); // 使用指定的 Connection 实例
SqlSession openSession(TransactionIsolationLevel level); // 
SqlSession openSession(ExecutorType execType,TransactionIsolationLevel level)
SqlSession openSession(ExecutorType execType)
SqlSession openSession(ExecutorType execType, boolean autoCommit)
SqlSession openSession(ExecutorType execType, Connection connection)
Configuration getConfiguration();
```

默认的 openSession()方法没有参数，它会创建有如下特性的 SqlSession：

- 会开启一个事务（也就是不自动提交）。
- 将从由当前环境配置的 DataSource 实例中获取 Connection 对象。
- 事务隔离级别将会使用驱动或数据源的默认设置。
- 预处理语句不会被复用，也不会批量处理更新。

#### SqlSession



# 注解

略写

## CRUD

把 XML方式的CRUD 修改为注解方式，对比配置文件 sql-config.xml，其实就是把SQL语句从XML挪到了注解上来

```java
public interface CategoryMapper {
  @Insert(" insert into category_ ( name ) values (#{name}) ") 
  public int add(Category category); 

  @Delete(" delete from category_ where id= #{id} ") 
  public void delete(int id); 

  @Select("select * from category_ where id= #{id} ") 
  public Category get(int id); 

  @Update("update category_ set name=#{name} where id=#{id} ") 
  public int update(Category category);  

  @Select(" select * from category_ ") 
  public List<Category> list(); 
}
```

这种注解好low啊

##注解方式的动态SQL

首先新建动态SQL类

然后在Mapper类中取消CRUD的注解方式，修改为注解引用CategoryDynaSqlProvider类的方式

比如：

```java
@Insert(" insert into category_ ( name ) values (#{name}) ")  
public int add(Category category);  
```

修改为：

```java
@InsertProvider(type=CategoryDynaSqlProvider.class,method="add")  
public int add(Category category);  
```

# 相关概念

## 开启LOG4J显示执行的SQL语句

放上lib后，居然只需要设置properties就OK了，震惊

原因在于：

Mybatis内置的日志工厂提供日志功能，具体的日志实现有以下几种方式：
SLF4J
Apache Commons Logging
Log4j 2
Log4j
JDK logging
具体选择哪个日志实现由MyBatis的内置日志工厂确定。它会使用最先找到的（按上文列举的顺序查找）。 如果一个都未找到，日志功能就会被禁用。

```properties
# Global logging configuration
log4j.rootLogger=ERROR, stdout
# MyBatis logging configuration...
log4j.logger.com.how2java=TRACE
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

## 事务管理

在Mysql中，只有当表的类型是INNODB的时候，才支持事务，所以需要把表category_的类型设置为INNODB,否则无法观察到事务.

在一个事务中，遇到错误则之前的执行操作也会撤销

## 延迟加载

延迟加载策略就是等系统需要集合属性时才从数据库装在关联的数据。通过延迟加载技术可以避免过多、过早地加载数据表中的数据，从而降低内存开销。

在mybatis-config.xml中加上如下配置即可

```xml
<settings> 
  <!-- 打开延迟加载的开关 --> 
  <setting name="lazyLoadingEnabled" value="true" /> 
  <!-- 将积极加载改为消息加载即按需加载 --> 
  <setting name="aggressiveLazyLoading" value="false"/> 
</settings>
```

##PageHelper

PageHelper是一款犀利的Mybatis分页插件，使用了这个插件之后，分页开发起来更加简单容易。

查询很有意思，只需要在执行查询所有的调用之前，执行一条语句即可：

`PageHelper.offsetPage(0, 5);`

这就表示查出第一页的数据，每页5条

## 一级缓存

Mybatis的一级缓存在session上，只要通过session查过的数据，都会放在session上，下一次再查询相同id的数据，都直接冲缓存中取出来，而不用到数据库里去取了。

## 二级缓存

Mybatis二级缓存是SessionFactory，如果两次查询基于同一个SessionFactory，那么就从二级缓存中取数据，而不用到数据库里去取了。

## 逆向工程

Mybatis Generator是一个用于Mybatis逆向工程的工具。**没错就是他**

用逆向工程的方式，首先保证数据库里有表，然后通过Mybatis Generator生成pojo, mapper和xml。 



