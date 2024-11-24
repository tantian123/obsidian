事件机制是基于观察者模式的实现



组成：事件源 事件对象 监听者
分别对应spring中的
实现`ApplicationEventPublisher` 发布事件
继承`ApplicationEvent`类  事件对象
实现 `ApplicationListener<E>`定义事件监听器


### 1.定义事件
```
@Getter   
public class TestEvent extends ApplicationEvent {  
  
    private String eventName;  
  
    private String eventContent;  
  
    public TestEvent(Object source,String eventName,String eventContent) {  
        super(source);  
        this.eventName = eventName;  
        this.eventContent = eventContent;  
    }  
      
}
```
### 2.实现监听器有两种方法
1.新建一个类实现ApplicationListener接口，并且重写onApplicationEvent方法。注入到Spring容器中
2.使用 @EventListener 注解标注处理事件的方法

```
@Component  
public class TestListener implements ApplicationListener<TestEvent> {  
    @Override  
    public void onApplicationEvent(TestEvent testEvent) {  
    //拿到参数 执行操作
    }  
}
```
### 3.发布事件
1.直接注入我们的ApplicationEventPublisher，使用@Autowired注入一下
2.直接使用`ApplicationContext`来发布事件，而不必单独定义一个`ApplicationEventPublisher`发布类
```
public class TestMyEvent {  
  
    @Autowired  
    private TestPublisher testPublisher;  
  
    @Test  
    void testMyEvent() {  
        testPublisher.publishEvent(new TestEvent(this,"test","testMyEvent"));  
    }  
}



@Component  
public class TestPublisher implements ApplicationEventPublisher {  
    @Override  
    public void publishEvent(Object o) {  
       
    }  
}
```



### @Async注解
监听器默认是同步执行的，如果我们想实现异步执行，可以搭配@Async注解使用
**使用@Async时，需要配置线程池，否则用的还是默认的线程池也就是主线程池，线程池使用不当会浪费资源，严重的会出现OOM事故**

1.在我们的启动类上添加@EnableAsync开启异步执行配置
2.在我们想要异步执行的监听器上添加@Async注解



### 原理
在refresh方法中，有这样两个方法，initApplicationEventMulticaster()和registerListeners()


实战
我们需要在完成某个业务之后，发送短信给客户，消息内容是客户id和消息内容



