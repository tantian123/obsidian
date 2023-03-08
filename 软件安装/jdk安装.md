### 下载
```
wget https://repo.huaweicloud.com/java/jdk/8u201-b09/jdk-8u201-linux-x64.tar.gz
tar -zxvf jdk-8u201-linux-x64.tar.gz
mv jdk1.8.0_201 /usr/local/jdk1.8/
```

### 配置环境变量
```
vim /etc/profile

按下insert键，然后移动到最后一行，添加如下语句

export JAVA_HOME=/usr/local/jdk1.8/ 
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar 
export PATH=$JAVA_HOME/bin:$PATH
```

### 更新配置
```
source /etc/profile
```

### 查看结果
```
java -version
```

