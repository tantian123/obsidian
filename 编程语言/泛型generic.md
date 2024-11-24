
#### 多态
参数化多态 即泛型
子类型多态 即子类型多态（区别**子类型**和**继承**这两者之间是不一样的，subtype和subclass的区别，type重点是类型关系，class的重点是代码复用）
特设多态 即函数重载 运算符重载 或者是强制转换
行多态（类型只跟结构有关跟类型的名称无关）：处理结构化类型的多态 很像ducktyping 被称作类型安全的鸭子类型
#### 泛型 是实现多态的方式之一
![[Pasted image 20231011221903.png]]
例如有一个数据结构例如List,我们想要其能对多种数据类型生效
有两个思路 1.把所有类型包装为同一个包装类型 2.对代码进行复制  每种类型一份拷贝
前者是装箱 后者是单态化
装箱需要更多的内存分配和类型检查 单态化编译时间增加 但是运行更快

单态化的语言有C和Go
#### java泛型
java是通过类型擦除+虚函数表来实现多态的
在编译期间 进行类型擦除 转化为Object类型 只需要保存一份函数的副本 保证了其对历史代码的兼容性 但是这样也有许多限制（看后面）



#### 为什么说java是静态多分派和动态单分派的语言（和泛型无关 暂时放这里）
1.什么是分派调用Dispatch
在java中方法调用分为解析调用和分派调用
解析调用：
**在类加载的解析阶段**，符号引用转为直接引用 前提是这个方法在编译期间是确定的 在运行期间是不变的。符合这个条件的主要有**静态方法和私有方法** 
前者和类型有关 后者无法重写 所以都是确定的


分派调用：
在运行时才能确定调用方法的版本
**重载和重写**就是其中

注意不要混淆 **解析和静态分配**
静态方法会在类加载期进行解析 但是静态方法也可能会进行重载


2.什么是静态类型和动态类型
静态类型在编译期可知，而动态类型只有实际运行时能够获知。
Javac编译器是通过参数静态类型作为重载的判定依据


3.什么是静态分派和动态分派
所有依赖静态类型来定位方法执行版本的分派动作就是静态分派
动态分派就是依据动态类型
```
Object str=new String(); 这个例子的静态类型是Object 动态类型是String
```


静态方法会在类加载期进行解析，但是静态方法也可能有多个重载版本，这就是静态分派了

4.单分派与多分派
方法的接受者与方法的参数统称为方法的 **宗量**
单分派和多分派，顾名思义，根据一个宗量来对目标方法选择就是单分派，多于一个，则是多分派

```
还是fruit的例子

class  Fruit{}  
class  Apple extends Fruit{}  
class  Banana extends Fruit{}  
  
class  Person{  
    public void eat(Fruit f){System.out.println("print people eat fruit");}  
    public void eat(Apple f){System.out.println("print people eat apple");}  
    public void eat(Banana f){System.out.println("print people eat banana");}  
}  
class Boy extends Person{  
    public void eat(Fruit f){System.out.println("print boy eat fruit");}  
    public void eat(Apple f){System.out.println("print boy eat apple");}  
    public void eat(Banana f){System.out.println("print boy eat banana");}  
}

main(){
Person person = new Person();  
Person boy = new Boy();  
person.eat(new Apple());  
boy.eat(new Banana());  

Fruit banana = new Banana();  
boy.eat(banana);
}


结果是person eat apple
boy eat banana
boy eat fruit

我们看前两个结果  可以知道编译器如何进行重载的判断
依据是方法的接受者类型是Person还是Boy  参数的静态类型是Apple还是Banana
根据两个宗量进行选择，所以Java的静态分派是多宗量分派
如此，我们就确定了是哪个类型调用的哪个方法
静态类型Person并不会对方法的选择造成影响 此时它仅仅依据接受者这一宗量 所以是java的动态分派是单分派

看第三个结果 因为重载是编译器进行的 是根据静态类型的 所以接受者是boy 参数的静态类型是fruit

```



##### 虚拟机动态分派的实现
重载是编译器进行的 重写是JVM进行的
java代码被编译为字节码之后，方法的调用是使用符号引用来表示的
jvm加载字节码后，需要把符号引用转为实际的内存地址（直接引用），
会有一个问题，如何确定重载重写的方法是属于谁的？

Object obj=new String("");这个对象再编译期间 我们只知道它是Object类型的 
此时重载是静态分派的 是编译器进行处理的 所以调用的是父类的方法
但是对于重写，进行的是动态分派 由JVM进行操作，使用的是虚函数表Vtable派发
子类如果重写父类函数 会将存储类成员函数的指针 加入这个表中

有一个例子
```
class Fruit{}
class Apple extends Fruit{}
class Banana extends Fruit{}

class person{
public void eat(Fruit f){print people eat fruit};
public void eat(Apple f){print people eat apple};
public void eat(Banana f){print people eat banana};
}
class boy extends person{
public void eat(Fruit f){print boy eat fruit};
public void eat(Apple f){print boy eat apple};
public void eat(Banana f){print boy eat banana};
}

main(){
Person boy=new Boy();
Fruit apple=new Apple();
Fruit banana=new Banana();

boy.eat(apple);
boy.eat(banana);
}
返回结果是
boy eat fruit
boy eat fruit
```
思考过程：
因为重载是编译器进行的 此时只知道apple对象是Fruit类型（也就是根据静态类型） 所以调用的是eat(Fruit)
子类的方法重写是运行时进行的 所以知道boy对象是Boy类型 调用的也就是Boy类型的方法了









===============

为什么说java没有真正的泛型  
泛型不存在于运行时，泛型被擦除后在字节码里只保留泛型的原始类型
java这样做是为了兼容历史
带来的局限性：
1. 不能用基本类型实例化类型参数
因为泛型类型变量只能是引用类型，不能是 Java 中的 8 种基本类型（`char`、`byte`、`short`、`int`、`long`、`boolean`、`float`、`double`）。因为Object 类型不能存储 int 类型的值，只能存储引用类型 Integer 的值
2. 运行时类型查询只适用于原始类型
泛型对象使用 instanceof 进行类型判断的时候就不能使用具体的类型，而只能使用通配符`？`
```
List<String> list=new ArrayList<String>();
if( list instanceof ArrayList<String>) {} //编译错误
if( list instanceof ArrayList<?>) {} //正确的使用方法
```
2. 泛型类的静态上下文中类型变量无效 也是因为类型擦除
总结：**本质都是同一个问题：虚拟机并不知道你传入的类型是什么**。
其他语言例如go的泛型机制就是依靠编译器生成多份代码

============================================
关于哪种泛型的实现最好
为什么需要类型系统->通过类型检查 减少犯错