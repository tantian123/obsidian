学习一个完整的知识
1.事件风暴 2.5w1h 3.提出问题用于复习思考 理清整个逻辑链路

为什么需要aop ：oop提供的方式无法消除横向的重复代码 aop可以**不修改原有业务**，而是**对所有业务生效**

aop是什么
spring aop是什么 如何实现的（就是通过动态代理实现的 JDK 动态代理和 CGLib 动态代理）
Aspect4J是什么 区别是运行时织入和编译期织入 springaop只能方法织入 而aspectj字段 方法构造函数
Spring使用AspectJ提供的库 但是并不依赖AspectJ的编译器或织入 使用Spring AOP来织入切面

日常开发中如何使用：
如果是使用springaop
定义一个切面 
定义切点写切入点表达式
定义通知
@Aspect 
@Pointcut
@Before @Around
弊端是什么
未来接手的人不知道这个方法被aop代理了

### AOP和循环依赖的那些事？

大家可能会遇到过或者听说过Spring的循环依赖的问题。Spring使用了“三级缓存”来解决Bean的循环依赖，但可能很多人不知道为什么要使用三级缓存，其实这个也跟AOP有关。

如果没有AOP，其实Spring使用二级缓存就可以解决循环依赖的问题。若使用二级缓存，在AOP情形下，注入到其他Bean的，不是最终的代理对象，而是原始目标对象。

因为Spring对Bean有一个生命周期的定义，而代理对象是在Bean初始化完成后，执行后置处理器的时候生成的。所以不能在二级缓存的时候就直接生成代理对象，放进缓存。


===================





是一种编程范式，最小的单元是切面，目的是解耦 
根本是解决OOP的缺陷：
将非核心逻辑和核心业务逻辑分离

实现aop框架
关注点和其对应的行为
实现一个机制来识别连接点，并在连接点上执行适当的通知，可以使用代理的方式


切面 连接点 通知 织入 

应用 Netflix Hystrix Alibaba Sentinel Spring Security JMX Spring Cache

主流 AOP 框架：
- AspectJ：完整的 AOP 实现框架
- Spring AOP：非完整的 AOP 实现框架

AspectJ是一个java实现的AOP框架


spring默认使用jdk动态代理 也可以使用cglib
jdk动态代理

cglib动态代理

###  Spring AOP 自动代理的实现、
AOP 底层的技术是动态代理，在Spring内实现依赖的是BeanPostProcessor
即织入的方式：
Spring IoC 中 Bean 的加载过程，在整个过程中，Bean 的实例化前和初始化后等生命周期阶段都提供了扩展点，会调用相应的 BeanPostProcessor 处理器对 Bean 进行处理。当我们开启了 AspectJ 自动代理（例如通过 `@EnableAspectJAutoProxy` 注解），则会往 IoC 容器中注册一个 `AbstractAutoProxyCreator` 自动代理对象，该对象实现了几种 BeanPostProcessor，例如在每个 Bean 初始化后会被调用，解析出当前 Spring 上下文中所有的 Advisor（会缓存），如果这个 Bean 需要进行代理，则会通过 JDK 动态代理或者 CGLIB 动态代理创建一个代理对象并返回，所以得到的这个 Bean 实际上是一个代理对象。这样一来，开发人员只需要配置好 AspectJ 相关信息，Spring 则会进行自动代理，和 Spring IoC 完美地整合在一起。



如何使用
```
@Aspect  
@Component  
public class LogAspect {  
    @Pointcut("@annotation(Loggable)")  
    public void printLog(){  
    }  
  
    @Before("printLog()")  
    public void beforeLog(){  
        System.out.println("before log");  
    }  
}

@Target(ElementType.METHOD)  
@Retention(RetentionPolicy.RUNTIME)  
public @interface Loggable {  
}
```