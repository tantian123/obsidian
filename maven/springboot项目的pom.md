

基于 maven 的 Spring Boot 项目，我们通常会指定 `spring-boot-starter-parent` 作为当前项目 pom 的 parent，maven 提供了类似 Java 的继承机制，只要将依赖加入到 parent，继承 parent 的项目会自动引入 parent 中的依赖

```
<parent>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-parent</artifactId>  
        <version>2.5.0</version>  
</parent>
指定`spring-boot-starter-parent` 为父项目
```