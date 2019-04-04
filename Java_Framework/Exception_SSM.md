1. 乱码！！！

   1. tomcat乱码
   2. cmd控制台乱码
   3. swagger乱码

   https://www.cnblogs.com/vhua/p/idea_1.html   -Dfile.encoding=UTF-8

   https://blog.csdn.net/m0_37893932/article/details/78280663

2. Class 都以J显示

   1. Project Structure的Project --> Project compiler outpur 路径改为:项目路径\out
   2. Moudules 项目路径的Sources
   3. Artifacts: 

3. gradle  错误: 编码GBK的不可映射字符 https://blog.csdn.net/truesword/article/details/79626227

   ```groovy
   tasks.withType(JavaCompile) {
       options.encoding = "UTF-8"
   }
   ```

4. 手动引入gradle管理的jar包，Project Structure --> Modules --> Dependencies --> '+' --> library --> 全选apply

5. 项目安装包是怎么自动生成的呢？

   1. 重启IDEA，可能会有提示
   2. Project Structure --> Facets --> + Web项目 --> 自动提示让添加Artifacts


