# Lambda

### 方法构造器

静态方法

对象方法

引用容器中的对象的方法

引用构造器

<u>有个软用？</u>

### 函数式接口

函数式接口是**只包含一个抽象方法声明**的接口（函数式接口只能有一个抽象方法，如果你尝试添加第二个抽象方法，将抛出编译时错误。）；所以，当用lambda表达式替换这种函数式接口时不会出现你的表达式替换接口中哪个函数的歧义。

@FunctionalInterface 是 Java 8 新加入的一种接口，用于指明该接口类型声明是根据 Java 语言规范定义的函数式接口。

### Stream

Stream 和Collection结构化的数据不一样，Stream是一系列的元素，就像是生产线上的罐头一样，一串串的出来。
管道指的是一系列的聚合操作。

管道又分3个部分
​	**管道源**：在这个例子里，源是一个List
​	**中间操作**： 每个中间操作，又会返回一个Stream，比如.filter()又返回一个Stream, 中间操作是“懒”操作，并不会真正进行遍历。distinct(), filter(), map(), reduce()
​	**结束操作**：当这个操作执行后，流就被使用“光”了，无法再被操作。所以这必定是流的最后一个操作。 结束操作不会返回Stream，但是会返回int、float、String、 Collection或者像forEach，什么都不返回, 结束操作才进行真正的遍历行为，在遍历的时候，才会去进行中间操作的相关判断 。summaryStatistics()

#####加入Predicate

```java
Predicate<Integer> startsWithJ = (n) -> n%2 == 0;
Predicate<Integer> fourLetterLong = (n) -> n >= 4;
list.stream()
  .filter(startsWithJ.and(fourLetterLong))
  .forEach((n) -> System.out.print("n % 2 == 0 and n >= 4: " + n));
```

可按需要将 Predicate 作为单独条件然后将其合并起来使用。简而言之，你可以以传统Java命令方式使用 Predicate 接口，也可以充分利用lambda表达式达到事半功倍的效果。

##### Map和Reduce

```java
// 为每个订单加上12%的税
// 老方法：
List<Integer> costBeforeTax = Arrays.asList(100, 200, 300, 400, 500);
double total = 0;
for (Integer cost : costBeforeTax) {
    double price = cost + .12*cost;
    total = total + price;
}
System.out.println("Total : " + total);

// 新方法：
List<Integer> costBeforeTax1 = Arrays.asList(100, 200, 300, 400, 500);
double bill = costBeforeTax1.stream().map((cost) -> cost + .12*cost).reduce((sum, cost) -> sum + cost).get();
System.out.println("Total : " + bill);
```

### Lambda表达式 vs 匿名类

既然lambda表达式即将正式取代Java代码中的匿名内部类，那么有必要对二者做一个比较分析。

一个关键的不同点就是关键字 this。匿名类的 this 关键字指向匿名类，而lambda表达式的 this 关键字指向包围lambda表达式的类。

另一个不同点是二者的编译方式。Java编译器将lambda表达式编译成类的私有方法。使用了Java 7的 invokedynamic 字节码指令来动态绑定这个方法。

