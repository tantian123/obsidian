关键词 BeanFactory ApplicationContext BeanDefinition

容器 BeanFactory ApplicationContext

BeanFactory
可以理解为就是个 HashMap，Key 是 BeanName，Value 是 Bean 实例
只负载加载 Bean，获取 Bean

ClassPathXmlApplicationContext 高级容器


1.  低级容器 加载配置文件（从 XML，数据库，Applet），并解析成 BeanDefinition 到低级容器中。
2.  加载成功后，高级容器启动高级功能，例如接口回调，监听器，自动实例化单例，发布事件等等功能。