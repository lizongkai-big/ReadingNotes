1. `java.io.FileNotFoundException: D:\Program%20Files\Apache%20Software%20Foundation\Tomcat%205.0\webapp (系统找不到指定的路径。)`

   因为windows系统中路径名中原有的空格被替换成了`%20`，造成文件路径找不到；简单的方法是把把tomcat放在根目录下，还有一种就是`String encodedPath = filePath.replaceAll("%20", " ");`，将文件路径中含有的%20替换回空格

2. ​