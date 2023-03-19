
![[Pasted image 20230317233347.png]]

引入了一个Gateway的概念，使用DIP（Dependency Inversion Principle，依赖倒置）反转了Domain层和Infrastructure层的依赖关系，这样的话，Domain层会变得更加纯粹，完全摆脱了对技术细节（以及技术细节带来的复杂度）的依赖，只需要安心处理业务逻辑就好了。

其实现方式如下图所示，主要是在Domain层定义Gateway接口，然后在Infrastructure提供Gateway接口的实现，app层直接调用gateway的接口，从而实现解耦。我们在修改数据源时，只用修改gateway的具体实现类gatewayimpl，不用修改具体的app层的业务逻辑

![[Pasted image 20230318001149.png]]

### 分包策略调整
**更好的分包策略是按领域划分，而不是按功能。因为，领域更内聚，功能是为领域服务的，应该归属于领域。**

先按领域分包 再按功能分包

![[Pasted image 20230318000729.png]]

![[Pasted image 20230318000744.png]]

在每一个module下面首先按照领域做一个顶层划分，然后在领域内，再按照功能进行分包





### 用适配层Adatper代替Controller

![[Pasted image 20230318001007.png]]
![[Pasted image 20230318151530.png]]

![[Pasted image 20230318151536.png]]

![[Pasted image 20230318151542.png]]






cqrs 读写分离

在cola架构中，cmd负责修改，query负责查询



依赖关系
-   基础设施层依赖领域层和应用层；
-   领域层不依赖其他层；
-   应用层依赖领域层和基础设施层；
-   适配层依赖应用层和基础设施层。


![[Pasted image 20230318151449.png]]
![[Pasted image 20230318151459.png]]
