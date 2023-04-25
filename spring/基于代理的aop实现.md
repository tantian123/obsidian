1.使用@Aspect注解定义一个切面类
2.使用@PointCut定义一个切点，规则表达式
3.使用@Before @After @Around 注解，针对切点，切入指定内容

环绕通知Around接受ProceedingJoinPoint作为参数，它来调用被通知的方法。通知方法中可以做任何的事情，当要将控制权交给被通知的方法时，需要调用ProceedingJoinPoint的proceed()方法。当你不调用proceed()方法时，将会阻塞被通知方法的访问。



```
<!--aop 切面--> 
<dependency> 
<groupId>org.springframework.boot</groupId> 
<artifactId>spring-boot-starter-aop</artifactId> 
</dependency>
```
```
@Aspect  
@Component  
public class MyAspect {  
    @Pointcut("@annotation(NoticeTT)")  
    private void notice(){  
    }  
  
    @Around("notice()")  
    private Object doAround(ProceedingJoinPoint pjp) throws Throwable {  
        System.out.println("start===================");  
        Object proceed = pjp.proceed();  
        System.out.println("end===================");  
        return proceed;  
    }  
}
```
```
@Retention(RetentionPolicy.RUNTIME)  
@Target(ElementType.METHOD)  
public @interface NoticeTT {  
    String desc() default "";  
}
```
```
@RestController  
public class AopController {  
  
    @RequestMapping("/hello")  
    @NoticeTT(desc = "tt")  
    public String sayHello(){  
        System.out.println("hello");  
        return "hello";  
    }  
}
```

