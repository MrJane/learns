## Redux
Redux is a predictable state container for JavaScript apps.
Redux是一个可以预测状态的JS app容器。

通过例子来学习Redux：
下载https://github.com/reactjs/redux/
然后打开\examples\counter-vanilla

The whole state of your app is stored in an object tree inside a single store.
你的App中全部的state都被存储在一个单独的store中，形式是object tree。

The only way to change the state tree is to emit an action , an object describing what happened
唯一的更改state的途径就行emit一个action，这个action描述了发什么什么事。

To specify how the actions transform the state tree, you write pure reducers
为了指定这些actions如何改变state tree，你必须书写简单的、纯净的reducers。
##三、React-Redux
###3.1 不使用React-Redux遇见的灾难
刚才我们学习了Redux，已经基本知道Redux怎么玩儿了，大致步骤：
设置一个reducer，
弄一个store，从Redux.createStore(reducer);
弄一个render函数
注册一下render，store.subscibe(render)
写监听了，此时要记得store.dispatch(action)，不是直接改store。
此时和React还没有直接产生关系，换句话说在React中没有使用Redux技术
###我们现在看一下counter这个案例：
```jsx harmony
import React from 'react';
import ReactDom from 'react-dom';
import counter from './counter';
import Counter from './MyCounter'
import {createStore} from 'redux';

const rootEl = document.getElementById('root')
let store = createStore(counter);
const render = () => ReactDom.render(
    <Counter
        value={store.getState().n}
        increment ={()=>{store.dispatch({type:'INCREMENT'})}}
        decrement={()=>{store.dispatch({type:'DECREMENT'})}}
    ></Counter>,
    rootEl
);
render();
store.subscribe(render);
```
这个案例纯属霸王硬上弓，就是我们现在通过标签的属性，将store的值，和对store的操作传给了组件！这太二了！
```jsx harmony
const render = () => {ReactDOM.render(
  <Counter
	  value={store.getState()}
	  onIncrement={() => store.dispatch({ type: 'INCREMENT' })}
	  onDecrement={() => store.dispatch({ type: 'DECREMENT' })}
  />,
  rootEl
)};

```
那次此时灾难是什么？比如一个组件内部要多store有10种操作，你还传10个属性下去啊？再比如，组件内部还有组件要操作store，你还一直往下传啊？

所以我们使用一个粘合剂，要优雅的把react和redux合并起来，这个包就叫做react-redux。
react-redux可以让任何组件在任何地方看见store，机理实际上就是昨天学习的context。但是我们用的时候和context无关，人家帮我们写好了。

此时我们怎么学习react-redux，还是老方法，结合官方文档看官方demo。
我们要攻克todomvc这个官方demo。
###3.2 结合官方文档看官方demo(todomvc)
React-Redux给我们提供了两个东西：Provider组件、connect函数。
Provider组件要求是最大的组件，传入store属性，此时天下无人不识君。
官方文档：https://github.com/reactjs/react-redux/tree/master/docs


Provider自定义组件：
Makes the Redux store available to the connect() calls in the component hierarchy below。
这个Provider组件使得它内部的自定义组件可以使用connect()函数。

Normally, you can’t use connect() without wrapping a parent or ancestor component in <Provider>
通常的，你不能在没有Provider父亲或者组件的情况下，使用connect()函数。

属性store (Redux Store): APP中唯一的那个store
```jsx harmony
import React, { PropTypes } from 'react'
import { bindActionCreators } from 'redux'
import { connect } from 'react-redux'
import Header from '../components/Header'
import MainSection from '../components/MainSection'
import * as TodoActions from '../actions'

const App = ({ todos , actions }) => (
  <div>
    {JSON.stringify(todos)}
    <Header addTodo={actions.addTodo} />
    <MainSection todos={todos} actions={actions} />
  </div>
)
App.propTypes = {
  todos: PropTypes.array.isRequired,
  actions: PropTypes.object.isRequired
}

const mapStateToProps = state => ({
  todos: state.todos
})
const mapDispatchToProps = dispatch => ({
    actions: bindActionCreators(TodoActions, dispatch)
})
export default connect(
  mapStateToProps,
  mapDispatchToProps
)(App)

```
