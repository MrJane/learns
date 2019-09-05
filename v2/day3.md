##规定propType(自定义属性类型)：
如果要限制一个组件父亲给它传的参数的类型和个数，此时要用PropTypes:
全部的写法请参考：https://reactjs.org/docs/typechecking-with-proptypes.html
```
import React, { Component } from "react";
import PropTypes from 'prop-types';
export default class Compo1 extends Component {
    constructor(props) {
        super(props);
 
    }
 
    render() {
        return <div>
            {this.props.a + 1}
        </div>
    }
}

//**写完类之后**，定义类的propTypes，我们这里规定必须传入一个数值类型的a
Compo1.propTypes = {
    a : PropTypes.number.isRequired ,	//number表示数字，isRequired表示不可省略必传
    fn : PropTypes.func.isRequired		//func表示函数，isRequired表示不可省略必传
}
/*
还有：
 	optionalArray: PropTypes.array,
  	optionalBool: PropTypes.bool,
  	optionalFunc: PropTypes.func,
  	optionalNumber: PropTypes.number,
  	optionalObject: PropTypes.object,
  	optionalString: PropTypes.string
  	方便团队合作，互相之间的组件引用就方便很多
*/
```
##四、生命周期（面试爱考）
##4.1 生命周期三阶段10个必须熟背
组件从诞生到消亡，react提供了很多各个时段会发生的函数，供程序员写一些业务。
**	上树阶段（Mounting阶段）**
constructor()   构造函数，写state什么的
componentWillMount()     将要上树，这里发出Ajax请求
render()                呈递视图
componentDidMount()     上树之后，可以操作DOM
本阶段需要注意的是，在componentDidMount()生命周期函数中，就能操作DOM了，DOM就已经上树了；本阶段的4个函数都没有参数。
**	更新阶段（Updating阶段）**
props的改变或者state的改变将触发本阶段。
componentWillReceiveProps(nextProps) 当父亲传给组件的prop改变的时候发生。在这个函数中，nextProps参数表示新的父亲传入的props对象
shouldComponentUpdate(nextProps, nextState)  门神。当父亲传入的props改变或者自己state改变发生。return true此时才会继续执行后面的声明周期；return false不执行后面的生命周期了。如果没有写门神函数，默认return true。
componentWillUpdate(nextProps, nextState)       更新之前做的事情，此时视图没有变化，state和props都没有变化呢
render()
componentDidUpdate(prevProps , prevState)   更新之后做的事情
**	下树阶段（Unmounting阶段）**
componentWillUnmount()  将要下树
###4.2 防止生命周期死循环
只要进入了某一个阶段，则这个阶段的生命周期都会做。
所以在Updaing整个阶段中，5个生命周期：componentWillReceiveProps、shouldComponentUpdate、componentWillUpdate、render、componentDidUpdate中，都不能操作state！如果操作了state，则再来一遍！子子孙孙无穷匮也，就死循环了。
```
比如在render函数中调用this.setState方法 状态更新 又会调用render函数 在以上几个函数道理一样
```

##六、初识Redux - Redux菜鸡计数器案例
Redux属于React套件的一部分，也来自facebook。
官网的第一句话，说了Redux是什么：
Redux is a predictable state container for JavaScript apps.
Redux是一个可预测的状态容器。
**我们先用一个最最简单的案例，学习Redux**
只有一个index.html页面，这个案例和React没有关系！
具体代码见案例，笔记上是代码的剖析。
第一遍
```
<!DOCTYPE html>
<html>
<head>
    <title>Redux</title>
    <script src="redux.min.js"></script>
</head>
<body>
    <div>
        <h1 id="title">0</h1>  
        <button id="jiaBtn">+</button>
        <button id="jianBtn">-</button>
    </div>
    <script>
        //这个函数我们称为reducer，这个函数接受两个参数state和action，返回新的state。
        //这里罗列了所有action会导致的state的变化
        //对于state的形式取决于你，可以是一个基本值，可以是一个对象。
        //下面的案例中，state是一个对象，并且初始值已经通过ES6的默认属性特性，写在了圆括号中，就是v:100
        //你看，这个函数里面通过if语句分支，根据action的type属性不同，返回了不同的state。
        //state的v值要么被加了1；要么被减了1。但是，都是返回的新的state，没有直接改变原来的state。
        function counter(state = {"v" : 100} , action){
            if(action.type == "ZENGJIA"){
                return {"v" : state.v + 1};
            }else if(action.type == "JIANSHAO"){
                return {"v" : state.v - 1};
            }
            return state;
        }

        //store我们叫做“仓库”是存储、管理state的地方。
        //创建仓库需要提供reducer，也就是说store是存储state的地方，而reducer是对数据一系列可能的操作。
        var store = Redux.createStore(counter);

        //得到DOM元素
        var oTitle = document.getElementById('title');

        //这里写一个render函数，sotore.getState()从仓库中取出state。让DOM的html显示
        function render(){
            oTitle.innerHTML = store.getState().v;
            console.log(store.getState());
        }

        //调用一下
        render();
        
        //将store注册到视图，这样一旦store中的数据变化，就会自动调用render函数
        store.subscribe(render);

        //按钮的监听
        //改变state的唯一的方法就是要dispatch一个action。action我们叫做“命令”。
        //至此，我们用Redux.createStore(reducer)造出来的store小王八蛋，调用了三个方法：
        //  store.getState();   从仓库中读值
        //  store.subscribe()   将store注册到视图，store一变化，自动改变视图
        //  store.dispatch();   发送命令
        document.getElementById("jiaBtn").onclick = function(){
            store.dispatch({"type" : "ZENGJIA"});
        }

        document.getElementById("jianBtn").onclick = function () {
            store.dispatch({ "type": "JIANSHAO" });
        }
        
    </script>
</body>

</html>
```
第二遍
```

```
第三遍
```

```
第四遍
```

```
第五遍
```

```
###6.1 Reducer - 实现可被预测的状态
下面这个函数就是reducer，这里就是所谓的“predictable”可以被预测的含义。所有对state的操作，都要罗列出来。
**reducer函数必须符合的形态：接受之前的state和action，返回新的state：**
```
function counter(state = {"v" : 100} , action){
    if(action.type == "ZENGJIA"){
        return {"v" : state.v + 1};
    }else if(action.type == "JIANSHAO"){
        return {"v" : state.v - 1};
    }
    return state;
}

```
**在reducer中是绝对不能更改state的，而是应该返回新的state。**
###6.2 创建Store，并且注册视图
store存储了state数据，并且维持了一套操作state的API，就是reducer。
```
var store = Redux.createStore(counter);
/*
store有几个功能：
	可以读取数据，通过store.getState();
	允许更新state，通过store.dispatch(action);
	注册视图，通过，subscribe(listener);
*/
store.subscribe(render);

```
###6.3 发送action
按钮监听不是直接改变state的值，而是调用store的dispatch方法发送命令给仓库，比对reducer中的if语句，执行相应的改变。
```
document.getElementById("jiaBtn").onclick = function(){
     store.dispatch({"type" : "ZENGJIA"});
}

document.getElementById("jianBtn").onclick = function () {
      store.dispatch({ "type": "JIANSHAO" });
 }

```
###6.4 数据的单向流动
数据的单向流动的思维来自Flux。
> 从今天开始，我们进入了Redux思维，以后的编程：
> 1）先罗列reducer中的if语句，想好所有可能的改变；
> 2）改变的时候不直接改，而是dispatch。
