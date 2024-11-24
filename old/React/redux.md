为什么不用一个全局变量替代redux 
因为一个全局变量在改变之后无法驱动React组件重新渲染，
Redux本质上也是管理一个单例的全局变量 只不过暴露了一堆API来操作它





一句话概括 redux ：dispatch一个action然后被reducer处理 得到新的state 通知订阅者并rerender


![[Pasted image 20230522215711.png]]
对于 Store 的修改都必须通过Reducer完成，而不是直接修改 Store。

State即Store， 是一个js object
Action 用于描述发生的动作
Reducer是一个函数 接收Action和State作为参数 计算得到new Store

通过Reducer去改变state而不是直接修改

```
1.定义Action和Reducer

2.index.js 引入React-Redux，用Provider组件将store 提供给整个应用

3.定义一个counter组件，显示当前的计数值，并且包含两个按钮来增加和减少计数值
该组件通过`connect`函数连接到Redux store，这样它就可以通过props获取到state和dispatch函数，然后在组件中使用它们

点击"Add"按钮时，组件会dispatch一个add action，这个action会被送到Redux store，并且被`counterReducer`处理。`counterReducer`会根据这个action的类型来更新state，因为这个action的类型是`ADD`，所以它会将count的值增加1
```

Redux的工作流程可以概括为：`dispatch action -> action被送到store -> reducer处理action并更新state -> state的更新反映到UI上`。




react-redux
react-redux是一个react插件库,专门用来简化react应用中使用redux。他是从redux封装而来，因此基本原理和redux是一样的
React-Redux 将所有组件分成两大类：UI 组件和容器组件
React-Redux 规定，所有的 UI 组件都由用户提供，容器组件则是由 React-Redux 自动生成(由connect()函数生成)。也就是说，用户负责视觉层，状态管理则是全部交给它
这些功能的实现得益于四个react-redux的API:provider、connect、mapStateToprops、mapDispatchToProps
![[Pasted image 20230522225440.png]]

```
//APP组件被包裹后，所有子组件都可以得到state数据 <Provider store={store}> <App /> </Provider>
```

```
import { connect } from 'react-redux' 
connect( mapStateToprops, mapDispatchToProps )(OurComponent)

const mapStateToProps = (state) => { return { todos: "todo value" } }

const mapDispatchToProps = ( dispatch, ownProps ) => { return { onClick: () => { dispatch({ type: 'SET_VISIBILITY_FILTER', filter: ownProps.filter }); } }; }

  


provider传入store，所有子组件都能拿到state，那具体怎么拿呢？就需要用到connect，它用于包装 UI 组件生成容器组件。connect包裹后生成了一个容器组件，connect方法接受两个参数：mapStateToProps和mapDispatchToProps。它们定义了 UI 组件的业务逻辑。前者负责输入逻辑，即将state映射到 UI 组件的参数（props），后者负责输出逻辑，即将用户对 UI 组件的操作映射成 Actio,此时就可以与store交互了。


```

使用hooks简化代码

