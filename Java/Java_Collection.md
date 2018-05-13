### Collection

#### ArrayList

1. 线性列表

#### LinkedList

1. 双向链表 (Deque)   addFirst(), addLast(), getFirst(), getLast(), removeFirst(), removeLast()
2. 队列 (Queue)  **FIFO**  offer 在最后添加元素，poll 取出第一个元素，peek 查看第一个元素


####HashMap

#### HashSet

​	本质上使用HashMap实现；对于已经存在的值，再往HashSet插入（add()）会失败

#### Collections

Collections是一个类，容器的工具类,就如同Arrays是数组的工具类。reverse(), shuffle(), swap(), sort(), rotate, synchronizedList()

**Collections.sort()** 在对对象排序时，Collections不确定要对哪个属性排序，可以通过两种方式解决：

1. 在函数主体中引入Comparator

   ```java
   //引入Comparator，指定比较的算法
   Comparator<Hero> c = new Comparator<Hero>() {
     @Override
     public int compare(Hero h1, Hero h2) {
       //按照hp进行排序
       if(h1.hp>=h2.hp)
         return 1;  //正数表示h1比h2要大
       else
         return -1;
     }
   };
   ```

2. 使Hero类实现Comparable接口，在类里面提供比较算法

   ```java
   public class Hero implements Comparable<Hero>{
     ...
     ...
     @Override
     public int compareTo(Hero anotherHero) {
       if(damage<anotherHero.damage)
         return 1; 
       else
         return -1;
     }
   }
   ```

3. Collections在比较时会通过判断compare(A, B) > 0 作为交换值的依据



####关系与区别

ArrayList VS HashSet

|           | 有序   | 重复   |
| --------- | ---- | ---- |
| ArrayList | 有    | 可    |
| HashSet   | 无    | 无    |



ArrayList VS LinkedList

|            | 插入、删除 | 定位   |
| ---------- | ----- | ---- |
| ArrayList  | 慢     | 快    |
| LinkedList | 快     | 慢    |



HashMap VS HashTable

HashMap和Hashtable都实现了Map接口，都是键值对保存数据的方式

|           | k-v为null | 线程安全 |
| --------- | -------- | ---- |
| HashMap   | 可以       | 不是   |
| HashTable | 不可以      | 是    |

Set 比较

|               | 特点     |
| ------------- | ------ |
| HashSet       | 无序     |
| LinkedHashSet | 按照插入顺序 |
| TreeSet       | 从小到大排序 |



#### hashcode原理

散列值，存在于HashMap中，方便查找value；通过获取key的hashcode，hashcode是否一样
​	如果hashcode不一样，就是在不同的坑里，一定是不重复的
​	如果hashcode一样，就是在同一个坑里，还需要进行equals比较
​		如果equals一样，则是重复数据
​		如果equals不一样，则是不同数据。



#### 聚合操作

​	Stream