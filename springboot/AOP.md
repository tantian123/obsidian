属性 方法 代码块 批量进行功能的增强，减少代码量

重复的代码->抽成方法->隐藏重复的调用


实现方式：代理模式


替换的时机叫做连接点JoinPoint
需要被增强的行为叫做切点PointCut


动态代理AOP（cglib和jdkproxy）和静态代理aspectJ
springboot默认使用cglib进行

应用场景：性能统计，事务处理，缓存层，统一异常处理、日志、权限控制等