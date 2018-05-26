## 基本概念

###使用java 执行ping命令

借助 Runtime.getRuntime().exec() 可以运行一个windows的exe程序

##Socket

### 建立连接（TCP/IP）

Server 端

```java
//服务端打开端口8888
ServerSocket ss = new ServerSocket(8888);

//在8888端口上监听，看是否有连接请求过来
System.out.println("监听在端口号:8888");
Socket s =  ss.accept();
...
s.close();
ss.close();
```

Client 端

```java
//连接到本机的8888端口
Socket s = new Socket("127.0.0.1",8888);
...
s.close();
```

使用**一对 socket** 建立连接 -> 一旦两个 socket 建立好了连接，他们可以**单向或双向**进行数据传输。收发数字 or 字符串

### 收发数字

发送数字

```java
// 打开输出流
OutputStream os = s.getOutputStream();
// 发送数字110到服务端
os.write(110);
...
os.close();
```

读取数字

```java
//打开输入流
InputStream is = s.getInputStream();
//读取客户端发送的数据
int msg = is.read();
System.out.println(msg);
...
is.close();
```

注：

1. socket 使用输入流、输出流来读取、发送消息，与服务器端还是客户端无关
2. 一个 socket 套接字既可以发送消息也可以读取消息

### 收发字符串

发送字符串

```java
//把输出流封装在DataOutputStream中
DataOutputStream dos = new DataOutputStream(os);
//使用writeUTF发送字符串
dos.writeUTF("Legendary!");
```

读取字符串

```java
//把输入流封装在DataInputStream
DataInputStream dis = new DataInputStream(is);
//使用readUTF读取字符串
String msg = dis.readUTF();
```

