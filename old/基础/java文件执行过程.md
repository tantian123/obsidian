编译 加载 解释 执行
加载是类加载过程的第一个阶段，在加载阶段，虚拟机需要完成以下三件事情：

> ① 通过一个类的全限定，名来获取其定义的二进制字节流。  
> ② 将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构。  
> ③ 在Java堆中生成一个代表这个类的java.lang.Class对象，作为对方法区中这些数据的访问入口。
加载阶段完成后，虚拟机外部的 二进制字节流就按照虚拟机所需的格式存储在方法区之中，而且在Java堆中也创建一个java.lang.Class类的对象，这样便可以通过该对象访问方法区中的这些数据。
  
验证：确保被加载的类的正确性
准备：为类的静态变量分配内存，并将其初始化为默认值
解析：**把类中的符号引用转换为直接引用**


java文件编译为class字节码文件
JVM加载class文件 
类加载完成之后，会进行字节码校验，字节码校验通过之后JVM解释器会把字节码翻译成机器码交由操作系统执行

这里具体看jvm那章（JVM 内部可以分为四大部分，运行时数据区域、类加载系统、执行引擎、本地接口和本地方法库）


### 和其他语言的区别 c python golang
解释性和编译性的区别
编译型语言在程序执行之前，先会通过编译器对程序执行一个编译的过程，把程序转变成机器语言。运行时就不需要翻译，而直接执行就可以了。最典型的例子就是C语言。
解释型语言就没有这个编译的过程，而是在程序运行的时候，通过解释器对程序逐行作出解释，然后直接运行，最典型的例子是Ruby。

  
  


Java是一种[解释型语言](https://www.zhihu.com/search?q=%E8%A7%A3%E9%87%8A%E5%9E%8B%E8%AF%AD%E8%A8%80&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22article%22%2C%22sourceId%22%3A%22608601460%22%7D)，它的源代码需要先被编译成字节码文件（.class），然后再由Java虚拟机（JVM）[解释执行]
C++是一种[编译型语言](https://www.zhihu.com/search?q=%E7%BC%96%E8%AF%91%E5%9E%8B%E8%AF%AD%E8%A8%80&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22article%22%2C%22sourceId%22%3A%22608601460%22%7D)，它的源代码需要先被编译成目标代码（二进制可执行文件，然后再由操作系统加载和执行
Python解释器把源代码转换成中间形式：字节码，然后再由Python虚拟机来解释执行这些字节码。

c和java的编译和执行是分开的两个过程 所以运行起来快 反之python慢

从三种不同语言的代码执行的整个过程，看出C/C++是一种编译型语言，而java这是一种先编译后解释的语言。
简单来说，C/C++的执行过程是编译—>连接—>运行，java的执行过程是编译—>交给虚拟机—>运行，而python则是解释—>运行。


### jar包
jar包 war包都是文件的压缩包
war包是web应用的压缩包 jar包是java应用的压缩包 

### 如何打包
在src目录下 执行javac 生成每个java文件对应的字节码文件
jar -cvf my.jar class文件路径 对class文件进行打包 生成jar包
java -jar my.jar 执行jar包
此时jar包会以二进制流的形式读到内存，随后在合适的时机加载到jvm中

通过maven进行打包
maven package 打包
maven install 打包 安装到本地仓库

### springboot打包
```
<build>  
    <plugins>  
        <plugin>  
            <groupId>org.springframework.boot</groupId>  
            <artifactId>spring-boot-maven-plugin</artifactId>  
        </plugin>  
    </plugins>  
</build>
```
当你使用 java -jar 命令来运行 Spring Boot JAR 包时，实际上是通过 jarlauncher 来启动应用程序。
位于 org.springframework.boot.loader.JarLauncher 包中。


### springboot打的jar包和其他有什么区别  
SpringBoot 的可执行jar包又称fat jar ，fat jar中主要增加了两部分，第一部分是lib目录，存放的是Maven依赖的jar包文件，jar 包中嵌入了除 java 虚拟机以外的所有依赖,第二部分是spring boot loader相关的类。在 \BOOT-INF\classes 目录下才是我们的代码，因此无法被直接引用