1. gradle引用包：
   1. 在ssm框架中，jar包是要有引用顺序的；**猜测**：在spring包引入之前，引入别的jar包这些包是不被支持依赖注入的？
   2. 对于一般的包，使用 compile 或者 testCompile，gradle 都可以引入，google 搜索`gradle sql server` 即可找到 `sql server` 所需的 jar 包的 gradle 引用语句
   3. 万一无法引入（找不到对应的包或者是程序员自己的包），则可以使用 `compile fileTree(dir: 'libs', include: ['*.jar'])` 引入放在本地项目下的包

2. 基本概念：
   1. Gradle本身只是一个架子，真正起作用的是Task和Plugin。Gradle其实就是groovy代码。

   2. Task: 一个Task表示一个逻辑上较为独立的执行过程，比如编译Java源代码，拷贝文件，打包Jar文件，甚至可以是执行一个系统命令或者调用Ant。另外，一个Task可以读取和设置Project的Property以完成特定的操作。

      比如 build.gralde 中的最简单的 task，执行代码其实就是 groovy 代码。这里的“<<”表示追加的意思，即向 helloWorld 中加入执行过程。该代码执行时需要单独写`gradle helloWorld` ，如果不写 ‘<<’ ，就可以通过执行build.gradle自动执行

      ```groovy
      task helloWorld << {
         println "Hello World!"
      }
      ```

   3. Gradle的Project从本质上说只是含有多个Task的容器。Project为Task提供了执行上下文，所有的Plugin要么向Project中添加用于配置的Property，要么向Project中添加不同的Task，要么两者都添加，比如java Plugin

3. Task: 

   1. 分为DefaultTask类型和显示声明Task的类型（如：Copy），也可以自定义Task类型
   2. Gradle还默认为我们提供了dependencies、projects和properties等Task。dependencies用于显示Project的依赖信息，projects用于显示所有Project，包括根Project和子Project，而properties则用于显示一个Project所包含的所有Property。
   3. Task之间可以存在依赖关系
   4. 创建Task的多种方法
      1. 调用Project的task()方法创建Task
      2. 通过TaskContainer的create()方法创建Task
      3. 声明Task之间的依赖关系
      4. 配置Task；Gradle在执行Task时分为两个阶段，首先是配置阶段，然后才是实际执行阶段。

4. 读懂Gradle语法

   1. Gradle是一种声明式的构建工具。在执行时，Gradle并不会一开始便顺序执行build.gradle文件中的内容，而是分为两个阶段，第一个阶段是配置阶段，然后才是实际的执行阶段。在配置阶段，Gradle将读取所有build.gradle文件的所有内容来配置Project和Task等，比如设置Project和Task的Property，处理Task之间的依赖关系等。
   2. 事实上，对于每一个Task，Gradle都会在Project中创建一个同名的Property，所以我们可以将该Task当作Property来访问
   3. 要读懂Gradle，我们首先需要了解Groovy语言中的两个概念，一个Groovy中的Bean概念，一个是Groovy闭包的delegate机制。
      1. Groovy中的Bean和Java中的Bean有一个很大的不同，即Groovy为每一个字段都会自动生成getter和setter，并且我们可以通过像访问字段本身一样调用getter和setter
      2. 简单来说，delegate机制可以使我们将一个闭包中的执行代码的作用对象设置成任意其他对象。

5. 增量式构建

   1. 在增量式构建中，我们为每个Task定义输入（inputs）和输入（outputs），如果在执行一个Task时，如果它的输入和输出与前一次执行时没有发生变化，那么Gradle便会认为该Task是最新的（UP-TO-DATE），因此Gradle将不予执行。
   2. tips：有些task的输入和输出需要专门定义

6. 自定义Property

   1. 在build.gradle文件中定义Property
   2. 通过命令行参数定义Property
   3. 通过JVM系统参数定义Property
   4. 通过环境变量设置Property

7. 使用Java Plugin：java Plugin也有比较与众不同的地方，其中之一便是它在项目中引入了构建生命周期的概念，就像Maven一样。但是，和Maven不同的是，Gradle的项目构建生命周期并不是Gradle的内建机制，而是由Plugin自己引入的。

   1. 使用source set的目的是将一些源文件组合起来，为了某个特殊的目的在逻辑上进行分组。这只是个概念的问题，你知道知道你分组的意义：方便你管理文件。[java Plugin](https://blog.csdn.net/itfootball/article/details/42672271)

   2. Gradle实际上为我们创建了2个source set，一个名为main，一个名为test。这里的source set的名字main与上图目录结构中的main文件夹并无必然的联系，只是在默认情况下，Gradle为了source set概念到文件系统目录结构的映射方便

   3. 从本质上讲，Gradle的每个source set都包含有一个名字，并且包含有一个名为java的Property和一个名为resources的Property，他们分别用于表示该source set所包含的Java源文件集合和资源文件集合。

   4. Gradle会自动地为每一个新创建的source set创建相应的Task，创建规律为：对于名为mySourceSet的source set，Gradle将为其创建compile<mySourceSet>Java、process<mySourceSet>Resources和<mySourceSet>Classes这3个Task。你可能会注意到，对于main而言，Gradle并没有相应的compileMainJava，原因在于：由于main是Gradle默认创建的source set，并且又是及其重要的source set，Gradle便省略掉了其中的“Main”，而是直接使用了compileJava作为main的编译Task。对于test来说，Gradle依然采用了compileTestJava。

   5. **compileJava**：目的是编译java源文件，利用javac。依赖compile任务，属于JavaCompile类型。

   6. **processResource**：将项目的资源文件复制到项目class目录中。无依赖。属于Copy类型，严格来说是Copy的子类。

   7. **依赖管理**

      1. 在声明对第三方类库的依赖时，我们需要告诉Gradle在什么地方去获取这些依赖，即配置Gradle的Repository。在配置好依赖之后，Gradle会自动地下载这些依赖到本地。

      2. 定义一个Configuration（依赖组），并向依赖组中加入依赖

         ```groovy
         // 定义一个Configuration（依赖组）
         configurations {
            myDependency
         }
         // 向依赖组中加入依赖
         dependencies {
            myDependency 'org.apache.commons:commons-lang3:3.0'
         }
         ```

      3. java Plugin会自动定义compile和testCompile，分别用于编译Java源文件和编译Java测试源文件。

   8. 构建多个Project

      - 在多个Project中，gradle 仍非常灵活，可以针对所有的项目（root-project），子项目（sub-project），带特定字符的项目添加task以及依赖。（Gradle的Project之间的依赖关系是基于Task的，而不是整个Project的。）

   9. 自定义Task类型

      1. 在build.gradle文件中直接定义

         ```groovy
         // 自定义Task
         class HelloWorldTask extends DefaultTask {
             // 表示在配置该Task时，message是可选的
             @Optional
             String message = 'I am davenkin'

             // @TaskAction表示该Task要执行的动作
             @TaskAction
             def hello(){
                 println "hello world $message"
             }
         }

         // 使用默认的message
         task hello(type:HelloWorldTask)

         // 重新设置了message的值
         task hello1(type:HelloWorldTask){
            message ="I am a programmer"
         }
         ```

      2. 在当前工程中定义Task类型（未实现）

      3. 在单独的项目中定义Task类型（未实现）

   10. 自定义Plugin

      1. 在build.gradle文件中直接定义Plugin
      2. 在当前工程中定义Plugin
      3. 在单独的项目中创建Plugin

      ​

      ​


