1. 命名规则
   1. 类名（UpperCamelCase）：Hero
   2. 属性名、方法名（lowerCamelCase 风格）：name，getSpeed()

2. 数组：
   1. 数组的长度是不可变的，一旦分配好空间，是多长，就多长，不能增加也不能减少；如果需要变长的数组，可以用Collection.ArrayList
   2. 数组复制System.arraycopy(src, srcPos, dest, destPos, length)
   3. for(int value : array) 增强型for循环
   4. Arrays类，用于对数组的复制，查找，排序，填充，输出

3. Java 变量作用域

   1. Java的变量作用域一共有四种，分别是类级、对象实例级、方法级、块级。

   2. ```java
      public class demo
      {
        public static String name = "hello";//类级变量
        public int i;//对象级变量，默认为0
        static{
          int j = 1;//块级变量，只能在块内部访问
        }
        public void test()
        {
          int k = 2;//方法级变量，只能在该方法内使用
          System.out.println("i=" + i);
        }
        public static void main(String[] args)
        {
          System.out.println("name");//类级变量不需要实例化对象就可使用
          demo d = new demo();
      	for (int i = 0; i < 10; i ++) { // 方法级变量
            int j = 2;  // 块级变量
      	}
          d.test();
        }
      }
      ```

   3. ​

### 为什么要使用getter，setter方法？

**5-8还不理解**

1. <u>方便增加额外功能。如setter()中增加参数验证功能，getter()使得内部存储于外部表现不同</u> 
2. <u>可以保持外部接口不变的情况下，修改内部存储方式和逻辑。</u>
3. <u>提供一个debug接口。</u>
4. <u>允许继承者改变语义。</u>
5. 任意管理变量的生命周期和内存存储方式。
6. 能够和模拟对象、序列化乃至WPF库等融合。
7. 可以将getter、setter用于lambda表达式。（大概即作为一个函数，参与函数传递和运算）
8. getter和setter可以有不同的访问级别。

### 构造方法

构造方法，一旦提供了一个**有参的构造方法**，同时又没有显式的提供一个无参的构造方法，那么<u>默认的无参的构造方法，就“木有了“</u>

方法重载之可变参数

```java
// 可变数量的参数
public void attack(Hero... heros) {
  for (int i = 0; i < heros.length; i++) {
    System.out.println(name + " 攻击了 " + heros[i].name);
  }
}
```

### this

1. 通过this访问属性

```java
String name; //姓名

//参数名和属性名一样
//在方法体中，只能访问到参数name
public void setName1(String name){//wrong
  name = name;
}
//为了避免setName1中的问题，参数名不得不使用其他变量名
public void setName2(String heroName){ // right
  name = heroName;
}
//通过this访问属性
public void setName3(String name){ //right
  //name代表的是参数name
  //this.name代表的是属性name
  this.name = name;
}
public static void main(String[] args) {
  Hero  h =new Hero();
  h.setName1("teemo");
  System.out.println(h.name); //为 null
}
```

2. 通过this调用其他的构造方法

```java
//带一个参数的构造方法
public Hero(String name){
  System.out.println("一个参数的构造方法");
  this.name = name;
}

//带两个参数的构造方法
public Hero(String name,float hp){
  this(name); // 通过this调用其他的构造方法
  System.out.println("两个参数的构造方法");
  this.hp = hp;
}
```

###传参

基本类型传参（副本）

类类型传参（引用）

```java
//复活
public void revive(Hero h){ // 创建了一个新的对象，使得h指向新的地址空间 但是main函数中的temmo作为一个引用（是因为作用域的问题），在调用revive方法的时候并没有发生变化。
  h = new Hero("提莫",222);
}

public static void main(String[] args) {
  Hero teemo =  new Hero("提莫",333);

  teemo.revive(teemo);

  //问题： System.out.println(teemo.hp); 输出多少？ 怎么理解？
  System.out.println(teemo.hp); // 333 teemo仍然指向原来的引用

}
```

### 四种访问修饰符

![访问修饰符](http://stepimagewm.how2j.cn/612.png)

那么什么情况该用什么修饰符呢？
从作用域来看，public能够使用所有的情况。 但是大家在工作的时候，又不会真正全部都使用public,那么到底什么情况该用什么修饰符呢？

1. **属性**通常使用private封装起来
2. **方法**一般使用public用于被调用
3. 会被**子类继承**的方法，通常使用protected
4. package用的不多，一般新手会用package, 因为还不知道有修饰符这个东西

再就是**作用范围最小原则**
简单说，能用private就用private，不行就放大一级，用package,再不行就用protected，最后用public。 这样就能**把数据尽量的封装起来，没有必要露出来的，就不用露出来了**

### [单例模式](http://how2j.cn/k/class-object/class-object-singleton/349.html#nowhere)

指的的一个类，在一个JVM里，只有一个实例存在。

单例模式三要素

1. 构造方法私有化。只有这样才能保证该类不会被随便实例化，出现多个实例
2. 静态属性指向实例。首先为了保证单例，这种属性只有一个；因为一个类只有一个，所以应该是static；因为是属性所以从面向对象的角度（应该封装属性），该属性应为private，同时也有了第三个要素。
3. public static的 getInstance方法，返回第二步的静态属性。

分类：

- **饿汉式**是**立即加载**的方式，无论是否会用到这个对象，都会加载。

  如果在构造方法里写了性能消耗较大，占时较久的代码，比如建立与数据库的连接，那么就会在启动的时候感觉稍微有些卡顿。

- **懒汉式**，是**延迟加载**的方式，只有使用的时候才会加载。 

  使用懒汉式，在启动的时候，会感觉到比饿汉式略快，因为并没有做对象的实例化。 但是在第一次调用的时候，会进行实例化操作，感觉上就略慢。

  懒汉式线程安全的考量：

```java
// 由于对getInstance()做了同步处理，synchronized将导致性能开销。如果getInstance()被多个线程频繁的调用，将会导致程序执行性能的下降。
public static Synchronized ThreadSafeSingleton getSingleInstance(){  
  if(null == singObj ) singObj = new ThreadSafeSingleton();
  return singObj；
}  
```

```java
// 双重检查锁定（double-checked locking）
// 这看起来似乎两全其美，但是这可能会因为一个错误的优化造成错误！原因在于singObj = new DoubleCheckedSingleton()这一句。 http://www.cnblogs.com/big-xuyue/p/4074645.html
public static DoubleCheckedSingleton getSingleInstance(){  
  if(null == singObj ) { //第一次检查
    Synchronized(DoubleCheckedSingleton.class){     //加锁
      if(null == singObj)                      //第二次检查
        singObj = new DoubleCheckedSingleton();
    }
  }
  return singObj；
}  
```

```java
// 静态内部类的方式
// 这种写法最大的美在于，完全使用了Java虚拟机的机制进行同步保证，没有一个同步的关键字。
private static class SingletonHolder    
{    
  public final static Singleton instance = new Singleton(); // 由于是静态的域，因此只会在虚拟机装载类的时候初始化一次，并由虚拟机来保证它的线程安全性。   
}    

public static Singleton getInstance()    
{    
  return SingletonHolder.instance;    
}    
```



- 看业务需求，如果业务上允许有比较充分的启动和初始化时间，就使用饿汉式，否则就使用懒汉式

- 饿汉和懒汉，区别在于启动时加载和使用时（延迟）加载

- **[构造函数传参](https://www.jianshu.com/p/bdab698ff338)**  

  饿汉式的构造函数不能传递参数，因为饿汉式需要在类加载的时候就生成对象，那个时候无法传递参数。虽然通过重载getInstance(args)来传递参数，但是不起作用

  ```java
  // 懒汉式
  public static Son getSonInstance(String s,int i){
    if(son==null){ // son 为空，所以son会被重新赋值
      synchronized (Son.class) {
        son=new Son(s,i);
      }
    }
    return son;
  }
  ```

  懒汉式可以，通过重载getInstance(args)，进而使用重载的构造函数。

  ```java
  // 静态内部类
  private static class LoadSonB {
    private static final SonB SONB_INTANCE=new SonB();
  }
  public static SonB getInstance(String s1,int i1){
    s=s1;
    i=i1;
    return LoadSonB.SONB_INTANCE;
  }
  ```

  静态内部类的方式：因为静态内部类<u>在类加载的时候不会加载</u>，静态内部类和非静态内部类一样，都是在被调用时才会被加载，而静态变量、静态方法、静态块都是在类加载的时候就已经”准备好了”, 类加载的时候静态内部类还没加载，所以构造函数还没运行，当静态内部类被调用时，参数已经被赋值了，所以可以。

  ```java
  // 饿汉式
  public class SonC extends Father{
    private static String s;
    private static int i;
    public SonC() {
      super(s, i);
    }
    private static SonC sonc=new SonC();
    public static SonC getInstance(String s1,int i1){
      s=s1;
      i=i1;
      return sonc;
    }
  }
  ```

  而使用饿汉式，由于sonc属于static变量，而且在声明的时候new了，那时参数还没有赋值时，构造函数已经被调用了，sonc已经被赋值了，所以不能对sonc造成改变。

  从**构造函数传参+线程安全**来看，使用单例模式的时候，**最好使用内部类的方式来实现。**
