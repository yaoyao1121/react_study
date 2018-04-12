#  第一章 通过create-react-app新建react项目

   **首先，要确认已经安装了nodejs和npm**
  

#### **1.设置开发环境**
	  

```
  npm install --global create-react-app  
  全局安装create-react-app工具
```

####  **2.创建新项目**
     

```
 create-react-app 项目名称
```

#### **3.启动服务器**
     

```
在当前项目下打开终端（cmd命令窗口）
npm start 启动项目（在package.json 中 scripts 里查看）
**与 vue 相同的是，react 也同样会热加载，即浏览器会自动刷新
```

#### **4.分析文件结构及代码**
   **4.1分析create-react-app给我们自动产生的代码**
```
  项目目录下包含如下文件夹：src public README.md package.json node_modules
  在开发过程中，我们要关注的 src 目录中的内容，这个目录中是所有的源代码
```
 **4.2入口文件 src/index.js 文件，代码如下：**
```
    import React from 'react'; //核心库
    import ReactDOM from 'react-dom';//React的Dom适配库
    import App from './App'; //组件
    import './index.css'; //css样式
    ReactDOM.render(
	    <App />,
	    document.getElementById('root')  //public/index.html
    ); // 注册组件（实例挂载）

//这个应用所做的事情，只是渲染一个名字叫做App的组件，App组件在同目录下的App.js文件中定义
```
#### **5.增加一个新的React组件**
```
   功能：定义一个能够点击计算增加减少数的组件
```
在src目录下增加一个Click Counter.js，代码如下：

```
import React,{Component} from 'react';  //导入react组件
//ES6语法  定义一个继承React的Click Counter类
class ClickCounter extends Component{
    constructor(props){  //构造函数
        super(props); //接受父组件的值，调用父类
        this.onClickadd = this.onClickadd.bind(this);  //定义事件 加一
        this.onClickReduce = this.onClickReduce.bind(this); // 减一
        this.state ={count : 0}; //给定初始值
    }
    onClickadd(){
        //加一函数
        this.setState({count : this.state.count+1});
    }
    onClickReduce(){
        //减一函数
        this.setState({count : this.state.count-1});
    }
    render(){   //render函数是最主要的，渲染页面 只能有一个根元素 同vue
        return(
            <div>
                <button onClick = {this.onClickadd}>+</button>
                <button onClick = {this.onClickReduce}>-</button>
                <div>
                    Click Count : {this.state.count}
                </div>
            </div>
        );
    }
}
export default ClickCounter;  //默认抛出组件
```
在src目录的index.js文件中注册，代码如下：

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import ClickCounter from './ClickCounter'
ReactDOM.render(<ClickCounter />, document.getElementById('root'));
```
#### **6.组件之间的传值**
 **6.1 父传子**（用props接收）
```
功能：点击任何一个Counter的 + 或者 - 按钮 ，实现对应的Counter的计数变化
```
 在src目录下创建父组件CounterPanel.js，代码如下：

```
import React , {Component} from "react";
import Counter from "./Counter" //引入子组件
class CounterPanel extends Component{
    render(){
        return(
        //变量说明 caption 标题传给子组件的值   initValue 初始值  step 步长（决定加减多少）
            <div>
                <Counter  caption="First" />
                <Counter  caption="Second" initValue={10}/>
                <Counter  caption="Third" initValue={5} step={2}/>
            </div>
        )
    }
}
export default CounterPanel 
```
在src目录下创建子组件Counter.js，代码如下：

```
import React,{Component} from 'react';
class Counter extends Component {
    constructor(props){
        super(props); //接受父元素的值，调用父类
        this.newAdd = this.newAdd.bind(this);
        this.reduce = this.reduce.bind(this);
        this.state = {
            count : props.initValue || 0, //初始值，设置默认值也可以用Counter.defaultProps
            step : props.step || 1 //父元素有值就用父元素的值，没有默认为1
        }
        //propTypes 类型检查 可以不写
        //Counter.propTypes={
		    //caption:PropTypes.string.isRequired, //caption 类型必须是字符串
		    //initValue:PropTypes.number,
		    //step:PropTypes.number
		//}
        //设置默认值
        //Counter.defaultProps ={
            //initValue:0,
		    //step:1
        //}
    }
    
    newAdd(){
       //设置count值
       this.setState({count :this.state.count + this.state.step})
    }
    reduce(){
       this.setState({count : this.state.count - this.state.step})
    }
    render(){
        const {caption} =this.props;
        return(
            <div>
                <button onClick = {this.newAdd}>+</button>
                <button onClick = {this.reduce}>-</button>
                <span>
                    {caption} Count :{this.state.count}
                </span>
            </div>
        )
    }
}
export default Counter; //默认抛出组件
```
在src目录下index文件中注册，代码如下：
```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import CounterPanel from "./CounterPanel"
ReactDOM.render(<CounterPanel />, document.getElementById('root'));
```
 **6.2 子传父**（子组件用函数形式返回，父组件用函数调用以及接收）
```
 功能：点击任何一个Counter的 + 或者 - 按钮，除了可以看见数值的变化外，底部的总计数也会随之变化
```
  在src目录下创建父组件CounterPanel.js，代码如下：

```
import React , {Component} from "react";
import Counter from "./Counter"  //引入子组件
class CounterPanel extends Component{
    constructor(props){ //父组件需要有自己的构造函数
        super(props);
        this.onCounterUpdate=this.onCounterUpdate.bind(this);
        this.initValues=[1,5,10]; //初始值
        const initSum=this.initValues.reduce((a,b)=> a+b,0); //ES6 累和函数
        this.state={
            sum:initSum //总和初始值
        }
    }
    onCounterUpdate(newValue,previousValue){
        const valueChage=newValue-previousValue; //增加或减少了多
        this.setState({sum:this.state.sum +valueChage}) //设置总和
    }
    render(){
        return(
            <div>
                <Counter onUpdate={this.onCounterUpdate} caption="First" initValue={this.initValues[0]}/>
                <Counter onUpdate={this.onCounterUpdate} caption="Second" initValue={this.initValues[1]}/>
                <Counter onUpdate={this.onCounterUpdate} caption="Third" initValue={this.initValues[2]} step={2}/>
                <div>Total Count :{this.state.sum}</div>
            </div>
        )
    }
}
export default CounterPanel
```
在src目录下创建子组件Counter.js，代码如下：

```
import React,{Component} from 'react';
class Counter extends Component {
    constructor(props){
        super(props);
        this.newAdd = this.newAdd.bind(this);
        this.reduce = this.reduce.bind(this);
        this.state = {
            count : props.initValue || 0,
            step : props.step || 1
        }
        //设置默认值
        Counter.defaultProps ={
            onUpdate: f=>f //默认是什么都不执行的函数
        }
    }
    newAdd(){
        this.updateCount(true);//只需调用updateCount更新函数
    }
    reduce(){
        this.updateCount(false);
    }
    updateCount(isIncrement){
        const previousValue = this.state.count;
        //newValue 是当前赠或加之后得值  previousValue 是原来的值
        const newValue = isIncrement ? previousValue+this.state.step : previousValue-this.state.step;
        this.setState({count:newValue});
        this.props.onUpdate(newValue,previousValue) //父组件的函数
    }
    render(){
        const {caption} =this.props;
        return(
            <div>
                <button onClick = {this.newAdd}>+</button>
                <button onClick = {this.reduce}>-</button>
                <span>
                    {caption} Count :{this.state.count}
                </span>
            </div>
        )
    }
}
export default Counter;
```
在src目录下index文件中注册，代码如下：
```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import CounterPanel from "./CounterPanel"
ReactDOM.render(<CounterPanel />, document.getElementById('root'));
```
#### **7. React-Router 路由**
     

 **首先 npm i react-router --save-dev   下载到生产环境**
 
 在src目录下创建pages文件夹里面包含三个组件 分别为Home.js   About.js   NotFound.js  代码如下：

```
 //Home.js 组件
 import React , {Component} from "react";
 class Home extends Component{
    render(){
        return(
            <div>
                <h1>Home</h1>  //内容
            </div>
        )
    }
}
export default Home;
//About.js 组件 和 NotFound.js 组件 同上  内容不同
```
在src目录下创建routes.js 路由文件，代码如下：

```
//React-router库提供了两个组件来完成路由功能，
//一个是Router（在整个应用中只需要一个实例，代表整个路由器）
//另一个是Route（代表每一个路径对应页面的路由规则，一个应用中应该会有多个Route实例）
import React, { Component } from 'react';
import {Router,Route,hashHistory} from 'react-router'  //导入路由
import Home from "./pages/Home.js"  //导入组件
import About from "./pages/About.js";
import NotFound from "./pages/NotFound.js";
//browserHistory 和 hashHistory 区别 https://www.cnblogs.com/liuna/p/6137970.html
//history 属性有三个值：browserHistory  hashHistory  createMemoryHistory
//创建路由
class Routes extends Component{
    render(){
        return(
            <Router history={hashHistory}>
                <Route path="/" component={Home} /> //默认打开Home页
                <Route path="home" component={Home} />
                <Route path="about" component={About} />
                <Route path="*" component={NotFound} />
            </Router>
        )
    }
}
export default Routes;
```
在src目录下index文件中注册，代码如下：
```
import React from 'react';
import ReactDOM from 'react-dom';
import Routes from "./Routes.js"
ReactDOM.render(<Routes />, document.getElementById('root'));
```