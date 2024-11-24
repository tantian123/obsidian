1.基本概念，它是什么
2.好处是什么
3.衍生的概念 SAM invokedynamic
4.底层实现原理 字节码层面讲讲(从创建->编译为invokedynamic指令 ->通过LambdaMetafactory生成方法实例)



作用：在不显式创建类的情况下传递函数,**是对函数式编程的支持**
本质上就是个匿名函数


实现原理：SAM 接口（Single Abstract Method interface）和 invokedynamic 指令

Single Abstract Method（SAM）接口是指只包含一个抽象方法的接口(**Lambda 表达式可以隐式地转换为 SAM 接口的实例**)
在 Java 8 之前，如果需要使用函数式编程风格，需要通过创建匿名内部类的方式来实现接口中的抽象方法
```
// `MyFunction` 接口是一个 SAM 接口，因为它只包含一个抽象方法
@FunctionalInterface
interface MyFunction {
    void doSomething();
}

public class Main {
    public static void main(String[] args) {
        // 使用 Lambda 表达式创建 SAM 接口的实例
        MyFunction function = () -> System.out.println("Doing something...");

        // 调用 SAM 接口的方法
        function.doSomething();
    }
}

```


invokedynamic 指令允许在运行时**动态地绑定方法调用**
（相比传统的静态绑定方式（比如 `invokevirtual` 指令），`invokedynamic` 允许在运行时才决定要调用的方法）

在Lambda表达式被调用的时候，会使用invokedynamic指令来动态地绑定这个静态方法。在绑定的过程中，会创建一个调用点限定符(CallSite)，然后在运行时绑定一个适当的方法句柄(Method Handle)到这个调用点限定符
执行过程：
1.编译器将 Lambda 表达式转换为函数式接口的实例
2.在运行时，invokedynamic 指令根据 Lambda 表达式的目标类型和参数来动态创建实现该目标类型的类的实例
3.创建的实例实际上是一个内部类的实例，它实现了函数式接口的抽象方法，并在该方法中执行 Lambda 表达式的代码。
4.总结一下就是创建内部类的实例 使用invokedynamic执行来调用实例的方法


生成函数式接口实例的具体步骤：
LambdaMetafactory` 是 Java 标准库中的一个类，它是用于创建 Lambda 表达式的工厂。它提供了一个 `metafactory` 方法，可以根据传入的方法句柄（MethodHandle）、目标函数式接口描述符和调用方法（invokedynamic）的方法类型来生成实现了目标函数式接口的 Lambda 表达式的实例

`invokedynamic` 指令在运行时调用 Bootstrap 方法，传递 Lambda 表达式的方法句柄、目标函数式接口描述符等参数，并接收 Bootstrap 方法返回的 CallSite 对象
`invokedynamic` 指令在运行时解析 CallSite 对象，获取目标函数式接口的实例，并将 Lambda 表达式转换为该实例
`
看字节码
```
0: invokedynamic #2, 0

#2表示
表示常量池中引导方法（Bootstrap Method）的索引。Bootstrap Method 是一个静态方法，它的作用是在运行时决定如何绑定方法调用
0表示方法类型（Method Type）的索引。方法类型描述了被调用方法的参数类型和返回类型

```