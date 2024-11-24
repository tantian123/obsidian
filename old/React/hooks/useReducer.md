干嘛的
用于状态管理的 hook api

怎么用


原生 JavaScript的reduce() 方法吗，对数组中的每个元素执行一个由您提供的 reducer 函数(升序执行)，将其结果汇总为单个返回值。
```
`useReducer(reducer, initialState)` 接受2个参数，分别为 reducer 函数 和 初始状态
`reducer(currentState, action)` 也是接受2个参数，分别为当前状态state和 action，返回一个 new state
```
```
const initialValue = { count: 0 };

const [state, dispatch] = useReducer(Reducer, initialValue);

function Reducer(state, action) {
    switch (action.type) {
        case 'add':
            return {
                ...state, count: state.count + 1
            };
        case 'sub':
            return {
                ...state, count: state.count - 1
            };
        default:
            return state;
    }
}
```


和usestate的区别
-   如果 state 的类型为 Number, String, Boolean 建议使用 useState，如果 state 的类型 为 Object 或 Array，建议使用 useReducer
-   如果 state 变化非常多，也是建议使用 useReducer，集中管理 state 变化，便于维护
-   如果 state 关联变化，建议使用 useReducer
-   业务逻辑如果很复杂，也建议使用 useReducer
-   如果 state 只想用在 组件内部，建议使用 useState，如果想维护全局 state 建议使用 useReducer

