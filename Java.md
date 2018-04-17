1. 命名规则
   1. 类名（UpperCamelCase）：Hero
   2. 属性名、方法名（lowerCamelCase 风格）：name，getSpeed()
2. 数组：
   1. 数组的长度是不可变的，一旦分配好空间，是多长，就多长，不能增加也不能减少；如果需要变长的数组，可以用Collection.ArrayList
   2. 数组复制System.arraycopy(src, srcPos, dest, destPos, length)
   3. for(int value : array) 增强型for循环
   4. Arrays类，用于对数组的复制，查找，排序，填充，输出

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

3. 传参

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
// 这看起来似乎两全其美，但是这是一个错误的优化！原因在于singObj = new DoubleCheckedSingleton()这一句。 http://www.cnblogs.com/big-xuyue/p/4074645.html
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
  public final static Singleton instance = new Singleton();    
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
  public static Son getSonInstance(String s,int i){
      if(son==null){
          synchronized (Son.class) {
              son=new Son(s,i);
          }
      }
          return son;
  }
  ```

  懒汉式可以，通过重载getInstance(args)，进而使用重载的构造函数。

  ```java
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

### [对象转型](http://www.cnblogs.com/xdp-gacl/p/3647810.html)

1. 向上转型：**父类对象的引用或者叫基类对象的引用指向子类对象，这就是向上转型** ，也就是子类转换为父类，这是说得通的

2. 向下转型：**父类对象引用father转换成子类类型**的时候，就有可能成功，有可能失败；因此要进行强制转换，换句话说转换后果自负；到底能不能转换成功，要看**father引用到底指向的是哪种对象** 

3. **父类(基类)对象的引用不可以访问其子类对象新增加的成员(属性和方法)。**对象继承的编译器表现：Animal ----> Dog，Animal a = new Dog(); 因为子类Dog从父类Animal继承下来，所以new出一个子类对象的时候，这个子类对象里面会包含有一个父类对象， 因此这个a指向的正是这个子类对象里面的父类对象，因此尽管a是指向Dog类对象的一个引用， 但是在**编译器**眼里你a就是只是一个Animal类的引用对象，你a就是只能**访问**Animal类里面所具有的成员变量，别的你都访问不了。<u>*因此一个父类(基类)对象的引用是不可以访问其子类对象新增加的成员(属性和方法)的。*</u>  

4. 如果真的想访问Dog对象的furColor属性，那就采用**对象向下转型**的办法，把父类对象的引用转型成子类对象的引用。Dog d1 = (Dog)a; （在这个例子里，a指向的是一个Dog对象，所以转换成Dog类型，是可以的），这样d1和a都是指向堆内存里面的Dog对象了，而且d1指向的就是这只Dog**所有的部分**，通过这个d1就可以访问Dog对象里面所有的成员了。

   ![img](./image/java_extends_memory_analysis.png)

   ​

5. instanceof 探索的是实际当中你整个对象到底是什么东西，并不是根据你的引用把对象看出什么样来判断的。

   ```java
   a instanceof Animal // true 
   a instanceof Dog // true 
   ```

6. **对象的向上转型可以使父类对象的引用可以指向子类对象，给程序带来了比较好的可扩展性：我们可以在一个方法的参数里面定义父类的引用，然后实际当中传的时候传的是子类的对象，然后我们再在方法里面判断这个传过来的子类对象到底属于哪个子类，然后再去执行这个子类里面的方法或者调用这个子类里面的成员变量，因此程序的可扩展性比单独定义好多个方法要好一些。**不过这个可扩展性还没有达到最好，使用多态就可以让程序的扩展性达到极致。

   ```java
   public void  f(Animal a) { // 参数可为子类，向上转型
       System.out.println(a.name);
       if (a instanceof Cat) {
           Cat cat = (Cat)a; // 安全的向下转型
           System.out.println(cat.eyeColor+" eye");
       }else if (a instanceof Dog) {
           Dog dog = (Dog)a;
           System.out.println(dog.furColor+" fur");
       }
   }
   ```

7. <u>实现类</u>转换成<u>接口</u>(向上转型)

   ```java
   // ADHero实现了AD接口
   ADHero ad = new ADHero();
   // AD是接口类型
   AD adi = ad; // 是可以的
   ```

8. <u>接口</u>转换成<u>实现类</u>(向下转型)

   ```java
   // ADHero实现了AD接口
   ADHero ad = new ADHero();
   // AD是接口类型  向上转型是可以的
   AD adi = ad;
   // 向下转型，要看adi引用指向的是何种对象，此处指向的是ADHero对象，要转换成ADHero，所以可以
   ADHero adHero = (ADHero) adi;
   // 要把指向ADHero的引用adi指向ADAPHero就是失败的
   ADAPHero adapHero = (ADAPHero) adi;
   ```




多态

1. 多态: 都是同一个类型（都是父类类型，实际上指向的是不同的对象），调用同一个方法（该方法有重写），却能呈现不同的状态
2. ​

​

​

​









