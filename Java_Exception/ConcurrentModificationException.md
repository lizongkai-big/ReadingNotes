### Java ConcurrentModificationException异常原因和解决方法

`转自https://www.cnblogs.com/dolphin0520/p/3933551.html，有修改`

#### ConcurrentModificationException异常出现的原因

```java
// 代码1
public class Test {
    public static void main(String[] args)  {
        ArrayList<Integer> list = new ArrayList<Integer>();
        list.add(2);
        Iterator<Integer> iterator = list.iterator();
        while(iterator.hasNext()){
            Integer integer = iterator.next(); // java.util.ConcurrentModificationException
            if(integer==2)
                list.remove(integer);
        }
    }
}
```

首先，最简单的解决方法：不使用iterator，使用for循环遍历

```java
public class Test {
    public static void main(String[] args)  {
        ArrayList<Integer> list = new ArrayList<Integer>();
        list.add(2);
        for (int i = 0; i < list.size(); i++) {
            Integer integer = list.get(i);
            if(integer == 2){
                list.remove(integer);
            }
        }
    }
}
```

其实应该正视这种错误，bug出在`at java.util.ArrayList$Itr.checkForComodification(ArrayList.java:901)`

```java
final void checkForComodification() {
  if (modCount != expectedModCount)
    throw new ConcurrentModificationException();
}
```

从函数中只能看出，因为modCount != expectedModCount造成的异常；

那就只好从<u>回过头来</u>从`代码1`中发现问题，对于`Iterator<Integer> iterator = list.iterator();` iterator指向list.iterator()，实际上是指向了ArrayList的内部类Itr()，该类含有hasNext(), next(), remove(), forEachRemaining()和checkForComodification()方法，具有cursor, lastRet, expectedModCount属性；初始时，cursor为0，lastRet为-1，那么调用一次next()之后，cursor的值为1，lastRet的值为0。注意此时，modCount为0，expectedModCount也为0。

```java
public E next() {
  checkForComodification();
  int i = cursor;
  if (i >= size)
    throw new NoSuchElementException();
  Object[] elementData = ArrayList.this.elementData;
  if (i >= elementData.length)
    throw new ConcurrentModificationException();
  cursor = i + 1;
  return (E) elementData[lastRet = i];
}
```

接着往下看，程序中判断当前元素的值是否为2，若为2，则调用list.remove()方法来删除该元素。

对于 `代码1`中的`list.remove(integer);`  该remove()为ArrayList的remove()，通过remove方法删除元素最终是调用的fastRemove()方法，在fastRemove()方法中，首先对modCount进行加1操作（因为对集合修改了一次），然后接下来就是删除元素的操作，最后将size进行减1操作，并将引用置为null以方便垃圾收集器进行回收工作。

```java
public boolean remove(Object o) {
  if (o == null) {
    for (int index = 0; index < size; index++)
      if (elementData[index] == null) {
        fastRemove(index);
        return true;
      }
  } else {
    for (int index = 0; index < size; index++)
      if (o.equals(elementData[index])) {
        fastRemove(index);
        return true;
      }
  }
  return false;
}

/*
* Private remove method that skips bounds checking and does not
* return the value removed.
*/
private void fastRemove(int index) {
  modCount++;
  int numMoved = size - index - 1;
  if (numMoved > 0)
    System.arraycopy(elementData, index+1, elementData, index,
                     numMoved);
  elementData[--size] = null; // clear to let GC do its work
}
```

那么注意此时各个变量的值：对于iterator，其expectedModCount为0，cursor的值为1，lastRet的值为0。

对于list，其modCount为1，size为0。

接着看程序代码，执行完删除操作后，继续while循环，调用hasNext()判断，由于此时cursor为1，而size为0，那么返回true，所以继续执行while循环，然后继续调用iterator的next()方法：

注意，此时要注意next()方法中的第一句：checkForComodification()。如果modCount不等于expectedModCount，则抛出ConcurrentModificationException异常。很显然，此时modCount为1，而expectedModCount为0，因此程序就抛出了ConcurrentModificationException异常。

到这里，想必大家应该明白为何上述代码会抛出ConcurrentModificationException异常了。

**关键点就在于：调用list.remove()方法导致modCount和expectedModCount的值不一致。**



#### 在单线程环境下的解决办法

其实很简单，细心的朋友可能发现在Itr类中也给出了一个remove()方法：

```java
public void remove() {
  if (lastRet < 0)
    throw new IllegalStateException();
  checkForComodification();

  try {
    ArrayList.this.remove(lastRet);
    cursor = lastRet;
    lastRet = -1;
    expectedModCount = modCount;
  } catch (IndexOutOfBoundsException ex) {
    throw new ConcurrentModificationException();
  }
}
```

在这个方法中，删除元素实际上调用的就是list.remove()方法，但是它多了一个操作：

`expectedModCount = modCount;` **因此，在迭代器中如果要删除元素的话，需要调用Itr类的remove方法。**

```java
public class Test {
    public static void main(String[] args)  {
        ArrayList<Integer> list = new ArrayList<Integer>();
        list.add(2);
        Iterator<Integer> iterator = list.iterator();
        while(iterator.hasNext()){
            Integer integer = iterator.next();
            if(integer==2)
                iterator.remove();   //注意这个地方
        }
    }
}
```



#### 在多线程环境下的解决方法

在多线程环境下，使用`iterator.remove()`仍会出现ConcurrentModificationException异常。有可能有朋友说ArrayList是非线程安全的容器，换成Vector就没问题了，实际上换成Vector还是会出现这种错误。

原因在于，虽然Vector的方法采用了synchronized进行了同步，但是实际上通过Iterator访问的情况下，**每个线程里面返回的是不同的iterator，也即是说expectedModCount是每个线程私有。**假若此时有2个线程，线程1在进行遍历，线程2在进行修改，那么很有可能导致线程2修改后导致Vector中的modCount自增了，线程2的expectedModCount也自增了，但是线程1的expectedModCount没有自增，此时线程1遍历时就会出现expectedModCount不等于modCount的情况了。

因此一般有2种解决办法：

　1）在使用iterator迭代的时候使用synchronized或者Lock进行同步；

　2）使用并发容器CopyOnWriteArrayList代替ArrayList和Vector。