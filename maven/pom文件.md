完整的pom文件
重要的有properties dependencies modules

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example.project</groupId>
    <artifactId>project-parent</artifactId>
    <version>1.0.0</version>
    <packaging>pom</packaging>

    <name>Project Parent</name>
    <description>A parent POM for all modules in the project</description>

    <properties>
        <!-- Define common project properties here -->
        <java.version>11</java.version>
        <spring-boot.version>2.5.0</spring-boot.version>
    </properties>

    <modules>
        <module>module1</module>
        <module>module2</module>
    </modules>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <!-- Define build plugins here -->
        </plugins>
    </build>

    <repositories>
        <!-- Define repositories here -->
    </repositories>

    <distributionManagement>
        <!-- Define deployment targets here -->
    </distributionManagement>

    <profiles>
        <profile>
            <id>development</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <!-- Define profile-specific properties here -->
                <spring.profiles.active>dev</spring.profiles.active>
            </properties>
        </profile>
        <profile>
            <id>production</id>
            <properties>
                <!-- Define profile-specific properties here -->
                <spring.profiles.active>prod</spring.profiles.active>
            </properties>
        </profile>
    </profiles>
</project>

```


```
<properties>，声明一些属性，可以在文件的其他位置引用，通过${project.version} 

<properties>
    <spring.version>5.2.8.RELEASE</spring.version>
</properties>

...

<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>${spring.version}</version>
    </dependency>
    ...
</dependencies>

```

```
`<dependencies>` 元素用于定义项目所依赖的第三方库或者其他模块

每个 `<dependency>` 元素通常包含以下信息：

-   `groupId`：依赖库的组织ID。
-   `artifactId`：依赖库的唯一标识符。
-   `version`：依赖库的版本号。
-   `scope`：依赖库的作用范围（compile、test、runtime、provided、system）。
-   `optional`：指示该依赖是否为可选的。
-   `type`：指定依赖的类型（例如 jar、war、pom）。
-   `exclusions`：排除传递依赖关系。
- 
dependencyManagement里只是声明依赖，并不实现引入
在dependencyManagement元素中声明所依赖的jar包的版本号等信息，那么所有子项目再次引入此依赖jar包时则无需显式的列出版本号。Maven会沿着父子层级向上寻找拥有dependencyManagement 元素的项目，然后使用它指定的版本号。

区别：
-   在父项目中的dependencies中定义的所有依赖，在子项目中都会直接继承，
-   在父项目中的dependencyManagement中定义的所有依赖，子项目并不会继承，我们还要在子项目中引入我们需要的依赖，才能进行使用，但是，我们在子项目中不用设置版本
- 
例如：父项目
   <modules>
        <module>module1</module>
    </modules>
    <properties>
            <spring-version>3.1.1.RELEASE</spring-version>
    </properties>

    <dependencyManagement>
          <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-web</artifactId>
                <version>${spring-version}</version>
          </dependency>
    </dependencyManagement>
子项目
   <dependencies>
            <dependency>
                  <groupId>org.springframework</groupId>
                  <artifactId>spring-web</artifactId>
            </dependency>
    </dependencies>

```



```

```
