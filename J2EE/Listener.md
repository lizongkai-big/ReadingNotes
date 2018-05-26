## Hello Listener

Listener 的作用是用于监听 web应用的创建和销毁，以及在其上attribute发生的变化。 

web应用即ServletContext对象(jsp的隐式对象application)

除了对web应用的监听外，还能监听session和request的生命周期，以及他们的attribute发生的变化。

## 监听 CONTEXT

对Context的监听分生命周期的监听，和Context上Attribute变化的监听两种

###监听生命周期

ContextListener 实现接口ServletContextListener 

`public void contextDestroyed(ServletContextEvent arg0) ` 对应当前web应用的销毁

`public void contextInitialized(ServletContextEvent arg0)`对应当前web应用的初始化

### 监听Context上Attribute的变化

`public void attributeAdded(ServletContextAttributeEvent e)` 监听属性的增加 

`public void attributeRemoved(ServletContextAttributeEvent e)` 监听属性的移除

`public void attributeReplaced(ServletContextAttributeEvent e)` 监听属性的替换

## 监听Session

对Session的监听也分生命周期的监听，和Session上Attribute变化的监听两种。

### 监听生命周期

SessionListener 实现了接口 HttpSessionListener 

`sessionCreated()` 表示session创建的时候执行

`sessionDestroyed()` 表示session销毁的时候执行

### 监听Session上Attribute的变化

SessionAttributeListener 实现接口 HttpSessionAttributeListener 

`attributeAdded()` 当在session中增加属性时触发

`attributeRemoved() ` 当在session中移除属性时触发

`attributeReplaced()` 当替换session中的属性时触发

## 监听Request

对Request的监听分生命周期的监听，和Request上Attribute变化的监听两部分。

```java
public class RequestListener implements ServletRequestListener, ServletRequestAttributeListener {
  // 当本次请求结束的时候触发
  @Override
  public void requestDestroyed(ServletRequestEvent arg0) {
    System.out.println("销毁了一个Request ");
  }
  
  // 当新创建了一个Request的时候触发，只要访问了服务端的资源，就会创建一个Request，无论是jsp,servlet还是html
  @Override
  public void requestInitialized(ServletRequestEvent arg0) {
    System.out.println("创建了一个Request ");
  }

  // 当有新增属性时触发
  @Override
  public void attributeAdded(ServletRequestAttributeEvent e) {
    System.out.println("request 增加属性 ");
    System.out.println("属性是" + e.getName());
    System.out.println("值是" + e.getValue());
  }

  // 当有移除属性时触发
  @Override
  public void attributeRemoved(ServletRequestAttributeEvent arg0) {
    System.out.println("request 移除属性 ");
  }

  // 当有替换属性时触发
  @Override
  public void attributeReplaced(ServletRequestAttributeEvent arg0) {
    System.out.println("request 替换属性 ");
  }
}
```



## 统计在线人数

HTTP协议是短链接的，所以无法在服务端根据建立了多少连接来统计当前有多少人在线。 不过可以通过统计session有多少来估计在线人数。 

一旦一个用户访问服务器，就会创建一个session. 如果该用户持续访问，那么该session会持续有效。 

如果经历了[30分钟](http://how2j.cn/k/jsp/jsp-session/583.html#step1676)，该用户也没有做任何操作，就表示该用户“下线” 了，其对应的session也会被销毁。 

所以可以**通过统计有多少session被保留来估计当前在线人数。**

使用了全局作用域 application来保存session的个数