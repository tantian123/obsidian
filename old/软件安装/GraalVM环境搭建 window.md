总体而言，GraalVM也是一个JDK，基本上按照JDK的安装方法来就行，另外就是需要安装Visual Studio

1.下载  解压
[Download GraalVM](https://www.graalvm.org/downloads/)

2.增加环境变量
JAVA_GRAALVM_HOME   修改 JAVA_HOME的值为%JAVA_GRAALVM_HOME%
![[Pasted image 20240414155516.png]]
**JAVA_GRAALVM_HOME改短一点 如果你的路径过长可能会被截断，导致环境变量配置失效**
记得前移 因为有一个默认的配置 
![[Pasted image 20240414161929.png]]


3.测试 
 javac -version  
 java -version



3.下载native-image  
gu install native-image
（gu.exe在graalvm的安装目录下有，可能没有被加入环境变量，可以cd到对应的目录，然后install native-image）


4.下载c++环境（ **Visual Studio**和**Microsoft Visual C++ （MSVC）**）
[免费的开发人员软件和服务 - Visual Studio (microsoft.com)](https://visualstudio.microsoft.com/zh-hans/free-developer-offers/)
  配置Visual Studio环境变量


5.编译Java代码为原生应用
```
打开x64 native tools
找一个HelloWorld.java
javac HelloWorld.java
native-image HelloWorld.java
编译完成后，当前目录下会生成helloworld.exe文件
```

