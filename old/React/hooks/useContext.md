它是干嘛的
多层嵌套的时候 传递变量

如何使用
1.创建context
```
const UserContext = React.createContext('')
```
2.提供Provider给根节点
```
<UserContext.Provider value={'tt'}> <ComponentC /> </UserContext.Provider>
```
3.子孙节点 消费context
```
<UserContext.Consumer> { 
(user) => 
( <div> User context value {user} </div> ) } 
</UserContext.Consumer>

```
使用useContext简化 不用Consumer
```
const value = useContext(MyContext)
return <div>{value}</div>
```