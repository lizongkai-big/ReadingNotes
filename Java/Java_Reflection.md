#反射机制

##获取类对象

###什么是类对象

所有的类，都存在一个类对象，这个类对象用于提供类本身的信息，比如有几种构造方法， 有多少属性，有哪些普通方法。在一个JVM中，一种类，只会有一个类对象存在。

###获取类对象

获取类对象有3种方式

1. Class.forName
2. Hero.class
3. new Hero().getClass()

以上三种方式取出来的类对象，都是一样的。

获取类对象的时候，会导致类属性被初始化

##创建对象

与传统的通过new 来获取对象的方式不同 
反射机制，会先拿到Hero的“类对象”,然后通过类对象获取“构造器对象” 
再通过构造器对象创建一个对象

```java
//使用反射的方式创建对象
String className = "charactor.Hero";
//类对象
Class pClass=Class.forName(className);
//构造器
Constructor c= pClass.getConstructor();
//通过构造器实例化；若是不需要访问熟悉，只调用方法，则可以不转型
Hero h2= (Hero) c.newInstance();
h2.name="gareen";
System.out.println(h2);
```

##访问属性

通过反射机制修改对象的属性

```java
Hero h =new Hero();
//使用传统方式修改name的值为garen
h.name = "garen";
try {
  //获取类Hero的名字叫做name的字段
  Field f1= h.getClass().getDeclaredField("name");
  //修改这个字段的值
  f1.set(h, "teemo");
  //打印被修改后的值
  System.out.println(h.name);
  // private 属性
  Field f2= h.getClass().getDeclaredField("adugen_c");
  //修改这个字段的值
  f2.setAccessible(true);
  f2.set(h, 222);
  int t = (int)f2.get(h);
  System.out.println(t);

} catch (Exception e) {
  // TODO Auto-generated catch block
  e.printStackTrace();
}
```

###getField和getDeclaredField的区别

这两个方法都是用于获取字段
getField 只能获取public的，包括从父类继承来的字段。
getDeclaredField 可以获取本类所有的字段，包括private的，但是不能获取继承来的字段。 (注： 这里只能获取到private的字段，但并不能访问该 private 字段的值，除非加上setAccessible(true))

##调用方法

通过反射机制，调用一个对象的方法

```java
// 获取这个名字叫做setName，参数类型是String的方法
Method m = h.getClass().getMethod("setName", String.class);
// 对h对象，调用这个方法；第一个参数是m方法的调用者，之后的参数是m方法的参数
m.invoke(h, "盖伦");
// 使用传统的方式，调用getName方法
System.out.println(h.getName());
```

## 有什么用

两个类中，有方法参数相同。

使用**非反射方式**，在执行的时候必须修改代码，并且重新编译运行，才可以达到效果；

使用**反射方式**，首先准备一个配置文件，就叫做spring.txt吧, 放在src目录下。 里面存放的是类的名称，和要调用的方法名。

当需要从调用第一个业务方法，切换到调用第二个业务方法的时候，不需要修改一行代码，也不需要重新编译，只需要**修改配置文件**spring.txt，再运行即可。

```java
//从spring.txt中获取类名称和方法名称
File springConfigFile = new File("e:\\project\\j2se\\src\\spring.txt");
Properties springConfig= new Properties();
springConfig.load(new FileInputStream(springConfigFile));
String className = (String) springConfig.get("class");
String methodName = (String) springConfig.get("method");

//根据类名称获取类对象
Class clazz = Class.forName(className);
//根据方法名称，获取方法对象
Method m = clazz.getMethod(methodName);
//获取构造器
Constructor c = clazz.getConstructor();
//根据构造器，实例化出对象；不需要转型
Object service = c.newInstance();
//调用对象的指定方法
m.invoke(service);
```

