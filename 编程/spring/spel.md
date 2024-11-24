为了实现SpEL, 我们需要如下几个模型:
- LogRootObject: 运行数据来源
- LogEvaluationContext: 解析上下文, 用于整个解析环境 (Spring提供了MethodBasedEvaluationContext, 我们只需要继承它, 并实现对应的构造方法)
- LogEvaluator: 解析器, 解析SpEL, 获取数据(我们需要初始化表达式编译器/表达式编译模板/参数解析器)
