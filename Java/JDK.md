1. rotate()

```java
// Collections.rotate(numbers, 2);
//初始化集合numbers
List<Integer> numbers = new ArrayList<>();
for (int i = 0; i < 11; i++) {
  numbers.add(i);
}
/*rotate 滚动*/
int distance = 2;
int size = numbers.size();
if (size == 0)
  return;
distance = distance % size;
if (distance < 0)
  distance += size;
if (distance == 0)
  return;

for (int cycleStart = 0, nMoved = 0; nMoved != size; cycleStart++) {
  int displaced = numbers.get(cycleStart);
  int i = cycleStart;
  System.out.printf("第%d次循环\n", cycleStart);
  do {
    i += distance;
    if (i >= size)
      i -= size;
    displaced = numbers.set(i, displaced);
    System.out.printf("-----------第%d个位置设置成%d\n", i, displaced);
    nMoved ++;
  } while (i != cycleStart);
}
System.out.println("把集合向右滚动2个单位，标的数据后，集合中的数据:");
System.out.println(numbers);
```

2. ArrayList

   ```java
   // c.toArray might (incorrectly) not return Object[] (see 6260652)
   //反射,获取数组类型,判定c.toArray类型是否为Object[]类型
   elementData.getClass() != Object[].class


   ```