理解此概念需要的基础知识：web编程的概念，servlet是什么，mvc框架
关于此概念的一个现实的比喻 不用学了
如何使用这个概念： 不用学了
如何实现这个：不用学了

是一个MVC框架，model view controller 模型视图控制器


controller这一层
本质上就是一个servlet，通过封装来屏蔽使用的繁琐的细节

springmvc的使用流程
1.客户端请求 ，DispatcherServlet，它会拦截所有请求，根据请求通过mapper找到对应的处理方法来处理
2.对应的controller处理完了后将数据放在modelAndView中并返回modelAndView
3.渲染并将html通过http返回

现在用不上view这一层 基本上都是返回json给前端处理



