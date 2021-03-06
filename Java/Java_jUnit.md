### 为什么要使用测试工具

1. 测试框架可以帮助我们对编写的程序进行有目的地测试，帮助我们最大限度地避免代码中的bug，以保证系统的正确性和稳定性。
2. 很多人对自己写的代码，测试时就简单写main，然后sysout输出控制台观察结果。这样非常枯燥繁琐，不规范。缺点：测试方法不能一起运行，测试结果要程序猿自己观察才可以判断程序逻辑是否正确。
3. JUnit的断言机制，可以直接将我们的预期结果和程序运行的结果进行一个比对，确保对结果的可预知性。`Assert.assertEquals`

### JUnit使用的最佳实践

1. 测试方法上必须使用@Test进行修饰
2. 测试方法必须使用public void 进行修饰，不能带任何的参数
3. 新建一个源代码目录来存放我们的测试代码，即将测试代码和项目业务代码分开
4. 测试类所在的包名应该和被测试类所在的包名保持一致
5. 测试单元中的每个方法必须可以独立测试，测试方法间不能有任何的依赖
6. 测试类使用Test作为类名的后缀（不是必须）
7. 测试方法使用test作为方法名的前缀（不是必须）



注意：测试用例是用来达到测试想要的预期结果，而不能测试出程序的逻辑错误。

### 测试失败的两种情况

1. Failure一般由单元测试使用的断言方法判断失败所引起的，这经表示测试点发现了问题，就是说程序输出的结果和我们预期的不一样。
2. Error是由代码异常引起的，它可以产生于测试代码本身的错误，也可以是被测试代码中的一个隐藏的bug。

### JUnit详解之运行流程及常用注解

1. @Test:将一个普通的方法修饰成为一个测试方法
   1. @Test(expected=XX.class)
   2. @Test(timeout=毫秒)
2. @BeforeClass：它会在所有的方法运行前被执行，static修饰
3. @AfterClass:它会在所有的方法运行结束后被执行，static修饰
4. @Before：会在每一个测试方法被运行前执行一次
5. @After：会在每一个测试方法运行后被执行一次
6. @Ignore:所修饰的测试方法会被测试运行器忽略
7. @RunWith:可以更改测试运行器 org.junit.runner.Runner



### JUnit测试套件使用及参数化设置

都要修改 @RunWith()

1. @RunWith(Suite.class)  用于运行所有的单元测试代码类
2. @RunWith(Parameterized.class) <有需要再学习吧>

