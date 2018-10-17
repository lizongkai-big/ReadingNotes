#### 字符串比较

字符串是对象，比较两个字符串是否相等要用equal！！



### 装箱拆箱

1. 所有的基本类型，都有对应的类类型
2. 装箱 拆箱

```java
//装箱：基本类型 -> 类类型
Integer it2 = i;
// 拆箱：类类型 -> 基本类型
int i3 = it2;
```

2. 为了和double区别，float型定义的数据末尾必须 有"**f** "或"F"

### 字符串转换

其他数字类型转String，就是把Integer换成其他的

```java
int i = 10;
// important
String s = String.valueOf(i);
s = i + "";  // recommend
s = Integer.toString(i);

i = Integer.parseInt(s); // recommend
i = Integer.valueOf(i).intValue()
```

### 数学方法

四舍五入, 随机数，开方，次方，π，自然常数

### 格式化输出

使用 **printf** 格式化输出

```java
String sentenceFormat ="%s 在进行了连续 %d 次击杀后，获得了 %s 的称号%n";
//使用printf格式化输出
System.out.printf(sentenceFormat,name,kill,title);
```

### MyStringBuffer

用了很多System.arraycopy(src, srcPos, des, desPos, length);  src，des为char[]格式

设计步骤：属性 -> 构造方法 带参构造方法 -> reverse(), **toString()** -> insert(), append() -> delete()

注意capacity扩容，length变化；以及对参数的容错，比如start < 0, str == null

```java
public class MyStringBuffer implements IStringBuffer{
    int capacity = 19;
    int length = 0;
    char[] value;
    public MyStringBuffer(){
        value = new char[capacity];
    }

    //有参构造方法
    public MyStringBuffer(String str){
        this();
        if(null==str)
            return;

        if(capacity<str.length()){
            capacity  = value.length*2;
            value=new char[capacity];
        }

        if(capacity>=str.length())
            System.arraycopy(str.toCharArray(), 0, value, 0, str.length());

        length = str.length();

    }

    @Override
    public void append(char c) {
        append(String.valueOf(c));
    }

    @Override
    public void append(String str) {
        this.insert(length, str);
    }

    @Override
    public void insert(int pos, char b) {
        insert(pos,String.valueOf(b));
    }

    @Override
    public void insert(int pos, String b) {
        if(pos < 0)
            return;
        if(pos > length)
            return;
        if(b == null)
            return;
        int len = b.length();
        while(len + length > capacity){
            capacity = (int) ((length + len)*1.5f);
            char[] newValue = new char[capacity];
            System.arraycopy(value, 0, newValue, 0, length);
            value = newValue;
        }
        System.arraycopy(value, pos, value, pos + len, length - pos);
        System.arraycopy(b.toCharArray(), 0, value, pos, len);
        length += len;
    }

    @Override
    public void delete(int start) {
        this.delete(start, length);
    }

    @Override
    public void delete(int start, int end) {
        if(start < 0 || end < start || end > length || end<0)
            return;
        System.arraycopy(value, end, value, start, length - end);
        length -= end - start;
    }

    @Override
    public void reverse() {
        for (int i = 0; i < length / 2; i++) {
            char c = value[i];
            value[i] = value[length - 1 - i];
            value[length - 1 - i] = c;
        }
    }

    @Override
    public int length() {
        return length;
    }

    @Override
    public String toString() {
        char[] realValue = new char[length];

        System.arraycopy(value, 0, realValue, 0, length);

        return new String(realValue);
    }
    public static void main(String[] args) {
        int count = 1000000;
        StringBuffer s = new StringBuffer();
        long time = System.currentTimeMillis();
        while(count -- > 0){
            s.append(createRandomChar(10));
        }
        System.out.println(System.currentTimeMillis() - time);
        time = System.currentTimeMillis();
        MyStringBuffer myStringBuffer = new MyStringBuffer();
        count = 1000000;
        while(count -- > 0){
            myStringBuffer.append(new String(createRandomChar(10)));
        }
        System.out.println(System.currentTimeMillis() - time);

    }

    public static char[] createRandomChar(int count){
        int random = 0;

        char[] chars = new char[count];
        while(count -- > 0){
            // 判断是小写or大写字母，不是就再来一次
            do{
                random = (int)(Math.random() * 100) + 48;
            }while(!(random >= 65 && random <= 90 || random >= 97 && random <= 122 || random >= 48 && random <= 57));
            //System.out.println(random);
            chars[count] = (char)random;
        }
        return chars;
    }
}
```



性能比较：

```java
long time = System.currentTimeMillis();
...
...
System.out.println(System.currentTimeMillis() - time);
```

随机数

```java
Math.random() [0, 1)
long difference = 1000000;
long random1 = (long)(Math.random() * difference);  // right
long random2 = (long)Math.random() * difference;  // Wrong  long先对[0,1]强制类型转换变为0，所以结果就不是随机数，恒为0
```