1. list clear 之后，newList 也会变空；因为list传递的是reference呀，对象实例是放在堆里的

```java
List<Integer> list = new ArrayList<>();
for (int i = 0; i < 100; i++) {
  list.add(i);
}
List<Integer> newList = list; // 1
list.clear(); // 2
System.out.println(newList.size());
for (int i = 0; i < newList.size(); i++) {
  System.out.println(newList.get(i));
}
```

2. [为什么**局部变量**需要显式设置初始化值？](https://droidyue.com/blog/2018/07/16/variable-localname-might-not-have-been-initialized/)

   1. javac足够有能力推断出局部变量并初始化默认值。然而它并没有这样做。

   2. **成员变量**却可以不初始化即可使用。因为对于成员变量而言，其赋值和取值访问的先后顺序具有不确定性。name的赋值可以发生在dumpField之前，也可以发生在dumpField之后。这是在运行时发生的，在编译器来看确定不了的。对于没把握的事情，javac是不会去做的，这种事情交给运行时的JVM就可以了

   3. 而对于**局部变量**而言，其赋值和取值访问顺序是确定的。因为localName的作用范围只限定于dump方法中，必然的顺序就是先赋值（声明），再进行访问。

   4. 其实之所以这样做就是一种**对程序员的约束限制**。因为程序员（人）是（有些情况下）是靠不住的，假使局部变量可以使用默认值，我们总会无意间忘记赋值，进而导致不可预期的情况出现。

      ```java
      public class Test {
        public void dump() {
          String localName;
          // 不能通过编译 Variable 'localName' might not have been initialzed
          System.out.println("dump localName=" + localName);
        }
        
        public String name;
        public  void dumpField() {
          // 成员变量却可以不初始化即可使用
          System.out.println("dumpField name=" + name);
        }
      }
      ```

3. 引用传递与值传递

   1. ```java
      /*
      https://www.nowcoder.com/test/question/done?tid=18034094&qid=22461#summary
      str变量是一个引用变量，它存在栈区中，str变量保存的是堆区中"good"字符串所在数组的首地址。但是呢 这里把"good"的首地址赋给change(String str, char ch[]) 函数的形参str，形参str又指向了新的“test ok”了 ， 这里形参str指来指去最后change调用结束，形参str释放。 属性str还是指向原来的“good”数组。
      */
      public class test {
        String str = new String("good");
        char[] ch = { 'a', 'b', 'c' };

        public static void main(String args[]) {
          test ex = new test();
          // ex.str作为参数传递给str
          ex.change(ex.str, ex.ch);
          System.out.print(ex.str + " and ");
          System.out.print(ex.ch); // good and gbc
        }

        public static void change(String str, char ch[])      
        {
          str = "test ok";
          ch[0] = 'g';
        }
      }
      ```

4. java 类中默认访问权限 **类和内部类**

   1. Java 中的访问权限控制符有四个. 
      作用域         当前类          同一package           子孙类                其他package 
      public              √                    √                           √                             √ 
      protected         √                    √                            √                            × 
      friendly            √                    √                            ×                            × 
      private             √                     ×                            ×                             × 
   2. ​

5. 泛型

   1. 1. 只看尖括号里边的！！明确点和范围两个概念
      2. 如果尖括号里的是一个类，那么尖括号里的就是一个点，比如List<A>,List<B>,List<Object>
      3. 如果尖括号里面带有问号，那么代表一个范围，<? extends A> 代表小于等于A的范围，<? super A>代表大于等于A的范围，<?>代表全部范围
      4. 尖括号里的所有点之间互相赋值都是错，除非是俩相同的点
      5. 尖括号小范围赋值给大范围，对，大范围赋值给小范围，错。如果某点包含在某个范围里，那么可以赋值，否则，不能赋值
      6. List<?>和List 是相等的，都代表最大范围
      7. 补充：List既是点也是范围，当表示范围时，表示最大范围

   2. ```java
      public static void main(String[] args) {
        List<A> a;
        List list;
        list = a;   //A对，因为List就是List<?>，代表最大的范围，A只是其中的一个点，肯定被包含在内
        List<B> b;
        a = b;      //B错，点之间不能相互赋值
        List<?> qm;
        List<Object> o;
        qm = o;     //C对，List<?>代表最大的范围，List<Object>只是一个点，肯定被包含在内
        List<D> d;
        List<? extends B> downB;
        downB = d;  //D对，List<? extends B>代表小于等于B的范围，List<D>是一个点，在其中
        List<?extends A> downA;
        a = downA;  //E错，范围不能赋值给点
        a = o;      //F错，List<Object>只是一个点
        downA = downB;  //G对，小于等于A的范围包含小于等于B的范围，因为B本来就比A小，B时A的子类嘛
      }
      ```

6. try...catch...finally，finally语句块总会执行

   1. finally{}代码块比return先执行。

   2. 多个return是按顺序执行的的，多个return执行了一个后，后面的return就不会执行了。

   3. 记住一点，不管有不有异常抛出， finally都会在return返回前执行

   4. ```java
      public abstract class Test {
        public static void main(String[] args) {
          System.out.println(beforeFinally1()); // output 1
          System.out.println(beforeFinally2()); // output 2
        }

        public static int beforeFinally1(){
          int a = 0;
          try{
            a = 1;
            return a;
          }finally{
            a = 2;
          }
        }
        
        public static int beforeFinally2(){
          int a = 0;
          try{
            a = 1;
            return a;
          }finally{
            a = 2;
            return a;
          }
        }
      }
      ```

   5. 单步调试可以发现，finally 是在return 之前执行的

   6. 关于beforeFinally1，为什么beforeFinally1 中 `a` 的值没有被覆盖了？实际过程是这样的：当程序执行到try{}语句中的return方法时，它会干这么一件事，将要返回的结果存储到一个**临时栈**中，然后程序不会立即返回，而是去执行finally{}中的程序， **在执行`a = 2`时，程序仅仅是覆盖了a的值，但不会去更新临时栈中的那个要返回的值** 。执行完之后，就会通知主程序“finally的程序执行完毕，可以请求返回了”，这时，就会将临时栈中的值取出来返回。这下应该清楚了，要返回的值是保存至临时栈中的。

   7. 关于beforeFinally2，在这里，finally{}里也有一个return，那么在执行这个return时，就会更新临时栈中的值。同样，在执行完finally之后，就会通知主程序请求返回了，即将临时栈中的值取出来返回。故返回值是2.

7. [transient](http://www.cnblogs.com/lanxuezaipiao/p/3369962.html)

   1. java 的transient关键字为我们提供了便利，你只需要实现Serilizable接口，将不需要序列化的属性前添加关键字transient，序列化对象的时候，这个属性就不会序列化到指定的目的地中。

8. ​数组元素在栈区，链表元素在堆区？

9. package语句必须作为源文件的第一条非注释性语句，一个源文件只能指定一个包，只能包含一条package语句

10. 在接口里面的变量默认都是public static final 的，它们是公共的,静态的,最终的常量.相当于全局常量，可以直接省略修饰符。实现类可以直接访问接口中的变量

11. ​
