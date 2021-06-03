---
title: java
date: 2018-07-11 12:21:13
tags:
---

### 1. vert.x启动慢，线程被阻塞
原因是sierra解析主机名时间过长，在/etc/hosts设置主机名解析就好了。

https://thoeni.io/post/macos-sierra-java/

<!-- more -->

### 2. 内部类修改外部变量
1. 如果外部变量是外部类的本地变量（如在方法中定义的变量），必须声明成final才能在内部类中使用，正常是不可以修改这个变量的，但如果变量是对象，可以修改对象内的属性

2. 如果外部变量是外部类的成员变量或类变量，内部类可以直接修改

### 3. java jar
#### 3.1 javac xx.java -cp 编译java字节码 
``` bash
javac ChatServer.java -cp Java-WebSocket-1.4.0.jar # 生成ChatServer字节码
```

#### 3.2 运行字节码
``` bash
java -Xbootclasspath/a:Java-WebSocket-1.4.0.jar:slf4j-api-1.7.25.jar ChatServer # Xbootclasspath选择外部包, 用':'分割多个jar
```

#### 3.3 运行jar
前提是要把字节码文件打包成jar包。
``` sh
# 打包jar, 默认MANIFEST.MF文件
jar cvf test.jar test1.class test2.class test3.class
# 打包jar, 指定MANIFEST.MF文件， 里面有Main-Class: cn.my.Main
jar cvfm test.jar --manifest MAINIFEST.MF -C foo/ . # foo是class文件根目录， '.'代表当前目录下？
# 运行jar包
java -jar xxx.jar
```
https://www.jb51.net/article/131101.htm

##### 3.3.1 使用-cp或-classpath
-cp和-classpath 一样，是指定类运行所依赖其他类的路径，通常是类库，jar包之类，需要全路径到jar包,window上";"分隔，linux上是":"分隔。不支持通配符，需要列出所有jar包，用"."代表当前路径。

``` bash
java -cp ..\lib\hsqldb.jar org.hsqldb.Server -database mydb   或  
java -cp ../lib/hsqldb.jar org.hsqldb.Server -database.0 mydb -dbname.0 mydb  
```
##### 3.3.2 使用MANIFEST.MF,在其中添加如下代码：
Class-Path: lib/some.jar

https://blog.csdn.net/qq_41338341/article/details/91337827
https://blog.csdn.net/w47_csdn/article/details/80254459
https://my.oschina.net/u/2971292/blog/2960777