控制反转 ​**是设计模式**   将对象的控制权交给外部容器
DI 是**实现方式**
DIP依赖倒置原则 是**设计原则** 高层不依赖于底层，二者同时依赖于抽象

控制反转的实现方式有依赖注入DI和依赖查找

```
是什么 控制反转 将对象的控制权反转给对象的容器 而不是程序
为什么 解耦解耦解耦解耦  程序不应该关注对象的创建等过程
怎么使用 在spring中通常是通过DI的方式实现IOC

是什么 依赖注入 通过将对象注入程序
为什么 体现了DIP依赖导致原则 顶层不依赖于底层 而是同时依赖于抽象 减少耦合
怎么使用 构造器注入 setter方法注入 字段注入 @Autowired

更多：反转的是程序的执行流
注入bean和new对象的区别？new对象可能并不是一个简单的过程，它可能是全局对象，可能有其他复杂的依赖，使用该对象的程序不应该关注和实现这些

解耦的好处也有更好做单元测试UT


举例子 如果不使用DI会怎么样？ 任何语言都逃不掉管理依赖 如何解耦？
如何处理依赖？ 使用全局变量或者传递函数(将依赖项抽象为函数参数) 
依赖注入也可以手动完成，而根本不需要DI framework。所以任何支持subtyping或者first class function的语言都完全可以做dependency injection

import就是最简单的依赖注入
在Java中，由于语言的限制，import只能引入无法直接使用的Type，却不能引入已经初始化好的Object，只能import静态的

联想 
模板模式也体现了控制反转，父类制定了框架即控制流的走向，而子类实现抽象方法


为什么spring的@Autowired不能用在函数参数上 类似RequestParam
```



最佳实践：
构造函数注入：
`final` 字段加 `@RequiredArgsConstructor`(自动生成带有 `final` 字段的构造函数) ，
它确保了类的依赖关系在创建时就被确定，提高了代码的**安全**性（保证依赖不为空 保证依赖不可变 避免循环依赖），**方便测试**
```
@Component
public class MyService {
    @Autowired
    private MyDependency myDependency;
}
@Component
public class MyService {
    private final MyDependency myDependency;

    @Autowired
    public MyService(MyDependency myDependency) {
        this.myDependency = myDependency;
    }
}

```
字段注入：
`@Autowired` 字段注入 ，依赖项可能为 `null` 这是缺点