## webpack配置
引入babel-loader翻译js文件，但babel-loader依赖babel-core 所以webpack 要安装这两个包,还需要装babel-preset-es2015 //需要注意三个的版本差异
npm install babel-loader babel-core babel-preset-es2015 -D

配置react！安装react
npm install react -D  //为什么是开发依赖？为什么不用bower安装而是npm安装
1.react并不是在html里引入而是在js中import导入,所以npm安装都会node_modules文件夹里，import导入会自动寻找node_modules文件夹
2.


安装完依赖，webpack.config.js babel-loader中presets要增加一项react,
```
{
                test:/\.js$/,
                exclude: /(node_modules|bower_components)/ ,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['es2015','react']
                    }
                }
            }
```
增加这项只之后又要安装一个```babel-preset-react```，不然```webpack```会报错


###Hello组件
```
import React , {Component} from "react";

//我们定义一个叫做App的组件，在HTML中可以用<App></App>来使用它
//React要求自定义组件必须是大写字母开头！
//React要求自定义组件的类必须继承于React.Component类
class App extends Component{
	//组件中最重要的方法就是render方法，render是渲染的意思
	render(){
		//返回一个jsx语法，非常牛逼语法
		return <h1>我是React，很高兴遇见你！说{5000+5000}次我爱你！</h1>;
	}
}

//向外暴露
export default App;
```
使用Hello组件
```
import React from "react";
import {render} from "react-dom";
//引入组件
import App from "./App.js";

//使用、挂载组件，两个参数
//第一个参数是jsx语法
//第二个参数表示组件挂在哪里
render(
	<App></App> ,
	document.getElementById("app-container")
);

```

##JSX语法：
jsx不能直接运行，是被babel-loader中的react这个preset翻译的
```
{
     test:/\.js$/,
     exclude: /(node_modules|bower_components)/ ,
     use: {
     loader: 'babel-loader',
     options: {
          presets: ['es2015','react'] //
     }
           }
}
```
需要注意：
① 必须被一个单独的大标签包裹，比如div或者section
错误的：
```
return <h1>我是React，很高兴遇见你！HAHAHAHA</h1>
<h1>我是React，很高兴遇见你！HAHAHAHA</h1>;

```
正确的：应该被一个div包裹起来，小括号也挺好用的：
```
return (
	<div>
		<h1>我是React，很高兴遇见你！HAHAHAHA</h1>
		<h1>我是React，很高兴遇见你！HAHAHAHA</h1>
	</div>
);

```
② 标签必须封闭
错误的：
```
return (
	<div>
		<img src="" >
	</div>
);

```
正确的：
```
return (
	<div>
		<img src="" />
	</div>
);

```
③ class要写成className，for要写成htmlFor
错误的：
```
return (
	<div>
		<div class="box" ></div>
	</div>
);

```
正确的：
```
return (
	<div>
		<div className="box" ></div>
	</div>
);

```
④ HTML注释不能使用，只能使用JS注释。
```

```
⑤ 原生标签比如p、li、div如果要使用自定义属性，必须用data-前缀
错误：
```
return (
			<div>
				<h1>我是React，很高兴遇见你！</h1>
				<p a="10"></p>
			</div>
		);

```
正确：
```
return (
			<div>
				<h1>我是React，很高兴遇见你！</h1>
				<p data-a="10"></p>
			</div>
		);

```
如果是自定义标签，随便传属性：
```
<App a="123213"></App>
```
⑥ JS表达式用{}单大括号包裹，在 JSX 中不能使用 if else 语句，但可以使用 conditional (三元运算) 表达式来替代。以下实例中如果变量 i 等于 1 浏览器将输出 true, 如果修改 i 的值，则会输出 false.
```
<h1>我是React，很高兴遇见你！{1+2}</h1>
{3 > 8 ? "A" : "B"}
```
⑦ 可以运行函数：
```
import React from "react";

//我们定义一个叫做App的组件，在HTML中可以用<App></App>来使用它
//React要求自定义组件必须是大写字母开头！
//React要求自定义组件的类必须继承于React.Component类
class App extends React.Component{

	//定义一个函数haha，接受一个参数
	haha(lanmu){
		if(lanmu == "篮球"){
			return (

					<ul>
			<li>乔丹</li>
						<li>科比</li>
						<li>奥尼尔</li>
					</ul>
			)
		}else if(lanmu == "足球"){
			return (
				<ul>
					<li>李铁</li>
					<li>郝海东</li>
					<li>C罗</li>
				</ul>
			)
		}
	}

	//组件中最重要的方法就是render方法，render是渲染的意思
	render(){
		//返回一个jsx语法，非常牛逼语法
		return (
			<div>
				<h1>我是React，很高兴遇见你！</h1>

				<h3>JSX语法很好用哦！</h3>
				{3 > 8 ? "A" : "B"}

				<h3>JSX可以调用函数哦！</h3>
				{this.haha("足球")}
			</div>
		);
	}
}

//向外暴露
export default App;

```
⑧ 样式使用双大括号
```
<h3>JSX可以设置样式哦！</h3>
<div style={{"width":"100px","height":20 + 30 + "px","backgroundColor" : "red"}}></div>

```
⑨ 可以使用数组，数组中如果是JSX语法，数组会被自动展开。所以不需要使用ng-repeat这样的指令展开数组。


##React中的数据传递
React中跟数据有关系的就三个东西：state、props、context。我们可以叫做数据传递三兄弟。
###组件自己身上的属性：
组件自己身上的属性值变化，不会引起视图改变
我们可以在组件的类定义里面写constructor构造器，里面定义this.a = 100 。表示给组件的实例绑定一个a属性，值是100。在jsx中使用的时候，直接this.a即可。
```
import React from "react";

class App extends React.Component{
	//构造函数
	constructor(){
		super();  //要求调用super
		this.a = 100;
	}
	add () {
            this.a++;
            console.log(this.a); //点击按钮的时候this.a确实变化了，但是视图没有更新，所以React中，组件自己的属性的变化不会引发视图的变化！！
    }
	//组件中最重要的方法就是render方法，render是渲染的意思
	render(){
		//返回一个jsx语法，非常牛逼语法
		return (
			<div>
				{this.a}
				<button onClick={this.add.bind(this)}>点我++</button>
			</div>
		);
	}
}
//向外暴露
export default App;
```
① 绑定监听使用onClick、onMousedown、onMouseenter、onBlur，把on后面的字母大写，React会自动识别React事件。
② 绑定监听函数的时候，this上下文是有问题的，所以需要使用bind()方法来设置上下文
③ 绑定监听函数的时候，注意用{}而不是""
```
错误的：
<input type="button" value="按我" onClick="(this.add).bind(this)" />
```
###4.2 闭包中的值变化，不会引起视图改变
```
import React from "react";

class App extends React.Component{
	render(){
		let b = 99; //这个b在render函数中相当于闭包
		return (
			<div>
				<p>{b}</p>
				<p>
					<input type="button" value="按我" onClick={()=>{b++;console.log(b) //点击按钮b值确实变化;}} />
				</p>
			</div>
		);
	}
}

//向外暴露
export default App;
//闭包中的值变化，也不会引起视图的改变！
```
###4.3 三兄弟之一 ：state
React 把组件看成是一个状态机（State Machines）。通过与用户的交互，实现不同状态，然后渲染 UI，让用户界面和数据保持一致。
React 里，只需更新组件的 state，然后根据新的 state 重新渲染用户界面（不要操作 DOM）。
```
import React from "react";
class App extends React.Component{
 	constructor(){
 		super();
 		this.state = {
 			a : 100,
 			b : 200,
 			c : 300
 		}
 	}
 	add(){
 			this.setState({a : this.state.a + 1});
 	}
 	render(){
 		return (
 			<div>
 				<h1>我是APP组件</h1>
 				<p>我有状态state</p>
 				<p>a : {this.state.a}</p>
 				<p>b : {this.state.b}</p>
 				<p>c : {this.state.c}</p>
 				<p>
 					<input type="button" value="按我" onClick={(this.add).bind(this)}/>
 				</p>
 			</div>
 		)
 	}
}
//向外暴露
export default App;
```
只有更新三兄弟，才会引发Virtual DOM的改变，从而改变DOM。
定义state： 在构造函数中使用this.state属性即可
使用state ：在JSX中{this.state.a}
改变state： this.setState({a : this.state.a + 1});  不能写++，因为state属性值只读this.state.a++会改变本身
state是内部的（所以也叫作local state），只有组件自己能改变自己的state，别人想改变自己的state，都不可能！
后面要介绍Redux架构，所有组件自己的state越来越少用了，而是变为了Redux中的state，不要混淆
###4.4props
就是定义在自定义组件标签上面的值，就是props。当props改变的时候，会引发Virtual DOM的改变，从而引发视图的重绘。
react崇尚数据的单向流动，所以设计的时候就是让数据从父组件流向子组件。props在子组件中是只读的，不能修改的。
如果父组件App，想往子组件MyCompo中传值，使用属性：
```
app.js：
import React from "react";
import MyCompo from "./MyCompo.js";
class App extends React.Component{
 	constructor(){
 		super();
 	}
 	render(){
 		return (
 			<div>
 				<MyCompo a="66" b="77" c="88"></MyCompo>
 			</div>
 		)
 	}
}
//向外暴露
export default App;

```
子组件MyCompo中就可以无脑使用this.props来枚举传入的属性：
```
MyCompo.js:
import React from "react";
class MyCompo extends React.Component{
 	constructor(){
 		super();
 	}
 	render(){
 		return (
 			<div>
 				我是MyCompo组件
 				<p>{this.props.a}</p>
 				<p>{this.props.b}</p>
 				<p>{this.props.c}</p>
 			</div>
 		);
 	}
}
//向外暴露
export default MyCompo;
```
如果需要在构造函数中使用这个值，此时系统会将props最为构造函数的第一个参数传入：
```
class MyCompo extends React.Component{
 	constructor(props){
 		super();
 		this.state = {
 			c : props.c
 		}
 	}
 }
//在子组件中，props是只读的，不能修改props的值。如果要修改，用state来接收。
```
####props是标签上的属性可以被验证有效性：
npm install --save-dev prop-types //react分离了
```
import React from "react";
import { PropTypes } from "prop-types";
class MyCompo extends React.Component{
 	constructor(props){
 		super();
 		this.state = {
 			c : props.c
 		}

 	}
 	//MyCompo.propTypes={}  //propTypes不能写在实例上，这是类的属性，只能在外面挂载
 	render(){
 		return (
 			<div>
 				我是MyCompo组件
 				<p>{this.props.a}</p>
 				<p>{this.props.b}</p>
 				<p>{this.state.c}</p>
 				<p>
 					<input type="button" value="按我" onClick={()=>{this.setState({c : this.state.c + 1});}}/>
 				</p>
 			</div>
 		);
 	}
}
//定义组件需要传入的参数，这些都是语法规定
//类名.propTypes，值是一个JSON。key就是需要传进来的props属性名，v就是对它的限制,propTypes不能写在实例上
MyCompo.propTypes = {
	a : PropTypes.string.isRequired,  	//a属性是一个字符串，必传
	b : PropTypes.string,  				//b属性是一个字符串，不必传
	c : PropTypes.number.isRequired		//c属性是一个数组，必传
};
//向外暴露
export default MyCompo;
```
###子组件穿父组件
如果非要从下到上传输数据呢？子组件要把数据返回给父组件，此时只能用奇淫技巧。就是父组件传一个函数给子组件，
子组件通过传参数调用函数将数据返回给父组件的函数，父组件的函数接受实参改变父组件中的state等值。
父组件App现在想让子组件MyCompo设置App自己的state.d值，此时就需要传入d值，并且传入一个设置d值的函数到子组件：


##4.5 三兄弟之三： context
上下文的精髓是可以跨级传递数据，爷爷组件可以直接传递数据到孙子组件。
语法比较晦涩：

##五、其他API
###5.1 无状态组件
当我们现在的组件仅仅是为了呈递一些DOM元素，没有state、props等东西，此时可以不用费劲
class My extends React.Component{}
而是一个暴露简单函数即可：
```
My.js
import React from "react";

export default () => {
	return (
		<div>
			<h1>我是My组件</h1>
		</div>
	)
}
```
此时真的可以用它：
```
import React from "react";
import My from "./My.js";

class App extends React.Component{
 	constructor(){
 		super();

 	}

 	render(){
 		return (
 			<div>
 				<My></My>
 			</div>
 		)
 	}
}

//向外暴露
export default App;
```
###5.2 默认属性
如果没有传入name输入，那么就以“菜鸟”作为属性：
```
	constructor(props){
		super();
    this.state = {
         name : props.name || "菜鸟"
        }
	}
	//现在很少用getDefaultProps了
```
###5.3 组件的生命周期
组件从没有诞生到诞生，到被移除的完整生命周期，都提供了可以触发事件的函数接口，就是生命周期。
组件的生命周期包含三个主要部分：

挂载： 组件被插入到DOM中。
更新： 组件被重新渲染，查明DOM是否应该刷新。
移除： 组件从DOM中移除。
```
//挂载之前
	componentWillMount(){
		console.log("componentWillMount");
	}

	//在挂载结束之后马上被调用。需要DOM节点的初始化操作应该放在这里。
	componentDidMount(){
		console.log("componentDidMount");
	}

 	//当组件做出是否要更新DOM的决定的时候被调用。“门神”，在改变状态的时候可以选择通过或者不通过。
 	shouldComponentUpdate(nextProps , nextState){
 		if(nextState.a > 0.8){
 			return true;
 		}
 		alert("本次没有随机到大于0.8的，更新被阻止");
 		return false;
 	}

 	//在更新发生之前被调用，没有把门的功能
 	componentWillUpdate(){
		console.log("componentWillUpdate");
 	}

 	//在组件移除和销毁之前被调用
 	componentWillUnmount(){
 		console.log("componentWillUnmount");
 	}

```
###5.4 React中的表单
React没有提供类似Angular中复杂的表单验证功能，也没有双向数据绑定！
如果非要做一个效果：文本框中输入内容，实时改变h1中的内容此时就要用模拟的方法，监听文本框的事件，然后改变组件的state状态。
```
import React from "react";
class My2 extends React.Component{
	constructor(props){
		super();
	 	this.change = (this.change).bind(this);
	 	this.state = {
	 		"txt" : ""
	 	}
	}
	change(event){
		this.setState({"txt" : event.target.value });
	}
	render(){
		return (
			<div>
			 	<input type="text" onInput={this.change} />
			 	<h1>{this.state.txt}</h1>
 			</div>
		)
	}
}
export default My2;
```
###5.5 ref DOM钩子
```
```
有时候需要操作DOM元素，ref属性表示一个钩子。可以在其他地方使用this.refs来获得这个DOM元素。
import React from 'react';
class My3 extends React.Component{
    constructor (){
        super();
        this.handleChange =this.handleChange.bind(this);
    }
    handleChange(){
        this.refs.mybox.style.background='red';
        console.log(this.refs.mybox);
    }
    render(){
        return(
            <div>
                <input type="button" onClick={this.handleChange}/>
                <div ref="mybox" style={{width:'100px',height:'100px',backgroundColor:'orange'}}></div>
            </div>
        )
    }
}
export default My3;