##复习
###一、Redux深入
> 我们自己试着实现一个变量私有化、可预测变量状态的小东西，我们要私有化一个a，让a可以被预测： 
> 1）正着说：所有对a的操作，都要罗列出来；
> 2）反着说：对a操作只能通过我们罗列的函数。

所以我们利用了JS中的闭包特性，在一个大函数的里面定义一个a变量，这个大函数返回一个对象，
这个对象以KV对儿的形式，返回了一组对闭包a操作的函数，我们叫做API：
```
function createStore(){
    var a = 100;   //这就相当于state
    return {		//这就相当于reducer
        getValue : function(){
            return a;
        },
        add : function(){
            a++;
        },
        minus : function(){
            a--;
        }
    }
}

```
接下来就能使用了，我们发现**要想读取a、操作a必须使用我们提供的API：**
```
var store = createStore();
console.log(store.getValue()); //读取
store.add(); //加1
store.add();
store.add();
store.add();
console.log(store.getValue());
//a是绝对不可能被乘2的，这就叫做可以预测状态。
```
下面就是完整的Redux版本的计数器：
```
<script>
        //这里就是一个reducer，函数头(state,action)=>state
        function reducer(state = {"v" : 100} , action){
            if(action.type == "ADD"){
                return {
                    ...state,
                    "v" : state.v + 1
                }
            }else if (action.type == "MINUS") {
                return {
                    ...state,
                    "v": state.v - 1
                }
            }
            return state;
        }

        const store = Redux.createStore(reducer);

        //视图函数
        //在纯Redux中，这里显式地写了一个render函数，这样的话我们点击按钮的时候只需要发送action改变state，而不需要操作DOM。
        //为什么要写这个render函数呢？因为我们没有类似JSX的{}的侵入式的HTML模板标记。
        function render(){
            document.getElementById("title").innerHTML = store.getState().v;
        }
        render();

        //注册视图
        store.subscribe(render);

        document.getElementById("btn1").onclick = function(){
            store.dispatch({"type" : "ADD"})
        }

        document.getElementById("btn2").onclick = function () {
            store.dispatch({ "type": "MINUS" })
        }
    </script>

```
我们做几点非常重要的说明：
**● 不管业务程序有多大，state、reducer、store只有一组。**
比如我们现在我们有计数器和学生列表两个功能，reducer确实是将他们合二为一。
虽然业务有两部分：计数器、学生清单，但是reducer一个，绿色背景的是计数器的业务，蓝色背景的是学生清单的业务。
```
function reducer(state =  defaultState, action){
    if(action.type == "ADD"){
        return {
            ...state,
            "v" : state.v + 1
        }
    }else if (action.type == "MINUS") {
        return {
            ...state,
            "v": state.v - 1
        }
    } else if (action.type == "ZENGJIAXUESHENG") {
        return {
            ...state,
            "students" : [
                ...state.students ,
                {"id" : action.id , "name" : action.name}
            ]
        }
    } 
    return state;
}

```
此时为了reducer的形式规整，我们可以拆分为策略模式。




**● 为了方便代码压缩，所有的Action的大写字母Type可以单独写为一个外置文件**
向外暴露add，本质是字符串"ADD"，目的何在？方便代码压缩，代码中没有必要大量出现"ADD"字样。**任何代码压缩工具不会压缩字符串的**压缩软件会压缩变量名

**● action creator**
返回action的函数我们叫做action creator。
我们现在做一个顶层命名空间actions，里面有二层命名空间叫做counter，列出一个个返回Action对象的函数。我们把add函数、minus函数叫做action creator。
```
var actions = {
    "counter" : {
        add(n = 1){
            return { "type": counter1.add , n} ;
        },
        minus(){
            return { "type": counter1.minus };
        }
    }
}

```
##二、React和Redux结合
###2.1 redux和react-redux介绍
到目前为止，我们的原生React的项目的开发依赖和运行依赖：
```
  "devDependencies": {
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.2",
    "babel-plugin-transform-object-rest-spread": "^6.26.0",
    "babel-preset-env": "^1.6.1",
    "babel-preset-react": "^6.24.1"
  },
  "dependencies": {
    "classnames": "^2.2.5",
    "react": "^16.1.1",
    "react-dom": "^16.1.1"
  }

```
我们现在要把react和redux结合起来，我们需要增加2个运行依赖：
> 	redux : 提供诸如createStore()、bindActionCreator()、combineReducer()等等redux的常用功能，都是函数；
> 	react-redux：就是将react和redux绑定在一起时需要的小工具，里面就提供了两个功能，<Provider>组件和connect()函数。

###2.2 react和redux结合制作计数器
第一步，创建reducer文件，所以我们在app文件夹中创建reducers文件件，在内创建index.js文件
```
export default (state = {"v" : 100} , action) => {
    return state;
}

```
第二步，在main.js中创建store。
```
import React from "react";
import ReactDOM from "react-dom";
import {createStore} from "redux";

import App from "./App.js";
import reducer from "./reducers/index.js";

//创建store，创建store要提供reducer
const store = createStore(reducer);

ReactDOM.render(
    <App></App>
    ,
    document.getElementById("app-container")
);

```
第三步，共享这个store让所有组件可以看见。
```
import React from "react";
import ReactDOM from "react-dom";
import {createStore} from "redux";
import {Provider} from "react-redux";

import App from "./App.js";
import reducer from "./reducers/index.js";

//创建store，创建store要提供reducer
const store = createStore(reducer);

ReactDOM.render(
    <Provider store={store}>
        <App></App>
    </Provider>
    ,
    document.getElementById("app-container")
);

```
Provider干嘛的呢？答：Makes the Redux store available to component hierarchy below. 让所有下辖的组件都能够使用store。

第四步，更改App组件，引入connect函数，将App进行装饰之后暴露！
```
import React from "react";
import {connect} from "react-redux";

class App extends React.Component{
    constructor(props){
        super(props)
    }

    render(){
        return <div>
            我是App组件
        </div>
    }
}

export default connect()(App);

```
第五步，给connect函数里面增加第一个参数，是一个函数，这个函数叫做mapStoreStateToProps，将store中的state和组件的props进行合并、
映射。在组件的JSX中，使用this.props.v来得到store中的v值。也就是说，我们的v感觉就是props的一部分。
```
import React from "react";
import {connect} from "react-redux";

class App extends React.Component{
    constructor(props){
        super(props)
    }

    render(){
        return <div>
            <h1>{this.props.v}</h1>
        </div>
    }
}

export default connect(
    //mapStoreStateToProps
    (state)=>{
        return {
            v : state.v
        }
    }
)(App);

```

第六步，加按钮。我们要先改变app/reducers/index.js中的reducer函数：
```
export default (state = {"v" : 100} , action) => {
    if(action.type == "ZENGJIA"){
        return {
            ...state , 
            "v" : state.v + 1
        }
    }else if(action.type == "JIANSHAO"){
        return {
            ...state ,
            "v" : state.v - 1
        }
    }
    return state;
}

```
更改App组件，我们给connect函数增加第二个函数。
```
import React from "react";
import {connect} from "react-redux";

class App extends React.Component{
    constructor(props){
        super(props)
    }

    render(){
        return <div>
            <h1>{this.props.v}</h1>
            <button onClick={this.props.add}>按我加1</button>
            <button onClick={this.props.minus}>按我减1</button>
        </div>
    }
}

export default connect(
    //mapStoreStateToProps
    (state)=>{
        return {
            v : state.v
        }
    },
    (dispatch)=>{
        return {
            add(){
                dispatch({"type" : "ZENGJIA"});
            },
            minus(){
                dispatch({"type" : "JIANSHAO"});
            }
        }
    }
)(App);

```